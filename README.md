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
 If `len(faces) == 0` this means that no face is detected and so we make sure that all motors stop running, as well as `r1` and `r2` which are out ready signals that we are aimed at the face.
 
 
