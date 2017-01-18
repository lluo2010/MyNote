# Android图片优化 note


## 图片像素格式

---

为了避免加载一张超大的图片，需要尽量减少这张图片所占用的内存大小，Android为图片提供了4种解码格式，他们分别占用的内存大小如下图所示：

![像素格式](http://hukai.me/images/android_perf_2_pixel_format.png)

随着解码占用内存大小的降低，清晰度也会有损失。我们需要针对不同的应用场景做不同的处理，大图和小图可以采用不同的解码率。

## 缓存

---

xxxx


## 对bitmap做缩放

---

对bitmap做缩放的意义很明显，提示显示性能，避免分配不必要的内存。

缩放方法:

1. createScaledBitmap(): 这个方法可以获取到一张经过缩放的图片,这个方法能够执行的前提是，原图片需要事先加载到内存中，如果原图片过大，很可能导致OOM。
1. 使用BitmapFactory.Options.inSampleSize来decodeFile: 属性值inSampleSize表示缩略图大小为原始图片大小的几分之一，即如果这个值为2，则取出的缩略图的宽和高都是原始图片的1/2，图片大小就为原始大小的1/4.inSampleSize能够等比的缩放显示图片，同时还避免了需要先把原图加载进内存的缺点。
1. 还可以使用BitmapFactory.Options的inScaled，inDensity，inTargetDensity的属性来对解码图片做处理.

还有一个经常使用到的技巧是inJustDecodeBounds，使用这个属性去尝试解码图片，可以事先获取到图片的大小而不至于占用什么内存.



## 使用第三方图片加载库

---

上面说的很多策略这些库都使用了.