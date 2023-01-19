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
 
