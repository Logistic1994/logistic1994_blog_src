title: CursorAdapter出现空指针异常
date: 2014-11-28 13:28:19
categories: Android
tags: [Android, NullPointerError]
---
- 0x01
	使用Loader<Cursor>配合CursorAdapter时遇到NullPointerException。
	代码精简后如下：
	{%codeblock lang:java%}
	protected ListView mListView;
	@Override
	public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState){
		mListView = getListView();
		mListView.addFootHeader(new LoadingFooter());
		// 定义CursorAdapter
		CursorAdapter cursorAdapter = new CursorAdapter(){
			// 创建新的View
			@Override
			public View newView(Context context, Cursor cursor, ViewGroup viewGroup){
				return LayoutInflater.from(context).inflate(R.layout.listitem, null);
			}
			// 绑定数据
			@Override
			public void bindView(View view, Context context, Cursor cursor){
				String name = cursor.getString(cursor.getColumnIndex(ContactsContract.Contacts.DISPLAY_NAME));
        		String hasnumber = cursor.getString(cursor.getColumnIndex(ContactsContract.Contacts.HAS_PHONE_NUMBER));
        		TextView tv_name = (TextView)view.findViewById(R.id.name);
        		TextView tv_hasnumber = (TextView)view.findViewById(R.id.hasname);
        		tv_name.setText(name);
        		t_hasnumber.setText(hasnumber);
			}
		}
		mListView.addAdapter(cursorAdapter);
	}
	// implement LoaderManager.LoaderCallBacks<Cursor>
	// 创建Loader<Cursor>回调，此处不在主线程中
	@Override
	public Loader<Cursor> onCreateLoader(int i, Bundle bundle){
		return new CursorLoader(getActivity(), 
					ContactsContract.Contacts.CONTENT_URI,
					null, null, null, null);
	}
	// 从sqllite中提取数据完毕回调
	@Override
	public void onLoadFinished(Loader<Cursor> cursorLoader, Cursor cursor){
		((CursorAdapter)getAdapter()).changeCursor(cursor);
	}
	// reset
	@Override
	public void onLoaderRest(Loader<Cursor> cursorLoader){
		((CursorAdapter)getAdapter()).changeCursor(null);
	}
	{%endcodeblock%}

	发生的异常为:
	tv_name 为 null

	真机调试的结果：
	- 偶尔完美运行
	- 大部分情况是 listview中第一个item为空，上拉时发生空指针异常

- 0x02
	思考过程：
	- 1.tv_name之所以出现异常，肯定是因为参数中的view不是从R.layout.listitem中inflate过来的，直接将view.toString()输出到控制台
		结果为正常的view的是LinearLayout,不正常的view是FrameLayout。得证。
	- 2.是否是newView()函数调用的:debug发现，此view不是从newView中包括过来的。
	- 3.上拉时发生空指针异常是因为ListView对View复用的结果，第一个不包含tv_name的View被复用，导致下一次使用的时候发生空指针
	- 4.为什么第一次没有对这个额外的view进行bindView操作?
	- 5.这个View到底是从哪里来的？？？

- 0x03
	通过查阅CursorAdapter源代码，发现它在覆盖BaseAdapter的getView()时，先检查数据有效性，然后检查mCursor在position位置的有效性，然后如果convertView为空，则调用newView()函数，最后在convertView上绑定数据。所以，从理论上讲，那个额外的View应该是通过某个其他的途径生成，从而绕过了getView函数，否则不调用bindView。
	CursorAdapter的getView源码
	{% codeblock lang:java %}
	/**
     * @see android.widget.ListAdapter#getView(int, View, ViewGroup)
     */
    public View getView(int position, View convertView, ViewGroup parent) {
        if (!mDataValid) {
            throw new IllegalStateException("this should only be called when the cursor is valid");
        }
        if (!mCursor.moveToPosition(position)) {
            throw new IllegalStateException("couldn't move cursor to position " + position);
        }
        View v;
        if (convertView == null) {
            v = newView(mContext, mCursor, parent);
        } else {
            v = convertView;
        }
        bindView(v, mContext, mCursor);
        return v;
    }
	{% endcodeblock %}

- 0x04
	最后经过排查发现mListView中的footerView有问题。理论上讲，footerView是不会被adapter生成的。但是巧的是，这个footerView中有一段和状态相关的动画。
	{% codeblock lang:java %}
	mLoadingText.animate().withLayer().alpha(1).setDuration(mAnimationDuration);
	{% endcodeblock %}
	在动画没有完成之前，动画过程可能临时在mListView中添加了一个childView，这个childView没有经过adapter的getView()函数，因而没有tv_name这个TextView，所以就出现了第一个没有任何显示，也没有绑定数据的空view，因此出现NullPointerException。
	在footerView中插入一段文字，结果原先出bug时的空白部分显示出一段文字，验证我的猜想。

- 0x05
	如果在bindView中，检查tv_name是否存在，如果不存在，将view替换为自己新创建的有效的view会怎样？
	结果没有出现异常，但是新创建的有效的view也没有完成代替操作，这个view在listview中被重用，导致空白部分的位置随滚动变化。
	Over!