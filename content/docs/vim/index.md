---
title: 'Vim'
date: 2019-02-11T19:27:37+10:00
weight: 2
---

### Save/Quit

- Save file -\> **:w**
- Save file and quit -\> **:x** or **ZZ**
- Quit -\> **:q**
- Quit discarding changes -\> **:q!** or **ZQ**

### Modes

- Normal -\> **ESC**, **Ctrl** + **c**
- Insert -\> **i**, **I**, **a**, **A**
- Replace -\> **R**
- Visual -\> **v**, **V**, **Ctrl** + **v**
- Command -\> **:**

### Indentation

- Insert Mode:
  - **Ctrl** + **d** : shift left
  - **Ctrl** + **t** : shift right
- Normal mode:
  - **Shift** + **«** : shift current line left
  - **Shift** + **»** : shift current line right
- Visual mode:
  - **<** : shift selection left
  - **>** : shift selection right


#### Visual mode multi-line editing
1. Move the cursor exactly where you want to insert the new text
2. `Ctrl + v`
3. Move up or down the lines to select several lines
4. `Shift + i`
5. Type in the text
6. `ESC`

The typed text will replicate to all selected lines


### Moving around

- Search forward and backward once -\> **/** and **?**
- On search: (next hit -\> **n**, previous hit -\> **N**)
- **f**, **F** -\> "find" the next character, including the character
- **t**, **T** -\> "find" the next character, up to that character
- Search for next/previous match currently under cursor -\> **asterisk** / **#**
- Find and replace all occurrences of *foo* with *bar* -\> **:%s/foo/bar/g**
- Move to the paired parenthesis/curly braces -\> **%**
- Move to the beginning/end of a word (forward) -\> **w** / **e**
- Move to the beginning/end of a word (backward) -\> **b** / **ge**
- Move to beginning/end of line and start editing -\> **I** / **A**
- Move to the beginning/end of the line -\> **0** / **$**
- Move to the first character of the line -\> **_**
- Jump forward/backward one line -\> **j** / **k**
- Jump forward/backward one paragraph -\> **}** / **{**
- Jump to the top/middle/bottom of the page -\> **H** / **M** / **L**
- Jump forward/backward one page -\> **Ctrl** + **f**  / **Ctrl** + **b**
- Jump up/down half page -\> **Ctrl** + **u**  / **Ctrl** + **d**
- Jump to next line with same content -\> **Right Alt** + **3**
- Move cursor to the /top/middle/bottom of the page -\> **zt** / **zz** / **zb**
- Move cursor to the beginning/end of document -\> **gg** / **G**
- Move cursor to a specific line number -\> **:10**
- Move cursor X lines above/below -\> **10k**/**10j**
- Jump to the previous/next file cursor position -\> **Ctrl** + **o** / **Ctrl** + **i**

### Line numbers
- Activate absolute line numbers -\> **:set nu**
- Activate relative line numbers -\> **:set rnu**
- Toggle absolute line numbers -\> **:set nu!**
- Toggle relative line numbers -\> **:set rnu!**

### Editing

#### Operators
Operators specify which operation to perfom:

- **d** -\> Delete/Cut
- **y** -\> Yank (copy)
- **c** -\> Change (delete and enter insert mode)
- **r** -\> Replace
- **v** -\> Visually select

#### Motion
Motion specifies where the Operators operate in relation to the cursor position:

- **w** -\> word 
- **p** -\> paragraph 
- **_** -\> line
- **2j** -\> down 2 lines
- **e** -\> until the end of the word
- **$** -\> until the end of the line
- **0** -\> from the beginning of the line
- **t/T** -> until a specific character
- **f/F** -> until a specific character, including the specific character
- **Ctrl** + **v** -\> Select block
- **i** -\> INNER: inside/between something
  - **iw** -\> "inner word" (works from anywhere in a word)
  - **it** -\> "inner tag" (works within the contents of an HTML tag)
  - **i"** -\> "inner quotes"
  - **i_** -\> "inner line"
  - **is** -\> "inner sentence"
  - **ip** -\> "inner paragraph"
- **a** -\> AROUND: like INNER, but including the tag, quotes, etc
  - **at** -\> "around tag" (works within the content of an HTML tag including the tags)
  - **a"** -\> "around quotes" (works within the content of including the quotes)
  - **as** -\> "around sentence"
  - **ap** -\> "around paragraph"

Examples:

