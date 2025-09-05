#### **1. On Ubuntu 24.04 LTS**:

<ins>Step 1: Install Build Dependencies</ins>

Compiling Python from source on Ubuntu requires installing the necessary build tools and development libraries first. We need a C compiler and various development libraries for Python's standard library modules to be built correctly.

```bash
# Update package lists
sudo apt-get update

# Install essential build tools and Python development libraries
sudo apt install -y build-essential gdb lcov pkg-config libbz2-dev libffi-dev libgdbm-dev libgdbm-compat-dev liblzma-dev libncurses5-dev libreadline-dev libsqlite3-dev libssl-dev lzma lzma-dev tk-dev uuid-dev zlib1g-dev
```

<ins>Step 2: Download and Extract the Python Source Code</ins>

Use `wget` to download the specified source file and `tar` to extract it.

```bash
# Navigate to a directory where you want to download the source, e.g., /tmp
cd Download

# Download the source tarball
wget https://www.python.org/ftp/python/3.11.11/Python-3.11.11.tar.xz

# Extract the archive
tar -xf Python-3.11.11.tar.xz

# Navigate into the extracted directory
cd Python-3.11.11
```

<ins>Step 3: Configure, Compile, and Install</ins>

We will configure the build to install into a separate directory (`/opt/python/3.11.11`) to avoid interfering with the system Python. We will also use `make altinstall` to prevent overwriting the default `python3` binary.

```bash
# Configure the build with optimizations and a custom install location
# --prefix specifies the installation directory.
# --enable-optimizations runs profile-guided optimization for a faster Python binary.
./configure --enable-optimizations --prefix=/opt/python/3.11.11

# Compile the source code.
# The -j flag specifies the number of cores to use for faster compilation.
# $(nproc) automatically uses all available processor cores.
make -j $(nproc)

# Install Python.
# IMPORTANT: Use 'altinstall' to prevent replacing the default python3 symlink.
# This creates python3.11 instead of python3.
sudo make altinstall
```

<ins>Step 4: Create a Virtual Environment with the New Python</ins>

You can now use the full path to your newly installed Python executable to create a virtual environment.

```bash
# Go to your project directory
cd ~/myproject

# Create a virtual environment using the specific Python binary
/opt/python/3.11.11/bin/python3.11 -m venv venv

# Activate the new environment
source venv/bin/activate

# Verify the Python version inside the environment
python --version
# Expected output: Python 3.11.11

# After you complete the tasks, stop (deactivate) the environment
deactivate
```

#### **2. On macOS**

**Attention❗️** Compiling Python from source on MacOS is not always the first choice for developers. The standard and fully supported method is to choose one version with official precompiled installers from [python.org](https://www.python.org/downloads/release/python-3119/), or directly install a specific version of python with Homebrew. If you use precompiled installer, skip step 1-3 and directly go to step 4. If you use Homebrew, skip step 2 and 3 and focus on step 1 and step 4.

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
```

If you install python 3.11 with Homebrew, run the following code:

```bash
brew install python@3.11
```
If if successfully installed via Homebrew, you will see the following information:

```bash
Python is installed as
  /opt/homebrew/bin/python3.11
```

<ins>Step 2: Download and Extract the Python Source Code</ins>

Use `curl` to download the file and `tar` to extract it.

```bash
# Navigate to a temporary directory
cd Downloads

# Download the source tarball
curl -O https://www.python.org/ftp/python/3.11.11/Python-3.11.11.tar.xz

# Extract the archive
tar -xf Python-3.11.11.tar.xz

# Navigate into the extracted directory
cd Python-3.11.11
```

<ins>Step 3: Configure, Compile, and Install</ins>

On macOS, you need to provide flags to the configure script so it can find the libraries installed by Homebrew.

```bash
# Configure the build, pointing to Homebrew's OpenSSL
# We will install it into /usr/local/opt/python/3.11.11
LDFLAGS="-L$(brew --prefix openssl)/lib" \
CPPFLAGS="-I$(brew --prefix openssl)/include" \
./configure --prefix=/usr/local/opt/python/3.11.11 --with-openssl=$(brew --prefix openssl)

# Compile the source code using all available cores
make -j $(sysctl -n hw.ncpu)

# Install Python using 'altinstall'
sudo make altinstall
```

<ins>Step 4: Create a Virtual Environment with the New Python</ins>

Use the absolute path to your new `python3.11` executable.

```bash
# Go to your project directory
cd ~/myproject

# Create a virtual environment
/usr/local/opt/python/3.11.11/bin/python3.11 -m venv venv

# Or if you have created a virtual environment using the Homebrew-installed Python
$(brew --prefix python@3.11)/bin/python3.11 -m venv venv

# Activate the environment
source venv/bin/activate

# Verify the version
python --version
# Expected output: Python 3.11.11

# After you complete the tasks, stop (deactivate) the environment
deactivate
```

#### **3. On Windows**

**Attention❗️** Compiling Python from source on Windows is highly complex and not recommended for general use. It requires installing Visual Studio and the C++ build toolchain, and the process is significantly different from Unix-based systems. The standard and fully supported method is to choose one version with official precompiled installers from [python.org](https://www.python.org/downloads/release/python-3119/). If you use precompiled installer, skip step 1-3 and directly go to step 4.

We list the steps to proceed without precompiled installers here just for reference.

<ins>Step 1: Install Build Dependencies</ins>

1.  Download and run the **Visual Studio Installer**.
2.  Select the **"Desktop development with C++"** workload. This will install the necessary MSVC compiler, Windows SDK, and other tools.
3.  You will also need to install Git for Windows to get some Unix-like tools and `tar`.

<ins>Step 2: Download and Extract the Source Code</ins>

Open a **Developer Command Prompt for VS** (this is important as it sets up the compiler environment variables).

```powershell
# Navigate to a source directory, e.g., C:\src
cd C:\src

# Download the file using PowerShell
Invoke-WebRequest -Uri "https://www.python.org/ftp/python/3.11.11/Python-3.11.11.tar.xz" -OutFile "Python-3.11.11.tar.xz"

# Extract the files (assuming tar is installed with Git for Windows)
tar -xf Python-3.11.11.tar.xz

# Navigate into the directory
cd Python-3.11.11
```

<ins>Step 3: Compile the Code</ins>

The Windows build uses a different script-based system located in the `PCbuild` directory.

```powershell
# Navigate to the Windows build directory
cd PCbuild

# Run the build script for a 64-bit version
.\build.bat -p x64
```

This command compiles Python. The output `.exe` and `.dll` files will be placed in the `PCbuild\amd64` subdirectory. There is no `install` step like on Linux/macOS. The build is self-contained within this folder.

<ins>Step 4: Create a Virtual Environment with the New Python</ins>

You must use the full path to the `python.exe` that was just built.

```powershell
# Navigate to your project directory
cd C:\Users\YourUser\myproject

# Create a virtual environment by pointing to the compiled python.exe
# The path will be relative to where you compiled the source
C:\src\Python-3.11.11\PCbuild\amd64\python.exe -m venv venv

# Activate the environment
.\venv\Scripts\activate

# Verify the version
# After you complete the tasks, stop (deactivate) the environment
deactivate
```
