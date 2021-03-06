# README #

### NEWS
| Date       |                                                         News                                                                     |     Version       |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------- | ----------------- |
|April 2019 | yolo v3 added to simple tensor     |      v0.5.0       |


### Model Info

| Lib         |     Model                                                             |     Size          | Version       | INFO |
| ----------- | --------------------------------------------------------------------- | ----------------- | --------------|------|
|  darknet    | yolo v3 .weigth original (Big)                                        |      200 MB       | v0.5.0        |
|  tensorflow | yolo v3 (.data, .index, .meta) (Big) [Download](https://drive.google.com/file/d/1Rsxgtngy8Gl0N7UFdbjSFf1Bvh5U89tQ/view?usp=sharing) | 400 MB | >=v0.5.0 | you can use it directly |
|  tensorflow | yolo v3 (.data, .index, .meta) (Medium) [Download](https://drive.google.com/file/d/1yVRtJO0TRyer_fJIAayejzpYiG2NTLVj/view?usp=sharing)| 234 MB | >=v0.5.0 | you mast retraint it first (truncated from big architecture) |
|  tensorflow | yolo v3 (.data, .index, .meta) (Small) [Download](https://drive.google.com/file/d/1qqkjAtgJwYT4utrp_Dwu1YS1fLQ4Tka0/view?usp=sharing) | 187 MB | >=v0.5.0 | you mast retraint it first (truncated from big architecture)|
|  tensorflow | yolo v3 (.data, .index, .meta) (Very Small)[Download](https://drive.google.com/file/d/1vCDeUE9qQ_IozK5IepNzTdCdbQX8u2Cy/view?usp=sharing)|16.6 MB| >=v0.5.0 | you mast retraint it first (truncated from big architecture) |
|  tensorflow | yolo v3 (.data, .index, .meta) (Special)[Download](https://drive.google.com/file/d/1It4CyR_yqOcz3jUUru3qCHxL3rP2qlR7/view?usp=sharing)|7.5 MB | >=v0.5.0 | you mast retraint it first (truncated from big architecture) |


### Inferencing Example
```python
import cv2
import tensorflow as tf
from simple_tensor.object_detector.yolo import *


def draw_rect(bbox, img):
    for i in bbox:
        img = cv2.rectangle(img, (i[0], i[1]), (i[2] + i[0], i[3]+i[1]), (255,255,0), 2)
    return img


simple_yolo = Yolo(num_of_class=80,
                    objectness_loss_alpha=10., 
                    noobjectness_loss_alpha=0.1, 
                    center_loss_alpha=10., 
                    size_loss_alpha=10., 
                    class_loss_alpha=10.,
                    add_modsig_toshape=True,
                    dropout_rate = 0.2,
                    convert_to_tflite=False) 

simple_yolo.build_net(input_tensor=simple_yolo.input_placeholder, 
                      is_training=False, 
                      network_type='big') 
# --------------------------------- #
# IMPORTANT INFO ....
# we provides 5 tipes of architecture 
# you can choose one of it
# big => 'big'
# medium => 'medium'
# small => 'small'
# very small => 'very_small'
# special => 'special' (special for tf lite)
# --------------------------------- #


# => if you are using big architecture
saver_all = tf.train.Saver(simple_yolo.yolo_big_vars)
# => if you are using medium architecture
# saver_all = tf.train.Saver(simple_yolo.yolo_medium_vars)
# => if you are using small architecture
# saver_all = tf.train.Saver(simple_yolo.yolo_small_vars)
# => if you are using very small architecture
# saver_all = tf.train.Saver(simple_yolo.yolo_very_small_vars)
# => if you are using medium architecture
# saver_all = tf.train.Saver(simple_yolo.yolo_special_vars)
session = tf.Session()
session.run(tf.global_variables_initializer())
saver_all.restore(session, 'models/yolov3')    # path to your downloaded model

# start inferencing
img = cv2.imread('sample_image/dog.jpg')
img = cv2.resize(img, (416, 416)).reshape((1, 416, 416, 3))
img = img.astype(np.float32)/255.

detection_result = sess.run(simple_yolo.boxes_dicts, feed_dict={simple_yolo.input_placeholder: img})
bboxes = simple_yolo.nms(detection_result, 0.8, 0.1) #[[x1, y1, w, h], [...]]

# show image
img = draw_rect(bboxes, img[0])
cv2.imshow('test', img)
cv2.waitKey(10000)
```

For more examples, see [here](https://github.com/fatchur/Simple-Tensor/tree/master/example)

### Training Example
see the example [here](https://github.com/fatchur/Simple-Tensor/tree/master/example)





