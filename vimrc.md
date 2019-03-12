---
tags: vimrc
date: 2018-08-09 17:17:19
---

```vim
" curl -fLo ~/.vim/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
" cp -r ~/.vim/plugged/gruvbox/colors ~/.vim/
syntax enable
set t_Co=256
set background=dark
" Numbers
set number
set ts=2
set expandtab
set autoindent
set formatoptions+=t
set shiftwidth=2
set showmatch
set cursorline
" def end 跳转  括号跳转
runtime macros/matchit.vim
" set tags=/home/mc/code/ctag_source/tags
set tags=tags;
let mapleader = ' '
" 启用缩进折叠
set fdm=indent
" 打开文件时，不折叠代码
set foldlevel=99
map ; :
" - 移动到一行末尾 _ 移动到行的第一个非空白字符
map - $
vmap - $h
imap <C-k> <Up><End><kEnter>
imap <C-d> <Home><Del>
nnoremap <leader>log :AnsiEsc<cr>
nnoremap <c-]> <c-]>zz
inoremap jk <ESC>
" noremap <Up> <Nop>
" noremap <Down> <Nop>
" noremap <Left> <Nop>
" noremap <Right> <Nop>
" nnoremap :e :w<cr>:e<cr>
" 删除至行首
nnoremap d0 v0d==
nnoremap d_ v0d==
" 屏幕行 与 真实行
nnoremap k gk
nnoremap gk k
nnoremap j  gj
nnoremap gj j
" 从系统剪切板粘贴
nnoremap P "+p
" " 复制到系统剪切板
vmap Y "+y
nmap Y "+y
" 跳转到上次修改的位置，并移到屏幕中间
nmap g, g,zz
nmap g; g;zz
nmap * *zz
" 快速编辑vimrc，灵感稍纵即逝
nnoremap <leader>ev :vsplit $MYVIMRC<cr>
nnoremap <leader>cn :set cursorcolumn!<cr>
" 切换窗口
nnoremap <leader>w <c-w>w
" 打开折叠
nnoremap <leader>o zozz
" 折叠
nnoremap <leader>g zczz
" 删除末尾的空格
nnoremap <leader>s :%s/\s\+$//g<cr>
nnoremap <leader>rm :Emodel
nnoremap <leader>rc :Econtroller
nnoremap <leader>rv :Eview
nnoremap <leader>rh :Ehelper
nnoremap <leader>rj :Ejavascript
nnoremap <leader>rs :Estylesheet
nnoremap <leader>rt :Etask
" " 映射切换buffer的键位
nnoremap { :bp<CR>
nnoremap } :bn<CR>
nnoremap ]\ :bd<CR>


"colorscheme solarized
colorscheme gruvbox
"colorscheme molokai

" Specify a directory for plugins
" - For Neovim: ~/.local/share/nvim/plugged
" - Avoid using standard Vim directory names like 'plugin'
call plug#begin('~/.vim/plugged')
" Plug 'chrisbra/csv.vim'
Plug 'vim-scripts/indentpython.vim'
" 缩进线
" Plug 'Yggdroot/indentLine'
" let g:indentLine_char = '|'
" 自动补全符号
Plug 'Raimondi/delimitMate'
Plug 'dccmmtop/vim_script'
Plug 'powerman/vim-plugin-AnsiEsc'
Plug 'SirVer/ultisnips'
Plug 'isRuslan/vim-es6'
Plug 'terryma/vim-multiple-cursors'
Plug 'prettier/vim-prettier', { 'do': 'yarn install' }
Plug 'honza/vim-snippets' "optional
Plug 'mattn/emmet-vim'
" coffee script color
Plug 'kchmck/vim-coffee-script'
" Shorthand notation; fetches https://github.com/junegunn/vim-easy-align
Plug 'junegunn/vim-easy-align'
Plug 'morhetz/gruvbox'
Plug 'tomasr/molokai'
" Multiple Plug commands can be written in a single line using | separators
Plug 'SirVer/ultisnips' | Plug 'honza/vim-snippets'
Plug 'tpope/vim-surround'

" On-demand loading
Plug 'scrooloose/nerdtree', { 'on':  'NERDTreeToggle' }
Plug 'tpope/vim-fireplace', { 'for': 'clojure' }
"注释
Plug 'tomtom/tcomment_vim'
"快速定位
Plug 'easymotion/vim-easymotion'
let g:EasyMotion_smartcase = 1
map  <Tab> <Plug>(easymotion-sn)
omap / <Plug>(easymotion-tn)
map  n <Plug>(easymotion-next)
map  N <Plug>(easymotion-prev)
" Gif config
" 高亮本行光标之后所有单词的头和尾
map fl <Plug>(easymotion-lineforward)
map fj <Plug>(easymotion-j)
map fk <Plug>(easymotion-k)
" 高亮本行光标之前所有单词的头和尾
map fh <Plug>(easymotion-linebackward)
let g:EasyMotion_startofline = 0 " keep cursor column when JK motion
Plug 'rking/ag.vim'
Plug 'airblade/vim-gitgutter'
Plug 'tpope/vim-rails'
Plug 'vim-airline/vim-airline'
Plug 'vim-airline/vim-airline-themes'
let g:airline_powerline_fonts = 1
if g:airline_powerline_fonts == 0
  if !exists('g:airline_symbols')
    let g:airline_symbols = {}
  endif
  let g:airline_left_sep = '▶'
  let g:airline_left_alt_sep = '❯'
  let g:airline_right_sep = '◀'
  let g:airline_right_alt_sep = '❮'
  let g:airline_symbols.paste = 'ρ'
  let g:airline_symbols.linenr = '¶'
  let g:airline_symbols.branch = '§'
  let g:airline_symbols.whitespace = 'Ξ'
  let g:airline_symbols.readonly = ''
endif
let g:airline_mode_map = {
      \ '__' : '-',
      \ 'n'  : '标准',
      \ 'i'  : '插入',
      \ 'R'  : '替换',
      \ 'c'  : '命令行',
      \ 'v'  : '可视',
      \ 'V'  : '可视',
      \ 's'  : '选择',
      \ 'S'  : '选择'
      \}
set laststatus=2
let g:airline#extensions#tabline#enabled = 1
let g:airline#extensions#tabline#buffer_nr_show = 1
let g:airline#extensions#tabline#fnamemod = ':p:t' "只显示文件名，不显示路径内容。

if g:airline_powerline_fonts == 0
  let g:airline#extensions#tabline#left_sep = '▶'
  let g:airline#extensions#tabline#left_alt_sep = '❯'
  let g:airline#extensions#tabline#right_sep = '◀'
  let g:airline#extensions#tabline#right_alt_sep = '❮'
endif
" Initialize plugin system
Plug 'kien/ctrlp.vim'
call plug#end()

"文件搜索"
" set runtimepath^=~/.vim/plugged/ctrlp.vim
let g:ctrlp_map = '<c-p>'
let g:ctrlp_cmd = 'CtrlP'
" 设置过滤不进行查找的后缀名
" let g:ctrlp_custom_ignore = '\v[\/]\.(git|hg|svn|pyc)$'
"end "
let g:NERDTreeWinPos = "right"
" NERDTree config"
map <leader>tt :NERDTreeToggle<CR>
autocmd BufWritePost $MYVIMRC source $MYVIMRC
```
