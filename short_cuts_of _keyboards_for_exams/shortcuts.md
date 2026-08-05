Essential keyboard shortcuts, terminal hotkeys, Vim maneuvers, and shell configurations for Linux Foundation Kubernetes exams (CKA, CKAD, CKS):

## Exam Environment & Remote Desktop

* **`Ctrl + Alt + W`**: Close tab inside the exam browser. (DO NOT use `Ctrl + W` — it closes the entire Chrome browser tab hosting your exam).
* **`Ctrl + Shift + C` / `Ctrl + Shift + V**`: Copy and paste inside the remote Linux terminal.
* **`Ctrl + C` / `Ctrl + V**`: Copy and paste in non-terminal GUI apps on the remote desktop.
* **`Ctrl + F`**: Search text inside the embedded Firefox browser / official docs.

---

## Bash / Terminal Navigation

* **`Ctrl + R`**: Search reverse command history.
* **`Ctrl + A`**: Jump cursor to the beginning of the line.
* **`Ctrl + E`**: Jump cursor to the end of the line.
* **`Ctrl + U`**: Clear the line before the cursor position.
* **`Ctrl + K`**: Clear the line after the cursor position.
* **`Ctrl + L`**: Clear the terminal screen (same as typing `clear`).
* **`Tab` / `Tab Tab**`: Trigger autocompletion for `kubectl` commands and resources.

---

## Essential Vim Shortcuts (Editing YAML Files)

### Mode Switching

* **`i`**: Enter Insert mode (Note: `Insert` key is disabled during the exam).
* **`Esc`**: Exit to Command mode.
* **`:`**: Enter Command-Line mode.

### Navigation & Searching

* **`gg`**: Jump to the first line of the file.
* **`G`**: Jump to the last line of the file.
* **`:<number>`**: Jump to a specific line number (e.g., `:42`).
* **`/pattern`**: Search forward for `pattern` (press `n` for next match, `N` for previous match).

### Editing & Indentation

* **`u`**: Undo last change.
* **`Ctrl + R`**: Redo last undone change.
* **`dd`**: Delete (cut) current line.
* **`yy`**: Copy (yank) current line.
* **`p`**: Paste below current line.
* **`>>`**: Indent line right (by 2 spaces if `.vimrc` is configured).
* **`<<`**: Indent line left.
* **`Shift + V`** (Visual Line mode) $\rightarrow$ select lines $\rightarrow$ press **`>`** or **`<`** to shift multiple lines of YAML.

---

## Must-Have Setup Snippets for the Exam

Add these directly to your shell environment and Vim config at the start of the exam to save time:

### 1. Bash Setup (`~/.bashrc`)

```bash
# Enable kubectl autocompletion with 'k' alias
alias k=kubectl
complete -o default -F __start_kubectl k

# Shortened dry-run variables
export do="--dry-run=client -o yaml"
export now="--force --grace-period=0"

```

### 2. Vim Setup (`~/.vimrc`)

Ensure 2-space tab indenting so YAML structure does not break:

```vim
set ts=2
set sw=2
set et
set nu

```
----------------------------------------------------------------------------------
Q:from where i can practice vi shrtcuts 

Here are the best interactive platforms and terminal tools to build Vim keyboard shortcut muscle memory:

## 1. Interactive Web Games (Fun & Visual)

