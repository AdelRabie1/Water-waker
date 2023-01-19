# Water-waker
## Overview
A water gun that utilizes AI to locate your face and shoot water at it. I use OpenCV python library to implement the face detection. Afterwards, 2 DC motors are used to implement the face tracking. As soon as the Water gun is aimed at my face, a third servo motor activates the trigger, hence causing the gun to shoot water on subject's face. RasberryPi is used as the brain of the entire operation.
## Code
First we need to import cv2 for face detection, gpiozero for GPIO pin control on RasberryPi, time for delays in motor control.
```
import cv2
from gpiozero import LED, Servo
from time import sleep
```
Afterwards we initialize all the GPIO pins that we are going to be using:
```
motorxR = LED("GPIO17")
motorxB = LED("GPIO18")
motoryR = LED("GPIO5")
motoryB = LED("GPIO6")
servo = Servo("GPIO22")
```
The following line is responsible to create the classifier system that opencv will use to detect faces. Note: you need to download the xml file and place in the same directory as your python code.
 ```
 face_cascade = cv2.CascadeClassifier('haarcascade_frontalface_default.xml')
 ```
 Then, we run this line to capture video from the camera connected to the RasberryPi.
 ```
 cap = cv2.VideoCapture(0)
 r1 = 0
 r2 = 0
 ```
 We also initialize 2 ready variable `r1` and `r2` to 0. `r1` will be set to 1 if we aim at the face in the x direction, same with `r2` in the y direction.
 
 In a while loop that runs forever until manually broken by user the following happens:
```
while True:
    ret, img = cap.read()
    gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
    faces = face_cascade.detectMultiScale(gray, 1.1, 4)
```
`img` will contain a single frame from the video, then we convert `img` into grayscale for easier face detection. Afterwards `face_cascade.detectMultiScale(gray, 1.1, 4)` takes the frame gray and returns a list faces that contains coordinate information and size of the detected faces and returns it in variable faces.

```
     if len(faces) == 0:
            motorxR.off()
            motorxB.off()
            motoryR.off()
            motoryB.off()
            r1 = 0
            r2 = 0
 ```
 If `len(faces) == 0` this means that no face is detected and so we make sure that all motors stop running, as well as set our ready signals to 0.
 
 The below code is in the following for loop:
 ```
     for (x, y, w, h) in faces:
            #cv2.rectangle(img, (x, y), (x+w, y+h), (255, 0, 0), 2)
 ```
 This for loop takes the x, y coordinates as well as the width and height of the detected face from the variable faces.
 
 For the x direction, the entire frame goes from 0 to 500,
 If position of face in the x direction is greater than 300: rotate clockwise, and since the motor moves too fast for face detect to keep up, the motor is on for only 0.05 seconds in every loop.
 ```
     if x > 300:
            r1 = 0
            motorxB.on()
            motorxR.off()
            sleep(0.05)
            motorxB.off()
  ```
 Same code for if x is less than 200:
 ```
      elif x < 200:
            r1 = 0
            motorxR.on()
            motorxB.off()
            sleep(0.05)
            motorxR.off()
 ```
 If x position of face is in the center of the frame then we turn off the motor andset `r1 = 1` since we are aimed in the x direction. The following code does that.
 
```
       else:
            motorxR.off()
            motorxB.off()
            r1 = 1
```
Same process is done for the motor aiming the device in the y direction(the frame goes from 0 to 350)
```
       if y < 130:
            r2 = 0
            motoryB.on()
            motoryR.off()
            sleep(0.05)
            motoryB.off()  
            
        elif y > 220:
            r2 = 0
            motoryR.on()
            motoryB.off()
            sleep(0.05)
            motoryR.off()
        
        else:
            motoryR.off()
            motoryB.off()
            r2 = 1
```
Once the face is in the center of the frame in the y and x direction, the following code runs:
```
        if  r1 == 1 and r2 == 1:
            servo.mid()
            sleep(0.2)
            servo.value= -0.5
            sleep(0.2)
            servo.mid()
```
The above code tells the servo motor to switch between 2 positions: pulling the trigger and then moving away; similar motion to a finger pulling a trigger.
   
 
 
 
 
 
