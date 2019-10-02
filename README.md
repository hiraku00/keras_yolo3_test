# To Do

- [Last time](https://github.com/hiraku00/ssd_keras_test) I tried to detect objects with SSD, but this time I tried to detect objects with YOLO v3

# Summary

# Procedure overview
- ### 1. Download the code
- ### 2. Download trained model
- ### 3. Model conversion
- ### 4. Correction of code
- ### 5. Code execution
- ### 6. Using the tiny model

# Operatiing Environment
- macOS Catalina 10.15 beta
- python 3.7.4
- tensorflow 1.14.0
- keras 2.2.4
- pillow 6.1.0


# 1. Download the code
- Download the keras version of yolov3 from below

```terminal
$ git clone https://github.com/qqwweee/keras-yolo3
```

# 2. Download trained model
- Get the trained model with the following command

```terminal
$ wget https://pjreddie.com/media/files/yolov3.weights
```

# 3. Model conversion
- Convert the acquired learned data for Keras. Enter the following command
- The trained data for Keras is now in the "model_data" folder (yolo.h5)

```terminal
$ python convert.py yolov3.cfg yolov3.weights model_data / yolo.h5
```

# 4. Code fixes
- When yolo_video.py is executed, the following error is output (can the file itself be output?)

```python:AttributeError
Traceback (most recent call last):
  File "yolo_video.py", line 75, in <module>
    detect_video(YOLO(**vars(FLAGS)), FLAGS.input, FLAGS.output)
  File "/Users/XXXXX/anaconda_project/keras-yolo3/yolo.py", line 191, in detect_video
    image = Image.fromarray(frame)
  File "/Users/XXXXX/anaconda3/envs/yolo_v3_keras/lib/python3.7/site-packages/PIL/Image.py", line 2642, in fromarray
    arr = obj.__array_interface__
AttributeError: 'NoneType' object has no attribute '__array_interface__'
```

- Add exception handling (when frame becomes NoneType) on line 191 as shown below

```python:yolo.py
# before
    while True:
        return_value, frame = vid.read()
        image = Image.fromarray(frame)

# after
    while True:
        return_value, frame = vid.read()
        if type(frame) == type(None): break
        image = Image.fromarray(frame)
```

# 5. Code execution
- Specify the video file with the following command and output the result of object detection

```terminal:code execution
$ python yolo_video.py --input AAA.mp4 --output AAA_out.mp4
```

# 6. Using the tiny model
- [Last time](https://qiita.com/hiraku00/items/6b89c3d54e278056df0a), it was the same when running SSD, but my environment is only running on CPU, so the execution speed is slow
- So use a light tiny model instead of a heavy full model
- The tiny model is a model with little yolo-v3 calculation, so it is easy to use even on the CPU
- The tiny model is less accurate, but faster
- Download and convert models in the same way as # 2 and # 3

```terminal
$ wget https://pjreddie.com/media/files/yolov3-tiny.weights
$ python convert.py yolov3-tiny.cfg yolov3-tiny.weights model_data/yolo-tiny.h5
```

- Also, copy yolo.py and create a new yolo_tiny.py.
- And change the path of the 23rd to 24th lines as follows.

```python:yolo_tiny.py
# before
        "model_path": 'model_data/yolo.h5',
        "anchors_path": 'model_data/yolo_anchors.txt',

# after
        "model_path": 'model_data/yolo-tiny.h5',
        "anchors_path": 'model_data/tiny_yolo_anchors.txt',
```

- In addition, copy yolo_video.py and create a new yolo_video_tiny.py
- Then change the import source on the third line from yolo.py to yolo_tiny.py as follows.

```python:yolo_video_tiny.py
# before
from yolo import YOLO, detect_video
# after
from yolo_tiny import YOLO, detect_video
```

- Now that you are ready to run, run the code like # 5

```terminal:code execution
$ python yolo_video_tiny.py --input AAA.mp4 --output AAA_tiny_out.mp4

# Result
- Since only CPU is used in my environment, both ssd executed last time and yolo executed this time were slow.
- The process using the tiny model is faster than the above, but the accuracy (bounding box part) is also worse.

# In Japanese
https://qiita.com/hiraku00/items/fe7d55b3ab556ff43786
