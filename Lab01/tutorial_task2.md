### **Task II: The "Vibe Coding"**

1.  Download ASCII-Artify.zip, and unzip it within "ISDN3000C_Lab1" folder.
2.  Inside it, you should observe the files shown as below. And finish the tasks in main.py.

#### **File Structure**
```
ASCII-Artify/
├── main.py
├── assets/
    ├── hkust-logo.jpeg
```

**The Core Logic:**
1.  **Resize Image:** Shrink the image to fit the terminal width (e.g., 100 characters).
2.  **Convert to Grayscale:** Convert pixels from color to brightness values (0-255).
3.  **Map Brightness to Characters:** Use a character ramp (e.g., `'.:-=+*#%@'`) where dark pixels map to dense characters (`@`) and bright pixels map to sparse ones (`.`).
4.  **Print:** Construct the string of characters and print it.

### **Guided Workflow**

#### **Step 1: The First Hurdle - Loading an Image**

▶️ **AI Prompt Idea:** *"Write a Python script using Pillow that opens an image from a path given in the command line."*

**Goal:** A script that loads your test image without crashing.

#### **Step 2: The Logic - Resizing and Grayscale**

▶️ **AI Prompt Idea:** *"I have a Pillow image object. Write a Python function that takes it and a target width of 100 pixels. It should resize the image, maintain the aspect ratio, and convert it to grayscale."*

**Goal:** A function that returns a small, grayscale image object.

#### **Step 3: The "Art" - Mapping Pixels to Characters**

▶️ **AI Prompt Idea:** *"Write a Python function that takes a grayscale pixel value (0-255) and a character ramp string like `'.:-=+*#%@'`. It should map the pixel value to the correct character in the string."*

**Goal:** A function that can translate a brightness value into a character.

#### **Step 4: Putting It All Together**

▶️ **AI Prompt Idea:** *"Combine the previous steps. Write a full Python script that:
1. Takes an image path from the command line.
2. Resizes it to 100 characters wide and converts to grayscale.
3. Iterates through every pixel.
4. Maps each pixel's brightness to a character.
5. Prints the final ASCII art to the console."*

**Goal:** Run `python main.py assets/hkust-logo.jpeg` and see your first piece of ASCII art!

#### **Step 5: The "Vibe" Upgrade**

Now, make it cooler. Choose a challenge and work with your AI.

*   **Easy:** Let the user provide a custom character ramp via a command-line flag.
    ▶️ **AI Prompt:** *"How do I use Python's `argparse` to add an optional argument like `--chars '10'`?"*

*   **Medium:** Add color using the `rich` library (`pip install rich`).
    ▶️ **AI Prompt:** *"Using the Python `rich` library, how can I print a character to the terminal with a specific RGB color from a Pillow pixel?"*

*   **Hard:** Create a real-time ASCII webcam using `OpenCV` (`pip install opencv-python`).
    ▶️ **AI Prompt:** *"Write a Python script using OpenCV to capture my webcam. For each frame, convert it to a Pillow image so I can run my ASCII art function on it."*