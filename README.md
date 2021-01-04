Glide 4.11 Source Code Reading
=====

Glide is a fast and efficient open source media management and image loading framework for Android that wraps media
decoding, memory and disk caching, and resource pooling into a simple and easy to use interface.

Glide supports fetching, decoding, and displaying video stills, images, and animated GIFs. Glide includes a flexible API
that allows developers to plug in to almost any network stack. By default Glide uses a custom `HttpUrlConnection` based
stack, but also includes utility libraries plug in to Google's Volley project or Square's OkHttp library instead.

Glide's primary focus is on making scrolling any kind of a list of images as smooth and fast as possible, but Glide is
also effective for almost any case where you need to fetch, resize, and display a remote image.


ProGuard
--------
Depending on your ProGuard (DexGuard) config and usage, you may need to include the following lines in your proguard.cfg (see the [Download and Setup docs page][25] for more details):

```pro
-keep public class * implements com.bumptech.glide.module.GlideModule
-keep class * extends com.bumptech.glide.module.AppGlideModule {
 <init>(...);
}
-keep public enum com.bumptech.glide.load.ImageHeaderParser$** {
  **[] $VALUES;
  public *;
}
-keep class com.bumptech.glide.load.data.ParcelFileDescriptorRewinder$InternalRewinder {
  *** rewind();
}

# for DexGuard only
-keepresourcexmlelements manifest/application/meta-data@value=GlideModule
```

Simple use cases will look something like this:

```java
// For a simple view:
@Override public void onCreate(Bundle savedInstanceState) {
  ...
  ImageView imageView = (ImageView) findViewById(R.id.my_image_view);

  Glide.with(this).load("http://goo.gl/gEgYUd").into(imageView);
}

// For a simple image list:
@Override public View getView(int position, View recycled, ViewGroup container) {
  final ImageView myImageView;
  if (recycled == null) {
    myImageView = (ImageView) inflater.inflate(R.layout.my_image_view, container, false);
  } else {
    myImageView = (ImageView) recycled;
  }

  String url = myUrls.get(position);

  Glide
    .with(myFragment)
    .load(url)
    .centerCrop()
    .placeholder(R.drawable.loading_spinner)
    .into(myImageView);

  return myImageView;
}
```

Status
------
Version 4 is now released and stable. Updates are released periodically with new features and bug fixes.

Compatibility
-------------

 * **Minimum Android SDK**: Glide v4 requires a minimum API level of 14.
 * **Compile Android SDK**: Glide v4 requires you to compile against API 26 or later.

 If you need to support older versions of Android, consider staying on [Glide v3][14], which works on API 10, but is not actively maintained.

 * **OkHttp 3.x**: There is an optional dependency available called `okhttp3-integration`
 * **Volley**: There is an optional dependency available called `volley-integration`
 * **Round Pictures**: `CircleImageView`/`CircularImageView`/`RoundedImageView` are known to have [issues][18] with `TransitionDrawable` (`.crossFade()` with `.thumbnail()` or `.placeholder()`) and animated GIFs, use a [`BitmapTransformation`][19] (`.circleCrop()` will be available in v4) or `.dontAnimate()` to fix the issue.
 * **Huge Images** (maps, comic strips): Glide can load huge images by downsampling them, but does not support zooming and panning `ImageView`s as they require special resource optimizations (such as tiling) to work without `OutOfMemoryError`s.

