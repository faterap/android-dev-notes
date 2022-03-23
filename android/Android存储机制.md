# Android存储机制



## Android 10 变化

- App specific directories



## Android 11 变化



## 内部存储 

这些目录既包括用于存储持久性文件的专属位置，也包括用于存储缓存数据的其他位置。系统会阻止其他应用访问这些位置，并且在 Android 10（API 级别 29）及更高版本中，系统会对这些位置进行加密。这些特征使得这些位置非常适合存储只有应用本身才能访问的敏感数据。

## 外部存储

这些目录既包括用于存储持久性文件的专属位置，也包括用于存储缓存数据的其他位置。虽然其他应用可以在具有适当权限的情况下访问这些目录，但存储在这些目录中的文件仅供您的应用使用。如果您明确打算创建其他应用能够访问的文件，您的应用应改为将这些文件存储在外部存储空间的[共享存储空间](https://developer.android.com/training/data-storage/shared?hl=zh-cn)部分。

## 权限相关

###  READ_EXTERNAL_STORAGE



### WRITE_EXTERNAL_STORAGE



## 总结

|                                                              | 内容类型                                 | 访问方法                                                     | 所需权限                                                     | 其他应用是否可以访问？                                       | 卸载应用时是否移除文件？ |
| :----------------------------------------------------------- | :--------------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- | :----------------------- |
| [应用专属文件](https://developer.android.com/training/data-storage/app-specific?hl=zh-cn) | 仅供您的应用使用的文件                   | 从内部存储空间访问，可以使用 `getFilesDir()` 或 `getCacheDir()` 方法  从外部存储空间访问，可以使用 `getExternalFilesDir()` 或 `getExternalCacheDir()` 方法 | 从内部存储空间访问不需要任何权限  如果应用在搭载 Android 4.4（API 级别 19）或更高版本的设备上运行，从外部存储空间访问不需要任何权限 | 如果文件存储在内部存储空间中的目录内，则不能访问  如果文件存储在外部存储空间中的目录内，则可以访问 | 是                       |
| [媒体](https://developer.android.com/training/data-storage/shared/media?hl=zh-cn) | 可共享的媒体文件（图片、音频文件、视频） | `MediaStore` API                                             | 在 Android 10（API 级别 29）或更高版本中，访问其他应用的文件需要 `READ_EXTERNAL_STORAGE` 或 `WRITE_EXTERNAL_STORAGE` 权限  在 Android 9（API 级别 28）或更低版本中，访问**所有**文件均需要相关权限 | 是，但其他应用需要 `READ_EXTERNAL_STORAGE` 权限              | 否                       |
| [文档和其他文件](https://developer.android.com/training/data-storage/shared/documents-files?hl=zh-cn) | 其他类型的可共享内容，包括已下载的文件   | 存储访问框架                                                 | 无                                                           | 是，可以通过系统文件选择器访问                               | 否                       |
| [应用偏好设置](https://developer.android.com/training/data-storage/shared-preferences?hl=zh-cn) | 键值对                                   | [Jetpack Preferences](https://developer.android.com/guide/topics/ui/settings/use-saved-values?hl=zh-cn) 库 | 无                                                           | 否                                                           | 是                       |
| 数据库                                                       | 结构化数据                               | [Room](https://developer.android.com/training/data-storage/room?hl=zh-cn) 持久性库 | 无                                                           | 否                                                           | 是                       |



https://developer.android.com/training/data-storage?hl=zh-cn#scoped-storage 数据和文件存储概览

https://developer.android.com/training/data-storage/use-cases?hl=zh-cn Android 存储用例和最佳做法