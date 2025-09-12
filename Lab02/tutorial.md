### **Part 0: Lab Setup**

#### **1. Get a Unix System**

*   **On Windows:**
    We suggest using Windows Subsystem for Linux (WSL). Windows provides a virtualized Linux distribution on its app store. Go there and download Ubuntu. Then start it from the start menu. For official tutorial please check [Microsoft WSL](https://learn.microsoft.com/en-us/windows/wsl/install).
    
    If you cannot find a WSL on your windows laptop, please try using **Git Bash**. You should already have Git installed, and "Git Bash" came with it. Search for it in your Start Menu. It provides a Unix-like environment that is consistent with macOS and Linux, making your life much easier. The commands for macOS/Linux in this tutorial will work directly in Git Bash.

*   **On macOS:**
    Open the **Terminal** app.

*   **On Linux (Ubuntu/Debian):**
    Open the **Terminal** application. You can usually find it in your applications list or by using the keyboard shortcut `Ctrl+Alt+T`.

#### **2. The Prompt**
The prompt is where you type commands. It usually looks something like this: `user@hostname:~$`.
*   `user`: Your username.
*   `hostname`: The name of the computer.
*   `~`: The tilde is a shortcut for your home directory.
*   `$`: Indicates that the shell is ready for input from a regular user.

#### **3. Getting help**

Most commands have a built-in help function and a manual. If stuck at a step, try to look for help:
* Appending `--help` or `-h` at the end of a command is likely to trigger a simple help display
* Some commands like `git` have hierarchical help, try with each sub command
* If you need more details, use the command `man <command>`, e.g., `man ls` to get the full manual of the `ls` command. Navigate using arrows and quit using q.
* You may use AI to help you with this lab, but it is **NOT** recommended. AI is quite bad at finding the cause of errors when dealing with command lines and configuration files.

---

### **Part 1: Navigating across File System**

**Concept:** Your computer's files are organized in a tree structure of folders (called "directories") and files. You are always located in a "current working directory."

First, let's download the files we'll need for this lab. Type the following commands and press Enter.

```bash
# This command downloads a log file and saves it as webserver.log
curl https://raw.githubusercontent.com/logpai/loghub/master/Apache/Apache_2k.log -o webserver.log

# This command downloads the text of a book and saves it as dracula.txt
curl https://www.gutenberg.org/files/345/345-0.txt -o dracula.txt
```

| Task | Unix (macOS, Linux, Git Bash) | Explanation |
| :--- | :--- | :--- |
| **Where am I?** | `pwd` | **P**rint **W**orking **D**irectory. Shows your current folder's full path. |
| **Anything here?** | `ls -l` | `dir` | **L**i**s**ts the contents of the current directory. `-l` gives a detailed "long" view. |
| **Change directory** | `cd my_folder` | **C**hange **D**irectory. Moves you into `my_folder`. |
| **Go up one level** | `cd ..` | `..` is a special shortcut for the parent directory. |
| **Make a directory** | `mkdir my_project` | **M**a**k**e **Dir**ectory. Creates a new folder. |
| **Create an empty file** | `touch new_file.txt` | `touch` updates a file's timestamp or creates it if it doesn't exist. |
| **Copy a file** | `cp source.txt dest.txt` |  **C**o**p**ies a file. |
| **Move/Rename a file** | `mv old.txt new.txt` |  **M**o**v**es or renames a file. To move, make the destination a directory path. |
| **Delete a file** | `rm file.txt` |  **R**e**m**oves a file. **BE CAREFUL! This is permanent. There is no Trash Can.** |
| **Delete a directory** | `rm -r my_folder` | **R**emoves a directory and everything in it (`-r` for recursive, `/s` for subdirectories). |

#### **Part 1 Exercises:**

1.  **Create a Workspace:**
    *   Create a directory for this lab: `mkdir cli_lab`
    *   Navigate into it: `cd cli_lab`
    *   Create two subdirectories: `data` and `notes`.

2.  **Organize Files:**
    *   Move the `webserver.log` and `dracula.txt` files you downloaded into the `data` directory. (Hint: `mv ../webserver.log ./data/`)
    *   Navigate into the `notes` directory.
    *   Create a file called `my_ideas.txt`.
    *   Make a backup copy of it called `my_ideas_backup.txt`.
    *   List the files to confirm your work.
    *   Delete the backup file.

---

### **Part 2: Viewing and Searching Files**

**Concept:** You don't need to open a graphical editor just to see what's inside a file or to find a specific piece of text.

| Task | Unix (macOS, Linux, Git Bash) | Explanation |
| :--- | :--- | :--- |
| **View a short file**| `cat dracula.txt` |  Con**cat**enates and prints the entire file to the screen. Good for short files. |
| **View a long file**| `less dracula.txt` |  Shows the file one screen at a time. Use arrow keys to scroll, press `q` to quit. |
| **Search for text** | `grep "notice" webserver.log` |  Searches for lines containing the specified text pattern. `grep` stands for **G**lobal **R**egular **E**xpression **P**rint. |
| **Case-insensitive search** | `grep -i "dracula" dracula.txt` | The `-i` or `/i` flag tells the command to ignore whether the letters are upper or lower case. |
| **Shows the full path of a command.** | `which python` | It searches through the directories listed in the system's `PATH` environment variable. |
| **Locates the binary, source, and manual page for a command.** | `whereis python` |  add `-b` option to search only binaries; `-s` for source files etc. Please help check [tutorial](https://www.geeksforgeeks.org/linux-unix/whereis-command-in-linux-with-examples/) for more operators.|
| **Powerful search for files/directories based on criteria.** | `find . -name "*.txt"` | `find` searches the directory tree rooted at each given starting-point by evaluating the given expression from left to right.|

#### **Part 2 Exercises:**

1.  **`cat` vs. `less`:**
    *   Navigate to your `cli_lab/data` directory.
    *   Try to view `dracula.txt` using `cat` (or `type`). Notice how it floods your screen.
    *   Now, view it using `less` (or `more < dracula.txt`). Much better! You can scroll and quit when you're done.

2.  **Find the Clues:**
    *   Use `grep` (or `findstr`) to find every line containing the word "error" in `webserver.log`.
    *   Do a case-insensitive search for the word "vampire" in `dracula.txt`.

---

### **Part 3: The Command-Line**

**Concept:** So far, command output has gone to your screen (Standard Output). Now, we'll learn to redirect that output to files or even to other commands.

| Operator | Name | Unix Example | Explanation |
| :--- | :--- | :--- | :--- |
| `>` | **Redirect (Overwrite)** | `ls -l > file_list.txt`  | Takes the output and writes it to a file. **Overwrites the file if it already exists.** |
| `>>` | **Redirect (Append)** | `date >> activity.log` | Takes the output and **adds it to the end** of a file. Creates the file if it doesn't exist. |
| `\|` | **Pipe** | `grep "error" webserver.log \| wc -l` | Takes the output of the command on the left and uses it as the **input** for the command on the right. This lets you "chain" commands together. |

#### **Part 3 Exercises:**

1.  **Save Your Findings (`>`):**
    *   In your `cli_lab/data` directory, find all lines in `webserver.log` containing "error state 6".
    *   Instead of printing them to the screen, save them to a new file called `error_state_6.log`.
    *   *Command:* `grep "error state 6" webserver.log > error_state_6.log`

2.  **Create a Report (`>>`):**
    *   First, create a report file with a title: `echo "My Awesome Report" > report.txt`
    *   Now, append a separator and the current date to the report.
    *   *Unix:* `echo "---" >> report.txt; date >> report.txt`

3.  **The Ultimate Combo (`|`):**
    *   This is the key skill. We want to find all the "scoreboard" operations in `webserver.log` and then *count* how many there are, without creating an intermediate file.
    *   **Unix (macOS, Linux, Git Bash):**
        ```bash
        grep "scoreboard" webserver.log | wc -l
        ```
    *   This single line performs a complex task: it finds the relevant lines, then "pipes" that list to a second tool that counts them. This is the essence of the command line's power.

---

### **Part 4: Introduction to Shell Scripting**

**Concept:** A **shell script** is like writing down a complete command list. It's a simple text file containing a sequence of commands that the shell executes in order.

**Why use scripts?**
*   **Automation:** Run a complex sequence of commands with a single instruction.
*   **Consistency:** Ensure the same steps are performed exactly the same way every time.
*   **Reusability:** Save your "recipe" to use again later or share with others.

#### **Creating a Simple Script**

1.  **Create the file:**
    *   **For macOS/Linux/Git Bash users:** `touch hello.sh`

2.  **Edit the file:** Open the file in a simple text editor (like VS Code, etc.) and add the following content.

    *   **For `hello.sh` (Unix/Git Bash):**
        ```bash
        #!/bin/bash
        # This is a comment. The line above is a "shebang", telling the OS to use bash.
        
        echo "Hello from my first script!"
        echo "The current working directory is:"
        pwd
        echo "Script finished."
        ```

3.  **Run the script:** Save the file and run it from your terminal.

    *   **Unix/Git Bash:** `bash hello.sh`

You just automated a sequence of three commands! This is the foundation for the final assignment.

---

### **Assignment: The Log File Analysis**

**Objective:** Apply your new command-line skills to investigate a mock server log, generate a report, and submit your work. This assignment will test your ability to combine commands to produce a useful result.

#### **Tasks:**

1.  **Setup Your Workspace:**
    *   In your home directory (`~`), create a new directory called `ISDN3000C_Lab02`.
    *   Inside `ISDN3000C_Lab02`, create two more directories: `raw_data` and `final_report`.

2.  **Get the Data:**
    *   Write a shell script to copy&paste [assignment.log](Lab02/assignment.log) to your `raw_data` path, and rename it as `game.log`.

3.  **Create an Analysis Script:**
    Your main task is to create a script that automates the analysis. A script is just a text file with a list of commands.

    *   Create a file named `reporter.sh` inside your `ISDN3000C_Lab02` directory.

    Add commands to your script to perform the following actions **in order**:

    a.  **Generate a Report Header:** The script should create a new file at `final_report/analysis_report.md`. The first line of this file must be the title `# Server Analysis Report`, and the second line must be the current date. (Use `>` for the first line and `>>` for the second).

    b.  **Extract Connection Logs:** Find all lines in `game.log` that contain the word "INFO" and save these lines to a new file named `final_report/player_connections.log`.

    c.  **Count Critical Errors:** Count the total number of lines in `game.log` that contain the word "WARN" and "CRITICAL" respectively. Append a new section to `analysis_report.md` that looks like this (the `XX` should be the number you found):

        ```
        ## Summary

        - Total Warning: XX
        - Total Critical Errors: XX
        ```
        
        *(Hint: You will need to use `echo` with `>>` for the text and a pipe (`|`) to count the lines and append that number with `>>`)*.

    d.  **Count Player Logins:** Count the total number of lines that contain "LOGIN SUCCESS". Append this count to the summary in `analysis_report.md` so it looks like this:
        ```
        - Total Player Logins: YY
        ```

4.  **Run Your Script and Verify:**
    *   Navigate to your `ISDN3000C_Lab02` directory in the terminal.
    *   Execute your script:
        *   On macOS/Linux/Git Bash: `bash reporter.sh`
    *   Check the `final_report` directory. You should see `analysis_report.md` and `player_connections.log` with the correct content.

#### **Submission:**

1.  Ensure your `ISDN3000C_Lab02` directory contains your script (`reporter.sh` or `reporter.bat`) and the `final_report` directory with the generated files.
2.  You do **not** need to commit the `raw_data` directory.
3.  In `ISDN3000C_Lab02` directory, the final structure for submission should be:

    ```
    ISDN3000C_Lab0/
    ├── final_report/
    │   ├── analysis_report.md
    │   └── player_connections.log
    └── reporter.sh
    ```
4. Submit Github Repository:
   1. Option A: create a new Github Repository called "ISDN3000C_lab02", and push your work onto it.
   2. Option B: reuse "ISDN3000C_lab01" Github repository. Please push your work to a new folder, and rename the folder as "lab02". 
5. For both of the two options, please remember to submit your repo link to Canvas. The repo should be set to **public** visibility unless you have already invited TA as your collaborator in the first lab. **New private Github collaborator invitations after the submission date of `Lab01` will not be accepted**.
6. Please remember to update "readme.md" and "requirements.txt" if necessary.
