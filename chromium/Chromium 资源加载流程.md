# Chromium 资源加载流程

`Chromium`中有独立的资源加载机制（包括字符串和图片资源），这套机制和`Android`资源加载机制有一定相似之处。

![img](https://img-blog.csdn.net/20150725164916310?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

上图中涉及到几种类型的文件：

- `grd / grdp`: 默认翻译源文件，可以类比成`Android`中的`string.xml`(默认英语)
- `xtb`: 目标语言文件，每种语言对应有一个`xtb`文件。可以类比成`Android`中的`string-xx.xml`
- `pak`: 编译生成的资源压缩包。目前该文件放在项目的`assets`中，通过`ResourceExtractor.java`在上层进行读取。
- `grit.py`: 将`grd`和`xtb`文件编译成`pak`资源文件



## 1. grd / grdp

```xml
<grit base_dir="." latest_public_release="0" current_release="1"
      source_lang_id="en" enc_check="m?l">
  <outputs>
    <output filename="grit/google_chrome_strings.h" type="rc_header">
      <emit emit_type='prepend'></emit>
    </output>
    <output filename="google_chrome_strings_zh-CN.pak" type="data_package" lang="zh-CN" />
    …
  </outputs>
  <translations>
    <file path="resources/google_chrome_strings_zh-CN.xtb" lang="zh-CN" />
    …
  </translations>
  <release seq="1" allow_pseudo="false">
    <messages fallback_to_english="true">
      <message name="IDS_PRODUCT_NAME" desc="The Chrome application name">
        Google Chrome
      </message>
      …
    </messages>
  </release>
</grit>
```

grd 是xml的格式，它有三个部分： `outputs`、`translations`、`release`。

- `outputs` 中定义编译出的C++头文件的位置和语言资源文件的位置，默认是在 `src/build/Debug` 或 `Release/obj/global_intermediate/chrome/`

- `translations` 中定义了多语言的定义文件的位置，每一种支持的语言都应该有一个翻译文件，这些文件将作为输入。

- `release`中的 `messages` 定义的是默认语言的定义，当程序找不到合适的语言显示时，将使用默认语言中的定义。

  `message` 的属性如下：

  - `name`：标识一个字符串的变量名
  - `desc`：该变量的介绍
  - `translateable`：是否使用翻译，默认`true`
  - `use_name_for_id`：是否使用`name`作为翻译的id标识。默认是`false`，翻译文件（`xtb`）中的`id`使用转换后的数字做映射；如果`true`，则使用 `name` 的值来和 `grd` 文件中的记录来映射。



需要注意的是，每个`grd`文件都需要有一个唯一的标识即`id`，这个是在`resource_ids`中定义的，是在`src/tools/gritsettings/resource_ids`中。

## 2. xtb

```xml
<!DOCTYPE translationbundle>
<translationbundle lang="zh-CN">
<translation id="6676384891291319759">访问互联网</translation>
…
</translationbundle>
```

`xtb` 是多语言的翻译文件，也是`xml`格式，每种语言对应一个`xtb`文件。`<translateion/>` 结点中的 `id` 的数字对应于 `grd` 文件中 `<message/>` 中的 `name` 或 `content` 值，注意默认不是根据 `name`，而是根据内容来生成 `id`值，这样的好处是可以去除冗余的文字。 

## 3. pak

`chromium`使用`grit`工具打包程序所需的资源，如图片、`Web`页、字符串等，打包后的资源在安装目录的`chrome_100_percent.pak`中（图片会根据`dpi`不同分开为多个包）。

![img](https://upload-images.jianshu.io/upload_images/8908133-7acea42fa683dd4d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/762/format/webp)

`pak`文件结构如上所示。每个资源会在文件头对应一个`DataPackEntry`的结构，这个结构大小为`6`个字节，前两个字节为资源`id`，后面四个字节为偏移量。当根据资源`ID`获取对应的资源时，使用二分查找的方式，从文件中获取到该资源`ID`与其下一个资源`ID`对应的偏移量，两个偏移量之间的内容即为所取内容。

## 4. grit.py

`grit`（Google Resource and Internationalization Tool）是一个`python`开源工具，主要用来打包生成程序需要的资源，如图片资源、字符串资源等，尤其是字符串资源，用于支持多国际化，位于 `src/tools/grit` 目录下。`chrome_string` 编译时会执行 `grit.py` ，然后由`grit`工具将`grd`和`xtb`文件编译成`pak`资源文件。

`build`里调用`grit`命令，根据`grd`资源描述文件生成`.h`、`.rc`、`.pak`等文件资源。如`chrome_strings`工程生成国际化字符串资源、`content_resources`工程生成除字符串以外的资源，比如图片、`html`、`css`、`js`等文件资源。



------

[]: https://blog.csdn.net/Fantasy_116/article/details/47057877	"Chromium中添加pak资源"

[]: https://www.jianshu.com/p/edabb5433486	"Chromium 资源与多语言工具 grit"

[]: https://www.onthink.com/2015/01/29/chromium-grit/	"Grit - Chromium 国际化（1）"