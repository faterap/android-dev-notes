# MediaCodec

### 1. 流程

![MediaCodec buffer flow diagram](https://developer.android.com/images/media/mediacodec_buffers.svg)

### 2. 状态图

![MediaCodec state diagram](https://developer.android.com/images/media/mediacodec_states.svg)

### 3. 同步/异步处理

In synchronous mode, call `dequeueInput`/`OutputBuffer(…)` to obtain (get ownership of) an input or output buffer from the codec. In asynchronous mode, you will automatically receive available buffers via the `MediaCodec.Callback.onInput`/`OutputBufferAvailable(…)` callbacks.