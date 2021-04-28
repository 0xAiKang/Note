macOS 默认自带的是英文输入法，虽然也有简体中文，不过词库不丰富，不太好用。一般会选择安装一个第三方的输入法，这时就会有两个输入法共存了。

常常会遇到的一个痛点就是多应用切换时，下一个输入法总是不确定，有时候是中文有时候是英文。

系统输入法切换的快捷方式是`Control + Space`，而落格输入法切换中英文又是`Shift`，这就导致总是需要来回切换，这一点就很烦。

这种情况下，如果只保留一个输入法，那就不会有这种困扰了。

但是系统并不允许我们删除默认的英文输入法，不过可以一些小手段来达到目的，具体步骤如下：

1. 删除多余的输入法，只保留默认的英文输入法和正在使用的输入法
2. 把当前输入法切换到默认的英文输入法
3. 把`~/Library/Preferences/`路径下的`com.apple.HIToolbox.plist`文件拷贝到桌面，用Xcode 打开，找到并删除`AppleEnabledInputSources`中`KeyboardLayout Name`为`US` 那一项，然后保存。
4. 用修改后的文件替换`~/Library/Preferences/`路径下文件
5. 重启电脑即可

![](https://cdn.jsdelivr.net/gh/0xAiKang/CDN/blog/images/20210320090641.png)

### 参考链接
* [删除macOS自带的英文输入法](https://xdev.in/posts/remove-default-input-source/)
