```vim
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
let mapleader = ' '
" 启用缩进折叠
set fdm=indent
" 打开文件时，不折叠代码
set foldlevel=99
map ; :
" - 移动到一行末尾 _ 移动到行的第一个非空白字符
map - $
imap <C-k> <Up><End><kEnter>
imap <C-d> <Home><Del>
imap <C-d> <Home><Del>
inoremap jk <ESC>
noremap <Up> <Nop>
noremap <Down> <Nop>
noremap <Left> <Nop>
noremap <Right> <Nop>
nnoremap k gk
nnoremap gk k
nnoremap j  gj
nnoremap gj j
" 从系统剪切板粘贴
nnoremap P "+p
" 复制到系统剪切板
vmap Y "+y
nmap Y "+y
" 跳转到上次修改的位置，并移到屏幕中间
nmap g, g,zz
nmap g; g;zz
" 快速编辑vimrc，灵感稍纵即逝
nnoremap <leader>ev :vsplit $MYVIMRC<cr>
nnoremap <leader>vs :vsplit<cr>
nnoremap <leader>cn :set cursorcolumn!<cr>
" 打开折叠
nnoremap <leader>k zozz
" 折叠
nnoremap <leader>g zczz
"colorscheme solarized
colorscheme gruvbox
"colorscheme molokai

" Specify a directory for plugins
" - For Neovim: ~/.local/share/nvim/plugged
" - Avoid using standard Vim directory names like 'plugin'
call plug#begin('~/.vim/plugged')
Plug 'sheerun/vim-polyglot'

Plug 'SirVer/ultisnips'

" Currently, es6 version of snippets is available in es6 branch only
Plug 'letientai299/vim-react-snippets', { 'branch': 'es6' }
Plug 'prettier/vim-prettier', { 'do': 'yarn install' }
" js代码格式化
let g:prettier#autoformat = 0
" 异步格式化
autocmd BufWritePre *.js,*.jsx,*.mjs,*.ts,*.tsx,*.css,*.less,*.scss,*.json,*.graphql,*.md,*.vue PrettierAsync
" 发生语法错误时，不打开新的窗口提示
let g:prettier#quickfix_enabled = 0
Plug 'honza/vim-snippets' "optional

Plug 'godlygeek/tabular'
Plug 'plasticboy/vim-markdown'
let g:vim_markdown_folding_disabled = 1
let g:vim_markdown_override_foldtext = 0
let g:vim_markdown_toc_autofit = 1
let g:vim_markdown_conceal = 0
let g:tex_conceal = ""
let g:vim_markdown_math = 1
let g:vim_markdown_fenced_languages = ['csharp=cs']
let g:vim_markdown_new_list_item_indent = 2

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

" Using a non-master branch


" Plugin outside ~/.vim/plugged with post-update hook
Plug 'junegunn/fzf', { 'dir': '~/.fzf', 'do': './install --all' }

" Unmanaged plugin (manually installed and updated)
Plug '~/my-prototype-plugin'
"注释
Plug 'tomtom/tcomment_vim'
"快速定位
Plug 'easymotion/vim-easymotion'
let g:EasyMotion_smartcase = 1
map  <Tab> <Plug>(easymotion-sn)
omap / <Plug>(easymotion-tn)

" These `n` & `N` mappings are options. You do not have to map `n` & `N` to
" EasyMotion.
" " Without these mappings, `n` & `N` works fine. (These mappings just provide
" " different highlight method and have some other features )
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
" " 映射切换buffer的键位
nnoremap { :bp<CR>
nnoremap } :bn<CR>
nnoremap ]\ :bd<CR>
" Initialize plugin system


call plug#end()

"文件搜索"
set runtimepath^=~/.vim/plugged/ctrlp.vim
let g:ctrlp_map = '<c-p>'
let g:ctrlp_cmd = 'CtrlP'
" 设置过滤不进行查找的后缀名
" let g:ctrlp_custom_ignore = '\v[\/]\.(git|hg|svn|pyc)$'
"end "
let g:NERDTreeWinPos = "right"


" NERDTree config"
map <F2> :NERDTreeToggle<CR>

autocmd BufWritePost $MYVIMRC source $MYVIMRC
vmap <leader>qn :cal qiniu#get_file_url_from_qiniu()<cr><CR>
noremap <leader>fa :cal react_native#flush()<cr><CR>
```
