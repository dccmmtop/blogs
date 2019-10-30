---
tags: [DIY_script,vim]
---

### 选中的图片地址，将图片上传到七牛云，并得到外链

配合自定义命令[qiniu](https://dccmm.world/topics/qiniu_upload_file)使用

```vimscript
function! qiniu#get_file_url_from_qiniu()
    " Why is this not a built-in Vim script function?!
    let [line_start, column_start] = getpos("'<")[1:2]
    let [line_end, column_end] = getpos("'>")[1:2]
    let lines = getline(line_start, line_end)
    if len(lines) == 0
        return ''
    endif
    let lines[-1] = lines[-1][: column_end - (&selection == 'inclusive' ? 1 : 2)]
    let lines[0] = lines[0][column_start - 1:]
    " 得到选中的文本
    let re = join(lines,"\n")
    " 执行系统命令
    let  re1 = system("qiniu " . re)
    " url 处理
    let tmpq = split(re1,"\n")[-1]
    let tar = substitute(tmpq,"/","\\\\/","g")
    let src = substitute(re,"/","\\\\/","g")
    " 替换文本
    exec ":s/".src."/".tar."/"
endfunction
```
