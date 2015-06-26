#创建搜索接口
使用`SearchView`创建搜索接口。
##在ActionBar上添加搜索
```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@+id/search"
          android:title="@string/search_title"
          android:icon="@drawable/ic_search"
          android:showAsAction="collapseActionView|ifRoom"
          android:actionViewClass="android.widget.SearchView" />
</menu>
注意的是：
`android:showAsAction="collapseActionView|ifRoom"`当SearchView有焦点的时候，占据整个ActionBar，否则就是一个图标。
另外需要添加`android:actionViewClass="android.widget.SearchView"`
在类中：
```java
@Override
public boolean onCreateOptionsMenu(Menu menu) {
    MenuInflater inflater = getMenuInflater();
    inflater.inflate(R.menu.options_menu, menu);

    return true;
}
```
##配置搜索接口
在`res/xml`下新建`searchable.xml`文件：
```java
<?xml version="1.0" encoding="utf-8"?>

<searchable xmlns:android="http://schemas.android.com/apk/res/android"
        android:label="@string/app_name"
        android:hint="@string/search_hint" />
```
接着在清单文件的元数据中使用：
```xml
<activity ... >
    ...
    <meta-data android:name="android.app.searchable"
            android:resource="@xml/searchable" />

</activity>
```
在类中：
```java
 @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        // Inflate the menu; this adds items to the action bar if it is present.
        getMenuInflater().inflate(R.menu.menu_main, menu);

        if(Build.VERSION.SDK_INT >= Build.VERSION_CODES.HONEYCOMB) {
            // Associate searchable configuration with the SearchView
            SearchManager searchManager =
                    (SearchManager) getSystemService(Context.SEARCH_SERVICE);
            SearchView searchView =
                    (SearchView) menu.findItem(R.id.search).getActionView();
            searchView.setSearchableInfo(
                    searchManager.getSearchableInfo(getComponentName()));
           // searchView.setIconifiedByDefault(false);

        }
        return true;
    }
```
##创建可搜索的Activity
配置Intent-Filter。
如下：
```xml
<activity
            android:name=".MainActivity"
            android:label="@string/app_name"
            android:launchMode="singleTop">
            <meta-data
                android:name="android.app.searchable"
                android:resource="@xml/searchable" />

            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>

            <intent-filter>
                <action android:name="android.intent.action.SEARCH" />
            </intent-filter>


        </activity>
```
响应：
```java
 @Override
    protected void onNewIntent(Intent intent) {
        handleIntent(intent);
    }

    private void handleIntent(Intent intent) {
        if (Intent.ACTION_SEARCH.equals(intent.getAction())) {
            String query = intent.getStringExtra(SearchManager.QUERY);
            //use the query to search your data somehow
            Log.e("Test","result:" + query);
            tv.setText("result:" + query);
        }
    }
```
在Activity的onCreate()方法中，也调用handleIntent()方法，运行下，挂了，我的Activity继承的是AppComtActivity，我改成Activity,并把theme去掉，运行，好了。
#兼容低版本
在3.0一下版本调用`onSearchRequested();`，如下：
```java
@Override
public boolean onOptionsItemSelected(MenuItem item) {
    switch (item.getItemId()) {
        case R.id.search:
            onSearchRequested();
            return true;
        default:
            return false;
    }
}
```
##运行时检查
```java
@Override
public boolean onCreateOptionsMenu(Menu menu) {

    MenuInflater inflater = getMenuInflater();
    inflater.inflate(R.menu.options_menu, menu);

    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.HONEYCOMB) {
        SearchManager searchManager =
                (SearchManager) getSystemService(Context.SEARCH_SERVICE);
        SearchView searchView =
                (SearchView) menu.findItem(R.id.search).getActionView();
        searchView.setSearchableInfo(
                searchManager.getSearchableInfo(getComponentName()));
        searchView.setIconifiedByDefault(false);
    }
    return true;
}
```
#存储和搜索数据
这部分参考官方的demo。
实现后的效果图：
![1](http://1.infotravel.sinaapp.com/pic/18.gif)
由于是数据库的操作，不再陈列代码，详见源码。
#源码
[官方源码](https://github.com/leerduo/OfficialSearchView)
[我的demo源码](https://github.com/leerduo/MySearchViewDemo)