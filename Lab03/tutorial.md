# **Lab 3 - RDK X5 Bootcamp**

### **Introduction**

RDK X5 development board is a fully functional **ARM**-based Linux computer. It is specifically engineered to accelerate ROS-based robotics projects and other demanding edge AI applications. Key features of the RDK X5 include:

*   **Processor:** An 8-core Arm Cortex-A55 processor clocked at 1.5GHz.
*   **AI Performance:** A significant highlight is its dedicated 10 TOPS (Tera Operations Per Second) BPU (Neural Processing Unit), enabling it to run complex AI models like Transformers directly on the edge in real-time.
*   **Memory:** Available with 4GB or 8GB of LPDDR4 RAM .
*   **Graphics:** It includes a 32 GFLOPS GPU capable of H.265/H.264 video encoding and decoding up to 4K at 60 frames per second [.
*   **Connectivity:** Features Wi-Fi 6, Bluetooth 5.4, Gigabit Ethernet with optional Power over Ethernet (PoE) support, and a CAN FD interface for industrial integration .
*   **Software Support:** It runs Ubuntu 22.04 and supports popular open-source frameworks such as ROS, TensorFlow, and PyTorch.

---

### **Part 1: Basic Preparation & System Boot**

This section is the foundation for all subsequent operations. Please follow the steps carefully.

#### **1.1 Hardware & Software Checklist**

*   **Hardware**:
    *   RDK X5 Development Board & Heat Sink
    *   Power Supply & appropriate adapter plug
    *   64GB MicroSD (TF) Card
    *   SD Card Reader
    *   USB-C to Ethernet adapter
    *   Ethernet Cable

*   **Software (Please download on your personal laptop beforehand)**:
    *   **Ubuntu RDK OS Desktop Version**: [Official Link](https://archive.d-robotics.cc/downloads/os_images/rdk_x5/rdk_os_3.3.1-2025-8-8/rdk-x5-ubuntu22-preinstalled-desktop-3.3.1-arm64.img.xz), [Google Drive Backup](https://drive.google.com/drive/folders/1PGY9-gBivsWrB6LC3koPOLh3KDOOvrHV?usp=share_link)
    *   **Flashing Tool**: Windows users download **Rufus** ([Official Website](https://rufus.ie/)); macOS users download **balenaEtcher** ([Official Website](https://www.balena.io/etcher/)).
    *   **VNC Client**: To ensure a consistent experience for everyone, we recommend using **RealVNC Viewer**, which is available for both Windows and macOS. Please download from the [official RealVNC website](https://www.realvnc.com/en/connect/download/viewer/).

#### **1.2 Flashing the SD Card**

**Warning: This process will permanently erase all data on the SD card!**

##### **▶ For Windows Users (using Rufus)**

1.  **Unzip OS Image File**: Windows does not have native support for .xz files, so we'll need a third-party tool. 7-Zip is a free and open-source file archiver with a high compression ratio. It supports a wide range of formats, including .xz. So we could download and install 7-Zip from its official website. Once installed, right-click on your .img.xz file. From the context menu, select `7-Zip > Extract files...` or `Extract Here`. Choose a destination for the extracted .img file.
1.  **Connect**: Insert the SD card into your card reader and connect it to your computer.
2.  **Launch**: Open `rufus.exe`.
3.  **Configure**:
    *   **`Device`**: **TRIPLE-CHECK** that you have selected your SD card.
    *   **`Boot selection`**: Click `SELECT` and choose the downloaded `.img.xz` image file.
    *   Leave other options at their default settings.
4.  **Execute**: Click `START`, then `OK` in the warning dialog. Wait for the progress bar to show **READY**.
5.  **Eject**: Close Rufus and safely eject the SD card.

##### **▶ For macOS Users (using balenaEtcher)**

1.  **Unzip OS Image File**: Open the Terminal application, navigate to the directory where your .img.xz file is located, and use the unxz command:  

    ```bash
    unxz rdk-x5-ubuntu22-preinstalled-desktop-3.3.1-arm64.img.xz
    ```
    
2.  **Connect & Launch**: Connect the SD card and open `balenaEtcher`.
3.  **Configure**:
    *   **`Flash from file`**: Select the unzip output `.img` image file.
    *   **`Select target`**: **TRIPLE-CHECK** that you have selected your SD card.
    *   **`Flash!`**: Click to begin.
4.  **Complete**: Wait for the "Flash Complete!" message. At this point, macOS may show a "The disk you inserted was not readable by this computer" error. This is **NORMAL**. Click **`Eject`** in the dialog box, then remove the SD card.

---

### **Part 2: Network Connection & Remote Interaction**

Now, we will establish a direct physical link to the RDK and then learn the two primary software methods for remotely controlling it: one graphical (VNC) and one command-based (SSH).

#### **2.1 The Physical Connection**

1.  **Insert SD Card**: Make sure the flashed SD card is inserted into the slot on the bottom of the RDK board.
2.  **Connect Network Hardware**:
    *   Plug the **USB-C to Ethernet adapter** into the RDK's data USB-C port (the port that is **NOT** labeled "PWR").
    *   Connect one end of the **Ethernet cable** to the adapter.
    *   Connect the other end of the Ethernet cable directly to your laptop's Ethernet port (or via a USB adapter if your laptop lacks one).
3.  **Power On**: Plug the power supply cable into the RDK's dedicated **power port (labeled 'PWR')**. The RDK will boot automatically. Wait about a minute for it to start up.

#### **2.2 Configuring the Network Bridge: Static IPs**

**Connect your laptop to wifi `ISDN3000C`**

**Why do this?** The RDK's operating system is pre-configured with a fixed, static IP address: e.g. `192.168.127.10`. To communicate with it over our direct cable link, your laptop must also have a static IP address in the same "neighborhood" (i.e., on the same `192.168.127.x` subnet). We will set your laptop's IP to `192.168.127.12`.

##### **▶ For Windows (11 & 10)**

1.  Go to **Settings** > **Network**.
2.  Find and click on **Advanced network settings** or scroll down to **Change adapter options**.
3.  In the "Network Connections" window, **right-click** on your Ethernet adapter and select **Properties**.
4.  Select **Internet Protocol Version 4 (TCP/IPv4)** and click the **Properties** button.
5.  Select the radio button for **Use the following IP address**.
6.  Enter the following information exactly:
    *   **IP Address:** `192.168.127.12`
    *   **Subnet Mask:** change it to `255.255.255.0` 
    *   Leave the `Default gateway` and `DNS server` fields **empty**.
7.  Click **OK**, and then **Close**.

##### **▶ For macOS (Ventura and later)**

1.  Go to the **Apple menu** () > **System Settings**.
2.  Click **Network** in the sidebar.
3.  Select your **Ethernet** connection (Or other names such as the device name **`AX88179A`**).
4.  Click the **Details...** button.
5.  Select **TCP/IP** from the left sidebar.
6.  Change `Configure IPv4` from `Using DHCP` to **Manually**.
7.  Enter the following information exactly:
    *   **IP Address:** `192.168.127.12`
    *   **Subnet Mask:** change it to `255.255.255.0` 
    *   Leave the `Router` field **empty**.
8.  Click **OK**.

#### **2.3 Remote Control Methods**

With the network bridge configured, you can now access the RDK. Its credentials are:
*   **IP Address**: `192.168.127.10`
*   **Username**: `sunrise`
*   **Password**: `sunrise`

##### **Method 1: VNC (Graphical Control)**

VNC gives you access to the RDK's full graphical desktop.

1.  Open **RealVNC Viewer**.
2.  In the address bar, type the RDK's IP address and press Enter: `192.168.127.10`
3.  Accept any security warnings and enter the username `sunrise` and password `sunrise`.
4.  You should now see the RDK's Ubuntu desktop.
5.  **[Task] Connect the RDK to the Internet**: In the VNC desktop, click the network icon in the top-right corner, connect to the Wi-Fi network `ISDN3000C` with the password `12345678`.

##### **Method 2: SSH (Command-Line Control)**

SSH gives you a powerful and efficient text-based terminal on the RDK. Do this operation on your laptop's **Visual Studio Code Terminal**.

1.  Open your computer's Terminal (**Terminal** on macOS, **PowerShell** on Windows).
2.  Execute the command: `ssh sunrise@192.168.127.10`
3.  The first time you connect, type `yes` and press Enter.
4.  When prompted, type the password `sunrise` and press Enter.
5.  Your command prompt will change, indicating you are now issuing commands directly on the RDK. To leave, type `exit`.

#### **2.4 SCP (Secure File Copy)**

The basic syntax is `scp [source] [destination]`.

**A) Copying a file FROM your Laptop TO the RDK:**

This is useful for uploading data files.

1.  **Create a test file on your laptop's Desktop:**
    ```bash
    # On macOS/Linux
    echo "Hello from my laptop" > ~/Desktop/upload_test.txt
    
    # On Windows (in PowerShell)
    "Hello from my laptop" | Out-File -FilePath $HOME\Desktop\upload_test.txt -Encoding utf8
    ```

2.  **Use `scp` to copy it to the RDK's home directory:**
    ```bash
    # On macOS/Linux (run from any directory)
    scp ~/Desktop/upload_test.txt sunrise@192.168.127.10:/home/sunrise/
    
    # On Windows (run from any directory)
    scp $HOME\Desktop\upload_test.txt sunrise@192.168.127.10:/home/sunrise/
    ```
    You will be prompted for the `sunrise` password. The colon `:` after the IP address is essential.

**B) Copying a file FROM the RDK TO your Laptop:**

This is useful for downloading log files, data, or results.

1.  **Let's create a test file on the RDK using SSH:**
    *   `ssh sunrise@192.168.127.10`
    *   Once logged in, run: `echo "This is a file from the RDK" > rdk_file.txt`
    *   Then type `exit` to return to your laptop's terminal.

2.  **Use `scp` to copy it from the RDK to your laptop's Desktop:**
    ```bash
    # On macOS/Linux
    scp sunrise@192.168.127.10:/home/sunrise/rdk_file.txt ~/Desktop/
    
    # On Windows
    scp sunrise@192.168.127.10:/home/sunrise/rdk_file.txt $HOME\Desktop\
    ```
    You can now find `rdk_file.txt` on your laptop's desktop.

---

### **Part 3: Advanced Exploration**

All the following commands should be executed in the **SSH terminal** connected to the RDK, or via VNC we do the following experiments on RDK terminal app.

#### **3.1 Task 1: Command-Line**

We will combine several command-line tools to create a personalized welcome script.

1.  **Install Tools**:
    *   `figlet`: Creates ASCII art text.
    *   `cowsay`: Makes an ASCII cow say whatever you want.
    *   `lolcat`: Adds a rainbow effect to text.
    ```bash
    sudo apt install figlet cowsay lolcat
    ```
    *    **Several Core Commands**:

    | Action | `apt` (run with `sudo`) |
    | :--- | :--- |
    | **Update** list of available packages | `sudo apt update` |
    | **Search** for a package | `apt search <packagename>` |
    | **Install** a package | `sudo apt install <packagename>` |
    | **Uninstall** a package | `sudo apt remove <packagename>` |



2.  **Experience the Power of Pipes**:
    ```bash
    # Basic version
    date | cowsay
    
    # Artistic text version
    figlet "Hello RDK"
    
    # Ultimate cool version
    figlet "I am ALIVE" | lolcat
    date | cowsay | lolcat
    ```
3.  **Create Your Script**:
    *   Use the `nano` text editor to create a script file:
        ```bash
        nano welcome.sh
        ```
    *   In the editor, enter the following content (feel free to get creative and modify it!):
        ```bash
        #!/bin/bash
        clear
        figlet "My RDK" | lolcat
        echo "----------------------------------------" | lolcat
        cowsay "This awesome device is now ready!"
        echo "Current system time is:"
        date | lolcat
        ```
    *   Press `Ctrl + O` to save, then `Ctrl + X` to exit.
    *   **Grant Execution Permissions**: `chmod +x welcome.sh`
    *   **Run Your Masterpiece**: `./welcome.sh`

#### **3.2 Task 2: Building Your First Web Service**

We will use Python to launch a mini-website on the RDK, which you can then access from your own laptop's browser.

1.  **Create a Website Directory**:
    ```bash
    mkdir task1
    cd task1
    ```
2.  **Write the Homepage (index.html)**:
    *   `nano index.html`
    *   Enter the following HTML code (Design students are encouraged to show off their skills here by adding more CSS!):
        ```html
        <!DOCTYPE html>
        <html>
        <head>
            <title>Hello from my RDK X5!</title>
            <style>
                body { background-color: #282c34; color: #61dafb; font-family: sans-serif; text-align: center; margin-top: 100px; }
                h1 { font-size: 3em; }
                p { font-size: 1.2em; }
            </style>
        </head>
        <body>
            <h1>Welcome to My RDK!</h1>
            <p>This page is being served by a Python web server running on an RDK X5.</p>
        </body>
        </html>
        ```
    *   Save and exit (`Ctrl + O`, `Ctrl + X`).
3.  **Start the Server**:
    *   In the `task1` directory, execute the following command:
        ```bash
        python -m http.server
        ```
    *   You will see output like `Serving HTTP on 0.0.0.0 port 8000 ...`. **Do not close this terminal**.
4.  **Witness the Magic**:
    *   On your **laptop**, open any web browser (Chrome, Firefox, Safari).
    *   Go to the address: `http://192.168.127.10:8000`
    *   You should now see the webpage you just created!

---

### **Cleanup**

*   **Stop the Service**: Press `Ctrl + C` in the SSH terminal running the Python server.
*   **Exit SSH**: Type `exit` in all SSH windows.
*   **Safe Shutdown (Recommended)**: In an SSH terminal, execute `sudo shutdown now`. Wait a minute before unplugging the RDK's power.
*   **Restore Network Settings**: Remember to change your computer's Ethernet settings back from "Manual" to "Automatic (DHCP)", otherwise you might not be able to connect to other wired networks properly.

---

## **Assignment 3: The RDK X5 AI Vision Task**

**Objective:**
In this assignment, you will leverage the skills you learned in the lab to deploy a real AI model onto your RDK X5. You will use a pre-trained PyTorch model to classify an image and then present the result on the web server you created. This task simulates a fundamental "edge AI" application.

**Submission Method:**
You will submit a link of a **forked GitHub repository**. This repository will be **a fork of a provided template and will contain your modified webpage and a screenshot of your final result**.

**Prerequisites:**
*   Successful completion of all parts of the Lab 3 tutorial.
*   Your RDK X5 is powered on and connected to your laptop.
*   You can successfully connect to your RDK via **SSH**, or use **VNC** to operate.
*   Your RDK is connected to the lab's Wi-Fi (`ISDN3000C`) for internet access. If you don't have access to the WiFi, please just use your Phone Hotspot or home WiFi. HKUST Eduroam might lead to unknown failures.

---

### **Step 1: Fork the Assignment Repository (on your Laptop)**

1.  Navigate to the assignment template repository here:
    **[https://github.com/siyanhu/ISDN3000C_Lab03_template](https://github.com/siyanhu/ISDN3000C_Lab03_template)**

2.  Click the **"Fork"** button in the top-right corner of the page. This creates a personal copy of the repository under your GitHub account.

### **Step 2: Prepare Your RDK for AI**

Connect to your RDK via SSH. All the following commands are to be run on the RDK itself.

1.  **Update Package Lists:**
    ```bash
    sudo apt update
    ```

2.  **Install, build and activate Python virtual environment.**

3.  **Install PyTorch and Pillow via requirements.txt**

### **Step 3: Deploy and Run the AI Model**

1.  **Clone Your Repository:**
    Clone the code from **your forked repository** onto the **RDK**. Replace `<YOUR_GITHUB_USERNAME>` with your actual username.
    ```bash
    git clone https://github.com/<YOUR_GITHUB_USERNAME>/ISDN3000C_Lab03_template.git
    ```

2.  **Run the Classifier:**
    Navigate into the new directory and run the Python script.
    ```bash
    cd ISDN3000C_Lab03_template
    python classify.py
    ```

3.  **Analyze the Output:**
    The script will download a pre-trained AI model (ResNet18), analyze the included `sample_image.png` (a picture of a mouse), and print its top prediction. The output should look something like this:

    ```
    Loading model...
    Model loaded.
    Analyzing image: sample_image.png
    Prediction: computer mouse, with 97.44% confidence.
    ```

    **Copy this prediction text!** You will need it for the next step.

### **Step 4: Create Your AI Showcase Webpage**

This is where you integrate the AI's result with the web server from the tutorial.

1.  **Set up the Web Directory:**
    Create a new folder called `task1`, and enter the directory.
    ```bash
    mkdir task1 && cd task1
    ```

2.  **Copy the Image:**
    Copy the sample image from the parent folder into your web directory so the webpage can display it.
    ```bash
    cp ../sample_image.png .
    ```

3.  **Create the `index.html` file:**
    Use the `nano` editor to create your webpage. For syntax of `nano` command, please check [tutorial_nano.md](tutorial_nano.md).
    ```bash
    nano index.html
    ```

4.  **Add the HTML Content:**
    Paste the following HTML code into index.html via `nano` editor. Then, **modify the `<h4>` tag** to include the prediction result you copied from Step 3.

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <title>RDK X5 - AI Vision Report</title>
        <style>
            body { 
                background-color: #1a1a1a; 
                color: #f0f0f0; 
                font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
                text-align: center; 
                padding: 40px;
            }
            h1 { font-size: 2.5em; color: #00aaff; }
            img { 
                max-width: 80%; 
                height: auto; 
                border-radius: 10px; 
                border: 3px solid #00aaff;
                margin-top: 20px;
            }
            .result {
                background-color: #333;
                padding: 15px;
                border-radius: 8px;
                margin-top: 20px;
                display: inline-block;
            }
            h4 { font-size: 1.2em; color: #39ff14; margin: 0; }
        </style>
    </head>
    <body>
        <h1>My RDK X5 AI Vision Report</h1>
        <p>This page is served by my RDK. The AI model analyzed the image below:</p>
        
        <img src="sample_image.png" alt="Image analyzed by the AI">
        
        <div class="result">
            <h4>AI Prediction: [PASTE YOUR PREDICTION RESULT HERE]</h4>
        </div>
    </body>
    </html>
    ```
    Save and exit.

5.  **Launch the Server:**
    Start the web server from within the `task1` directory.
    ```bash
    python -m http.server
    ```

6.  **View Your Showcase:**
    On your `RDK's browser`, navigate to **`http://127.0.0.1:8000`**. You should see your custom webpage displaying the image and the AI's classification result.
    On your `laptop's browser`, navigate to **`http://192.168.127.10:8000`**. You should see your custom webpage displaying the image and the AI's classification result.

### **Step 5: Advanced Task (Batch Image Classification)**

**Objective:** Modify the project to process a whole folder of images at once and output the results into a structured CSV file—a common task in data science and AI projects.

1.  **Prepare the Workspace:**
    Inside your `ISDN3000C_Lab03_template` project folder on the RDK, you will also create a new directory named `test_images` to store the images for this task.
    ```bash
    # Run on RDK: Ensure you are in the project's root directory
    cd ~/ISDN3000C_Lab03_template
    
    # Create a directory to hold your test images
    mkdir test_images
    ```

2.  **Gather Test Images:**
    Your task is to collect a small dataset.
    *   On your **laptop**, create a temporary folder.
    *   Go to a site like [**Roboflow Universe**](https://universe.roboflow.com) or Google Images and search for classes from the [ImageNet labels list](https://raw.githubusercontent.com/anishathalye/imagenet-simple-labels/master/imagenet-simple-labels.json).
    *   Download a couple of images from at least 5 different classes (e.g., 2 pictures of a `tiger`, 2 of a `pizza`, 2 of a `school bus`, etc.).
    *   Place all of these images (e.g., 10+ images total) directly into the temporary folder folder.
    *   Use the `scp` command with the `-r` flag (for recursive) to upload the entire folder from your laptop to the `test_images` directory on your RDK.
        ```bash
        # Run on your Laptop: Upload the entire folder
        # Replace 'path/to/your/laptop_download' with the actual path
        scp -r path/to/your/laptop_download/* sunrise@192.168.127.10:~/ISDN3000C_Lab03_template/test_images/
        ```

3.  **Finish the Batch Classification Script:**
    On the RDK, activate the virtual environment. Then, finish and run the `classify_batch.py` script, providing the path to your `test_images` directory as the argument.
    ```bash
    # Run on RDK: Make sure you are in the project directory
    cd ~/ISDN3000C_Lab03_template
    source venv/bin/activate
    
    # Run the script on your folder of images
    python classify_batch.py test_images/
    ```
    The script will process each image and create a file named `results.csv` in your project directory.

4.  **Review the Output:**
    You can view the contents of the CSV file directly in the terminal to check your results.
    ```bash
    # Run on RDK
    cat results.csv
    ```
    You should see a table with headers `image_name`, `detected_class`, and `confidence_level`.

<hr>

### **Step 6: Final Submission**

For this final submission, we will adopt a standard software engineering practice. You will document your work in a `README.md` file and submit your clean, well-managed Git repository. The basic web task is optional for this part; the focus is on the advanced script and its output.

1.  **Prepare Files for Submission:**
    *   **On the laptop**, capture a screenshot of your result in `step 4`, and save it as `result.png`. Move the `result.png` file from your laptop into your RDK local ISDN3000C_Lab03_template Git repository folder with `scp` command.

2.  **Create `.gitignore`:**
    This file tells Git to ignore unnecessary files. Create the file and add the following content. This prevents you from accidentally committing your large image folder or Python's temporary files. Below is an example:
    ```
    # Python virtual environment
    venv/
    __pycache__/
    *.pyc

    # OS-specific files
    .DS_Store
    Thumbs.db

    # Datasets and large image folders
    test_images/
    ```

3.  **Overwrite and Complete `README.md`:**
    This file is the front page of your project. Create `README.md` and fill it with the following template, adding your own information.
    ```markdown
    # Lab 3: RDK X5 AI Vision Tasks

    This repository contains my work for the Lab 3 assignment.

    ## Basic Task

    The basic task involved running a classifier on a single image and displaying the result on a webpage hosted by the RDK. A screenshot of this task is in `result.png`.

    ## Advanced Task: Batch Image Classification

    The advanced task involved creating a Python script (`classify_batch.py`) that processes an entire folder of images and outputs the classification results into a structured CSV file.

    - **Script:** `classify_batch.py`
    - **How to Run:** `python3 classify_batch.py <path_to_folder>`
    - **Output:** The script generates `results.csv`.

    ### Result Summary

    The script was run on a folder containing [e.g., 12] images across [e.g., 5] classes. The full, machine-readable output is available in the `results.csv` file included in this repository.
    ```

4.  **Commit and Push Your Professional Repository:**
    **Run these commands on your laptop's terminal.** This sequence adds your new script, the CSV result, and your documentation to your repository for submission.
    ```bash
    # Run on your Laptop

    # 1. Add the new script, the CSV result, and the documentation files
    git add .

    # 2. Create a single, descriptive commit, e.g.
    git commit -m "Complete Lab03"

    # 3. Push all changes to your GitHub repository
    git push
    ```

5.  **Submit:**
    Submit the **URL of your forked GitHub repository** to the learning management system.