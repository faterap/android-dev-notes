# UIView介绍

## 布局更新

- **setNeedsLayout**：标记为需要重新布局，异步调用`layoutIfNeeded`刷新布局，不立即刷新，在下一轮runloop结束前刷新，对于这一轮`runloop`之内的所有布局和UI上的更新只会刷新一次，`layoutSubviews`一定会被调用。
- **layoutIfNeeded**：如果有需要刷新的标记，立即调用`layoutSubviews`进行布局（如果没有标记，不会调用`layoutSubviews`）。
- **layoutSubviews**：不能直接调用该方法，但是可以重写，在这个方法中可以取到View当前最新的frame信息，如果通过autoresizing生成的布局还无法满足需求，可以在这个方法里面根据最新值对frame做调整，会覆盖autoresizingMasks的结果。



