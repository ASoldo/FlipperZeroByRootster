# Vim Simple Setup

Create new file `.vimrc` in `/home/your_user` directory with following command.

```sh
touch .vimrc
```

With current Vim use this command.
```sh
vim .vimrc
```

Now you need to add some fields to have IDE like experience.

```sh
set syntax=on
set rnu
set nu
set background=dark
set tabstop=2
```

In order to paste your new config in Vim, you need to paste it with Shift+Ctrl+v while in Inser Mode (i).
After that you can save your changes with `:w` or `wq` if you want to save and exit.

>Note that you may encounter situation where you need to override changes, then use `:w!` or `:q!` depending on what you need to do.

# Basics:

#### Modes
>normal mode - esc
>insert mode - i
>visual mode - v
>visual line mode - V
>while in visual mode, change positon to head or tail - o

#### Movement
>move - h,j,k,l
>move one word - w
>move at the end of the word - e
>move backwards - b
>jump to end of line - 0
>jump to the start of the line - $

#### Find and Jump
>find and jump on the first character - f
>find and jump on the first character backwards - F
>iterate over found symbols forward - ;
>iterate over found symbols backwards - ,

#### Find and Jump up to
>find and jump before first character - t
>ind and jump before first character backwards - T
>iterate over found symbols forward - ;
>iterate over found symbols backwards - ,

#### Insert Options
>insert on cursor positon - i
>insert after current character - a
>insert below one line - o
>insert above one line - O
>paste - p 
>paste from history - :reg
>copy - yy
>delete - dd
>delete current character - x
>delete current character and enter insert mode - s
>replace current character - r
>repeat command --> 9p --> this will paste copied line 9 time downwards

#### Search
>search document - /
>iterate over found symbols forward - n
>iterate over found symbols backwards - N
>jump to the same symbol under the cursor - *
>clear highlight - :noh

#### Misc
>command line - :

#### Save operations
>save - :w
>force save - :w!

#### Exit operations
>exit - :q
>force exit - :q!
>alternative exit - Ctrl+z

#### Save and Exit
>save and exit - :wq
>force save and exit - :wq!

#### Position shortcuts
>go to top of the file - gg
>go to bottom of the file - G
>scroll up - Ctrl+u
>scroll down - Ctrl+d

#### Smart Selections
>select whole sentence - vis
>select whole paragraph - vip
>delete whole sentence - cis
>delete whole paragraph - cip
>select all inside () - vib
>delete all inside and start typing () - cib
>select all inside {} - viB
>delete all inside and start typing {} - ciB
>select all inside <> - viT
>delete all inside and start typing <> - ciT



