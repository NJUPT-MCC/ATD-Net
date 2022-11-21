## Adaptive Text Denoising Network for Image Caption Editing
This contains the source code for [Adaptive Text Denoising Network for Image Caption Editing], to appear at TOMM 2022

### Requirements
- Python 3.6 or 3.7
- PyTorch 1.2

For evaluation, you also need:
- Java 1.8.0 
- [coco-api](https://github.com/cocodataset/cocoapi) 
- [cococaption python 3](https://github.com/ruotianluo/coco-caption)
- [cider](https://github.com/ruotianluo/cider) 

### Download and Prepare Features
In this work, we use 36 fixed [bottom-up features](https://github.com/peteanderson80/bottom-up-attention). If you wish to use the adaptive features (10-100), please refer to `adaptive_features` folder in this repository and follow the instructions. 

First, download the fixed features from [here](https://imagecaption.blob.core.windows.net/imagecaption/trainval_36.zip) and unzip the file. Place the unzipped folder in `bottom-up_features` folder.  

Next type this command: 
```bash
python bottom-up_features/tsv.py
```

This command will create the following files:
<ul>
<li>An HDF5 file containing the bottom up image features for train and val splits, 36 per image for each split, in an (I, 36, 2048) tensor where I is the number of images in the split.</li>
<li>PKL files that contain training and validation image IDs mapping to index in HDF5 dataset created above.</li>
</ul>

### Download/Prepare Caption Data
You can either download all the related caption data files from [here](https://pan.baidu.com/s/1k9eNw7fggLD9imo3d3IktQ)(Extraction code: v3fa) or create them yourself. The folder contains the following:
-  `WORDMAP_coco`: maps the words to indices 
- `CAPUTIL`: stores the information about the existing captions in a dictionary organized as follows: `{"COCO_image_name": {"caption": "existing caption to be edited", "encoded_previous_caption": an encoded list of the words, "previous_caption_length": a list contaning the length of the caption, "image_ids": the COCO image id}`
- `CAPTIONS` the encoded ground-truth captions (a list with `number_images x 5` lists. Example: we have 113,287 training images in Karpathy Split, thereofre there is 566,435 lists for the training split)
- `CAPLENS`: the length of the ground-truth captions (a list with `number_images x 5` vallues)
- `NAMES`: the COCO image name in the same order as the CAPTIONS
- `GENOME_DETS`: the splits and image ids for loading the images in accordance to the features file created above

If you'd like to create the caption data yourself, download [Karpathy's Split](http://cs.stanford.edu/people/karpathy/deepimagesent/caption_datasets.zip) training, validation, and test splits. This zip file contains the captions. Place the file in `caption data` folder. You should also have the `pkl` files created from the 'Download Features' section: `train36_imgid2idx.pkl` and `val36_imgid2idx.pkl`.

Next, run: 
```bash
python preprocess_caps.py
```
This will dump all the files to the folder `caption data`. 

Next, download the existing captios to be edited, and organize them in a list containing dictionaries with each dictionary in the following format: `{"image_id": COCO_image_id, "caption": "caption to be edited", "file_name": "split\\COCO_image_name"}`. For example: `{"image_id": 522418, "caption": "a woman cutting a cake with a knife", "file_name": "val2014\\COCO_val2014_000000522418.jpg"}`. In our work, we use the captions produced by [AoANet](https://github.com/husthuaan/AoANet).

Next, run: 
```bash
python preprocess_existing_caps.py
```
This will dump all the existing caption files to the folder `caption data`.

### Prepare/Download Sequence-Level Training Data
Download the RL-data for sequence-level training used for computing metric scores from [here](https://pan.baidu.com/s/1j8OkPbwkJnJzoPU4O03dBg)(Extraction code: eh81). 

Alternitavely, you may prepare the data yourself: 

Run the following command:
```bash
python preprocess_rl.py
```
This will dump two files in the `data` folder used for computing metric scores.

### Training
##### XE training stage: 

For training ATDNet:
```bash
python ATDNet.py
```

##### Cider-D Optimization stage:

For training ATDNet:
```bash
python ATDNet_rl.py
```

### References
Our code is mainly based on [show edit and tell](https://github.com/fawazsammani/show-edit-tell) and [self-critical](https://github.com/ruotianluo/self-critical.pytorch) and [show attend and tell](https://github.com/sgrvinod/a-PyTorch-Tutorial-to-Image-Captioning). We thank both authors.