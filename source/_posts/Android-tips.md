title: Android_tips
date: 2014-11-08 16:32:35
categories: Android
tags: [Android, tips]
---
* 回到Home界面
{% codeblock lang:java 回到Home %}
	Intent gohomeIntent = new Intent("android.intent.action.MAIN");
	gohomeIntent.addCatory("android.intent.category.HOME");
	startActivity(gohomeIntent);
{% endcodeblock %}
	
* 全屏显示
{% codeblock lang:java 全屏显示 %}
	requestWindowFeature(Window.Featrue_NO_TITLE);
	setContentView(R.id.main_activity);
	getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN,
		WindowManager.LayoutParams.FLAG_FULLSCREEN);
{% endcodeblock %}
注意要把requestWindowFeature放在setContentView之前，因为在setContentView中会根据当前的feature决定放不放title

* 获取view的显示大小
可以在view中注册观察者接口
或者在onWindowFocusChanged方法中获取

* Application
{% codeblock lang:java %}
void onConfigurationChanged(Configuration newConfig)
/** Called by the system when the device configuration changed while your component is running */
{% endcodeblock %}

* Activity彻底启动后的回调
{% codeblock lang:java %}
protected void onPostCreate(Bundle savedInstanceState)
{% endcodeblock %}