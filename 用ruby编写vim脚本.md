---
tags: vim
date: 2018-08-30 17:36:12
---

在开始编写插件之前，你需要确认 Vim 是否支持 Ruby，通过以下命令来判别：

```shell
$ vim --version | grep +ruby
```

如果输出为空，则表示你当前的 vim 不支持 Ruby，需要重新编译一下，并启用对 Ruby 的支持。
如果没有问题那就开始吧！

下面的示例是我用来把本地图片上传到七牛云图床。

### 新建

在`.vim/autoload`中新建`test.vim`,复制以下代码

```vim
function! qiniu#get_picture_url()
ruby << EOF
class Qiniu
  def initialize
    @buffer = Vim::Buffer.current
  end

  def get_current_line
    s = @buffer.line       # gets the current line
    # qiniu 是七牛云的一个gem，修改了部分代码，并重命名。
    real_link = `qiniu #{s}`
    real_link = real_link.split(/\n/).last
    Vim::Buffer.current.line = "![](#{real_link})"     # sets the current line number
  end
end
gem = Qiniu.new
gem.get_current_line
EOF
endfunction
```

### 绑定快捷键

在`.vimrc`中，映射快捷键

```vim
noremap <leader>qn :cal qiniu#get_picture_url()<cr><CR>
```

[------------------------------------------------------------------------------]

我把图片的本地地址粘贴到 vim 中，然后使用`qn`快捷键，本地地址就会被修改成该图片在七牛云的外链，经常写博客用到

### vim API

在 vim 中执行`:h ruby` 查看 vim 提供 ruby 的 API
