## Text Editing with `nano`

`nano` is a simple, modeless, and intuitive command-line text editor. It's often recommended for beginners because its commands are always visible at the bottom of the screen.

To open an existing file or create a new one, type:
`nano <filename>`


Once inside `nano`, you'll see a two-line menu at the bottom. The `^` character represents the `Ctrl` key, and `M-` represents the `Alt` key. For example, `^X Exit` means you press `Ctrl + X` to exit the program.

| Action | Shortcut | Menu Notation | Description |
| :--- | :--- | :--- | :--- |
| **Exit `nano`** | `Ctrl + X` | `^X Exit` | If the file is modified, it will ask if you want to save. Press `Y` for Yes, `N` for No. |
| **Save the file** | `Ctrl + O` | `^O Write Out` | Writes the current file to disk. You'll be asked to confirm the filename, then press `Enter`. |
| **Get Help** | `Ctrl + G` | `^G Get Help` | Displays a full list of commands. Use `Ctrl + X` to exit the help screen. |
| **Cut (line)** | `Ctrl + K` | `^K Cut Text` | Cuts the entire current line and places it in the buffer. Use multiple times to cut multiple lines. |
| **Paste (uncut)** | `Ctrl + U` | `^U Uncut Text` | Pastes the contents of the buffer at the current cursor position. |
| **Search** | `Ctrl + W` | `^W Where Is` | Prompts you to enter a search term. |
| **Find Next** | `Alt + W` | `M-W Where Is Next` | After a search, finds the next occurrence of the term. |
| **Go to Line** | `Ctrl + _` | `^_ Go To Line` | Prompts for a line number (and column number) to jump to. You can also use `Alt + G`. |
| **Justify Text** | `Ctrl + J` | `^J Justify` | Wraps the current paragraph to fit the screen width. |
| **Mark Text** | `Alt + A` | `M-A Mark` | Sets a starting mark for selecting text. Move the cursor to select, then use `Ctrl+K` to cut or `Alt+^` to copy. |

---