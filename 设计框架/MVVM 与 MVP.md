# MVVM 与 MVP

## MVVM

> **View: **对应于Activity和XML，负责View的绘制以及与用户交互。 
>
> **Model: **实体模型。 
>
> **ViewModel: **负责完成View与Model间的交互，负责业务逻辑。

![img](https://cdn.journaldev.com/wp-content/uploads/2018/04/android-mvvm-pattern.png)

![img](https://upload-images.jianshu.io/upload_images/749674-0ce1bb9d1f1f44c0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

### Sample

```kotlin

class MainViewModel {

    val textValue = "Hello World!"
}

class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val viewModel = MainViewModel()
        helloTextView.text = viewModel.textValue
    }
}
```



### 工作原理：

1. View 接收用户交互请求
2. View 将请求转交给 ViewModel
3. ViewModel 操作 Model 数据更新
4. Model 更新完数据，通知 ViewModel 数据发生变化
5. ViewModel 更新 View 数据

## MVP



## 区别

- MVVM 中 ViewModel 不持有对 View 的引用，但 MVP 中 View 和 Presenter 双向持有引用
- Presenter 和 View 是一对一的关系， View 和 ViewModel 是一对多的关系
- Presenter 通过传统调用函数的方式更新 View， MVVM 中 View 则是监听 ViewModel 的变化
- Presenter 从 UI 中抽象出 View 的**事件逻辑**; ViewModel 为**事件驱动**页面提供数据流.

