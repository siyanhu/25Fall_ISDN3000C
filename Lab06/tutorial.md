# Lab: Introduction to GPIO on RDK X-5 with Python

## Safety Concerns:

*   **Static Discharge:** Before handling the RDK or components, touch a metal object to discharge any static electricity. 
*   **Correct Voltage:** The RDK X-5's GPIO pins operate at **3.3V**. Connecting them directly to a 5V source can permanently damage the board.
*   **Use Resistors for LEDs:** Never connect an LED directly between a GPIO pin and Ground. The LED will draw too much current and can burn out itself and the GPIO pin. **Always use a current-limiting resistor.**
*   **Double-Check Wiring:** Before running any code, double-check that your wiring is correct.

## Part 0: Setup and Pinout 

### Understanding the 40-Pin Header

The RDK X-5 has a 40-pin header that is largely compatible with the Raspberry Pi standard. This header provides access to power, ground, and GPIO pins.

You can reference the pins in two ways:
*   `BOARD`: Refers to the physical pin numbers on the header (1-40). This is the easiest for beginners.
*   `BCM` (Broadcom): Refers to the GPIO channel number on the processor.

**For this lab, we will exclusively use the `BOARD` numbering system.**

![alt text](https://rdk-doc.oss-cn-beijing.aliyuncs.com/doc/img/03_Basic_Application/03_40pin_user_guide/image/40pin_user_guide/image-20220828203147852.png)

![alt text](https://rdk-doc.oss-cn-beijing.aliyuncs.com/doc/img/03_Basic_Application/03_40pin_user_guide/image/40pin_user_guide/image-20241217-202319.png)

**Official Sample Codes**

On your RDK, run the following code to check their official sample code based on `GPIO` development, and vision projects:
```bash
bash cd /app/
```

### Prepare Your Workspace
1.  Connect your RDK X-5 and log in.
2.  Open a terminal and a text editor (like `gedit`, `nano`, or `VS Code`).
3.  The development board comes with a pre-installed GPIO Python library Hobot.GPIO. Users can import the GPIO library using something similar as the following command.
    ```bash
    sunrise@ubuntu:~$ sudo python3
    Python 3.10.12 (default, Oct 10 2025, 12:22:08) 
    Type "help", "copyright", "credits" or "license" for more information.
    >>> import Hobot.GPIO as GPIO
    Get board ID: 0x504
    >>> GPIO.VERSION
    '0.0.2'
    >>> GPIO.model
    'X3PI'
    ```
4.  Create a new directory for our lab work:
    ```bash
    mkdir ~/lab06
    cd ~/lab06
    ```
5. Create a new virtual environment on your RDK X-5 and point to system library path to use `Hobot.GPIO`:
    ```bash
    sudo apt-get install virtualenv
    virtualenv lab06_venv --system-site-packages
    source lab06_venv/local/bin/activate
    ```

## "Hello, World!" with GPIOs - Blinking an LED 
 
This first exercise is the classic "Hello, World" of hardware: making an LED blink. This teaches you how to configure a GPIO pin as an **output**.

### 1. The Circuit

1.  Place the LED on the breadboard. The longer leg is the positive anode (`+`), and the short leg is the negative cathode (`-`).
2.  Connect one leg of the 220Ω resistor to the same row as the LED's long leg.
3.  Using a jumper wire, connect the other leg of the resistor to **Pin 31 (GPIO6)** on the RDK X-5.
4.  Using another jumper wire, connect the LED's short leg to a **GND (Ground)** pin on the RDK X-5 (e.g., **Pin 6**).

**Your circuit should look like this:**
`RDK Pin 31` --> `Resistor` --> `LED Anode (+)` --> `LED Cathode (-)` --> `RDK GND Pin`

For reference, please check [blink.png](./media/blink.png)

### 2. The Code

Create a new file named `blink.py` and enter the following code:

```python
import Hobot.GPIO as GPIO
import time

# Pin Definitions
led_pin = 31  # BOARD pin 31

def main():
    # Set the GPIO numbering mode
    GPIO.setmode(GPIO.BOARD)

    # Set up the LED pin as an output
    GPIO.setup(led_pin, GPIO.OUT)

    print("Starting LED blink. Press Ctrl+C to exit.")

    try:
        # Loop forever
        while True:
            print("LED ON")
            GPIO.output(led_pin, GPIO.HIGH)  # Turn the LED on (HIGH is the voltage level)
            time.sleep(1)                    # Wait for 1 second

            print("LED OFF")
            GPIO.output(led_pin, GPIO.LOW)   # Turn the LED off (LOW is the voltage level)
            time.sleep(1)                    # Wait for 1 second

    finally:
        # This block will run when the program is interrupted (e.g., Ctrl+C)
        print("\nCleaning up GPIO...")
        GPIO.cleanup()  # Resets all GPIO ports you've used back to input mode.

if __name__ == '__main__':
    main()
```

### 3. Run the Code

Save your file and run it from the terminal:

```bash
python3 blink.py
```

You should see the LED on your breadboard turn on for one second, then off for one second, repeatedly. The terminal will print "LED ON" and "LED OFF" in sync. Press **Ctrl+C** to stop the program and observe the "Cleaning up GPIO..." message.

### 4. Manually clean the GPIO

If you see some messages showing "the port is in use", please run the following code to manually clean the GPIO usage status:
```python
import Hobot.GPIO as GPIO

try:
    GPIO.setmode(GPIO.BOARD)
    GPIO.cleanup()
    print("GPIO is cleanned successfully.")
except Exception as e:
    print(f"Error Message: {e}")
    exit()
```

## Reading a Push Button

Now, let's learn how to get information from the outside world. We'll read the state of a push button, which is a form of **digital input**.

### 1. The Circuit

We will use an internal *pull-down* resistor, which makes the wiring very simple. A pull-down resistor ensures the input pin reads `LOW` when the button isn't pressed.

1.  Place the push button on your breadboard (it should straddle the center channel).
2.  Connect one side of the button to a **3.3V** pin on the RDK X-5 (e.g., **Pin 1**).
3.  Connect the other side of the button to **Pin 13 (GPIO27)** on the RDK X-5.

**Your circuit should look like this:**
`RDK 3.3V Pin` --> `Button` --> `RDK Pin 13`

For reference, please check [button.png](./media/button.png)

### 2. The Code

Create a new file named `button_read.py` and enter the following code:

```python
import Hobot.GPIO as GPIO
import time

# Pin Definitions
button_pin = 13  # BOARD pin 13

def main():
    GPIO.setmode(GPIO.BOARD)

    # Set up button pin as an input, pulled down to ground
    # This means the pin is LOW (0) by default. When the button is pressed,
    # it connects to 3.3V and reads HIGH (1).
    GPIO.setup(button_pin, GPIO.IN, pull_up_down=GPIO.PUD_DOWN)

    print("Button ready. Press it! (Ctrl+C to exit)")

    try:
        while True:
            if GPIO.input(button_pin) == GPIO.HIGH:
                print("Button Pressed!")
            else:
                print("Button Not Pressed")
            
            time.sleep(0.1) # Debounce and prevent spamming the console

    finally:
        print("\nCleaning up GPIO...")
        GPIO.cleanup()

if __name__ == '__main__':
    main()
```

### 3. Run the Code

Run the script from your terminal:

```bash
python3 button_read.py
```

Press and hold the button. You should see "Button Pressed!" printed to the console. When you release it, it will go back to printing "Button Not Pressed". Press **Ctrl+C** to exit.

## Combining Input and Output

Let's combine the last two parts to make the button control the LED.

### 1. The Circuit

Combine the circuits from Part 1 and Part 2. You will have both the LED and the button wired up simultaneously.
*   **LED:** `Pin 31` -> Resistor -> LED -> `GND`
*   **Button:** `3.3V` -> Button -> `Pin 13`

For reference, please check [button.png](./media/combine.png)

### 2. The Code
Create a file named `button_led.py`.

```python
import Hobot.GPIO as GPIO
import time

# Pin definitions
led_pin = 31
button_pin = 13

def main():
    GPIO.setmode(GPIO.BOARD)

    # Setup pins
    GPIO.setup(led_pin, GPIO.OUT)
    GPIO.setup(button_pin, GPIO.IN, pull_up_down=GPIO.PUD_DOWN)

    print("Press the button to turn on the LED. (Ctrl+C to exit)")

    # Set initial LED state to OFF
    GPIO.output(led_pin, GPIO.LOW)

    try:
        while True:
            if GPIO.input(button_pin) == GPIO.HIGH:
                # Turn LED ON when button is pressed
                GPIO.output(led_pin, GPIO.HIGH)
            else:
                # Turn LED OFF when button is not pressed
                GPIO.output(led_pin, GPIO.LOW)
            
            time.sleep(0.02) # Small delay

    finally:
        print("\nCleaning up GPIO...")
        GPIO.cleanup()

if __name__ == '__main__':
    main()
```

### 3. Run the Code

```bash
python3 button_led.py
```
Now, the LED should light up only when you are actively pressing the button! This is the foundation of physical computing.

---

## Fading an LED with PWM 

**PWM (Pulse Width Modulation)** is a technique to simulate an analog signal using a digital pin. By turning a pin on and off very quickly, we can control the *average* voltage, which allows us to do things like dim an LED or control the speed of a motor.

### 1. Switch the Mode

According [RDK official tutorial](https://developer.d-robotics.cc/rdk_doc/en/Basic_Application/03_40pin_user_guide/40pin_define/#pin-multiplexing-configuration), some interfaces have multiplexed functions, and only one function can be enabled at a time. We must make sure to switch to `PWM` mode before going into the following task.

In your terminal, enter the following information:
```bash
sudo srpi-config
```

After selecting `3 Interface Options` -> `I3 Peripheral bus config`, you will enable PWM mode:

*   **Physical Pin 32:** enable `PWM4`
*   **Physical Pin 33:** enable `PWM0`

And make sure that you've tapped every confirmation button. You may require to reboot the system at ths stage.

### 2. The Circuit

The circuit is the same as in Part 1, but move the cable from Physical Pin 31 to 32, or 33.

### 3. The Code

Create a file named `led_fade.py`.

```python
import Hobot.GPIO as GPIO
import time

# Pin Definitions
led_pin = 32 # make sure that this pin is not in use when you doing PWM.

def main():
    GPIO.setmode(GPIO.BOARD)
    GPIO.setup(led_pin, GPIO.OUT)

    # Create a PWM instance:
    # PWM(pin, frequency_in_Hz)
    pwm = GPIO.PWM(led_pin, 100)  # Use 100 Hz frequency

    # Start PWM with 0% duty cycle (off)
    pwm.start(0)

    print("Fading LED. Press Ctrl+C to exit.")

    try:
        while True:
            # Fade in (from 0% to 100% duty cycle)
            for duty_cycle in range(0, 101, 5):
                pwm.ChangeDutyCycle(duty_cycle)
                time.sleep(0.05)
            
            # Fade out (from 100% to 0% duty cycle)
            for duty_cycle in range(100, -1, -5):
                pwm.ChangeDutyCycle(duty_cycle)
                time.sleep(0.05)

    finally:
        print("\nStopping PWM and cleaning up...")
        pwm.stop()      # Stop the PWM
        GPIO.cleanup()  # Reset the GPIO pins

if __name__ == '__main__':
    main()
```

## Exercise 1: Controlling the PWM with the push button

Write a script that does the following:
1. actuates the LED with PWM
2. Uses the push button to switch between different brightnesses, with 4 levels of brightness and "off".
3. Switches the LED to blinking with a long button press

## Exercise 2: The Camera

We have some USB camera modules. Camera images can be easily used in python using the OpenCV Library

### Setup

In your virtual environment, install opencv-python

```bash
pip install opencv-python numpy
```

###  OpenCV

**OpenCV (Open Source Computer Vision Library)** is an extremely powerful, industry-standard library for computer vision and image processing. It allows you to write code that can read and write images/videos, identify faces, track objects, and, in our case, apply filters and transformations.

Here are the key OpenCV functions you will need:

#### 1. Initializing and Releasing the Camera
You need to tell OpenCV to connect to the USB camera. `0` is usually the default built-in or first-connected camera. It's crucial to `release()` the camera when your program is done so other applications can use it.

```python
# At the beginning of your script
import cv2
cap = cv2.VideoCapture(0)

# IMPORTANT: At the end of your script (in the 'finally' block)
cap.release()
```

#### 2. Capturing a Frame
To take a picture, you use the `read()` method. It returns two values: a boolean (`ret`) that tells you if the capture was successful, and the `frame` itself, which is the image data.

```python
# To take a picture
ret, frame = cap.read()

# You should always check if it was successful
if not ret:
    print("Failed to capture frame!")
```

#### 3. Processing: Canny Edge Detection
Canny Edge Detection is a classic algorithm that finds sharp changes in intensity (edges) in an image. It works best on grayscale images, so you'll need to convert the color frame first.

```python
# Convert the captured frame to grayscale
gray_image = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)

# Apply the Canny algorithm. The numbers are min/max thresholds.
# You can experiment with these values!
edges = cv2.Canny(gray_image, 100, 200) 
```

#### 4. Saving an Image
Finally, you can save your image data (whether it's the original `frame` or the `edges` image) to a file on the disk.

```python
# Save the original image
cv2.imwrite("original_photo.jpg", frame)

# Save the resulting edges
cv2.imwrite("edges_photo.jpg", edges)
```
**Hint:** Use the `time` module to create unique filenames so you don't overwrite your pictures on every button press! Example: `filename = f"image_{time.time()}.jpg"`

### **Group Assignment**

Write a script that does the following:
1. Upon the click of a button, takes a picture with the camera
2. Applies the Canny Edge detector and saves the resulting image
3. Lights an LED for a half second to confirm that the process is succesful

#### Suggested program flow

1.  **Setup:**
    *   Import all necessary libraries: `Hobot.GPIO`, `cv2`, and `time`.
    *   Define your `led_pin` and `button_pin` variables.
    *   Use a `try...finally` block to ensure cleanup happens.
    *   Inside the `try` block, set up your GPIO pins (LED as `OUT`, Button as `IN` with `PUD_DOWN`).
    *   Initialize the camera using `cv2.VideoCapture(0)`.
    *   Print a "Ready" message to the user.

2.  **Main Loop:**
    *   Start a `while True:` loop that will run continuously.
    *   Inside the loop, constantly check if the button has been pressed (`GPIO.input(...) == GPIO.HIGH`).
    *   **If the button is pressed:**
        *   Print a message like "Button Pressed! Capturing..."
        *   Turn the LED **ON** (`GPIO.output(...)`).
        *   Capture a frame from the camera.
        *   Perform the grayscale conversion and Canny edge detection.
        *   Save both the original `frame` and the `edges` image to unique filenames.
        *   Wait for a short period (e.g., `time.sleep(1)`).
        *   Turn the LED **OFF**.
        *   **(Pro Tip):** Add a small loop that waits for the user to *release* the button before continuing. This prevents one long press from taking dozens of pictures.

3.  **Cleanup:**
    *   In the `finally` block of your `try...finally` structure:
        *   Print a "Cleaning up..." message.
        *   **Release the camera (`cap.release()`).**
        *   **Clean up the GPIO pins (`GPIO.cleanup()`).**

#### Submission

1. Take a picture of your circuit
2. Upload python scripts to a new public Github Repository with name `ISDN3000C_Lab06`.
3. submit the repo link to Canvas.
4. add `readme.md` (display your circuit picture here in `readme.md`), `.gitignore`, `requirements.txt` if necessary.

Please inform me in the Whatsapp group if your group members may be different from current default setttings in `Canvas/People`.

---