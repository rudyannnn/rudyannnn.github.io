---
layout  : wiki
title   : 
summary : 
date    : 2022-02-23 22:54:24 +0900
updated : 2022-02-23 23:03:15 +0900
tag     : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## vimrc 임시저장 2022. 02. 23
 
|set langmenu=en_US.UTF-8
|
|set background=dark
|"colorscheme solarized
|colorscheme desert
|"colorscheme pr0kter
|
|" Showcase comments in italics
|highlight Comment cterm=italic gui=italic
|
|syn on
|set nu
|set autoindent
|set cindent
|set smartindent
|set hlsearch
|set ts=4
|set shiftwidth=4
|set bs=2
|set ruler
|set paste
|set ai
|set backspace=indent,eol,start
|set noantialias
|set guioptions-=T
|set guifont=Consolas:h14
|set encoding=utf-8
|set fileencodings=utf-8,cp949
|set relativenumber
|set listchars=tab:▸\ ,eol:¬
|
|" Persistent undo
|set undodir=~/.vim/undo/
|set undofile
|set undolevels=1000
|set undoreload=10000
|
|" Ignored files/directories from autocomplete (and CtrlP)
|set wildignore+=*/tmp/*
|set wildignore+=*.so
|set wildignore+=*.zip
|set wildignore+=*/vendor/bundle/*
|set wildignore+=*/node_modules/
|
|syntax on
|
|set nocompatible              " be iMproved, required
|filetype off                  " required
|
|" set the runtime path to include Vundle and initialize
|set rtp+=~/.vim/bundle/Vundle.vim
|
|call vundle#begin()
|Plugin 'majutsushi/tagbar'
|Plugin 'altercation/vim-colors-solarized'
|Plugin 'mzlogin/vim-markdown-toc'
|Plugin 'beloglazov/vim-online-thesaurus'
|Plugin 'rhysd/vim-grammarous'
|Plugin 'dpelle/vim-LanguageTool'
|Plugin 'junegunn/goyo.vim'
|Plugin 'junegunn/limelight.vim'
|Plugin 'itchyny/calendar.vim'
|Plugin 'vim-airline/vim-airline'
|Plugin 'vim-airline/vim-airline-themes'
|call vundle#end()
|
|filetype plugin indent on    " required
|
|
|" Specify a directory for plugins
|" - For Neovim: stdpath('data') . '/plugged'
|" - Avoid using standard Vim directory names like 'plugin'
|call plug#begin('~/.vim/plugged')
|
|" Make sure you use single quotes
|
|" Shorthand notation; fetches https://github.com/junegunn/vim-easy-align
|Plug 'junegunn/vim-easy-align'
|
|" Any valid git URL is allowed
|Plug 'https://github.com/junegunn/vim-github-dashboard.git'
|
|" Multiple Plug commands can be written in a single line using | separators
|Plug 'SirVer/ultisnips' | Plug 'honza/vim-snippets'
|
|" On-demand loading
|Plug 'scrooloose/nerdtree', { 'on':  'NERDTreeToggle' }
|Plug 'tpope/vim-fireplace', { 'for': 'clojure' }
|
|" Using a non-master branch
|Plug 'rdnetto/YCM-Generator', { 'branch': 'stable' }
|
|" Using a tagged release; wildcard allowed (requires git 1.9.2 or above)
|Plug 'fatih/vim-go', { 'tag': '*' }
|
|" Plugin options
|Plug 'nsf/gocode', { 'tag': 'v.20150303', 'rtp': 'vim' }
|
|" Plugin outside ~/.vim/plugged with post-update hook
|Plug 'junegunn/fzf', { 'dir': '~/.fzf', 'do': './install --all' }
|Plug 'junegunn/fzf.vim'
|
|" Unmanaged plugin (manually installed and updated)
|Plug '~/my-prototype-plugin'
|
|Plug 'vimwiki/vimwiki', { 'branch': 'dev' }
|
|Plug 'mhinz/vim-startify'
|
|Plug 'jszakmeister/markdown2ctags', {'do' : 'cp ./markdown2ctags.py ~/markdown2ctags.py'}
|
|" johngrib made vimgame
|Plug 'johngrib/vim-game-code-break'
|
|" Initialize plugin system
|call plug#end()
|
|" 로컬 리더 키 설정은 취향이니 각자 마음에 드는 키로 설정한다
|let maplocalleader = "\\"
|
|let g:vim_wiki_set_path = expand('<sfile>:p:h')
|
|"1번 위키(공개용)와 2번 위키(개인용)
|let g:vimwiki_list = [
|    \{
|    \   'path': '~/SDB1/git/rudyannnn.github.io/_wiki',
|    \   'ext' : '.md',
|    \   'diary_rel_path': '.',
|    \},
|    \{
|    \   'path': '~/SDB1/git/Dropbox/wiki',
|    \   'ext' : '.md',
|    \   'diary_rel_path': '.',
|    \},
|\]
|let wiki = {}
|let wiki.path = '~/SDB1/git/rudyannnn.github.io/_wiki/'
|let wiki.ext = '.md'
|
|" vimwiki의 conceallevel 을 끄는 쪽이 좋다
|let g:vimwiki_conceallevel = 0
|let g:vimwiki_table_mappings = 0
|
|command! WikiIndex :VimwikiIndex
|nmap <LocalLeader>ww <Plug>VimwikiIndex
|" nmap <LocalLeader>wt <Plug>VimwikiTabIndex
|nmap <LocalLeader>ws <Plug>VimwikiUISelect
|nmap <LocalLeader>wi <Plug>VimwikiDiaryIndex
|nmap <LocalLeader>w<LocalLeader>w <Plug>VimwikiMakeDiaryNote
|nmap <LocalLeader>w<LocalLeader>t <Plug>VimwikiTabMakeDiaryNote
|nmap <LocalLeader>w<LocalLeader>y <Plug>VimwikiMakeYesterdayDiaryNote
|nmap <LocalLeader>wh <Plug>Vimwiki2HTML
|nmap <LocalLeader>whh <Plug>Vimwiki2HTMLBrowse
|nmap <LocalLeader>wt :VimwikiTable<CR>
|nmap <LocalLeader>s :SLoad 
|
|nmap <Tab>d 0f]lli__date<Space><esc>
|
|nmap <F8> :TagbarToggle<CR>
|
|" F4 키를 누르면 커서가 놓인 단어를 위키에서 검색한다.
|nnoremap <F7> :execute "VWS /" . expand("<cword>") . "/" <Bar> :lopen<CR>
|
|" Shift F4 키를 누르면 현재 문서를 링크한 모든 문서를 검색한다
|nnoremap <C-F7> :execute "VWB" <Bar> :lopen<CR>
|
|nmap <leader>l :set list!<CR>
|
|
|" set pythondll=C:\Users\Administrator\AppData\Local\Programs\Python\Python36-32\\python36.dll
|
|let g:vimwiki_autowriteall=1
|
|let g:ycm_key_list_select_completion = ['<C-n>']
|let g:ycm_key_list_previous_completion=['<C-p>']
|
|let g:ycm_server_python_interpreter = '/usr/bin/python'
|let g:ycm_server_pythonthree_interpreter = '/usr/local/bin/python3.6'
|let g:ycm_collect_identifiers_from_comments_and_strings = 1
|let g:ycm_complete_in_strings = 1
|let g:ycm_complete_in_comments = 1
|let g:ycm_min_num_of_chars_for_completion = 1
|let g:ycm_filetype_blacklist = {}
|
|let g:UltiSnipsExpandTrigger="<Tab>"
|let g:UltiSnipsJumpForwardTrigger="<Tab>"
|let g:UltiSnipsJumpBackwardTrigger="<S-Tab>"
|let g:UltiSnipsEditSplit="vertical"
|" let g:UltiSnipsSnippetDirectories = ['~/.vim/tiSnips']let g:SnipsSnippetDirectories = ['UltiSnips']
|
|let g:startify_bookmarks = [ {'c': '~/.vimrc'}, {'z': '~/.zshrc'} ]
|
|let g:startify_lists = [
|          \ { 'type': 'files',     'header': ['   MRU']            },
|          \ { 'type': 'dir',       'header': ['   MRU '. getcwd()] },
|          \ { 'type': 'sessions',  'header': ['   Sessions']       },
|          \ { 'type': 'bookmarks', 'header': ['   Bookmarks']      },
|          \ { 'type': 'commands',  'header': ['   Commands']       },
|          \ ]
|" If buffer modified, update any 'Last modified: ' in the first 20 lines.
|" 'Last modified: ' can have up to 10 characters before (they are retained).
|" Restores cursor and window position using save_cursor variable.
|
|let g:calendar_google_calendar = 1
|let g:calendar_google_task = 1
|let g:tagbar_use_cache = 0
|
|function! LastModified()
|    if g:md_modify_disabled
|        return
|    endif
|    if &modified
|        " echo('markdown updated time modified')
|        let save_cursor = getpos(".")
|        let n = min([10, line("$")])
|
|        exe 'keepjumps 1,' . n . 's#^\(.\{,10}updated\s*: \).*#\1' .
|                    \ strftime('%Y-%m-%d %H:%M:%S +0900') . '#e'
|        call histdel('search', -1)
|        call setpos('.', save_cursor)
|    endif
|endfun
|function! NewTemplate()
|
|    let l:wiki_directory = v:false
|
|    for wiki in g:vimwiki_list
|        if expand('%:p:h') == expand(wiki.path)
|            let l:wiki_directory = v:true
|            break
|        endif
|    endfor
|
|    if !l:wiki_directory
|        return
|    endif
|
|    if line("$") > 1
|        return
|    endif
|
|    let l:template = []
|    call add(l:template, '---')
|    call add(l:template, 'layout  : wiki')
|    call add(l:template, 'title   : ')
|    call add(l:template, 'summary : ')
|    call add(l:template, 'date    : ' . strftime('%Y-%m-%d %H:%M:%S +0900'))
|    call add(l:template, 'updated : ' . strftime('%Y-%m-%d %H:%M:%S +0900'))
|    call add(l:template, 'tag     : ')
|    call add(l:template, 'toc     : true')
|    call add(l:template, 'public  : true')
|    call add(l:template, 'parent  : ')
|    call add(l:template, 'latex   : false')
|    call add(l:template, '---')
|    call add(l:template, '* TOC')
|    call add(l:template, '{:toc}')
|    call add(l:template, '')
|    call add(l:template, '# ')
|    call setline(1, l:template)
|    execute 'normal! G'
|    execute 'normal! $'
|
|    echom 'new wiki page has created'
|endfunction
|
|augroup vimwikiauto
|    autocmd BufWritePre *wiki/*.md keepjumps call LastModified()
|    autocmd BufRead,BufNewFile *wiki/*.md call NewTemplate()
|    autocmd FileType vimwiki inoremap <C-Right> <C-r>=vimwiki#tbl#kbd_tab()<CR>
|    autocmd FileType vimwiki inoremap <C-Left> <Left><C-r>=vimwiki#tbl#kbd_shift_tab()<CR>
|	autocmd BufWritePost * call system("ctags -R")
|augroup END
|
|augroup vimwiki_tagbar
|    autocmd BufRead,BufNewFile *wiki/*.md TagbarOpen
|    autocmd VimLeavePre *.md TagbarClose
|augroup END
|
|function! RefreshTagbar()
|     let l:is_tagbar_open = bufwinnr('__Tagbar__') != -1
|     if l:is_tagbar_open
|         TagbarClose
|         TagbarOpen
|     endif
|endfunction
|
|function! UpdateBookProgress()
|    let l:cmd = g:vim_wiki_set_path . "/bookProgressUpdate.sh " . expand('%:p')
|    call system(l:cmd)
|    edit
|endfunction
|
|augroup todoauto
|    autocmd BufWritePost *wiki/book.md call UpdateBookProgress()
|augroup END
|
|let g:md_modify_disabled = 0
|"let $VIMRUNTIME+="/usr/local/share/vim/vim82"
|  
|"let g:tagbar_type_vimwiki = {
|"    \ 'ctagstype' : 'vimwiki',
|"    \ 'kinds' : [
|"        \ 'h:Heading'
|"	\],
|"    \ 'sort': 0
|"\ }
|
|let g:tagbar_type_markdown = {
|    \ 'ctagstype': 'markdown',
|    \ 'ctagsbin' : '~/markdown2ctags.py',
|    \ 'ctagsargs' : '-f - --sort=yes --sro=»',
|    \ 'kinds' : [
|        \ 's:sections',
|		\ 'i:images'
|        \ ],
|	\ 'sro' : '»',
|	\ 'kind2scope' : { 's' : 'section' },
|	\ 'sort': 0,
|\ }
|
|execute pathogen#infect()
|set clipboard=unnamedplus "windows clipboard compatible
|set nrformats=   "007 next number 008, not 010
|
|autocmd! User GoyoEnter Limelight
|autocmd! User GoyoLeave Limelight!
|
|" source ~/.cache/calendar.vim/credentials.vim
|
|let g:airline#extensions#tabline#enabled = 1              " vim-airline 버퍼 목록 켜기
|let g:airline#extensions#tabline#fnamemod = ':t'          " vim-airline 버퍼 목록 파일명만 출력
|let g:airline#extensions#tabline#buffer_nr_show = 1       " buffer number를 보여준다
|let g:airlINE#EXtensions#tabline#buffer_nr_format = '%s:' " buffer number format
|nnoremap <C-S-t> :enew<Enter>         " 새로운 버퍼를 연다
|nnoremap <S-F6> :bprevious!<Enter>    " 이전 버퍼로 이동
|nnoremap <S-F7> :bnext!<Enter>        " 다음 버퍼로 이동
|nnoremap <S-F8> :bp <BAR> bd #<Enter> " 현재 버퍼를 닫고 이전 버퍼로 이동
|
|" Easy tab navigation
|nnoremap <C-Left> :tabprevious<CR>
|nnoremap <C-Right> :tabnext<CR>
|
|" Leader Mappings
|map <Space> <leader>
|map <Leader>w :update<CR>
|map <Leader>q :qall<CR>
|map <Leader>gs :Gstatus<CR>
|
|" Recently edited files
|map <Leader>h :History<CR>
|
|" CtrlP use FZF (faster!)
|nnoremap <C-p> :Files<Cr>
|
|syntax on
|autocmd Filetype scss if getfsize(@%) > 300 | setlocal syntax=OFF | endif
|
|augroup twig_ft
|	au!
|	autocmd BufNewFile,BufRead *.html.inky   set syntax=eruby
|augroup END
|
|let s:clip = '/mnt/c/Windows/System32/clip.exe' 
|if executable(s:clip)
|    augroup WSLYank
|        autocmd!
|        autocmd TextYankPost * call system('echo '.shellescape(join(v:event.regcontents, "\<CR>")).' | '.s:clip)
|    augroup END
|end
|
|
|let g:dict_hosts = [
|\["dict.org", ["gcide", "wn", "moby-thes", "vera", "jargon", "foldoc", "bouvier", "devil"]]
|\]
|
|" run command in visual mode
|vnoremap r :w !bash<cr>
|
|" run command in normal mode
|nnoremap yr :.w !bash<cr>
|
