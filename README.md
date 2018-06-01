# NCCNet
Template  matching  by  normalized  cross  correlation (NCC) is widely used for finding image correspondences. We improve the robustness of this algorithm by preprocessing images with "siamese" convolutional networks trained to maximize the contrast between NCC values of true and false matches.

## Usage
Start docker environment
```
git clone https://github.com/seung-lab/NCCNet
nvidia-docker run -it --net=host \
      -v $(pwd)/NCCNet:/NCCNet \
      davidbun/cavelab:stable-gpu bash
```

To generate data using Cloud-volume start docker
```
nvidia-docker run -it --net=host \
      -v $(pwd)/NCCNet:/NCCNet \
      -v /usr/people/$USER/.cloudvolume/secrets/:/root/.cloudvolume/secrets/ \
      davidbun/cavelab:stable-gpu bash
```

Then CD to the folder and install additional dependencies
```
cd /NCCNet
pip install -r requirements.txt
```

## Data Collection
For training the model you will require to have pairs of image-templates defined by TFRecords files in `/data/tf/train.tfrecords`. If you are part of seunglab you can find example file here `seungmount/research/davit/NCCNet/data/tf/bad_trainset_24000_612_324.tfrecords`

To prepare your own data, open `hparams.json` file and modify section `"preprocessing"`:
`cloud_src`, `cloud_mip`, and `features`. Most important is to define image feature size. `in_width` is the width the image is generated. `width` is for training cropping only.

```
"features": {
    "data": {
      "search_raw": {"in_width": 512, "width": 384, "depth":1},
      "template_raw": {"in_width": 256, "width": 128, "depth":1}
    }, "type": "dict", "description": "Structure of input features"}
```

Run the following command to start data collection process. There are two additional parameters you would like to adjust.
```
mkdir data; mkdir data/tf; mkdir dump;
python src/prepare_data.py
```
To follow the process of data collection, `dump/{image, ncc, small_template, template }.jpg` files will get updated

## Train
To train the model defined in `hparams.json` run the following code. Parameters in the json file are self-explanatory.

```
python src/train.py
```
Logs and trained models will appear in `logs/` folder. Please change name in `hparams.json` for each experiment to log with different names, otherwise it will throw an error.


## Data
