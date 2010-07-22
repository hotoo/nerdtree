
fileexplorer.vim 插件支持让用户在 NERDTree 里执行
:Explor 或 :Explorer 命令快速的在系统资源管理器中
定位文件/夹。

cmdhere.vim 则让用户执行 :Cmd 或 :Cmdhere 命令，
快速打开 cmd 命令行窗口，并将默认目录定位在 NERDTree
焦点所在的目录。

关键的：fileexplorer.vim 需要 FileExplorer() 函数支持，
把下面的函数加到 vimrc 中即可：

function! FileExplorer(path)
    if a:path == ""
        let p = expand("%:p")
    else
        let p = a:path
    endif

    if executable("chcp")
        let code_page = 'cp' . matchstr(system("chcp"), "\\d\\+")
    else
        " If chcp doesn't work, set its value manually here.
        let code_page = 'cp936'
    endif
    if has('win32') && !has('win32unix') && (&enc!=code_page)
        let p = iconv(p, &enc, code_page)
    endif

    exec ":!start explorer /select, " . p
    " Open Explorer Tree.
    "exec ":!start explorer /e,/select, " . p
endfunction

下面的定义则让整个 Vim 都支持统一的命令，同样加到 vimrc 中：

if has("win32")
    " Open Windows Explorer and Fouse current file.
    "                                      %:p:h     " Just Fold Name.
    nmap <F6> :call FileExplorer("")<cr>
    imap <F6> <C-o><F6>
    command -nargs=0 Explor :call FileExplorer("")
    command -nargs=0 Explorer :call FileExplorer("")

    " Open command(cmd) window.
    command -nargs=0 Cmd :!start cmd
    command -nargs=0 Cmdhere :!start cmd
endif
