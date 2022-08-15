# CocoaPods介绍

### 1. 概述

CocoaPods 是开发 OS X 和 iOS 应用程序的一个第三方库的依赖管理工具，使用这个工具可以简化对组件依、更新的过程。新添加一些第三方组件可以直接修改 podfile 然后进行 `pod install`；更新已有第三方组件，可以修改 podfile 然后进行 `pod update`；自己开发的组件也可以上传到 CocoaPods 或者私有仓库，供其他人使用。

CocoaPods 是用 ruby 写的，由若干个 gems 组成。也就是说，iOS project 使用 CocoaPods 来进行组件管理，CocoaPods 本身也是一个 project，它使用 gem 进行组件管理。

### 2. 相关文件

- **Specification**

  这个文件用来描述第三方组件某个版本的信息。主要包含了组件拉取地址、应该拉取那些文件和项目配置信息。除此之外，还包含一些组件信息，例如组件的名字、版本等。后面章节会详细讲解字段含义和文件书写规范。

- **Podfile**

  这个文件用来指定工程中依赖了那些组件。主要包含了依赖的组件名、组件版本、组件地址等。后面章节会详细讲解字段含义和文件书写规范。

- **Podfile.lock**

  在第一次执行 `pod install` 时，执行完毕后会生成一个 podfile.lock 文件。这个文件主要标注了项目当前依赖的具体版本。

  有个问题需要牢记：**CocoaPods 强烈建议将 Podfile.lock 文件加入版本管理，这样其他人同步了你的 podfile.lock 文件之后，执行 `pod install` 时会将按照里面指定给的版本加载，避免多人协作时发生冲突**。后面的 pod install vs pod update 会详细讲解 podfile.lock 变更时机。

- **Manifest.lock**
  这是每次运行 `pod install` 命令时创建的 `Podfile.lock` 文件的副本。如果你遇见过这样的错误 **沙盒文件与 `Podfile.lock` 文件不同步 (The sandbox is not in sync with the `Podfile.lock`)**，这是因为 `Manifest.lock` 文件和 `Podfile.lock` 文件不一致所引起。由于 `Pods` 所在的目录并不总在版本控制之下，这样可以保证开发者运行 App 之前都能更新他们的 `Pods`，否则 App 可能会 crash，或者在一些不太明显的地方编译失败。

- **podspec**
  A specification describes a version of Pod library. It includes details about where the source should be fetched from, what files to use, the build settings to apply, and other general metadata such as its name, version, and description.

Manifest.lock 是由 Podfile.lock 生成的一个副本，每次生成或者更新 Podfile.lock，都会更下 Pods 文件夹下面的 Manifest.lock 文件。如果你遇见过这样的错误 沙盒文件与 Podfile.lock 文件不同步 (The sandbox is not in sync with the Podfile.lock)，这是因为 Manifest.lock 文件和 Podfile.lock 文件不一致所引起。

### 3. 流程

上图为组件开发者、CocoaPods、组件使用者三者的关系。

组件开发者开发完组件之后，会将组件上传到仓库 (Github or other)。然后创建一个 podspec 文件，文件中包含了使用组件时需要加载哪些文件以及从哪里加载。然后会将这个文件上传到 CocoaPods（也可以上传至私人构建的 spec 管理仓库）。

组件使用者想要使用某个组件，会在 Podfile 中指定组件的名字、版本、加载源以及更加详细的信息（例如想要加载某个 commit）。然后执行相关 Pod 命令。

CocoaPods 执行 Pod 命令，然后解析对应的 podspec 文件，确定需要加载的文件信息并将文件加载到项目工程里。并创建 Podfile.lock、Manifest.lock、Pods.xcodeproj 等文件。

### 4. podfile vs podspec

 Podfile is your **project** configuration, and Podspec is your **library** configuration.

#### Podfile:

- Present in the root directory, as file called `Podfile`
- All applications which want to use Cocoapods (e.g. add dependencies via `pod 'library_name'`) need to have a Podfile. This is where that information goes.

#### Podspec (Specification of Pod library):

- Present in the root directory, as file called `Library_name.podspec`, which is formatted with Ruby DSL syntax
- This file is **required** to upload your library to Cocoapods.org (or use it via `pod 'library_name'`). When you `pod trunk push`, you are pushing the JSON version of this file, e.g. `LibName.podpsec.json`, for example, [here](https://github.com/CocoaPods/Specs/blob/master/Specs/2/2/2/BIZGrid4plus1CollectionViewLayout/1.0.0/BIZGrid4plus1CollectionViewLayout.podspec.json).
- Libraries/ packages can also specify a **Podfile** on top of the Podspec, for specific code which the developers don't want to accessible when downstream users use the library.
- It has version limitations because to give the decision of selecting versions to the application which uses the library (the Podfile)