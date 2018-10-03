This folder contains the data stored in tfrecords:

It has the following folders:
- celebA_NCHW, contains 9 tfrecord files: celebA_000.tfrecords, ..., celebA_008.tfrecords
- cifar_NCHW, contains 2 tfrecord files: cifar.tfrecords, cifar_xy.tfrecords (which has labels)
- lsun_NCHW, contains 61 tfrecord files: lsun_000.tfrecords, ..., lsun_060.tfrecords
- stl_NCHW, contains 1 tfrecord file: stl.tfrecord
- imagenet_NCHW, contains 1000 tfrecord files: imagenet_000.tfrecords, ..., imagenet_999.tfrecords

To obtain the corresponding files, consider using the following codes:
```python
# add this before each block
from GeneralTools.misc_fun import FLAGS
FLAGS.DEFAULT_DOWNLOAD = data_download_folder
```
# cifar
```python
from GeneralTools.input_func import binary_image_to_tfrecords
filename = ['cifar/cifar_{}'.format(i) for i in range(1, 6)]
binary_image_to_tfrecords(
    filename, 'cifar_NCHW/cifar', 50000, [3, 32, 32], num_labels=1,
    image_format_in_file='NCHW', target_image_format='NCHW', save_label=False)
```
    
# stl
```python
from GeneralTools.input_func import binary_image_to_tfrecords
binary_image_to_tfrecords(
    'stl10/unlabeled_X', 'stl_NCHW/stl', 100000, [3, 96, 96],
    num_labels=0, resize=(48, 48), image_transpose=True,
    image_format_in_file='NCHW', target_image_format='NCHW')
```
# celebA 
```python
# original image size 178*218, number 22511*9
from GeneralTools.input_func import raw_image_to_tfrecords
image_folder = 'celebA/img_align_celeba_png'
output_filename = 'celebA_NCHW/celebA'
raw_image_to_tfrecords(
    image_folder, output_filename, resize=(72, 88), crop=(64, 64),
    num_images_per_tfrecord=22511, image_format='NCHW')
```
# lsun bedroom 
```python
# original image size 225*? or ?*225, number 3033042
from GeneralTools.input_func import raw_image_to_tfrecords
image_folder = 'LSUN/tr'
output_filename = 'lsun_NCHW/lsun'
raw_image_to_tfrecords(
    image_folder, output_filename, resize=(64, 64), crop=(64, 64),
    image_file_extension='webp', num_images_per_tfrecord=49722, image_format='NCHW')
```
# imagenet 
Original image size variable, number 1281167, class 1000
- Download in terminal, took around 5 days for me
```
wget -P "data_download_folder/ImageNet"
"http://www.image-net.org/challenges/LSVRC/2012/nnoupb/ILSVRC2012_img_train.tar"
```
- Extract .tar file into images (under 1000 folders), it took 4:04:59 to extract
```python
from Code.ImageNet.imagenet import extract_train
tar_name = 'data_download_folder/ImageNet/ILSVRC2012_img_train.tar'
target_dir = 'data_download_folder/ImageNet/train_jpeg'
extract_train(tar_name, target_dir)
```
- convert images to tfrecords, it took 5:45:09 to write tfrecord
```python
# to create 126 tfrecords with roughly equal size, set num_images_per_tfrecord=10168
- set num_images_per_tfrecord=None will produce one tfrecord per class
from GeneralTools.misc_fun import FLAGS
FLAGS.DEFAULT_DOWNLOAD = 'data_download_folder'
FLAGS.DEFAULT_IN = 'data_folder/imagenet_NCHW'
from GeneralTools.input_func import raw_image_labels_to_tfrecords
parent_folder = 'ImageNet/train_jpeg'
output_filename = 'imagenet'
raw_image_labels_to_tfrecords(
    parent_folder, output_filename, resize=(128, 128), crop=(128, 128),
    image_file_extension='JPEG', num_images_per_tfrecord=None, image_format='NCHW')
```
Hope these help.
