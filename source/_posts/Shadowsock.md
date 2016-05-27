title: Shadowsock-Android修改
category: scala
tags: scala
date: 2016-05-27 16:41:03
---

## 主要想法
网络上有不少免费的SS账号，但大多数都是每隔几个小时就变一次密码的。所以期望在SS-android版中增加一个小功能：点击一个按钮，便自动获取网络上某个免费账号。

#### SS-Android分析
java部分是java和scala混合编程的，其中java用于特定的安卓部分用scala无法实现的。
网络传输编码解码部分是c或者c++的。
我们这边主要修改的是主界面部分。

<!-- more -->

#### 界面修改
在`com.github.jorgecastilloprz.FABProgressCircle`末尾加上
``` xml
    <Button
        android:id="@+id/autoFecthButton"
        android:text="AUTO"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignBottom="@id/fabProgressCircle"
        android:layout_alignParentRight="true"
        android:layout_alignParentEnd="true"
        android:layout_marginRight="16dp"
        android:layout_marginEnd="16dp"
        android:layout_marginBottom="@dimen/fab_margin_bottom"/>
```

#### 逻辑修改
在`Shadowsocks.scala`第348行之后加上：
``` scala
    val autoFetchButton = findViewById(R.id.autoFecthButton).asInstanceOf[Button]
    autoFetchButton.setOnClickListener((v: View) => {
      ThrowableFuture {
        try {
          val connection = new URL("http://www.ishadowsocks.com").openConnection().asInstanceOf[HttpURLConnection]
          connection.setConnectTimeout(1000*10)
          connection.setReadTimeout(1000*10)
          connection.setRequestMethod("GET")
          connection.connect()
          val is = connection.getInputStream
          val bufferedReader = new BufferedReader(new InputStreamReader(is))
          val str2 = Stream.continually(bufferedReader.readLine()).takeWhile(_ != null)
          val str = str2.mkString
          val regexServer = """服务器地址:([\w.]*)<""".r
          val regexPassword = """密码:(\d*)<""".r
          val resServers = regexServer.findAllIn(str)
          val resPasswords = regexPassword.findAllIn(str)
          if (resServers.nonEmpty && resPasswords.nonEmpty) {
            val server = resServers.group(1)
            val pass = resPasswords.group(1)
            handler.post(() => {
              preferences.setPref(server, pass)
              Toast.makeText(this, server + ":" + pass, Toast.LENGTH_SHORT).show()
            })
          } else {
            handler.post(() => {
              Toast.makeText(this, "无法获取服务器和密码", Toast.LENGTH_SHORT).show()
            })
          }
        } catch {
          case _: Throwable => handler.post(() => {
              Toast.makeText(this, "网络连接中出现错误", Toast.LENGTH_SHORT).show()
            })
        }
      }
    })
```
同时顺便注释了第457行到461行的广告代码。

在`ShadowsocksSettings.scala`第192行之后加上监听preference数据源变化之后的显示代码
``` scala
    case Key.proxy => 
      val proxyStr = sharedPreferences.getString(Key.proxy, "")
      findPreference(Key.proxy).asInstanceOf[SummaryEditTextPreference].setText(proxyStr)
    case Key.sitekey =>
      val sitekeyStr = sharedPreferences.getString(Key.sitekey, "")
      findPreference(Key.sitekey).asInstanceOf[PasswordEditTextPreference].setText(sitekeyStr)
    case _ =>
```

第220行之后加上插入新数据的代码
``` scala
  def setPref(server: String, pass: String) {
    Log.e("sos", "in setPref function")
    findPreference(Key.proxy).getEditor().putString(Key.proxy, server).apply();
    findPreference(Key.sitekey).getEditor().putString(Key.sitekey, pass).apply();
  }
```

## 代码
[SS-android修改][1]

[1]: https://github.com/Logistic1994/shadowsocks-android "shadowsocks-android"
