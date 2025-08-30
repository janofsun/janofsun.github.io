---
title: Image preprocessing in Xception model
date: 2023-12-12 10:43:58
tags: 
- Deep Learning
- OpenCV
categories: 
- Tech
---
The image preprocessing process for the Xception model typically includes the following steps:
1. Size Adjustment:
    - The Xception model expects the input image size to usually be 299x299 pixels. 
2. Color Channel Processing:
    - The Xception model expects the input to be a color image, i.e., having 3 color channels (Red, Green, Blue). If your image is grayscale (single-channel), you need to convert it into a three-channel format.
<!--more-->
3. Pixel Value Normalization:
    - The preprocessing method used by Xception involves normalizing pixel values to the range of [-1, 1]. This is achieved by dividing the pixel values by 127.5 and then subtracting 1. 
    - When normalizing, it's important to ascertain the exact bit depth of the image and normalize accordingly. This is especially relevant for medical imaging or certain special image formats (like TIFF or DICOM) where the image data might not be standard 8-bit per channel.

<p align="center">
<img src="/img/image_processing_in_xception/RET002OS.jpg" width="50%" height="50%">
</p>

```Python
def load_image(image_path, size=(299, 299), to_grayscale = False):
    image = Image.open(image_path)
    image = image.resize(size)
    image_array = np.array(image, dtype=np.float32)
    max_value = np.max(image_array)
    if max_value > 255.0:
        image_array /= 65535.0
    else:
        image_array /= 255.0

    if len(image_array.shape) == 2 or (len(image_array.shape) == 3 and image_array.shape[2] == 1):
        # Add an additional dimension if the image is grayscale
        image_array = np.expand_dims(image_array, axis=-1)

    if image_array.shape[-1] == 1:
        # Convert grayscale to RGB
        image_tensor = tf.convert_to_tensor(image_array, dtype=tf.float32)
        image_array = tf.image.grayscale_to_rgb(image_tensor)

    # Subtracting the average color of the image
    mean_color = np.mean(image_array, axis=(0, 1), keepdims=True)
    adjusted_image = image_array - mean_color
```

<p align="center">
<img src="/img/image_processing_in_xception/rgb_torch_norm.png" width="50%" height="50%">
</p>

According to the official description of preprocessing in xception model, the normalization step is slightly different in tensorflow. 
- **caffe**: will convert the images from RGB to BGR, then will zero-center each color channel with respect to the ImageNet dataset, without scaling.
- **tf**: will scale pixels between -1 and 1, sample-wise.
- **torch**: will scale pixels between 0 and 1 and then will normalize each channel with respect to the ImageNet dataset.
The following code is modified from the built-in normalization function from Keras [tf.keras.applications.xception.preprocess_input](https://github.com/keras-team/keras-applications/blob/master/keras_applications/imagenet_utils.py)

```Python
image_array = np.array(image, dtype=np.float32)
max_value = np.max(image_array)
if max_value > 255.0:
    image_array /= 32767.5
else:
    image_array /= 127.5
image_array -= 1.
```

<p align="center">
<img src="/img/image_processing_in_xception/rgb_tf_norm.png" width="50%" height="50%">
</p>

Note that OpenCV reads in the image by BGR(Blue, Green, Red) order in default, which is different from PIL (Python Imaging Library) (i.e. RGB (Red, Green, Blue)).

```Python
# OpenCV
img = cv2.imread(image_path)
resized_image = cv2.resize(img, (299, 299))
resized_image = cv2.cvtColor(resized_image, cv2.COLOR_BGR2RGB)  # 转换为 RGB
resized_image = resized_image.astype('float32') / 127.5 - 1
```

<p align="center">
<img src="/img/image_processing_in_xception/opencv_imshow.png" width="100%" height="100%">
</p>

****

If we convert the above rgb image into grayscale format. This conversion is achieved by copying the content of the single grayscale channel into the three RGB channels. 
```Python
def rgb2grayscale(img):
    img_gray = tf.image.rgb_to_grayscale(img)
    img_gray_three_channel = tf.image.grayscale_to_rgb(img_gray)
    return img_gray_three_channel
```

<p align="center">
<img src="/img/image_processing_in_xception/rgb2grayscale.png" width="50%" height="50%">
</p>

****

When choosing to draw a single-channel grayscale image or one channel of a three-channel grayscale image, **pseudocolor** mapping is used to display the grayscale image. This is a common processing method that helps visually distinguish different levels of grayscale. Pseudocolor mapping is mainly used in the following situations:
- **Improving Visualization**
- **Data Analysis**
- **Data Augmentation**: we don't use this kind augmentation in general. This is because models typically need to learn from the original data, and pseudocolor mapping changes the original pixel values of the image, which may introduce unnecessary complexity or interfere with the learning of model features.

<p align="center">
<img src="/img/image_processing_in_xception/rgb2grayscale1channel.png" width="50%" height="50%">
</p>

#### References ⭐
- [Utilities for ImageNet data preprocessing & prediction decoding.](https://github.com/keras-team/keras-applications/blob/master/keras_applications/imagenet_utils.py)
- [Kaggle - ODIR5K: Models for predicting age and sex](https://www.kaggle.com/code/mateuszbagiski/odir5k-models-for-predicting-age-and-sex)