## **Part 0: The Foundation - Python Environment Management with virtualenv**

Before writing any code, we must set up a clean and reproducible workspace. We do this using an **environment manager**. 

### **Visual Studio Code**

Visual Studio Code (VS Code) is a free, lightweight, and powerful source code editor developed by Microsoft. It runs on Windows, macOS, and Linux. It supports basically all programming language, and multiple add-ons to help you coding. [Download](https://code.visualstudio.com) it first and install necessary extensions (Python, C++, and Markdown). To download a new extension, you need to follow the steps below:

1. Open Visual Studio Code, in the left sidebar you will see **Extenssion** bar item.
2. In **Extenssion**, search extentions named "Python", "C/C++ Themes", "Markdown All in One", and install them.

### **Python Package Download Sources**

The safest and most reliable place to get Python is from the official website: [python.org](https://www.python.org/downloads/) or pick one from its [ftp index](https://www.python.org/ftp/python/). The website will automatically detect your operating system and suggest the best installer for you. But it's also up to you to choose any version based on your own requirements. For AIML projects recently, origin/stylevpeople use more often Python 3.10 or Python 3.11.

Taking Python 3.11.11 as an example, we download the XZ compressed source tarball for Python 3.11.11 from [python.org](https://www.python.org/ftp/python/3.11.11/Python-3.11.11.tar.xz). We will use this version for the installation of Python 3.11 on Ubuntu and MacOS in the following steps. For windows, we strongly recommend to use an installer. 

### **Install Python and Build an Virtual Environment**
For different operation systems, the installation steps vary. Please find the correct installation steps based on your own operation system. For virtual environment, it lets us install specific versions of Python and other libraries for each project.

#### **1. On Windows**

**Attention❗️** Compiling Python from source on Windows is highly complex and not recommended for general use. It requires installing Visual Studio and the C++ build toolchain, and the process is significantly different from Unix-based systems. The standard and fully supported method is to choose one version with official precompiled installers from [python.org](https://www.python.org/downloads/release/python-3119/). If you are interested in compiling a specific python library from scratch, please check tutorial_compilation.md.

<ins>Step 1: Download and Install Python</ins>

1. Go to [python.org](https://www.python.org/downloads/release/python-3119/) and download the Windows executable installer for Python 3.1.
2. Run the installer. During setup:
   1. Check "Add Python to PATH".
   2. Select "Install for all users" if desired.
   3. Proceed with default options.

3. Verify installation by opening Command Prompt or PowerShell and running:

```powershell
python --version
```

<ins>Step 2: Create a Virtual Environment with the New Python</ins>

```powershell
# Navigate to your project directory
cd C:\Users\YourUser\myproject

# Create a virtual environment
python -m venv venv

# Activate the environment
.\venv\Scripts\activate

# Verify the version
python --version

# After you complete the tasks, stop (deactivate) the environment
deactivate
```

#### **2. On macOS**

**Attention❗️** Compiling Python from source on MacOS is not always the first choice for developers. The standard and fully supported method is to choose one version with official precompiled installers from [python.org](https://www.python.org/downloads/release/python-3119/), or directly install a specific version of python with Homebrew. If you are interested in compiling a specific python library from scratch, please check tutorial_compilation.md.

The process on macOS is similar to Ubuntu but uses Homebrew to install dependencies. You'll need Xcode's Command Line Tools first.

<ins>Step 1: Install Build Dependencies</ins>

First, install the [Xcode Command Line Tools](https://developer.apple.com/xcode/resources/) (Not the full Xcode App❗️) if you haven't already. Secondly, install [Homebrew](https://brew.sh) if you haven't. Then, use Homebrew to get necessary libraries.

```bash
# Install Xcode Command Line Tools
xcode-select --install

# Install homebrew
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install dependencies using Homebrew
brew install openssl readline sqlite3 xz zlib

# Install Python 3.11
brew install python@3.11
```

<ins>Step 2: Create a Virtual Environment with the New Python</ins>

Use the absolute path to your new `python3.11` executable.

```bash
# Go to your project directory
cd ~/myproject

# Create a virtual environment
$(brew --prefix python@3.11)/bin/python3.11 -m venv venv

# Activate the environment
source venv/bin/activate

# Verify the version
python --version
# Expected output: Python 3.11.x (or similar)

# After you complete the tasks, stop (deactivate) the environment
deactivate
```

#### **3. On Ubuntu 24.04 LTS**:

**Attention❗️** Compiling Python from source on Linux is not always the first choice for developers. The standard and fully supported method is to directly install a specific version of python with APT, the command-line tool for managing deb packages. If you are interested in compiling a specific python library from scratch, please check tutorial_compilation.md.

<ins>Step 1: Install Python</ins>

Use the system's package manager to install Python 3.11.

```bash
# Update package lists
sudo apt-get update

# Install Python 3.11
sudo apt install -y python3.11 python3.11-venv
```

<ins>Step 2: Create a Virtual Environment with the New Python</ins>

You can now use the full path to your newly installed Python executable to create a virtual environment.

```bash
# Go to your project directory
cd ~/myproject

# Create a virtual environment using the specific Python binary
python3.11 -m venv venv

# Activate the new environment
source venv/bin/activate

# Verify the Python version inside the environment
python --version
# Expected output: Python 3.11.11

# After you complete the tasks, stop (deactivate) the environment
deactivate
```

### **Installing Python Packages with pip**

Once your virtual environment is activated, you can install Python packages using `pip`, Python's package installer. This ensures dependencies are isolated to your project. Always install packages inside an activated venv to avoid polluting your system Python.

#### **Basic Installation**
To install a single library (e.g., requests for making HTTP requests):
```bash
# Install the library
pip install requests
```

This downloads and installs the latest version of the library from PyPI (Python Package Index).

#### **Installing with a Specific Version**
If your project requires a specific version (e.g., for compatibility), specify it with ==. For example, to install version 2.31.0 of requests:

```bash
pip install requests==2.31.0
```

You can find version numbers on the library's [PyPI page](pypi.org/project/requests) or in project documentation.

#### **Using a requirements.txt File**
For reproducible environments, use a requirements.txt file to list all dependencies. This is especially useful for sharing projects via Git.

**1. Generate a requirements.txt File**

After installing packages, "freeze" your current setup:

```bash
pip freeze > requirements.txt
```

This creates a file like:

```text
textrequests==2.31.0
numpy==1.26.4
# ... other packages
```

You can edit this file manually to add/remove packages or change versions.

**2. Install from requirements.txt**

In a new venv or on another machine, run the following command:

```bash
pip install -r requirements.txt
```

This installs all listed packages with the specified versions.

----

## **Part 1: Git and GitHub**

**Git** is a distributed version control system (VCS) installed on your local machine. It's a tool that tracks changes in your code, allowing you to revisit previous versions and collaborate with others. **GitHub** is a web-based hosting service for Git repositories. It allows you to store your Git projects (repositories) online, making it easier to share and collaborate with other developers.

### **Installing Git**

Before you can use Git, you need to install it. The process varies slightly depending on your operating system.

#### **1. Windows 10 / 11**
For Windows, we strongly recommend to use the official precompiled installer [Git for Windows installer](https://git-scm.com/downloads/win).

1.  **Download the Installer:** Go to the official Git downloads page at **https://git-scm.com/downloads**.

2.  **Run the Installer:** Once downloaded, run the `.exe` file. The installation process has many steps, but for most users, the default settings are perfectly fine.

3.  **Verify Installation:** After the installation is complete, open the Command Prompt or PowerShell and type:
    ```bash
    git --version
    ```
    If you see a version number, Git is successfully installed. The installation also includes **Git Bash**, a terminal that provides a Unix-like environment, which is excellent for running Git commands.**

#### **2. On MacOS**
The easiest way to install Git on macOS is using Homebrew, a package manager for macOS.

1.  **Install Git:** Once Homebrew is installed, run this command in your terminal:
    ```bash
    brew install git
    ```
    Homebrew will download and install the latest version of Git.

2.  **Verify Installation:** To confirm that Git was installed correctly, type:
    ```bash
    git --version
    ```
    This should display the installed Git version, for example, `git version 2.51.0`. 

#### **3. On Ubuntu 24.04**
On Ubuntu, you can install Git using the Advanced Package Tool (APT).

1.  **Update Your Package List:** Open a terminal and run the following command to make sure you have the latest package information:
    ```bash
    sudo apt update
    ```

2.  **Install Git:** Now, install Git with this command:
    ```bash
    sudo apt install git
    ```
    The system will ask for your password and then show you the packages to be installed. Press `Y` to continue.

3.  **Verify Installation:** Check that the installation was successful:
    ```bash
    git --version
    ```
    This should display the installed Git version, for example, `git version 2.51.0`. 

---

### **First-Time Git Configuration**

After installing Git, you must configure it with your name and email address. This information will be attached to every commit you make.

Open your terminal (or Git Bash on Windows) and run the following commands, replacing the example details with your own:

```bash
# Set your username for all repositories on your system
git config --global user.name "Your Name"

# Set your email address for all repositories on your system
git config --global user.email "your.email@example.com"
```
It's not necessary to use the same email address that you use for your GitHub account, but we strongly recommend to use the same email.

---

### **Authenticating with GitHub using a Personal Access Token (PAT)**

GitHub no longer accepts password authentication for command-line Git operations. Instead, you must use a **Personal Access Token (PAT)**. A PAT is a secure string of characters that acts as a password for a specific scope of permissions.

#### **Generating a Personal Access Token on GitHub**

1.  **Log in to GitHub** and navigate to your **Settings**.
2.  Scroll down and find **Developer Settings** on the left sidebar.
3.  Click on **Personal access tokens**, then select **Tokens (classic)**. 
4.  Click **Generate new token** and select **Generate new token (classic)**. 
5.  **Give your token a descriptive name** (e.g., "My Laptop CLI").
6.  **Set an expiration date.** For security, it's recommended to set an expiration.
7.  **Select scopes.** The scopes define what permissions the token has. For cloning, pulling, and pushing to repositories, the **`repo`** scope is sufficient.
8.  Scroll down and click **Generate token**.

**Attention❗️** Copy your new token immediately. You will not be able to see it again after you leave the page. Treat this token like a password and never share it publicly. 

#### **Using the PAT with Git Credential Manager**

The great news is that the Git installers for all three operating systems come with credential managers that securely store your PAT after you use it once.

*   **Windows:** Git for Windows includes Git Credential Manager.
*   **macOS:** Git uses the `osxkeychain` helper, which is integrated with the macOS Keychain.
*   **Linux:** Git can use `libsecret`, which integrates with services like GNOME Keyring.

The first time you perform an operation that requires authentication (like `git clone` or `git push`) on a private repository using an HTTPS URL, Git will prompt you for your username and password:
*   **Username:** Enter your GitHub username.
*   **Password:** Paste your newly generated Personal Access Token.

After this first time, the credential manager will securely store the token, and you won't be prompted again for future operations on that repository.

---

### **The Basic Git Workflow**

#### **Creating a New Repository**

The most common way to start working on a project is to create a git repository on Github, and clone it.

1.  Go to the repository's page on GitHub and click the "Code" button. Make sure you select the **HTTPS** tab and copy the URL.

2.  In your terminal, run the `git clone` command with the URL:
    ```bash
    # For a public repository
    git clone https://github.com/user/ISDN3000C_Lab1.git

    # For a private repository, you will be prompted for credentials
    git clone https://github.com/user/ISDN3000C_Lab1.git
    ```
    When prompted for the private repo, enter your GitHub username and use your PAT as the password.

#### **The Three Basic States: Staging and Committing**

1.  **Working Directory:** Your actual project folder.
2.  **Staging Area (Index):** A snapshot of files you want to include in your next commit.
3.  **Repository (`.git` dir):** Where Git permanently stores your committed snapshots.

**1. Check the Status (`git status`)**
This is your most-used command. It shows the state of the working directory and the staging area.
```bash
cd ~/ISDN3000C_Lab1
git status
```
It will tell you which files are modified, which are staged, and which are untracked.

**2. Add Files to the Staging Area (`git add`)**
Before you commit changes, you must add them to the staging area.

```bash
# Add a specific file
git add README.md

# Add all modified and new files in the current directory
git add .
```

**3. Commit Changes (`git commit`)**
A commit is a permanent snapshot of your staged changes. Each commit has a unique ID and a descriptive message.

```bash
git commit -m "Add initial project files and README"
```
The `-m` flag allows you to write a commit message directly. Good commit messages are crucial for understanding the project's history.

**4. View Commit History (`git log`)**
To see a history of all the commits you've made, use `git log`
```bash
git log
```
This will show the commit ID, author, date, and commit message for each commit.

### **Working with Remote Repositories**

Now next step is to interact with the remote repository on GitHub.

#### **Pushing Changes (`git push`)**
To upload your local commits to the remote repository on GitHub, you use `git push`.

If you cloned the repository, the remote is already configured under the name `origin`.

```bash
# The -u flag sets the upstream branch for the first push
git push -u origin main
```
After the first push with `-u`, you can simply use `git push` for subsequent pushes from that branch.

#### **Pulling Changes (`git pull`)**
If other people are working on the same project, you'll need to pull their changes from the remote repository to your local machine. `git pull` fetches the changes and merges them into your current branch.

```bash
git pull origin main
```

### **Branching and Merging**

Branches are a core concept in Git. They allow you to work on new features or bug fixes in an isolated environment without affecting the main codebase (often the `main` or `master` branch).

#### **Creating and Switching Branches**

1.  **Create a new branch:** 
    ```bash
    # Create a new branch named 'new-feature'
    git branch new-feature
    ```

2.  **Switch to the new branch:** 
    ```bash
    git checkout new-feature
    ```

    You can combine both steps into one command:
    ```bash
    # Create and switch to the 'new-feature' branch
    git checkout -b new-feature
    ```

Now, any commits you make will be on the `new-feature` branch, leaving the `main` branch untouched.

#### **Merging Branches (`git merge`)**

Once you are happy with the work on your feature branch, you can merge it back into the `main` branch. 

1.  First, switch back to the branch you want to merge into (the receiving branch):
    ```bash
    git checkout main
    ```

2.  Make sure your `main` branch is up-to-date:
    ```bash
    git pull origin main
    ```

3.  Finally, merge your feature branch into `main`: 
    ```bash
    git merge new-feature
    ```
    This command integrates the changes from `new-feature` into `main`.

4.  Push the newly merged `main` branch to GitHub:
    ```bash
    git push origin main
    ```

### **Task I: MarkdownPress**

1. In the Github repository "ISDN3000C_Lab1" you just created, build a virtual environment of Python 3.11.
2. Add Git Ignore file under folder "ISDN3000C_Lab1":
    ```bash
    # On MacOS/Ubuntu
    touch .gitignore

    # On Windows
    echo. > .gitignore
    ```

    Open your **.gitignore** file in a text editor and add the name of the virtual environment folder, followed by a slash to indicate it is a directory:

    ```
    # Ignore Python virtual environment
    venv/
    ```

    You may have used different names for their virtual environments, such as `.venv` or `env`. No matter what the name is, just add them to the file:
    ```
    # Ignore Python virtual environments
    venv/
    .venv/
    env/
    ```
3. clone and do the tasks in tutorial_task1.md.
4. Remember to add **requirements.txt** to let people know the libraries you installed to support the code.
3. Remember to add instructions on how to use your code in a **readme.md** file, to let people know how to use the code.
5. After you finish the tasks, commit your code and push the changes to Github.

## **Part 2: Vibe Coding**

Vibe coding is a software development practice where your role shifts from a manual coder to a tutor. You focus on the high-level goal, the "vibe" of what you want to create, and guide an AI through a conversational process to generate, refine, and debug the application. The workflow is an iterative loop:

1.  Describe the Goal: You start with a high-level prompt in natural language.
2.  AI Generates Code: The AI interprets your request and produces the initial code.
3.  Execute and Observe: You run the code to see if it meets your expectations.
4.  Provide Feedback and Refine: You provide new instructions to fix errors, add features, or change the style.

### Your Toolkit: The HKUST GenAI Platforms

HKUST provides us with access to popular AI platforms, which are a good helper for vibe coding. You can access it here: [https://openwebui.hkust.edu.hk](https://openwebui.hkust.edu.hk) (Login with your HKUST account).

### Let's Vibe: Building a To-Do List Web App

Let's build something practical. A to-do list is a classic starter project, and we can create it from scratch just by describing our vibe. 

#### Step 1: Define Your "Vibe" (The Goal)

First, let's think about what we want. We don't need to think in code yet.
*Our Vibe:* "A clean, simple, and functional to-do list. I want to be able to type in a task, add it to a list, and remove it when I'm done."

#### Step 2: Choose Your Tool and Model

Navigate to the **Open WebUI** platform and select **GPT-4o** from the model dropdown menu.

#### Step 3: The First Prompt - From Vibe to Blueprint

Now, let's translate our vibe into a prompt for the AI.

**Your Sample Prompt:**
```
Please generate a single, self-contained HTML file for a to-do list web app. Use HTML, CSS, and JavaScript.

The page should have:
1. A clear heading that says "My Vibe List".
2. A text input field where I can type my tasks.
3. An "Add Task" button.

The functionality should be:
1. When a user types a task into the input field and clicks the "Add Task" button, the task should appear in a list below.
2. The input field should clear itself after a task is added.
3. Each task in the list should have a "Delete" button next to it that removes the task from the list.

For the styling (the 'vibe'):
- Use a clean, modern sans-serif font.
- Center the main content of the app on the page.
- Give it a pleasant, minimalist color scheme with a light background.
```

Copy and paste this prompt into the Open WebUI chat. The AI will process your request and generate a complete block of HTML code that includes the CSS and JavaScript.

#### Step 4: Test and Refine

1.  **Test the Code**: Copy the entire code block generated by the AI. Create an html file in your "ISDN3000C_Lab1" folder, paste the code, and save the file with an `.html` extension (e.g., `todolist.html`).

2.  **Refine the Vibe**: The first version is good, but maybe it's not quite right. Let's guide the AI to make changes. You don't need to know *how* to code the changes, just *what* you want to change.

    **Follow-up Sample Prompt 1 (Function):**
    ```
    That's great! Now, add a new feature: when I click on the text of a task, it should be crossed out with a line-through to mark it as completed. Clicking it again should un-mark it.
    ```

    **Follow-up Sample Prompt 2 (Style):**
    ```
    I like the functionality. Let's change the vibe a bit. Make the "Add Task" button pop more with a gradient background. Also, instead of the word "Delete", use a simple '❌' emoji for the delete button.
    ```
    The AI will provide updated code snippets or a full new block of code. Replace the old code with the new version, save, and refresh your browser to see the changes instantly.

#### Step 6: Expanding the Vibe with Advanced Features

Let's explore more unique capabilities of the platform.

*   **Using Web Search for Persistence:** You might notice that when you refresh the page, your tasks disappear. Let's fix that.

    **Sample Prompt:**
    ```
    My tasks disappear when I refresh the page. Please search the web for how to use browser local storage to save the tasks. Update the JavaScript code to ensure my to-do list is saved and reloaded automatically.
    ```

*   **Comparing Models for a New Vibe:** Maybe you want a completely different design. You can switch models in the same chat to get a fresh perspective.

    **Action:** In the Open WebUI interface, switch the model to **Gemini 2.0 Flash**.

    **Sample Prompt:**
    ```
    Taking the latest version of our to-do list code, completely redesign it with a "dark mode" theme. Use a dark background, light text, and neon-accented buttons and borders.
    ```

*   **Generating Imagery with DALL-E:** A custom background could really complete the vibe. The HKUST GenAI platform includes DALL-E for image generation.

    **Sample Prompt (using an image generation model):**
    ```
    Generate a background image for my web app. The vibe is 'lofi digital sunset'. It should be abstract and visually calm.
    ```
    Once the image is generated, you can ask the coding model to incorporate it as the background for your app.


### **Task II: The "Vibe Coding"**

1. In the Github repository "ISDN3000C_Lab1" you created, clone and do the tasks in tutorial_task2.md.
2. Remember to add requirements.txt to let people know the libraries you installed to support the code.
3. Remember to add instructions on how to use your code in a readme.md file, to let people know how to use the code.
4. After you finish the tasks, commit your code and push the changes to Github.

## **Submission**

There are two ways to do submission:

**Option A:** Set the visibility of Github repository "ISDN3000C_Lab1" to public, and submit the link on Canvas.

**Option B:** Invite our TA as your collaborator
   1. In the repository page, you will see **Settings**, click it and go to Settings page. 
   2. In the left sidebar, you will see **Collaborators**. Here find our TA by searching user **siyanhu** and send out the invitation.
