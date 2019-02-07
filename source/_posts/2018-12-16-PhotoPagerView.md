---
layout: post
title:  "造一个轮子-PhotoPagerView"
date: 2018-12-16 08:52
comments: true
tags: 
	- Android
	- 自定义View
---

![图片.gif](http://plew0vk60.bkt.clouddn.com//blog/181231/translate.gif)

<!-- more -->







## 1.使用场景
需要使用到照片查看功能的时候

## 2.效果
![缩放动画.gif](https://upload-images.jianshu.io/upload_images/9271486-33efed404622071a.gif?imageMogr2/auto-orient/strip)

![完整过程和平移动画.gif](https://upload-images.jianshu.io/upload_images/9271486-9a61c0cb81219492.gif?imageMogr2/auto-orient/strip)

## 3.思路
![PhotoPagerView的思路.png](https://upload-images.jianshu.io/upload_images/9271486-d7e758cbc32449de.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
实现思路其实很简单，利用已知的```ViewPager```和[PhotoView](https://github.com/chrisbanes/PhotoView) 组合成新的控件，迎合日常的开发需求。
GitHub地址：[https://github.com/mCyp/PhotoPagerView](https://github.com/mCyp/PhotoPagerView)
## 4.代码
关于代码这一块的话并不打算介绍了，因为这是一个初级库，代码简单，整合出来只是为了项目的需要，想要代码的话可以直接查看源码。
## 5.使用
传入的参数说明：先传入bitmaps再配置当前的需要使用的属性。
```
List<Bitmap> bitmaps = ...;
PhotoPageView pageView = new PhotoPageView.Builder(MainActivity.this)
                        .addBitmaps(bitmaps) // 传入Bitmaps
                        .showDelete(true) // 是否可以删除
                        .setDeleteListener(new PhotoPageView.DeleteListener() {
                            @Override
                            public void ondelete(int position) {
                                //  删除指定位置之后的回调
                                Toast.makeText(MainActivity.this,"删除的位置是："+position,Toast.LENGTH_SHORT).show();
                            }
                        })
                        .showAnimation(true) // 是否显示动画
                        .setAnimationType(PhotoPageView.ANIMATION_TRANSLATION) // 动画的类型
                        .setStartPosition(0) // 设置起始的位置
                        .create();
 pageView.show();
```
动画类型：
- ` ANIMATION_SCALE_ALPHA` 缩放透明度动画
- `ANIMATION_TRANSLATION` 平移动画

### 6.注意
因为使用到了`Bitmap`，所以得注意一下OOM的问题，库里面的`FileUtils`工具类提供了解决办法，详见代码：[位置](https://github.com/mCyp/PhotoPagerView/blob/master/PhotoPagerView/src/main/java/com/orient/photopagerview/utils/FileUtils.java)，你也可以使用该方法通过提供一个地址，解析出你需要的`List<Bitmap>`，
代码：
```
     /**
        get bitmaps
     */
    public static List<Bitmap> getAlbumByPath(String path, String Extension, Activity activity) {
        List<Bitmap> bitmaps = new LinkedList<>();                //结果 List
        File[] files = new File(path).listFiles();
        if (files == null)
            return null;
        InputStream inputStream = null;

        try {
            for (File f : files) {
                if (f.isFile()) {
                    if (f.getPath().substring(f.getPath().length() - Extension.length()).equals(Extension)) {
                        BitmapFactory.Options options = new BitmapFactory.Options();
                        options.inJustDecodeBounds = true;
                        options.inPreferredConfig = Bitmap.Config.RGB_565;
                        inputStream = new FileInputStream(f);
                        BitmapFactory.decodeStream(inputStream, null, options);
                        options.inSampleSize = calculateInSampleSize(options,activity.getWindow().getDecorView().getWidth(),activity.getWindow().getDecorView().getHeight());
                        options.inJustDecodeBounds = false;
                        inputStream = new FileInputStream(f);
                        Bitmap bitmap = BitmapFactory.decodeStream(inputStream, null, options);
                        if (bitmap != null) {
                            bitmaps.add(bitmap);
                        }
                    }
                }
            }
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } finally {
            if (inputStream != null) {
                try {
                    inputStream.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }

        return bitmaps;
    }

     /*
        to prevent oom
     */
    public static int calculateInSampleSize(BitmapFactory.Options options, int reqWidth, int reqHeight) {
        final int height = options.outHeight;
        final int width = options.outWidth;
        int inSampleSize = 1;

        if (height > reqHeight || width > reqWidth) {

            final int halfHeight = height / 2;
            final int halfWidth = width / 2;

            while ((halfHeight / inSampleSize) >= reqHeight
                    && (halfWidth / inSampleSize) >= reqWidth) {
                inSampleSize *= 2;
            }
        }

        return inSampleSize;
    }
```
### 总结
大概内容就是这样了，有问题可以提出。地址：[PhotoPagerView](https://github.com/mCyp/PhotoPagerView)


