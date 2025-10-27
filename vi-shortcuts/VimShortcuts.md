# ⌨️ Module 05: Vi/Vim Editor - Complete Guide

Welcome back, Linux command-line wizards! 🎉 After mastering file management in Module 04, it’s time to unlock the full power of **Vi/Vim**, the ultimate text editor for DevOps and sysadmins. Whether you’re editing Nginx configs on a remote server or scripting in a minimal environment, Vi/Vim is your go-to tool for lightning-fast, keyboard-driven editing. 🚀 This guide will transform you into a Vim ninja, ready to tackle any text-editing challenge with confidence! 🥷 Let’s dive in!

## 📋 Table of Contents
- [Introduction to Vi/Vim](#introduction-to-vivim)
- [Vi Modes](#vi-modes)
- [Basic Navigation](#basic-navigation)
- [Editing Commands](#editing-commands)
- [Search and Replace](#search-and-replace)
- [Advanced Features](#advanced-features)
- [Vim Configuration](#vim-configuration)
- [Practical Tips and Tricks](#practical-tips-and-tricks)
- [Practice Exercises](#practice-exercises)
- [Quick Reference Cheat Sheet](#quick-reference-cheat-sheet)
- [Additional Resources](#additional-resources)
- [Module Completion Checklist](#module-completion-checklist)

---

## 📝 Introduction to Vi/Vim

### What is Vi/Vim?

**Vi** (Visual Editor) is a lightweight, powerful text editor pre-installed on nearly every Unix/Linux system. **Vim** (Vi IMproved) is its enhanced version, packed with modern features like syntax highlighting, plugins, and advanced scripting.

### Why Learn Vi/Vim? 🤔

- **Universal Availability**: Found on every Linux server, from cloud VMs to embedded systems. 🌐
- **Efficiency**: Master keyboard shortcuts for blazing-fast editing. ⚡
- **DevOps Essential**: Perfect for editing configs (`/etc/nginx/nginx.conf`) over SSH.
- **Powerful Features**: Search/replace, macros, and multi-file editing for complex tasks.
- **No Mouse Needed**: Fully keyboard-driven for minimal latency. 🖮
- **Customizable**: Tailor it to your workflow with `.vimrc` and plugins.

### Opening Files

```bash
# Open file with Vim
vim config.conf

# Open with Vi (may point to Vim)
vi script.sh

# Open at specific line
vim +50 app.py  # Line 50
vim + app.py    # Last line

# Open multiple files
vim nginx.conf apache.conf

# Open in read-only mode
vim -R /etc/passwd
view /etc/passwd  # Same as vim -R

# Compare files with vimdiff
vimdiff old.conf new.conf
vim -d old.conf new.conf

# Create new file
vim new_script.sh
```

**Example**:
```bash
# Edit Nginx config on a server
ssh user@server
vim /etc/nginx/nginx.conf
```

**Pro Tip**: Use `vimtutor` to learn interactively:
```bash
vimtutor
```

---

## 🎭 Vi Modes

Vim’s power lies in its **modes**, which separate navigation, editing, and commands. Think of it as switching gears in a high-performance car! 🚗

### 1. Normal Mode (Command Mode)
- **Default mode** when Vim starts.
- Used for navigation, copying, deleting, and executing commands.
- Return to Normal mode with `Esc` or `Ctrl+[`.

### 2. Insert Mode
- For typing and editing text.
- Enter from Normal mode:
  ```
  i    - Insert before cursor
  I    - Insert at line start
  a    - Append after cursor
  A    - Append at line end
  o    - New line below
  O    - New line above
  ```

### 3. Visual Mode
- For selecting text (like highlighting with a mouse).
- Enter from Normal mode:
  ```
  v    - Character-wise selection
  V    - Line-wise selection
  Ctrl+v - Block-wise selection (for columns)
  ```

### 4. Command-Line Mode
- For executing commands (e.g., save, quit, search).
- Enter from Normal mode:
  ```
  :    - Enter command-line mode
  /    - Search forward
  ?    - Search backward
  ```

### Mode Indicator
- Bottom left shows mode:
  - `-- INSERT --`: Insert mode
  - `-- VISUAL --`: Visual mode
  - `-- REPLACE --`: Replace mode
  - (No indicator): Normal mode

**Example**:
```bash
# Open Vim
vim test.txt
# Press i to enter Insert mode
# Type some text
# Press Esc to return to Normal mode
```

**Pro Tip**: If you’re lost, press `Esc` twice to ensure you’re in Normal mode.

---

## 🧭 Basic Navigation

Navigate like a pro with keyboard shortcuts! 🗺️ All commands are in **Normal mode**.

### Character Movement
```
h    - Move left
j    - Move down
k    - Move up
l    - Move right
```

**Tip**: Arrow keys work, but `h`, `j`, `k`, `l` keep your hands on the home row.

### Word Movement
```
w    - Next word start
W    - Next WORD start (space-separated)
e    - Next word end
E    - Next WORD end
b    - Previous word start
B    - Previous WORD start
```

**Example**:
```
# Text: The quick brown fox
# Cursor: |The quick brown fox
w    # -> The |quick brown fox
e    # -> The quic|k brown fox
b    # -> |The quick brown fox
```

### Line Movement
```
0    - Line start
^    - First non-blank character
$    - Line end
g_   - Last non-blank character
```

**Example**:
```
# Text:    Hello World   
0    # -> |   Hello World
^    # ->    |Hello World
$    # ->    Hello World|
```

### Screen Movement
```
H    - Top of screen
M    - Middle of screen
L    - Bottom of screen
Ctrl+f - Page down
Ctrl+b - Page up
Ctrl+d - Half page down
Ctrl+u - Half page up
zz   - Center current line
zt   - Move current line to top
zb   - Move current line to bottom
```

### File Movement
```
gg   - First line
G    - Last line
:50  - Go to line 50
50G  - Go to line 50
50%  - Go to 50% of file
```

### Jumping
```
%    - Jump to matching bracket/parenthesis ({}, [], ())
{    - Previous paragraph
}    - Next paragraph
Ctrl+o - Previous location
Ctrl+i - Next location
''   - Last cursor position
'.   - Last change position
```

**Example**:
```bash
# Navigate a Python file
vim app.py
gg     # Go to file start
/ def  # Search for function definitions
n      # Next match
50G    # Jump to line 50
```

**Pro Tip**: Combine motions with counts (e.g., `3w` moves three words forward).

---

## ✏️ Editing Commands

Edit text efficiently with Vim’s powerful commands! ✂️ All in **Normal mode**.

### Entering Insert Mode
```
i    - Insert before cursor
I    - Insert at line start
a    - Append after cursor
A    - Append at line end
o    - New line below
O    - New line above
s    - Substitute character
S    - Substitute line
C    - Change to end of line
```

### Deleting Text
```
x    - Delete character under cursor
X    - Delete character before cursor
dw   - Delete word
dd   - Delete line
D    - Delete to end of line
d$   - Same as D
d0   - Delete to line start
d2w  - Delete 2 words
dG   - Delete to file end
```

**Example**:
```
# Text: The quick brown fox
dw   # -> The brown fox
dd   # Deletes entire line
```

### Copying (Yanking) and Pasting
```
yy   - Copy line
yw   - Copy word
y$   - Copy to line end
p    - Paste after cursor/below line
P    - Paste before cursor/above line
"0p  - Paste last yanked text
"+y  - Copy to system clipboard (if supported)
"+p  - Paste from system clipboard
```

**Example**:
```
# Copy and paste a line
yy   # Copy current line
p    # Paste below
3yy  # Copy 3 lines
P    # Paste above
```

### Changing Text
```
cw   - Change word
cc   - Change line
C    - Change to end of line
c$   - Same as C
c2w  - Change 2 words
```

**Example**:
```
# Text: The quick fox
cw   # Delete "quick" and enter Insert mode
# Type: fast
# Result: The fast fox
```

### Undo and Redo
```
u      - Undo last change
Ctrl+r - Redo
U      - Undo all changes on line
3u     - Undo last 3 changes
```

### Repeat Commands
```
.    - Repeat last change
3.   - Repeat last change 3 times
```

**Example**:
```
# Delete 3 lines
dd   # Delete line
.    # Delete another line
.    # Delete one more
```

### Replacing Text
```
r    - Replace single character
R    - Enter Replace mode
5rx  - Replace next 5 characters with 'x'
```

### Joining Lines
```
J    - Join current and next line
3J   - Join 3 lines
```

### Case Conversion
```
~    - Toggle case of character
g~~  - Toggle case of line
guu  - Convert line to lowercase
gUU  - Convert line to uppercase
```

**Example**:
```
# Text: Hello World
guu  # -> hello world
gUU  # -> HELLO WORLD
```

**Pro Tip**: Use Visual mode for bulk edits:
```
v    # Select text
d    # Delete selection
y    # Copy selection
u    # Convert to lowercase
```

---

## 🔍 Search and Replace

Search and replace are Vim’s superpowers for editing configs and code! 🔎

### Searching
```
/pattern    - Search forward
?pattern    - Search backward
n           - Next match
N           - Previous match
*           - Search forward for word under cursor
#           - Search backward for word under cursor
/pattern\c  - Case-insensitive
```

**Example**:
```
/error  # Search for "error"
n       # Next occurrence
N       # Previous occurrence
```

### Find Character in Line
```
f{char}  - Find next {char}
F{char}  - Find previous {char}
t{char}  - To before next {char}
T{char}  - To after previous {char}
;        - Repeat last f/t/F/T
,        - Repeat in opposite direction
```

**Example**:
```
# Text: The quick fox
fa   # Move to next 'a'
;    # Move to next 'a'
```

### Search and Replace
```
:s/old/new/        - Replace first in line
:s/old/new/g       - Replace all in line
:%s/old/new/g      - Replace all in file
:%s/old/new/gc     - Replace with confirmation
:10,20s/old/new/g  - Replace in lines 10-20
:%s/\s\+$//g       - Remove trailing whitespace
```

**Example**:
```bash
# Replace "localhost" with "example.com"
:%s/localhost/example.com/g
# Confirm each replacement
:%s/error/warning/gc
```

### Global Commands
```
:g/pattern/d    - Delete lines matching pattern
:v/pattern/d    - Delete lines NOT matching pattern
:g/error/p      - Print lines with "error"
```

**Example**:
```bash
# Delete all empty lines
:g/^$/d
# Comment all lines with "TODO"
:g/TODO/s/^/# /
```

**Pro Tip**: Test replace commands with `:s` (current line) before using `:%s` (whole file).

---

## 🚀 Advanced Features

Take Vim to the next level with these powerful features! 🌟

### Working with Multiple Files
```
# Open multiple files
vim *.conf

# Navigate buffers
:n        - Next file
:N        - Previous file
:ls       - List buffers
:b 2      - Switch to buffer 2
:bn       - Next buffer
:bp       - Previous buffer
```

**Example**:
```bash
vim /etc/nginx/*.conf
:n     # Next config file
:b 3   # Switch to buffer 3
```

### Split Windows
```
:sp file.txt     - Horizontal split
:vsp file.txt    - Vertical split
Ctrl+w h/j/k/l   - Move between splits
Ctrl+w =         - Equalize split sizes
Ctrl+w q         - Close split
```

**Example**:
```bash
vimdiff old.conf new.conf
]c     # Next difference
[c     # Previous difference
do     # Copy from other file
dp     # Put to other file
```

### Tabs
```
:tabnew file.txt  - Open file in new tab
gt                - Next tab
gT                - Previous tab
:tabc             - Close current tab
:tabs             - List tabs
```

**Example**:
```bash
vim -p file1.txt file2.txt  # Open in tabs
gt                          # Switch to next tab
```

### Marks and Bookmarks
```
ma         - Set mark 'a'
'a         - Jump to line of mark 'a'
`a         - Jump to exact position
:marks     - List all marks
'.         - Jump to last change
```

**Example**:
```bash
# Mark a function
ma         # Set mark 'a'
/def       # Search for next function
`a         # Return to mark 'a'
```

### Macros
```
qa         - Start recording macro in register 'a'
<commands> - Record actions
q          - Stop recording
@a         - Replay macro
10@a       - Replay 10 times
```

**Example**:
```bash
# Add numbers to lines
qa         # Start macro 'a'
I1. <Esc>  # Insert "1. "
j          # Move down
q          # Stop
10@a       # Apply to 10 lines
```

### Registers
```
:reg       - List registers
"ayy       - Yank to register 'a'
"ap        - Paste from register 'a'
"+y        - Copy to system clipboard
```

**Example**:
```bash
# Copy a line to clipboard
"+yy
# Paste from clipboard
"+p
```

### Folding
```
zf5j       - Fold 5 lines
zo         - Open fold
zc         - Close fold
zR         - Open all folds
zM         - Close all folds
```

**Example**:
```bash
# Fold a function block
/^{        # Find opening brace
zfa{       # Fold block
zo         # Open fold
```

### Auto-completion (Insert Mode)
```
Ctrl+n     - Next completion
Ctrl+p     - Previous completion
Ctrl+x Ctrl+f - File path completion
Ctrl+x Ctrl+l - Line completion
```

**Pro Tip**: Use `:help` for any command:
```bash
:help :s
:help navigation
```

---

## ⚙️ Vim Configuration

Customize Vim to suit your workflow with `~/.vimrc`! 🛠️

### Sample `.vimrc`
```vim
" General Settings
set number              " Line numbers
set relativenumber      " Relative line numbers
set showcmd             " Show partial commands
set cursorline          " Highlight current line
set wildmenu            " Command-line completion
set showmatch           " Highlight matching brackets

" Indentation
set tabstop=4           " 4 spaces per tab
set shiftwidth=4        " 4 spaces for indent
set expandtab           " Spaces instead of tabs
set autoindent          " Auto-indent new lines

" Search
set incsearch           " Incremental search
set hlsearch            " Highlight matches
set ignorecase          " Case-insensitive
set smartcase           " Case-sensitive if capitals used

" Appearance
syntax on               " Syntax highlighting
set background=dark     " Dark theme
colorscheme gruvbox     " Color scheme
set termguicolors       " True color support

" Performance
set lazyredraw          " Faster macros
set ttyfast             " Faster scrolling

" Backups
set nobackup            " No backup files
set noswapfile          " No swap files
set undofile            " Persistent undo
set undodir=~/.vim/undo " Undo directory

" Key Mappings
nnoremap <C-s> :w<CR>   " Ctrl+s to save
nnoremap <C-q> :q<CR>   " Ctrl+q to quit
nnoremap <leader><space> :nohlsearch<CR>  " Clear search highlights
nnoremap <A-j> :m .+1<CR>==  " Move line down
nnoremap <A-k> :m .-2<CR>==  " Move line up
```

**Create/Edit `.vimrc`**:
```bash
vim ~/.vimrc
# Add settings
:wq
# Reload
:source ~/.vimrc
```

### Installing Plugins with `vim-plug`
```bash
# Install vim-plug
curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

**Add to `.vimrc`**:
```vim
call plug#begin('~/.vim/plugged')
Plug 'preservim/nerdtree'        " File explorer
Plug 'vim-airline/vim-airline'   " Status line
Plug 'junegunn/fzf.vim'          " Fuzzy finder
Plug 'tpope/vim-fugitive'        " Git integration
Plug 'neoclide/coc.nvim', {'branch': 'release'}  " Auto-completion
Plug 'morhetz/gruvbox'           " Color scheme
call plug#end()
```

**Install Plugins**:
```bash
vim
:PlugInstall
```

**Example**:
```bash
# Open NERDTree
vim
:NERDTree
```

**Pro Tip**: Backup `.vimrc` before editing:
```bash
cp ~/.vimrc ~/.vimrc.bak
```

---

## 💡 Practical Tips and Tricks

### Useful Commands
```
:w          - Save
:q          - Quit
:wq         - Save and quit
:q!         - Quit without saving
:w !sudo tee %  - Save with sudo
:r !ls      - Insert ls output
:sh         - Open shell (type exit to return)
```

### Productivity Tips
```
# Quick edit aliases
echo "alias vimrc='vim ~/.vimrc'" >> ~/.bashrc
echo "alias bashrc='vim ~/.bashrc'" >> ~/.bashrc
source ~/.bashrc

# Visual block for column editing
Ctrl+v    # Block mode
j/k       # Select lines
I         # Insert at start
Type text
Esc       # Apply to all lines

# Increment numbers
Ctrl+a    # Increment number under cursor
Ctrl+x    # Decrement number
```

**Example**:
```bash
# Comment multiple lines
Ctrl+v    # Block mode
10j       # Select 10 lines
I#        # Insert '#'
Esc       # Apply
```

### Common Workflows
1. **Edit Config File**:
   ```bash
   ssh user@server
   vim /etc/nginx/nginx.conf
   /server_name  # Find directive
   cw            # Change word
   :wq           # Save and quit
   ```

2. **Compare Configs**:
   ```bash
   vimdiff nginx.conf nginx.conf.bak
   ]c            # Next diff
   do            # Copy from right
   :wq           # Save
   ```

3. **Batch Comment Code**:
   ```
   :g/function/s/^/#
   ```

4. **Recover from Crash**:
   ```bash
   vim -r file.txt
   :w
   rm .file.txt.swp
   ```

**Pro Tip**: Use `:set paste` before pasting to avoid indentation issues:
```
:set paste
i
<Paste text>
Esc
:set nopaste
```

---

## 🛠️ Practice Exercises

### Exercise 1: Navigation and Editing
1. Create a file:
   ```bash
   vim practice.txt
   ```
2. Add text:
   ```
   i
   Line 1: Hello
   Line 2: World
   Line 3: Vim is awesome
   Esc
   ```
3. Navigate:
   ```
   gg    # First line
   $     # End of line
   2j    # Down 2 lines
   w     # Next word
   ```
4. Edit:
   ```
   cw    # Change "awesome" to "powerful"
   dd    # Delete a line
   u     # Undo
   :wq   # Save and quit
   ```

### Exercise 2: Search and Replace
1. Create a file:
   ```bash
   vim replace.txt
   i
   error log
   Error message
   ERROR critical
   Esc
   ```
2. Replace all "error" with "warning":
   ```
   :%s/error/warning/gi
   ```
3. Save and quit:
   ```
   :wq
   ```

### Exercise 3: Multiple Files and Splits
1. Open multiple files:
   ```bash
   vim file1.txt file2.txt
   ```
2. Split window:
   ```
   :sp file3.txt
   Ctrl+w j  # Move to bottom split
   ```
3. Navigate buffers:
   ```
   :bn       # Next buffer
   :bp       # Previous buffer
   ```
4. Save all and quit:
   ```
   :wqa
   ```

### Exercise 4: Macros
1. Create a file with numbers:
   ```bash
   vim numbers.txt
   i
   item
   item
   item
   Esc
   ```
2. Record a macro to add numbering:
   ```
   qa        # Start macro 'a'
   I1.       # Insert "1."
   <Space>   # Add space
   j         # Next line
   q         # Stop
   2@a       # Apply to 2 more lines
   ```
3. Save:
   ```
   :wq
   ```

### Exercise 5: Configure `.vimrc`
1. Edit `.vimrc`:
   ```bash
   vim ~/.vimrc
   ```
2. Add:
   ```vim
   set number
   set tabstop=4
   set expandtab
   nnoremap <C-s> :w<CR>
   ```
3. Reload and test:
   ```bash
   :source ~/.vimrc
   vim test.txt
   Ctrl+s  # Save
   ```

---

## 📚 Quick Reference Cheat Sheet

```
# Modes
Esc       - Normal mode
i         - Insert mode
v         - Visual mode
:         - Command mode

# Navigation
h j k l   - Left, Down, Up, Right
w b       - Next/Previous word
0 $       - Line start/end
gg G      - File start/end
Ctrl+f/b  - Page down/up

# Editing
x         - Delete char
dd        - Delete line
yy        - Copy line
p         - Paste
u         - Undo
Ctrl+r    - Redo
.         - Repeat

# Search
/pattern  - Search forward
?pattern  - Search backward
n/N       - Next/Previous match
*         - Search word under cursor

# Save/Quit
:w        - Save
:q        - Quit
:wq       - Save and quit
:q!       - Quit without saving
```

---

## 💻 Vi vs Vim vs Neovim

| Feature | Vi | Vim | Neovim |
|---------|-----|-----|---------|
| Availability | Universal | Common | Install Required |
| Syntax Highlighting | No | Yes | Yes |
| Plugins | No | Yes | Advanced |
| Performance | Fast | Fast | Faster |
| Async Support | No | Limited | Yes |
| Terminal Integration | No | Yes | Advanced |

**Recommendation**:
- Use **Vim** for most servers (widely available).
- Learn **Vi** commands for minimal systems.
- Try **Neovim** for modern development workflows.

---

## 📖 Additional Resources

- **[Vim Documentation](http://www.vim.org/docs.php)**: Official Vim docs.
- **[Vim Adventures](https://vim-adventures.com/)**: Interactive Vim learning game.
- **[Linux Journey: Vim](https://linuxjourney.com/lesson/vim)**: Beginner-friendly tutorial.
- **[Practical Vim](https://pragprog.com/titles/dnvim2/practical-vim-second-edition/)**: Book by Drew Neil.
- **[Reddit r/vim](https://www.reddit.com/r/vim/)**: Community for tips and plugins.
- **[Vim Cheat Sheet](https://vim.rtorr.com/)**: Printable reference.

**Community Tip**: Run `vimtutor` for a hands-on tutorial, and join [Stack Exchange: Vi and Vim](https://vi.stackexchange.com/) for expert advice! 🗣️

---

## ✅ Module Completion Checklist

- [ ] Understand Vi/Vim modes (Normal, Insert, Visual, Command-Line)
- [ ] Navigate files using `h`, `j`, `k`, `l`, `w`, `gg`, etc.
- [ ] Edit text with `dd`, `yy`, `p`, `cw`, etc.
- [ ] Perform search and replace with `:%s`
- [ ] Work with multiple files, splits, and tabs
- [ ] Customize `.vimrc` with basic settings
- [ ] Use macros for repetitive tasks
- [ ] Complete all practice exercises
- [ ] Feel confident editing configs over SSH

---

## 🎓 Next Steps

1. **Practice Daily**: Edit all files with Vim to build muscle memory.
2. **Learn One Command Daily**: Add new shortcuts to your workflow.
3. **Customize `.vimrc`**: Add plugins like NERDTree or fzf.
4. **Explore Neovim**: For modern features and async plugins.
5. **Use `vimtutor`**: Refresh your skills regularly.

---

**Previous Module**: [Module 04: File Management](../04-file-management/FileManagement.md)  
**Next Module**: [Module 06: File Permissions](../06-file-permissions/FilePermissions.md)

[⬆ Back to Main README](../README.md)