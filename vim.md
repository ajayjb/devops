# Linux Vim Editor - Complete Guide

## What is Vim?

Vim (Vi Improved) is a powerful, highly configurable text editor built on top of the traditional Unix vi editor. It's one of the most popular text editors among developers, system administrators, and Linux users worldwide. Vim is known for its efficiency, speed, and modal editing paradigm.

---

## Why Use Vim?

- **Lightweight & Fast**: Minimal resource usage, works over slow SSH connections
- **Modal Editing**: Different modes for different tasks (normal, insert, command)
- **Highly Customizable**: Extensive plugin ecosystem and configuration options
- **Powerful Commands**: Complex text manipulation with minimal keystrokes
- **Available Everywhere**: Pre-installed on most Unix/Linux systems
- **Steep Learning Curve Worth It**: Once learned, significantly increases productivity

---

## Vim Modes Explained

### 1. **Normal Mode** (Command Mode)
- Default mode when you open Vim
- Used for navigation and executing commands
- You cannot type text directly in this mode
- Press `Esc` to return to Normal Mode from any other mode

### 2. **Insert Mode**
- Allows you to type and edit text like a regular editor
- Enter with: `i`, `a`, `o`, `I`, `A`, `O`
- Press `Esc` to exit and return to Normal Mode

### 3. **Command Mode** (Ex Mode)
- Access advanced commands
- Enter by pressing `:` in Normal Mode
- Used for saving, quitting, searching, and more

### 4. **Visual Mode**
- For selecting text
- Enter with: `v` (character), `V` (line), `Ctrl+v` (block)
- Useful for copying, deleting, or formatting selected text

---

## Basic Commands

### Opening & Closing Vim

```bash
# Open a file
vim filename.txt

# Open multiple files
vim file1.txt file2.txt

# Open at specific line
vim +10 filename.txt

# Read-only mode
vim -R filename.txt
```

### Exiting Vim

| Command | Action |
|---------|--------|
| `:q` | Quit (if no changes) |
| `:q!` | Quit without saving |
| `:w` | Save file |
| `:wq` or `:x` | Save and quit |
| `:w filename` | Save as new file |
| `ZZ` | Save and quit (Normal Mode) |

---

## Navigation Commands (Normal Mode)

### Moving the Cursor

| Key | Action |
|-----|--------|
| `h` | Move left |
| `j` | Move down |
| `k` | Move up |
| `l` | Move right |
| `w` | Move to next word |
| `b` | Move to previous word |
| `0` | Move to line start |
| `$` | Move to line end |
| `gg` | Go to file start |
| `G` | Go to file end |
| `Ctrl+u` | Page up |
| `Ctrl+d` | Page down |

---

## Editing Commands (Normal Mode)

### Deleting Text

| Command | Action |
|---------|--------|
| `x` | Delete character under cursor |
| `dd` | Delete entire line |
| `dw` | Delete word |
| `d$` | Delete to end of line |
| `d0` | Delete to start of line |

### Copying & Pasting

| Command | Action |
|---------|--------|
| `yy` | Copy (yank) entire line |
| `yw` | Copy word |
| `p` | Paste after cursor |
| `P` | Paste before cursor |

### Undo & Redo

| Command | Action |
|---------|--------|
| `u` | Undo |
| `Ctrl+r` | Redo |

### Inserting Text

| Command | Action |
|---------|--------|
| `i` | Insert before cursor |
| `a` | Append after cursor |
| `I` | Insert at line start |
| `A` | Append at line end |
| `o` | Open new line below |
| `O` | Open new line above |

---

## Search & Replace

### Search Commands

```vim
# Search forward
/pattern

# Search backward
?pattern

# Find next occurrence
n

# Find previous occurrence
N
```

### Replace Commands

```vim
# Replace first occurrence on current line
:s/old/new

# Replace all occurrences on current line
:s/old/new/g

# Replace in entire file
:%s/old/new/g

# Replace with confirmation
:%s/old/new/gc
```

---

## Visual Mode Editing

### Selecting Text

| Command | Action |
|---------|--------|
| `v` | Start character selection |
| `V` | Start line selection |
| `Ctrl+v` | Start block selection |

### Common Operations on Selection

```vim
# Delete selected text
d

# Copy selected text
y

# Replace selected text
c

# Make selection uppercase
U

# Make selection lowercase
u
```

---

## Advanced Features

### Working with Multiple Files

```vim
# Split screen vertically
:vsp filename

# Split screen horizontally
:sp filename

# Switch between splits
Ctrl+w Ctrl+w

# Switch to next/previous file
:bn (next)
:bp (previous)
```

### Marks & Bookmarks

```vim
# Set mark at current position
ma (creates mark 'a')

# Jump to mark
'a (single quote goes to line)
`a (backtick goes to exact position)

# List all marks
:marks
```

### Using Tabs

```vim
# Create new tab
:tabnew

# Go to next tab
gt

# Go to previous tab
gT

# Close tab
:tabclose
```

---

## Useful Configuration

Vim settings can be configured in `~/.vimrc` file. Here are common settings:

```vim
" Enable syntax highlighting
syntax on

" Show line numbers
set number

" Enable mouse support
set mouse=a

" Set tab width to 4 spaces
set tabstop=4
set shiftwidth=4

" Enable auto-indentation
set autoindent

" Highlight search results
set hlsearch

" Show matching parentheses
set showmatch
```

---

## Quick Reference Cheat Sheet

### Entering Insert Mode
