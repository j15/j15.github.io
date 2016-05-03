---
layout: post
title: "iconoverlaying"
description: ""
category: 
tags: []
---
{% include JB/setup %}

# Overlaying application version on top of your icon 覆盖应用程序版本的图标
---

## 原文: [Overlaying application version on top of your icon](http://merowing.info/2013/03/overlaying-application-version-on-top-of-your-icon)
---

![Overlaying](http://merowing.info/2013/03/Icon.png)

I’ve just returned from NSConference #5, there were many good talks there, but my favourite one was the one about Flipboard development tools/setup by Evan Doll.

我刚刚从NSConference # 5回来,有许多好的会谈,但我最喜欢的一个是Evan Doll制作的关于Flipboard的开发工具/设置。

Especially how they add version information on top of the icons. Unfortunately they didn’t share it with us.

特别是如何添加版本信息的图标。不幸的是他们并没有与我们分享它。

So I wrote my own.

所以我自己造了一个。

What we will be overlaying ?

我们将覆盖什么呢?

I’ve decided that 3 parts of information should be enough:

我决定这三部分的信息就够了:

* version number 版本号
* branch name 分支名
* short commit hash 短提交哈希

## Version number 版本号
---

We can extract version number straight from our application .plist file by using PlistBuddy tool:

我们可以通过使用PlistBuddy工具直接从应用程序中 **.plist** 文件中提取版本号:

```bash
version=`/usr/libexec/PlistBuddy -c "Print CFBundleVersion" "${INFOPLIST_FILE}"`
```

> PS. You could extract any plist entry with this tool, just change CFBundleVersion to different key (show raw keys in Xcode)

## Branch and short commit hash
---

git command line tool offers rev-parse command which let’s you both of those variables:

git命令行工具提供了rev-parse命令让我们得到这两个变量:

```bash
commit=`git rev-parse --short HEAD`
branch=`git rev-parse --abbrev-ref HEAD`
```

## How to overlay it ? 怎么去覆盖呢？
---

ImageMagic is my go-to tool for playing with images from command line, it offers crazy amount of functions.

ImageMagic是我收集的图片命令行工具,它提供了不可思议的功能。

Make sure to install ImageMagick and ghostscript (fonts) first, you can use brew to simplify process:

确保安装**ImageMagick**和 **ghostscript**(字体)，首先,您可以使用 *homebrew* 简化流程:

```bash
brew install imagemagick
brew install ghostscript
```

We can use convert function, by specifing caption parameter imagemagick will layout our text on top of image, we also setup alignment to bottom and default height.

我们可以使用转换函数,设置一些参数imagemagick将我们的文本布局到图像上面,我们还设置对齐到下和默认高度。

```bash
convert -background '#0008' -fill white -gravity center -size ${width}x40 \
    caption:"${version} ${branch} ${commit}" \
    ${base_file} +swap -gravity south -composite  "${CONFIGURATION_BUILD_DIR}/${UNLOCALIZED_RESOURCES_FOLDER_PATH}/${target_file}"
```

### Setting it up in Xcode project 在Xcode项目中配置

There are few steps we need to take in order to set this up as part of Xcode build:

这里还有几步我们需要在Xcode编译前进行

Rename your Icon* files (where * is @2x, -568h etc.) to Icon_base*, e.g. Icon@2x_base.png

把带Icon文件更名为带_base。 比如 **Icon@2x.png** -> **Icon@2x_base.png**。

Add run script for your target under Build Phases

把执行脚本添加到 *Build Phases* -> *New Run Script Phase*  下。

Paste this code: 粘贴如下代码

```bash
commit=`git rev-parse --short HEAD`
branch=`git rev-parse --abbrev-ref HEAD`
version=`/usr/libexec/PlistBuddy -c "Print CFBundleVersion" "${INFOPLIST_FILE}"`

function processIcon() {
    export PATH=$PATH:/usr/local/bin
    base_file=$1
    base_path=`find ${SRCROOT} -name $base_file`

    if [[ ! -f ${base_path} || -z ${base_path} ]]; then
        return;
    fi

    target_file=`echo $base_file | sed "s/_base//"`
    target_path="${CONFIGURATION_BUILD_DIR}/${UNLOCALIZED_RESOURCES_FOLDER_PATH}/${target_file}"

    if [ $CONFIGURATION = "Release" ]; then
    cp ${base_file} $target_path
    return
    fi

    width=`identify -format %w ${base_path}`

    convert -background '#0008' -fill white -gravity center -size ${width}x40\
    caption:"${version} ${branch} ${commit}"\
    ${base_path} +swap -gravity south -composite ${target_path}
}

processIcon "Icon_base.png"
processIcon "Icon@2x_base.png"
processIcon "Icon-72_base.png"
processIcon "Icon-72@2x_base.png"
```

## Conclusion 结论
---

Few things about final script: 关于最终脚本的一些注意事项

* I’ve added support for skipping icons that don’t exist, no need to change script contents. 我添加了支持跳过不存在,不需要改变图标的脚本内容。
* I’m using sed to strip _base string from file name. 我使用sed把带_base字符串从文件名中去除。
* For release build we don’t want to overlay our development info. 我们不应该覆盖发布版本的开发信息
* Xcode likes messes up path resolution, so I’ve added usr/local/bin to it for this terminal lifetime. Xcode 似乎总是把路径搞混，于是我决定把 *usr/local/bin* 加入到终端路径中。

* Now run your project and you should see this:  现在你应该像下面这样使用。

Sample project on 示例在 [GitHub](https://github.com/krzysztofzablocki/IconOverlaying) 上。

视频演示： http://pan.baidu.com/share/link?shareid=939323736&uk=1971598613&fid=1105685155155796
