# Linux 下 LC_ALL=C 的含义

在很多的 shell 脚本中，我们经常会看见某一句命令的前面有一句“LC_ALL=C”
`SAR_CMD="LC_ALL=C sar -u -b 1 5 | grep -i average "`

这到底是什么意思？

**LC_ALL=C 是为了去除所有本地化的设置，让命令能正确执行。**
在 Linux 中通过 locale 来设置程序运行的不同语言环境，locale 由 ANSI C 提供支持。locale 的命名规则为<语言>\_<地区>.<字符集编码>，如 zh_CN.UTF-8，zh 代表中文，CN 代表大陆地区，UTF-8 表示字符集。在 locale 环境中，有一组变量，代表国际化环境中的不同设置：

- LC_COLLATE

定义该环境的排序和比较规则

- LC_CTYPE

用于字符分类和字符串处理，控制所有字符的处理方式，包括字符编码，字符是单字节还是多字节，如何打印等。是最重要的一个环境变量。

- LC_MONETARY

货币格式

- LC_NUMERIC

非货币的数字显示格式

- LC_TIME

时间和日期格式

- LC_MESSAGES

提示信息的语言。另外还有一个 LANGUAGE 参数，它与 LC_MESSAGES 相似，但如果该参数一旦设置，则 LC_MESSAGES 参数就会失效。LANGUAGE 参数可同时设置多种语言信息，如 LANGUANE="zh_CN.GB18030:zh_CN.GB2312:zh_CN"。

- LANG

LC*\*的默认值，是最低级别的设置，如果 LC*\*没有设置，则使用该值。类似于 LC_ALL。

- LC_ALL

它是一个宏，如果该值设置了，则该值会覆盖所有 LC\_\*的设置值。注意，LANG 的值不受该宏影响。
C"是系统默认的 locale，"POSIX"是"C"的别名。所以当我们新安装完一个系统时，默认的 locale 就是 C 或 POSIX。

### fix a locale setting warning from Perl

有时会出现如下警告

```shell
perl: warning: Setting locale failed.
perl: warning: Please check that your locale settings:
	LANGUAGE = (unset),
	LC_ALL = (unset),
	LC_PAPER = "zh_CN.UTF-8",
	LC_ADDRESS = "zh_CN.UTF-8",
	LC_MONETARY = "zh_CN.UTF-8",
	LC_NUMERIC = "zh_CN.UTF-8",
	LC_TELEPHONE = "zh_CN.UTF-8",
	LC_IDENTIFICATION = "zh_CN.UTF-8",
	LC_MEASUREMENT = "zh_CN.UTF-8",
	LC_CTYPE = "zh_CN.UTF-8",
	LC_TIME = "zh_CN.UTF-8",
	LC_NAME = "zh_CN.UTF-8",
	LANG = "en_US.UTF-8"
    are supported and installed on your system.
perl: warning: Falling back to a fallback locale ("en_US.UTF-8").
```

你可以在`.bashrc`或者`.bash_profile` 文件中添加如下代码：

```shell
# Setting for the new UTF-8 terminal support in Lion
export LC_CTYPE=en_US.UTF-8
export LC_ALL=en_US.UTF-8
```

如果用的是 zsh,就在`zshrc`中添加如下代码：

```shell
# Setting for the new UTF-8 terminal support in Lion
LC_CTYPE=en_US.UTF-8
LC_ALL=en_US.UTF-8
```
