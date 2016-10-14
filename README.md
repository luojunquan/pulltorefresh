

###设置Listview 
layout文件中定义PullToRefreshListView 

	<com.handmark.pulltorefresh.library.PullToRefreshListView
    android:id="@+id/list_view"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />


###设置Mode 

	PullToRefreshListView mListView = (PullToRefreshListView) findViewById(R.id.list_view);
	mListView.setMode(Mode.BOTH);


 - Mode.BOTH：同时支持上拉下拉
 - Mode.PULL_FROM_START：只支持下拉Pulling Down
 - Mode.PULL_FROM_END：只支持上拉Pulling Up

###关于模式的说明和解释

 - 如果Mode设置成Mode.BOTH，需要设置刷新Listener为OnRefreshListener2，并实现onPullDownToRefresh()、onPullUpToRefresh()两个方法。 
 - 如果Mode设置成Mode.PULL_FROM_START或Mode.PULL_FROM_END，需要设置刷新Listener为OnRefreshListener，同时实现onRefresh()方法。当然也可以设置为OnRefreshListener2，但是Mode.PULL_FROM_START的时候只调用onPullDownToRefresh()方法，Mode.PULL_FROM的时候只调用onPullUpToRefresh()方法. 

### MainActivity .java


	public class MainActivity extends Activity {
	
	private ArrayList<String> mListItems;
    private PullToRefreshListView mListView;
    private SampleListAdapter mAdapter;
	
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.sample);
        
        // 设置初期数据
        mListItems = new ArrayList<String>();
    	for (int i = 1; i <= 10; i++) {
    		mListItems.add("Item " + Integer.toString(i));
    	}

        // 设置ListView
        mListView = (PullToRefreshListView) findViewById(R.id.list_view);
        mAdapter = new SampleListAdapter();
        mListView.setAdapter(mAdapter);
        
        // 设置PullToRefresh
        mListView.setMode(Mode.BOTH);
        mListView.setOnRefreshListener(new OnRefreshListener2<ListView>(){
 
        	// 下拉Pulling Down
            @Override
            public void onPullDownToRefresh(PullToRefreshBase<ListView> refreshView) {
            	// 下拉的时候数据重置
                mListItems = new ArrayList<String>();
            	for (int i = 1; i <= 10; i++) {
            		mListItems.add("Item " + Integer.toString(i));
            	}
            	mAdapter.notifyDataSetChanged();
            	
                new FinishRefresh().execute();
            }
            
            // 上拉Pulling Up
            @Override
            public void onPullUpToRefresh(PullToRefreshBase<ListView> refreshView) {
            	// 上拉的时候添加选项
                int count = mListItems.size();
                mListItems.add("Item " + Integer.toString(++count));
                mAdapter.notifyDataSetChanged();
                
                new FinishRefresh().execute();
            }
 
        });
        
    }
    
    private class SampleListAdapter extends BaseAdapter {
    	 
        @Override
        public int getCount() {
            return mListItems.size();
        }
 
        @Override
        public Object getItem(int index) {
            return mListItems.get(index);
        }
 
        @Override
        public long getItemId(int index) {
            return index;
        }
 
        @Override
        public View getView(int index, View view, ViewGroup arg2) {
            if(view == null){
                LayoutInflater inflater = (LayoutInflater)getSystemService(Context.LAYOUT_INFLATER_SERVICE);
                view = inflater.inflate(R.layout.list_item, null);
            }
            TextView textView = (TextView)view.findViewById(R.id.listItemText);
            textView.setText(mListItems.get(index));
            return view;
        }
    }
 
    private class FinishRefresh extends AsyncTask<Void, Void, Void>{
        @Override
        protected Void doInBackground(Void... params) {
            return null;
        }
 
        @Override
        protected void onPostExecute(Void result){
            mListView.onRefreshComplete();
        }
  	  }
	}

### sample.xml

	<?xml version="1.0" encoding="utf-8"?>
	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
	    xmlns:tools="http://schemas.android.com/tools"
	    android:layout_width="match_parent"
	    android:layout_height="match_parent"
	    android:orientation="vertical">
	    <com.handmark.pulltorefresh.library.PullToRefreshListView
	        android:id="@+id/mylv"
	        android:layout_height="match_parent"
	        android:layout_width="match_parent">
	    </com.handmark.pulltorefresh.library.PullToRefreshListView>
	</LinearLayout>

### list_item.xml

	<?xml version="1.0" encoding="utf-8"?>
	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
	    android:layout_width="match_parent"
	    android:layout_height="match_parent"
	    android:orientation="vertical">
	<TextView
	    android:id="@+id/text_item"
	    android:layout_width="match_parent"
	    android:layout_height="match_parent"></TextView>
	</LinearLayout>

[PullToRefresh的github链接](https://github.com/luojunquan/pulltorefresh.git)
