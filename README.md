## 示例Demo
播放周杰伦的歌 ：

```
qqmusic://qq.com/ns/method?p={"action":"playSpecificSong","songIdList":["403778","5105986","410316","102066257","102065750","102065753","680279","107192078","449205","718477","97761","101091484","102340965","97771","97773","212877900","102340966","102065756","97744","718475"]}
```


## Action类型：
- const val PLAY_SPECIFIC_SONG = "playSpecificSong"   // 播放在线歌曲
- const val PLAY_ALBUM = "playAlbum"  // 播放在线歌单
- const val PLAY_COLLECT_SONG = "playCollectSong" // 播放收藏歌曲
- const val COLLECT_SONG = "collectSong"  // 收藏歌曲
- const val CANCEL_COLLECTED_SONG = "cancelCollectedSong" //  取消收藏歌曲
- const val PLAY_SINGLE_MODE = "playSingleMode"   // 单曲循环
- const val PLAY_LIST_MODE = "playListMode"   // 顺序播放
- const val PLAY_NEXT = "playNext"    // 播放下一首
- const val PLAY_PREV = "playPrev"    // 播放上一首
- const val PLAY_PAUSE = "playPause"    // 暂停
- const val PLAY_RESUME = "playResume"    // 继续

## songIdList
通过OpenAPI进行获取