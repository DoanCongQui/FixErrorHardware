Install vim 
```
sudo apt install vim curl git
```
Install plug.vim
```

```
Thêm vào file `~/.vimrc`
```
set number
set mouse=a                 " Enable mouse
set expandtab               " Tab setting 
set tabstop=4               " Tab setting 
set shiftwidth=4            " Tab setting
set listchars=tab:\¦\       " Tab charactor

syntax on

call plug#begin('~/.vim/plugged')
  Plug 'joshdick/onedark.vim',                  " Dark theme

" Status bar
  Plug 'vim-airline/vim-airline'
  Plug 'vim-airline/vim-airline-themes'

" File browser
  Plug 'preservim/nerdTree'                     " File browser  
  Plug 'ryanoasis/vim-devicons'                 " Icon

" Comment 
  Plug 'jiangmiao/auto-pairs'                   " Parenthesis auto
  Plug 'preservim/nerdcommenter'                " Comment code

call plug#end()

colorscheme onedark
highlight Normal ctermbg=none guibg=none

" ***************** MAP *****************
" Status bar
let g:airline#extensions#tabline#enabled = 1            " Enable Tab bar
let g:airline#extensions#tabline#left_sep = ' '         " Enable Tab seperator
let g:airline#extensions#tabline#left_alt_sep = '|'     " Enable Tab seperator
let g:airline#extensions#tabline#formatter = 'default'
let g:airline#extensions#tabline#fnamemod = ':t'        " Set Tab name as file name

" ------------ COMMENT ------------
vmap ++ <plug>NERDCommenterToggle
nmap ++ <plug>NERDCommenterToggle
nnoremap = <C-w>>

" Create default mappings
let g:NERDCreateDefaultMappings = 1

" Add spaces after comment delimiters by default
let g:NERDSpaceDelims = 1

" Use compact syntax for prettified multi-line comments
let g:NERDCompactSexyComs = 1

" Align line-wise comment delimiters flush left instead of following code indentation
let g:NERDDefaultAlign = 'left'

" Set a language to use its alternate delimiters by default
let g:NERDAltDelims_java = 1

" Add your own custom formats or override the defaults
let g:NERDCustomDelimiters = { 'c': { 'left': '/**','right': '*/' } }

" Allow commenting and inverting empty lines (useful when commenting a region)
let g:NERDCommentEmptyLines = 1

" Enable trimming of trailing whitespace when uncommenting
let g:NERDTrimTrailingWhitespace = 1

" Enable NERDCommenterToggle to check all selected lines is commented or not 
let g:NERDToggleCheckAllLines = 1

" ---------------- File Browers ---------------
" F5 to toggle 
map <silent> <C-n> :NERDTreeToggle<CR>

" Open the existing NERDTree on each new tab.
"autocmd BufWinEnter * silent NERDTreeMirror

" Exit Vim if NERDTree is the only window left.
autocmd BufEnter * if tabpagenr('$') == 1 && winnr('$') == 1 && exists('b:NERDTree') && b:NERDTree.isTabTree() | quit | endif

" Change arrow to expand/collapse tree
let g:NERDTreeDirArrowExpandable = '▸'
let g:NERDTreeDirArrowCollapsible = '▾'

"let NERDTreeMapOpenInTab='<ENTER>'
```
