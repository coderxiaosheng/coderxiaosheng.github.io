---
title: "Android图片加载库-google/volley"
date: 2023-01-09T08:35:08+08:00
categories: ["开发"]
tags: ["Android"]
slug: "android-github-volley"
draft: false
---

## google/volley

* 官方项目地址：[volley](https://android.googlesource.com/platform/frameworks/volley/)
* Demo项目地址：[android-cn/volley-demo](https://github.com/android-cn/android-open-project-demo/tree/master/volley-demo)

类继承关系图：
![](https://user-images.githubusercontent.com/136188064/263541183-d4a4b3e7-a97d-489b-8b9d-db38626f1822.png)
总体设计图
![](https://user-images.githubusercontent.com/136188064/263541185-5ad9d5cd-7ff5-494d-9e1e-d25e4a68d717.png)

```java
// 初始化相关
public class RequestManager {
    public static RequestQueue mRequestQueue = newRequestQueue();
    private static final int MEM_CACHE_SIZE = 1024 * 1024 * ((ActivityManager) AppData.getContext()
            .getSystemService(Context.ACTIVITY_SERVICE)).getMemoryClass() / 3;
    private static ImageLoader mImageLoader = new ImageLoader(mRequestQueue, new BitmapLruCache(MEM_CACHE_SIZE));
    private static DiskBasedCache mDiskCache = (DiskBasedCache) mRequestQueue.getCache();
    private RequestManager(){
    }
	// DiskLruCache缓存，自定义路径、大小
    private static Cache openCache() {
        return new DiskBasedCache(CacheUtils.getExternalCacheDir(AppData.getContext()),
                10 * 1024 * 1024);
    }
    private static RequestQueue newRequestQueue() {
        RequestQueue requestQueue = new RequestQueue(openCache(), new BasicNetwork(new HurlStack()));
        requestQueue.start();
        return requestQueue;
    }
    public static void addRequest(Request request, Object tag) {
        if (tag != null) {
            request.setTag(tag);
        }
        mRequestQueue.add(request);
    }
    public static void cancelAll(Object tag) {
        mRequestQueue.cancelAll(tag);
    }
    public static ImageLoader.ImageContainer loadImage(String requestUrl,
            ImageLoader.ImageListener imageListener) {
        return loadImage(requestUrl, imageListener, 0, 0);
    }
    public static ImageLoader.ImageContainer loadImage(String requestUrl,
            ImageLoader.ImageListener imageListener, int maxWidth, int maxHeight) {
        return mImageLoader.get(requestUrl, imageListener, maxWidth, maxHeight);
    }
}
public class CacheUtils {
    public static File getExternalCacheDir(final Context context) {
        if (hasExternalCacheDir())
            return context.getExternalCacheDir();
        // Before Froyo we need to construct the external cache dir ourselves
        final String cacheDir = "/Android/data/" + context.getPackageName() + "/cache/";
        return new File(Environment.getExternalStorageDirectory().getPath() + cacheDir);
    }
    public static boolean hasExternalCacheDir() {
        return Build.VERSION.SDK_INT >= Build.VERSION_CODES.FROYO;
    }
}
// LruCache内存缓存
public class BitmapLruCache extends LruCache<String, Bitmap> implements ImageLoader.ImageCache {
    public BitmapLruCache(int maxSize) {
        super(maxSize);
    }
    @Override
    protected int sizeOf(String key, Bitmap bitmap) {
        return ImageUtils.getBitmapSize(bitmap);
    }
    @Override
    public Bitmap getBitmap(String url) {
        return get(url);
    }
    @Override
    public void putBitmap(String url, Bitmap bitmap) {
        put(url, bitmap);
    }
}
```

应用：
```java
/ case1: StringRequest
StringRequest request = new StringRequest(StringUtil.preUrl(etUrl.getText().toString().trim()),
	new Listener<String>() {
		@Override
		public void onResponse(String response) {
			tvResult.setText(response);
		}
	}, new ErrorListener() {
		@Override
		public void onErrorResponse(VolleyError arg0) {
			ToastUtil.showToast(getActivity(), getResources().getString(R.string.request_fail_text));
		}
	});
RequestManager.addRequest(request, this);
// case2: JsonObjectRequest，同理JsonArrayRequest
JsonObjectRequest request = new JsonObjectRequest(StringUtil.preUrl(Constants.DEFAULT_JSON_REQUEST_URL), null,
	new Listener<JSONObject>() {
		@Override
		public void onResponse(JSONObject response) {
			try {
				// 解析JSON代码...
				JSONArray seriesArray = factory.getJSONArray("serieslist");
				carDataList.clear();
				for (int i = 0; i < seriesArray.length(); i++) {
					JSONObject series = seriesArray.getJSONObject(i);
					Map<String, String> seriesMap = new HashMap<String, String>();
					seriesMap.put("name", series.getString("name"));
					seriesMap.put("level", "级别："+series.getString("levelname"));
					seriesMap.put("price", "价格："+series.getString("price"));
					carDataList.add(seriesMap);
				}
				adapter.notifyDataSetChanged();
			} catch (Exception e) {
				ToastUtil.showToast(getActivity(), getResources().getString(R.string.request_fail_text));
			}
		}
	}, new ErrorListener() {
		@Override
		public void onErrorResponse(VolleyError arg0) {
			ToastUtil.showToast(getActivity(), getResources().getString(R.string.request_fail_text));
		}
	});
RequestManager.addRequest(request, this);
// case3: ImageRequest
ImageRequest request=new ImageRequest(StringUtil.preUrl(imageUrl), new Listener<Bitmap>() {
	@Override
	public void onResponse(Bitmap bitmap) {
		imageView.setImageBitmap(bitmap);
	}}, 0, 0, Config.RGB_565, new ErrorListener() {
	@Override
	public void onErrorResponse(VolleyError arg0) {
		imageView.setImageResource(R.drawable.ic_empty);
	}
});
RequestManager.addRequest(request, this);
// case4: ImageLoader
ImageListener listener=ImageLoader.getImageListener(imageView, R.drawable.ic_empty, R.drawable.ic_error);
ImageLoader.ImageContainer container = RequestManager.loadImage(StringUtil.preUrl(imageUrl), listener);
//在ImageView上存储当前请求的Container，用于取消请求
imageView.setTag(container);
// case5: NetworkImageView
NetworkImageView networkImageView=(NetworkImageView)imageView;
networkImageView.setDefaultImageResId(R.drawable.ic_empty);  
networkImageView.setErrorImageResId(R.drawable.ic_error);  
networkImageView.setImageUrl(StringUtil.preUrl(imageUrl), RequestManager.getImageLoader()); 
// case6: Post请求，重写getParams方法，传递Post参数
StringRequest request = new StringRequest(Method.POST, StringUtil.preUrl(etUrl.getText().toString().trim()), listener, errorListener) {
	//重写getParams设置post请求的参数
	@Override
	protected Map<String, String> getParams() throws AuthFailureError {
		Map<String, String> paramMap = new HashMap<String, String>();
		try {
			String paramsStr = etParams.getText().toString().trim();
			String[] paramsArray = paramsStr.split(";");
			for (String param : paramsArray) {
				paramMap.put(keyValueArray[0].trim(), keyValueArray.length > 1 ? keyValueArray[1].trim() : "");
			}
		} catch (Exception e) {
			ToastUtil.showToast(getActivity(), "参数格错误");
		}
		return paramMap;
	}
};
RequestManager.addRequest(request, this);
// 注：JsonObjectRequest可根据第二个参数来决定是GET还是POST
public JsonObjectRequest(String url, JSONObject jsonRequest, Listener<JSONObject> listener,
        ErrorListener errorListener) {
    this(jsonRequest == null ? Method.GET : Method.POST, url, jsonRequest,
            listener, errorListener);
}
// case7: 自定义Request，使用GSON解析JSON
public class GsonRequest<T> extends Request<T> {
    private final Gson gson = new Gson();
    private final Class<T> clazz;
    private final Map<String, String> headers;
    private final Listener<T> listener;
    public GsonRequest(String url, Class<T> clazz, Map<String, String> headers,
                       Listener<T> listener, ErrorListener errorListener) {
        super(Method.GET, url, errorListener);
        this.clazz = clazz;
        this.headers = headers;
        this.listener = listener;
    }
    @Override
    public Map<String, String> getHeaders() throws AuthFailureError {
        return headers != null ? headers : super.getHeaders();
    }
    @Override
    protected void deliverResponse(T response) {
        listener.onResponse(response);
    }
    @Override
    protected Response<T> parseNetworkResponse(NetworkResponse response) {
        try {
//            String json = Test.getFromAssets("test.txt", AppData.getContext());//yes
//            String json = new String(response.data, HttpHeaderParser.parseCharset(response.headers));//no
            String json = new String(response.data);
            return Response.success(
                    gson.fromJson(json, clazz), HttpHeaderParser.parseCacheHeaders(response));
        } catch (JsonSyntaxException e) {
            e.printStackTrace();
            return Response.error(new ParseError(e));
        } catch (Exception e) {
            e.printStackTrace();
            return Response.error(new ParseError(e));
        }
    }
}
```
参考文档
* [Volley 源码解析](http://a.codekk.com/detail/Android/grumoon/Volley%20%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90)
* [Android Volley完全解析(一)，初识Volley的基本用法](http://blog.csdn.net/guolin_blog/article/details/17482095/)

