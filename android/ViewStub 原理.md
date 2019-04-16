# ViewStub 原理

## 懒加载原理

ViewStub的原理简单描述：

1. ViewStub本身是一个视图，会被添加到界面上，之所以看不到是因为其源码设置为隐藏与不绘制。
2. 当调用infalte或者ViewStub.setVisibility(View.VISIBLE);时（两个都使用infalte方法逻辑），先从父视图上把当前ViewStub删除，再把加载的android:layout视图添加上
3. 把ViewStub layoutParams 添加到加载的android:layout视图上，而其根节点layoutParams 设置无效。
4. ViewStub是指用来占位的视图，通过删除自己并添加android:layout视图达到懒加载效果