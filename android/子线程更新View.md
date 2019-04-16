# 子线程真的不能更新 View？

## 原因

如果我们在onResume之前去子线程更新view，由于我们的ViewRootImpl还没创建，整个view树也还没关联到WindowManager中，这个时候也就不会去执行checkThread检查，那么也就不会抛出异常，比如，我们在onCreate这么做：

```Java

new Thread(new Runnable() {
            public void run() {
                /*try {
                    Thread.sleep(3000);
                } catch (InterruptedException e) {
                    // TODO Auto-generated catch block
                    e.printStackTrace();
                }*/
                TextView textView = new TextView(MainActivity.this);
                textView.setText("我是子线程中的view");
                setContentView(textView);
            }
        }).start();
```

其实，这里仅仅是将textView这个对象加入到了decorView树中，并设置了textView的text属性，在这个线程执行完成后，decorView还没添加到WindowManager中，所以也就不存在绘制，仅仅是保存了这些对象的状态而已。
一旦执行完onResume后，会去把decorView树添加到WM中，这个时候就开始绘制了，就把在onCreate里面textView设置的text属性绘制出来了。这里，**所谓的在子线程里面更新view其实是个假象。真正的绘制还是在主线程中完成的**。

## TextView.setText()

![img](https://upload-images.jianshu.io/upload_images/1319879-2c7e4d1df9ee3ff7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 总结

从上图看在页面进行视图更新的时候会触发`checkThread`,校验当前线程是否是`ViewRootImpl`被创建时所在的线程。而`ViewRootImpl`的创建是在Activity的`onResume`生命周期之后。

- 我们可以再`onResume`之前在异步线程进行视图更新,因为这个时候不会发生线程校验。
- 我们可以再异步线程初始化`ViewRootImpl`同时在该线程进行视图更新。



------

[]: https://juejin.im/entry/5a2e603bf265da432c23cdde	"ViewRootImpl的独白，我不是一个View(布局篇)"

