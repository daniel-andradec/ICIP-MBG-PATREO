# ICIP-MBG-PATREO
Entry to [ICIP Grand Challenge 2023](https://www02.smt.ufrj.br/~tvdigital/mosquito/challenge/) - Automatic Detection of Mosquito Breeding Grounds. This system was developed at [PATREO - Pattern Recognition and Earth Observation Laboratory](http://patreo.dcc.ufmg.br), as part of the WildPixels project funded by Serrapilheira. To know more, visit the [project website](http://patreo.dcc.ufmg.br/2021/09/01/wildpixels/).

### RESULTS
We won the 1st place in the challenge in Malasya! Check it out [here](https://dcc.ufmg.br/alunos-e-professor-do-dcc-vencem-challenge-promovido-durante-o-icip-2023/).
![Alt text](ICIP-Certificate.jpeg)
## Environment Setup
This setup adapts the set of recommendations from the original [yolo repository](https://github.com/WongKinYiu/yolov7)

```python
# create the docker container, you can change the share memory size if you have more.
docker run --gpus all --name yolov7_mbg -it -v dataset_path/:/dataset/ -v code_path/:/src --shm-size=2g nvcr.io/nvidia/pytorch:21.08-py3

# go to source code folder (as defined while running the above container)
cd /src

# run setup script (installs required packages)
sh setup.sh
```

## How to run

### Formatting dataset
Run ```mbg2yolo.py``` referencing the path containing **only test samples**. The script will produce YOLO a formatted dataset from xml annotations provided. Assuming the docker enviroment suggested above, the dataset should be located at ```/dataset```. Output files will be stored at ```/src/data```.   

For example:
```
python mbg2yolo.py --path /dataset/test/
````

> Note that we are assuming folder structure as released by the challenge (```flight-mbgXY/ann/videoXY.xml```, ```flight-mbgXY/avi/videoXY.avi```) 

### Download trained weights

Weights file should be store inside folder "weights" of this repository. Assuming the docker environment suggested above, the weights should be located at ```/src/weights``` as follows: 
```
wget https://www.dropbox.com/s/9anb8hfsbyt9j6e/yolov7_mbg_adam_960_alltrain.pt -P /src/weights/
```

### Run model

```
python yolo/test.py --weights weights/yolov7_mbg_adam_960_alltrain.pt --data cfg/mbg_test.yaml --conf-thres 0.5 --iou-thres 0.5 --img-size 960 --device 0 --batch-size 16 --name mbg_test --verbose
```

Important parameters you may want to change: 
- --batch-size: size of each image batch (default: 32)
- --device: cuda device, i.e. 0 or 0,1,2,3 or cpu
- --project: root folder to save results (default: runs/test)
- --name: folder created to store results (within --project)

