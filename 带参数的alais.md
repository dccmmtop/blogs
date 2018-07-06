我有一个固定的文件加专门放我的blog，每次写blog，都得回的这个文件夹下，现在我想在任何地方输入`wblog blog_name` 都能在 `~/blog/`下新建`blog_name`文件，并用vim打开，思路是给vim起一个别名，叫做`wblog`.
打开`.bash_profile`文件，添加如下代码：
```shell
alias wblog='function _blah(){ vim ~/blog/$1"; };_blah'
```
然后`source ~/.bash_profile`使改动生效
这样当我执行`wblog newBlog` 时，实际执行的是` vim ~/blog/newBlog`