* **[Vim Genius](https://vimgenius.com/)**: Fast-paced flashcard-style exercises designed explicitly to teach keys step-by-step (movement, insertion, deletion, copying).
* **[Vim Adventures](https://vimadventures.com/)**: A Zelda-like browser puzzle game where your character moves and interacts with the world solely through Vim keys (`h`, `j`, `k`, `l`, `w`, `b`, etc.).
* **[Vim Golf](https://www.vimgolf.com/)**: Real editor challenges where you attempt to transform sample text into a target format using the fewest keystrokes possible.

---

## 2. Built-in Terminal Practice (No Setup Required)

If you have a Linux terminal or macOS:

* **`vimtutor`**: Open your terminal and type `vimtutor`. It launches an interactive 30-minute hands-on tutorial directly inside Vim that covers all essential navigation, editing, registers, and search commands.

---

## 3. Dedicated Browser Trainers

* **[OpenVim](https://www.openvim.com/)**: An interactive, step-by-step online sandbox tutorial that walks you through basic to advanced commands with real-time feedback.
* **[Vim Cheat Sheet (Interactive)](https://vim.rtorr.com/)**: A quick reference page paired with visual shortcut layout maps to test yourself while practicing.

---

## Targeted Focus for Kubernetes Exams (YAML Practice)

For the **CKA/CKAD/CKS**, limit your focus to these specific commands inside your terminal:

1. Create a dummy file: `vim test.yaml`
2. Practice shifting blocks of YAML lines left and right:
* Select block: `Shift + V` + Arrow keys / `j`/`k`
* Shift right: `>`
* Shift left: `<`


3. Practice mass line deletions (`dd`), line jumps (`:20`), and fast searches (`/metadata`).

------------------------------------------------------------------------------------
Q: Give me most usable vim commands 
A:The Linux Foundation remote desktop environment provides `vim` natively. For the **CKAD exam**, you should focus specifically on commands that speed up **YAML navigation, block indentations, and bulk edits**.

---

## 1. Initial 10-Second `~/.vimrc` Setup

Run `vim ~/.vimrc` immediately at the start of the exam and add these lines to prevent auto-indentation bugs when pasting YAML:

```vim
set ts=2
set sw=2
set et
set nu

```

* **`set ts=2 sw=2`**: Sets tab and indent spacing to 2 spaces (Kubernetes standard YAML format).
* **`set et`**: Converts tabs into spaces so YAML parser won't throw errors.
* **`set nu`**: Shows line numbers so you can quickly jump to error lines reported by `kubectl`.

---

## 2. Multi-Line Indentation (YAML Saver)

When copying/pasting blocks of specs into existing files, formatting often breaks.

* **`Shift + V`**: Enter Visual Line mode.
* **`j` / `k**` (or Up/Down arrows): Highlight multiple YAML lines.
* **`>`**: Shift highlighted block right by 2 spaces.
* **`<`**: Shift highlighted block left by 2 spaces.
* **`.`**: Repeat the last indent command again immediately.

---

## 3. Fast Editing & Deletions

* **`dd`**: Delete (cut) current line.
* **`<number>dd`**: Delete `<number>` lines from cursor (e.g., `5dd` deletes 5 lines).
* **`cc`**: Delete the entire line and drop straight into **Insert mode** (great for replacing single field values).
* **`dw`**: Delete from cursor to the end of the current word.
* **`u`**: Undo last change.
* **`Ctrl + r`**: Redo last undone change.

---

## 4. Navigation Shortcuts

* **`G`**: Jump directly to the **last line** of the file.
* **`gg`**: Jump directly to the **first line** of the file.
* **`:<number>`**: Jump directly to line number (e.g., `:24` moves cursor to line 24).
* **`$`**: Jump to the end of the current line.
* **`0`**: Jump to the beginning of the current line.

---

## 5. Copying & Pasting inside Vim

* **`yy`**: Copy (yank) current line.
* **`<number>yy`**: Copy `<number>` lines starting from current line (e.g., `10yy` copies 10 lines).
* **`p`**: Paste copied/cut text **below** the cursor line.
* **`P`**: Paste copied/cut text **above** the cursor line.

---

## 6. Searching & Replacing

* **`/pattern`**: Search forward for `pattern` (e.g., `/containers` or `/image`).
* Press **`n`** to go to the next match.
* Press **`N`** to go to the previous match.


* **`:%s/old/new/g`**: Global find and replace across the entire file (useful when modifying full manifest specs, e.g., changing namespaces or ports).

---

## 7. Saving & Exiting

* **`:w`**: Save changes without quitting.
* **`:wq`** or **`ZZ`**: Save changes and quit.
* **`:q!`**: Force quit without saving changes (use if you accidentally corrupted the YAML beyond repair).