- Delete everything within/closest tag, type: `dit`
- Delete everything until it finds a dot: `dt.`
- Delete everything until it finds a dot, including the dot: `df.`
- Delete everything between paired parenthesis/curly braces: `da%`
- Select the content within/closest parenthesis: `vi(`
- Delete the content within/closest parenthesis: `di(`
- Delete the content within/closest parenthesis and start typing: `ci(`

#### Common commands
- repeat last operation -\> **.**
- copy selection -\> **y**
- copy character-\> **yl**
- copy word -\> **yiw**
- copy line -\> **yy**
- copy paragraph -\> **yap**
- Select paragraph -\> **vip**
- delete/cut selection -\> **d** 
- delete/cut word from cursor position forward -\> **dw**
- delete/cut word from cursor position backward -\> **db**
- delete/cut whole word -\> **diw**
- delete/cut word and insert mode -\> **cw** 
- delete/cut paragraph -\> **dap**
- paste -\> **p**
- undo -\> **u**
- redo -\> **ctrl** + **r**
- delete character (Del) -\> **x**
- delete character (Backspace) -\> **X**
- replace character -\> **r**
- delete character and insert mode -\> **s**
- Switch to 'Insert' mode -\> **i**
- Exit 'Insert' mode -\> **ESC** or **Ctrl** + **c**
- Increase/Decrease the first number in a line: **Ctrl** + **a**/**x**
- Format block of text -\> **V** + *text selection* + **=**
- Repeat command mode last command -\> **@:**


#### Lines

- create new line below -\> **o**
- create a new line above -\> **O**
- copy line -\> **yy**
- copy all lines under the cursor -\> **:.,$y** or **:+,$y**
- paste -\> **p**
- paste line above -\> **P**
- delete/cut line -\> **dd**
- delete/cut everything from the beginning of the line up to the cursor -\> **d0**
- delete/cut everthing from the cursor to the end of the line -\> **D** or **d$**
- delete/cut the rest of the line and insert mode -\> **C**
- delete/cut the whole line and insert mode -\> **S**
- delete/cut from the current line to the end of the file -\> **:.,$d** or **:+,$d**
- delete/cut from the current line to the beginning of the file -\> **:.,1d**
- delete/cut from line 3 to line 10 -\> **:3,10d**
- delete/cut from line 3 to the end of the file -\> **:3,$d**
- delete/cut the last line-\> **:$d**
- delete/cut all lines-\> **:%d**
- move current line after line 6 -\> **:m 6**
- move line two lines above -\> **:m-2** (note that **:m-1** won't work)
- move line one line below -\> **:m+1**
- move line to beginning/end of document -\> **:m 0** / **:m $**
- move block of lines -\> **:5,7m 12** (move lines 5,6,7 after line 12)
- move block of lines -\> **:5,7m+4** (move lines 5,6,7 four lines below)
- indent current line -\> **<<** or **>>**
- indent current line plus 2 lines below -\> **3<<** or **3>>**
- select a range of lines and indent -\> **:4,17<**
- Delete all lines containing the string “foo”. It also removes line where “foo” is embedded in larger words, such as “football” -\> **:g/foo/d**
- Delete all lines not containing the string “foo” -\> **:g!/foo/d**
- Remove all comments from a Bash script. The pattern ^# means each line beginning with # -\> **:g/^#/d**
- Remove all blank lines. The pattern ^$ matches all empty lines -\> **:g/^$/d**


#### Special operations

- Comment a block of lines:
  1. Select the block of lines with **V**
  2. Enter command mode and type: `norm I#`

- Uncomment a block of lines:
  1. Select the block of lines with **V**
  2. Enter command mode and type: `norm x` 

- Find and replace all occurrences of *foo* with *bar* -\> **:%s/foo/bar/g**

- Find and replace *foo* with *bar* X number of times:
  1. Search for *foo*: `/foo` + **enter**
  2. Type `cgn` + `bar`
  3. **ESC**
  4. Iterate through the results:
    - Press **.** to replace next *foo* instance for *bar*
    - Press **n** to skip next *foo* instance


- Move all lines that start with specific character/s to the end of the document. In this example, the specific characters is `static`:
```
:g/^static/m$`
```

## File Management

### Explorer

- Open file explorer -\> **:e .**
- Open file explorer -\> **:Ex**
- Open file explorer -\> **:Explore**
- Create a directory within Explorer -\> **d**
- Create a file within Explorer -\> **%**
- Delete a file/directory within Explorer -\> Place the cursor over the file/directory and press **D**
- Create a new file: `:e filename`
- List files in current directory -\> **:e** then press **Space** and **Ctrl**+**d**
- Open/create a file in new window horizontally -\> **split filename** or **sp: filename** 
- Open/create a file in new window vertically -\> **vsplit filename** or **vsp: filename**

### Windows
- Split window horizontally -\> **:split** or **:sp** or **Ctrl** + **w** + **s**
- Split window vertically -\> **:vsplit** or **:vs** or **Ctrl** + **w** + **v**
- Cycle through windows -\> **Ctrl** + **w**  + **w**
- Move to left window -\> **Ctrl** + **h** or **Ctrl** + **w** + **h**
- Move to right window -\> **Ctrl** + **l** or **Ctrl** + **w** + **l**
- Move to previous active window -\> **Ctrl** + **w** + **p**
- Swap windows positions -\> **Ctrl** + **w** + **r**
- Move windows directionally -\> **Ctrl** + **w** + **H/J/K/L**
- Close current window -\> **Ctrl + q** or **:hide**
- Close all windows except current one: -\> **:only**

### Tabs

- New tab -\> **:tabnew**
- Go to next tab -\> **gt**
- Go to previous tab -\> **gT**
- Go to tab number -\> **#gt**
- Go to first tab -\> **:tabr**
- Go to last tab -\> **:tabl**
- Move current tab to the last position -\> **:tabm**
- Move current tab to the # position -\> **:tabm #**
- Close tab -\> **:close**

### Buffers

- Open a new 100 characters width window buffer explorer -\> **:100vs .** 
- Toggle between open windows: -\> **Ctrl** + **w** + **w**
- Open new file in buffer -\> **:e <filename path\>**
- Open a new file and split screen horizontally -\> **:sp**
- Open a new file and split screen vertically -\> **:vsp**
- List active buffers -\> **:ls**
- Change to specific buffer -\> **:b <buffer-number\>**
- Move to next/previous buffer -\> **:bn** / **:bp**
- Delete current buffer -\> **:bd**
- Jump back/forward to last jump location -\> **Ctrl** + **o** / **Ctrl** + **i**

## Customize Vim

You can customize Vim by editing `~/.vimrc`

```
let mapleader = "," 
set tabstop=4
set autoindent
set number " enable line numbering
set relativenumber "enable relative line number
syntax enable " syntax highlighting enabled


" KEY MAPPINGS

nmap <leader>ee :vsplit<cr> :Explore<cr> " pressing ,ee will open Explore in a new vertical window
nmap <leader>zz :split $MYVIMRC<cr> " Pressing ,zz will open ~/.vimrc in a new window
nmap <leader>zx :source $MYVIMRC<cr> " Pressing ,zx will source the changes made in ~/.vimrc
vmap <buffer> ;bo "zdi<strong><c-r>z</strong><esc>
```

**Note**: For the changes to take affect, run this command inside Vim:

`:source $MYVIMRC`

<Message variant='info'>
  🎁 <b>Gift</b> <br/>
You can download a useful <i>~/.vimrc</i> file by running the command <em>wget https://raw.githubusercontent.com/cheomanigua/config/master/vimrc</em>
</Message>


### Key mapping

- List keymaps: `:map`
- Remove keymap: `:unmap <keymap>`

### Snippet insertion

1. Create in `~/.vim/templates/` directory the text file containing the snippet
2. Add this lines in `~/.vimrc`:

```
function Rsf() 
 :read ~/.vim/templates/<textfile>
endfunction
```
3. Restart vim: `:so $MYVIMRC`
4. When you need to insert the snippet, type: `:call Rsf()`

**Note**: `Rsf()` is used as an example. You can name your function whatever you want.

<Message variant='important'>
  💡 <b>Tip</b> <br/>
	You can keymap <strong>:call Rsf()</strong> so you won't need to type in the full command.
</Message>

## Macros
```
[move the cursor where you want to start to record the macro]
$ qq
[do all the staff]
$ ESC
$ q
[move the cursor where you want to apply the macro]
$ @q
```

# Plugins

It is recommended to install a plugin manager in order to install, load and uninstall plugins.

## Vim 8 plugin manager

Vim comes with a built-in plugin manager called **Vim 8 packages**.

### Activating the plugin manager

1. First, create a directory structure representing a plugin-group, say `plugins`, as follows:

```
$ mkdir -p ~/.vim/pack/plugins/start/
```

2. Activate the plugin manager:
```
$ sed -i -e '$a\'$'\n''filetype plugin indent on' ~/.vimrc
```

### Installing a plugin

Clone (or alternatively, download the zip, and unzip) the plugin you want to install inside the `start` directory:

```
$ git clone https://github.com/foo/bar.git ~/.vim/pack/plugins/start/bar
```

### Generating helptags for a plugin
```
:helptags ~/.vim/pack/plugins/start/foo
```

### Upgrading a plugin
```
$ cd ~/.vim/pack/plugins/start/bar
$ git pull origin master
```

### Deleting a plugin
```
$ rm -r ~/.vim/pack/plugins/start/bar
```

### Example: installing Vim-go plugin

Vim-go is a plugin that facilitates the Golang programming experience in Vim. This is how to install using the Vim 8 plugin manager:

1. Add the plugin:

```
$ git clone https://github.com/fatih/vim-go.git ~/.vim/pack/plugins/start/vim-go
```

2. Install all necessary binaries:

```
:GoInstallBinaries
```

3. Generate the plugin's help tags:

```
:helptags ALL
```

4. Code completion is enabled by default via `omnifunc`, which you can trigger
   with **Ctrl-X** + **Ctrl-O**


## Vim-plug plugin manager

1. Download <a href="https://github.com/junegunn/vim-plug" target="_blank">vim-plug</a>:

  `$ curl -fLo ~/.vim/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim`

2. Add **vim-plug** in your **~/.vimrc** file:
```
" vim-plug
call plug#begin('~/.vim/plugged')

" plugin section

" end vim-plug
call plug#end()
```

3. Add, for instance, <a href="https://github.com/MaxMEllon/vim-jsx-pretty" target="_blank">vim-jsx-pretty</a>, <a href="https://github.com/mattn/emmet-vim" target="_blank">emmet-vim</a> and <a href="https://github.com/neoclide/coc.nvim" target="_blank">Intellisense</a> plugins in your **~/.vimrc** file:
```
" vim-plug
call plug#begin('~/.vim/plugged')

" plugin section
Plug 'maxmellon/vim-jsx-pretty'
Plug 'mattn/emmet-vim'
Plug 'neoclide/coc.nvim', {'branch': 'release'}

" end vim-plug
call plug#end()
```
4. Launch Vim and run: `:PlugInstall`

That's it. This is how you install plugins in Vim with <a href="https://github.com/junegunn/vim-plug" target="_blank">vim-plug</a>.



# NeoVim 

## Install last version (Ubuntu)

```bash
$ sudo add-apt-repository ppa:neovim-ppa/unstable -y
$ sudo apt update
$ sudo apt install make gcc ripgrep unzip git xclip neovim
```

### Launch NeoVim

```bash
$ nvim
```

## kickstart.nvim

Readme Page: [https://github.com/nvim-lua/kickstart.nvim?tab=readme-ov-file](https://github.com/nvim-lua/kickstart.nvim?tab=readme-ov-file)

### Installation
```bash
$ git clone https://github.com/nvim-lua/kickstart.nvim.git "${XDG_CONFIG_HOME:-$HOME/.config}"/nvim
$ nvim
```

### Edit config file
Within NeoVim, type: `:e $MYVIMRC`

### Golang support
1. Edit the config file and uncomment this line:
```lua
-- gopls = {},
```

2. Edit the config file and make these lines to look like this:
```lua
        dependencies = {
          -- `friendly-snippets` contains a variety of premade snippets.
          --    See the README about individual language/framework/plugin snippets:
          --    https://github.com/rafamadriz/friendly-snippets
          {
            'rafamadriz/friendly-snippets',
            config = function()
              require('luasnip.loaders.from_vscode').lazy_load { include = {"go", "html", "css"}}
            end,
          },
        },
```

3. Edit the config file and add 'go' to this line:
```lua
ensure_installed = { 'go', 'bash', 'c', 'diff', 'html', 'lua', 'luadoc', 'markdown', 'markdown_inline', 'query', 'vim', 'vimdoc' },
```
Run `:TSUpdate`

4. Restart your computer

### Useful commands
- **Space** + **s** + **g** -\> Search for any word in the whole project
- **Space** + **s** + **w** -\> Search for the word where the cursor is on in the whole project
- **Space** + **s** + **f** -\> Search for files in the project
- **g** + **d** -\> Go to Definition of the function/variable where the cursor is on
- **g** + **D** -\> Go to Declaration of the function/variable where the cursor is on
- **g** + **I** -\> Go to Implementation of the function/variable where the cursor is on
- **g** + **I** -\> Go to Implementation of the function/variable where the cursor is on
- **:e .** -\> Open file explorer
