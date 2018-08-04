# Introduction to OpenCV
## Description 
A small project which does face detection using OpenCV library on RaspberryPi.
## Content
1. [What's Raspberry pi?](#rpi)
2. [Face Recognition?](#facerecognition)
3. [OpenCV Library?](#opencv)
4. [Project](#project)
   * [Requirements](#requirements)
   * [Procedure](#procedure)
<a name="rpi"> </a>   
## What's Raspberry pi?
> Raspberry pi is a mini computer(credit card sized), which is capable of doing various tasks but with less computation power as it doesn't have powerful resources. 
[Explore more](https://www.raspberrypi.org/)
<a name="facerecognition"> </a>
## what is Face Recognition?
> Face Recognition/Facial Recognition is a category of biometric software which identifies people by their faces. Face is captured by digital camera and the system is trained and then it is capable of identifying the person.
<a name="opencv"></a>
## OpenCV Library
> OpenCV (Open Source Computer Vision Library) is an open source computer vision and machine learning software library. OpenCV was built to provide a common infrastructure for computer vision applications and to accelerate the use of machine perception in the commercial products. 
[Read more](https://opencv.org)
## Project
> In this project we will learn how to detect real-time faces on Picam.
<a name="requirements"> </a>
### Stuff used in this projects
1. [Raspberry Pi 3 Model-B](https://www.raspberrypi.org/products/raspberry-pi-3-model-b/)
2. [Raspberry Pi camera module](https://www.amazon.in/gp/product/B00L1FOIIS/ref=oh_aui_detailpage_o07_s00?ie=UTF8&psc=1)
### Softwares
1. [OpenCV Library](https://opencv.org)
2. [Python3](https://www.python.org)
3. [TextEditor](https://atom.io)

## 1] Introduction :
>This project was done with "Open Source Computer Vision Library", the OpenCV. In this project, we will be using Raspberry Pi (so, Raspbian as OS) and Python.OpenCV was designed for computational efficiency and with a strong focus on real-time applications. So, it's perfect for real-time face recognition using a camera.

## 2] Project Structure :
1. Face Detection and Data Gathering.
2. Train the System.
3. Face Recognition.
<a name="procedure"></a>
## 3] Procedure :
**Step 1: Installing OpenCV library**
> I am using a Raspberry Pi V3 updated to the last version of Raspbian (Stretch), so the best way to have OpenCV installed, is to follow the awesome tutorial developed by Adrian Rosebrock: [Raspbian Stretch: Install OpenCV 3 + Python on your Raspberry Pi](https://www.pyimagesearch.com/2017/09/04/raspbian-stretch-install-opencv-3-python-on-your-raspberry-pi/)

> Once you finished the tutorial, you should have an OpenCV virtual environment ready to run our experiments on your Pi.
Let's go to our virtual environment and confirm that OpenCV 3 is correctly installed.

Run the command `source` each time you open up a new terminal to ensure your system variables have been set up correctly.  
`source ~/.profile` 

Next, let's enter on our virtual environment:  
`workon cv`

If you see the text (cv) preceding your prompt, then you are in the cv virtual environment:  
`(cv) pi@raspberry:~$`

Now, enter in your Python interpreter :  
`python`

then import the OpenCV library :  
`>>import cv2`

If no error messages appear, the OpenCV is correctly installed ON YOUR PYTHON VIRTUAL ENVIRONMENT.

You can also check the OpenCV version installed:  
`cv2.__version__`

**Step 2: Testing Camera**
> Once you have OpenCV installed in your Raspberry Pi, let's test to confirm that your camera is working properly.
I am assuming that you have a PiCam already installed on your Raspberry Pi.

> You must have the camera enabled when you ran through Adrian's tutorial, otherwise, the drivers will not be installed correctly.

In case you get an error like: OpenCV Error: Assertion failed , you can try solve the issue, using the command:  
`sudo modprobe bcm2835-v4l2`

Once you have all drivers correctly installed, enter the below Python code on your IDE or any Text Editor :  
```python
import numpy as np
import cv2
cap = cv2.VideoCapture(0)
cap.set(3,640) # set Width
cap.set(4,480) # set Height
while(True):
    ret, frame = cap.read()
    if ret:
        gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
        cv2.imshow('frame', frame)
        cv2.imshow('gray', gray)

        k = cv2.waitKey(30) & 0xff
    if k == 27: # press 'ESC' to quit
        break
cap.release()
cv2.destroyAllWindows()
```
The above code will capture the video stream that will be generated by your PiCam, displaying both, in BGR color and Gray mode.
You can download the code [CamTest.py](CamTest.py) from above as well.
Now, Run the program :  
`python CamTest.py`

Some may found issues when trying to open the camera ( "Assertion failed" error messages). That could happen if the camera was not enabled during OpenCv installation and so, camera drivers did not install correctly. To correct, use the command:  
`sudo modprobe bcm2835-v4l2`

**Step 3: Face Detection**
> The most basic task on Face Recognition is of course, "Face Detecting". Before anything, you must "capture" a face in order to recognize it, when compared with a new face captured on future.

> The most common way to detect a face (or any objects), is using the "Haar Cascade classifier"

> Object Detection using Haar feature-based cascade classifiers is an effective object detection method proposed by Paul Viola and Michael Jones in their paper, "Rapid Object Detection using a Boosted Cascade of Simple Features" in 2001. It is a machine learning based approach where a cascade function is trained from a lot of positive and negative images. It is then used to detect objects in other images.

> Here we will work with face detection. Initially, the algorithm needs a lot of positive images (images of faces) and negative images (images without faces) to train the classifier. Then we need to extract features from it. The good news is that OpenCV comes with a trainer as well as a detector. If you want to train your own classifier for any object like car, planes etc. you can use OpenCV to create one. Its full details are given here: [Cascade Classifier Training.](https://docs.opencv.org/3.3.0/dc/d88/tutorial_traincascade.html)

Enough theory, let's create a face detector with OpenCV!

Download the file: [FaceDetection.py](FaceDetection.py)

```python
import numpy as np
import cv2
faceCascade = cv2.CascadeClassifier('/home/pi/opencv-3.4.1/data/haarcascades/haarcascade_frontalface_default.xml')
cap = cv2.VideoCapture(0)
cap.set(3,640) # set Width
cap.set(4,480) # set Height
while True:
    ret, img = cap.read()
    gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
    faces = faceCascade.detectMultiScale(
        gray,
        scaleFactor=1.2,
        minNeighbors=5,
        minSize=(20,20)
    )
    for (x,y,w,h) in faces:
        cv2.rectangle(img,(x,y),(x+w,y+h),(255,0,0),2)
        roi_gray = gray[y:y+h, x:x+w]
        roi_color = img[y:y+h, x:x+w]
    cv2.imshow('video',img)
    k = cv2.waitKey(30) & 0xff
    if k == 27: # press 'ESC' to quit
        break
cap.release()
cv2.destroyAllWindows()
```

When you compare with the last code used to test the camera, you will realize that few parts were added to it. Note the line below:  
`faceCascade = cv2.CascadeClassifier('Cascades/haarcascade_frontalface_default.xml')`  
This is the line that loads the "classifier" (that must be in a directory named "Cascades/", under your project directory).

Then, we will set our camera and inside the loop, load our input video in grayscale mode (same we saw before).

Now we must call our classifier function, passing it some very important parameters, as scale factor, number of neighbors and minimum size of the detected face.

```python
faces = faceCascade.detectMultiScale(
        gray,     
        scaleFactor=1.2,
        minNeighbors=5,     
        minSize=(20, 20)
    )
```

Where,

`* gray` is the input grayscale image.  
`* scaleFactor` is the parameter specifying how much the image size is reduced at each image scale. It is used to create the scale pyramid.  
`* minNeighbors` is a parameter specifying how many neighbors each candidate rectangle should have, to retain it. A higher number gives lower false positives.  
`* minSize` is the minimum rectangle size to be considered a face.  

The function will detect faces on the image. Next, we must "mark" the faces in the image, using, for example, a blue rectangle. This is done with this portion of the code:  
```python
for (x,y,w,h) in faces:
    cv2.rectangle(img,(x,y),(x+w,y+h),(255,0,0),2)
    roi_gray = gray[y:y+h, x:x+w]
    roi_color = img[y:y+h, x:x+w]
```
If faces are found, it returns the positions of detected faces as a rectangle with the left up corner (x,y) and having "w" as its Width and "h" as its Height ==> (x,y,w,h).

Now, run the above python Script on your python environment, using the Raspberry Pi Terminal:  
`python FaceDetection.py`

After executing the above code you will be able to see a window popping which includes your face.

You can also include classifiers for "eyes detection" or even "smile detection". On those cases, you will include the classifier function and rectangle draw inside the face loop, because would be no sense to detect an eye or a smile outside of a face.

> [* FaceEyeDetection.py](FaceEyeDetection.py)  
> [* FaceSmileDetection.py](FaceSmileDetection.py)  
> [* FaceEyeSmileDetection.py](FaceEyeSmileDetection.py)  

**Step 4: Data Gathering**
> Let's start the first phase of our project. What we will do here, is starting from Face Detecting, we will simply create a dataset, where we will store for each id, a group of photos in gray with the portion that was used for face detecting.

First, create a directory where you develop your project, for example, FaceRecognition:  
`mkdir FaceRecognition`

In this directory, besides the 3 python scripts that we will create for our project, we must have saved on it the Facial Classifier. You can download it from above: [haarcascade_frontalface_default.xml](haarcascade_frontalface_default.xml)

Next, create a subdirectory where we will store our facial samples and name it "dataset":  
`mkdir dataset`

> Download [01_face_dataset.py](01_face_dataset.py)

```python
import cv2
import os
cam = cv2.VideoCapture(0)
cam.set(3, 640) # set video width
cam.set(4, 480) # set video height
face_detector = cv2.CascadeClassifier('haarcascade_frontalface_default.xml')
# For each person, enter one numeric face id
face_id = input('\n Enter user ID end press <Enter> ==>  ')
print("\n Initializing face capture. Look the camera and wait ...")
# Initialize individual sampling face count
count = 0
while(True):
    ret, img = cam.read()
    gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
    faces = face_detector.detectMultiScale(gray, 1.3, 5)
    for (x,y,w,h) in faces:
        cv2.rectangle(img, (x,y), (x+w,y+h), (255,0,0), 2)
        count += 1
        # Save the captured image into the datasets folder
        cv2.imwrite("dataset/User." + str(face_id) + '.' + str(count) + ".jpg", gray[y:y+h,x:x+w])
        cv2.imshow('image', img)
    k = cv2.waitKey(100) & 0xff # Press 'ESC' for exiting video
    if k == 27:
        break
    elif count >= 30: # Take 30 face sample and stop video
         break
# Do a bit of cleanup
print("\n [INFO] Exiting Program and cleanup stuff")
cam.release()
cv2.destroyAllWindows()
```
The code is very similar to the code that we saw for face detection. What we added, was an "input command" to capture a user id, that should be an integer number (1, 2, 3, etc)  
`face_id = input('\n enter user id end press  ==>  ')`

And for each one of the captured frames, we should save it as a file on a "dataset" directory:
`cv2.imwrite("dataset/User." + str(face_id) + '.' + str(count) + ".jpg", gray[y:y+h,x:x+w])`  

Note that for saving the above file, you must have imported the library "os". Each file's name will follow the structure:
`User.face_id.count.jpg`  

For example, for a user with a face_id = 1, the 4th sample file on dataset/ directory will be something like:  
`User.1.4.jpg`

In code, you guys can see the there's a `count` variable which counts no. of images captured. It is set to `30` , the more you capture the more accuratly system will work.

**Step 6: Trainer**
> On this second phase, we must take all user data from our dataset and "trainer" the OpenCV Recognizer. This is done directly by a specific OpenCV function. The result will be a `.yml` file that will be saved on a "trainer/" directory.

So, let's create trainer directory first:  
`mkdir trainer`

Download [02_face_training.py](02_face_training.py)  
```python
import cv2
import numpy as np
from PIL import Image
import os
# Path for face image database
path = 'dataset'

os.chdir("/home/pi/opencv-3.4.1/data/haarcascades")
recognizer = cv2.face.LBPHFaceRecognizer_create()
detector = cv2.CascadeClassifier("/home/pi/opencv-3.4.1/data/haarcascades/haarcascade_frontalface_default.xml");
# function to get the images and label data
def getImagesAndLabels(path):
    imagePaths = [os.path.join(path,f) for f in os.listdir(path)]
    faceSamples=[]
    ids = []
    for imagePath in imagePaths:
        PIL_img = Image.open(imagePath).convert('L') # convert it to grayscale
        img_numpy = np.array(PIL_img,'uint8')
        id = int(os.path.split(imagePath)[-1].split(".")[1])
        faces = detector.detectMultiScale(img_numpy)
        for (x,y,w,h) in faces:
            faceSamples.append(img_numpy[y:y+h,x:x+w])
            ids.append(id)
    return faceSamples,ids
print ("\nTraining faces. It will take few seconds. Wait ...")
faces,ids = getImagesAndLabels(path)
recognizer.train(faces, np.array(ids))
# Save the model into trainer/trainer.yml
recognizer.write('/home/pi/FaceRecognition/trainer/trainer.yml')
# Print the numer of faces trained and end program
print("\n [INFO] {0} faces trained. Exiting Program".format(len(np.unique(ids))))
```
Confirm if you have the PIL library installed on your Raspberry Pi. If not, run the below command in Terminal:  
`pip install pillow`

We will use as a recognizer, the LBPH (LOCAL BINARY PATTERNS HISTOGRAMS) Face Recognizer, included in OpenCV package. This can be done by following line:  
`recognizer = cv2.face.LBPHFaceRecognizer_create()`

The function "getImagesAndLabels(path)", will take all photos on directory: "dataset/", returning 2 arrays: "Ids" and "faces". With those arrays as input, we will "train our recognizer":  
`recognizer.train(faces, ids)`

As a result, a file named "trainer.yml" will be saved in the trainer directory that was previously created by us.  
>_Note: Make sure that whenever you collect dataset i.e run program 1, you must run program 2 as well to train Rpi._  

**Step 6: Recognizer**
> Now, we reached the final phase of our project. Here, we will capture a fresh face on our camera and if this person had his face captured and trained before, our recognizer will make a "prediction" returning its id and an index, shown how confident the recognizer is with this match.

 Download [03_face_recognition.py](03_face_recognition.py)  
```python
import cv2
import numpy as np
import os

os.chdir("/home/pi/opencv-3.4.1/data/haarcascades")
recognizer = cv2.face.LBPHFaceRecognizer_create()
recognizer.read('/home/pi/FaceRecognition/trainer/trainer.yml')
cascadePath = "/home/pi/opencv-3.4.1/data/haarcascades/haarcascade_frontalface_default.xml"
faceCascade = cv2.CascadeClassifier(cascadePath);

font = cv2.FONT_HERSHEY_SIMPLEX

#iniciate id counter
id = 0

# names related to ids: example ==> KUNAL: id=1,  etc
names = ['None', 'Kunal', 'Kaushik', 'Tushar', 'X', 'Y' , 'Z']

# Initialize and start realtime video capture
cam = cv2.VideoCapture(0)
cam.set(3, 640) # set video widht
cam.set(4, 480) # set video height

# Define min window size to be recognized as a face
minW = 0.1*cam.get(3)
minH = 0.1*cam.get(4)

while True:
    ret, img =cam.read()
    #img = cv2.flip(img, -1) # Flip vertically
    gray = cv2.cvtColor(img,cv2.COLOR_BGR2GRAY)

    faces = faceCascade.detectMultiScale(
        gray,
        scaleFactor = 1.2,
        minNeighbors = 5,
        minSize = (int(minW), int(minH)),
       )

    for(x,y,w,h) in faces:
        cv2.rectangle(img, (x,y), (x+w,y+h), (0,255,0), 2)
        id, confidence = recognizer.predict(gray[y:y+h,x:x+w])

        # Check if confidence is less them 100 ==> "0" is perfect match
        if (confidence < 100):
            id = names[id]
            confidence = "  {0}%".format(round(100 - confidence))
        else:
            id = "unknown"
            confidence = "  {0}%".format(round(100 - confidence))

        cv2.putText(img, str(id), (x+5,y-5), font, 1, (255,255,255), 2)
        cv2.putText(img, str(confidence), (x+5,y+h-5), font, 1, (255,255,0), 1)

    cv2.imshow('camera',img)

    k = cv2.waitKey(10) & 0xff # Press 'ESC' for exiting video
    if k == 27:
        break

# Do a bit of cleanup
print("\n Exiting Program")
cam.release()
cv2.destroyAllWindows()
```
We are including here a new array, so we will display "names", instead of numbered ids:

names = ['None', 'Kunal', 'Kaushik', 'Tushar', 'X', 'Y' , 'Z']
So, for example: Kunal will be the user with id = 1; Kaushik: id=2, etc.

Next, we will detect a face, same we did before with the haarCascade classifier. Having a detected face we can call the most important function in the above code:  
`id, confidence = recognizer.predict(gray portion of the face)`

The `recognizer.predict ()`, will take as a parameter a captured portion of the face to be analyzed and will return its probable owner, indicating its id and how much confidence the recognizer is in relation with this match.

>_Note that the confidence index will return **"zero"** if it will be cosidered a perfect match_

**Below is the final result image, you too should get similar output**

<div style="align:center">
  <img src ="/images/result.jpg"/>
</div>

For more projects, follow me on github [Kunal Yelne](https://github.com/kunalyelne):+1:  
Thankyou :heart:

## Credits
[Kunal Yelne](https://github.com/kunalyelne)  
2020 Batch  
[Indian Institute of Information Technology Nagpur.](https://github.com/iiit-nagpur)
  
