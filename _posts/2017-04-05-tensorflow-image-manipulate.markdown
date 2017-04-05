---
layout: post
title:  "Process Png with Tensorflow"
categories: "Tensorflow Image"
---

It is easy to process an image, either a Png or an Jpeg with Tensorflow library. Operations such as grayscale, crop, resize, rotate and flip are built into the Tensorflow image API.

```python
import tensorflow as tf
import matplotlib.image as mpimg
import matplotlib.pyplot as plt

class PngReader(object):

    def __init__(self, file):
        with open(file, 'rb') as f:
            self.image = f.read()

    def size(self):
        contents = tf.placeholder(tf.string)
        image = tf.image.decode_png(contents)
        shape_image = tf.shape(image)

        result = self._run(shape_image, contents)
        print(result)

    def grayscale(self):
        contents = tf.placeholder(tf.string)
        image = tf.image.decode_png(contents, channels=1)
        gray_image = tf.squeeze(image)

        result = self._run(gray_image, contents)
        self._show(result, cmap='gray')

    def crop(self, top, left, width, height):
        contents = tf.placeholder(tf.string)
        image = tf.image.decode_png(contents)
        crop_image = tf.image.crop_to_bounding_box(image, top, left, height, width)

        result = self._run(crop_image, contents)
        self._show(result)

    def rotate_right_90(self):
        self._rotate(3)

    def rotate_left_90(self):
        self._rotate(1)

    def rotate_180(self):
        self._rotate(2)

    def flip_vertical(self):
        contents = tf.placeholder(tf.string)
        image = tf.image.decode_png(contents)
        flip_image = tf.image.flip_up_down(image)

        result = self._run(flip_image, contents)
        self._show(result)

    def flip_horizontal(self):
        contents = tf.placeholder(tf.string)
        image = tf.image.decode_png(contents)
        flip_image = tf.image.flip_left_right(image)

        result = self._run(flip_image, contents)
        self._show(result)

    def _rotate(self, angle_90):
        contents = tf.placeholder(tf.string)
        image = tf.image.decode_png(contents)
        rotate_image = tf.image.rot90(image, angle_90)

        result = self._run(rotate_image, contents)
        self._show(result)

    def _run(self, fetches, tensor):
        with tf.Session() as session:
            result = session.run(fetches,
                                 feed_dict={tensor: self.image})
        return result

    def _show(self, image_data, cmap=None):
        plt.axis('off')
        plt.imshow(image_data, cmap=cmap)
        plt.show()
```