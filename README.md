## An Open-Source Tatar Speech Commands Dataset
### Paper
[An Open-Source Tatar Speech Commands Dataset for IoT and Robotics Applications](https://www.techrxiv.org/users/682600/articles/1231801-an-open-source-tatar-speech-commands-dataset-for-iot-and-robotics-applications)

### Dataset 
The dataset covers 35 commands used in robotics, IoT, and smart systems. In total, the dataset contains 3,547 one-second utterances from 153 people. The dataset can be downloaded from [Google Drive](https://drive.google.com/file/d/1CBmVeAYgNrkNKhL1wtG7KUKuLJ9hOfHL/view?usp=sharing).

### Data Augmentation
To preprocess and augment the dataset, you can use the ```data_preprocessing_augmentation.ipynb``` notebook. However, first you need to download the [ESC-50: Dataset for Environmental Sound Classification](https://github.com/karolpiczak/ESC-50) dataset.

### Model Training, Testing, and Optimization
In this project, we used [Keyword-MLP](https://github.com/AI-Research-BD/Keyword-MLP) model. We sincerely thank the authors for open sourcing their code. 

```
git clone https://github.com/IS2AI/TatarSCR.git
cd TatarSCR
cd Keyword-MLP
pip3 install -r requirements.txt
```

#### Training and Testing
```
python3 train.py --conf configs/kwmlp_tscd.yaml
```
The model automatically runs on the test set at the end of the training.  

#### Convert to ONNX 
```
python3 convert_to_onnx.py --conf configs/kwmlp_tscd.yaml \
                           --ckpt checkpoints/best.pth \
                           --out checkpoints/best.onnx
```
### Inference

#### PyTorch 
- `inference.py`: For short ~1s clips, like the audios in the Speech Commands dataset
- `window_inference.py`: For running inference on longer audio clips, where multiple keywords may be present. Runs inference on the audio in a sliding window manner.

```
python3 inference.py --conf configs/kwmlp_tscd.yaml \
                    --ckpt checkpoints/best.pth \
                    --inp <path to audio.wav / path to audio folder> \
                    --out <output directory> \
                    --lmap label_map.json \
                    --device cpu \ # change to cuda if you have a gpu
                    --batch_size 8   # should be possible to use much larger batches if necessary, like 128, 256, 512 etc.

python3 window_inference.py --conf configs/kwmlp_tscd.yaml \
                    --ckpt checkpoints/best.pth \
                    --inp <path to audio.wav / path to audio folder> \
                    --out <output directory> \
                    --lmap label_map.json \
                    --device cpu \ # change to cuda if you have a gpu
                    --wlen 1 \
                    --stride 0.5 \
                    --thresh 0.85 \
                    --mode multi
```
#### ONNX 
```
python3 onnx_inference.py --onnx_model checkpoints/best.onnx \
                          --conf configs/kwmlp_tscd.yaml \
                          --lmap label_map.json \
                          --inp <path to audio.wav / path to audio folder>
```
### In case of using our dataset/model please cite our work
```
Askat Kuzdeuov, Rinat Gilmullin, Bulat Khakimov, and Huseyin Atakan Varol. An Open-Source Tatar Speech Commands Dataset for IoT and Robotics Applications. TechRxiv. October 18, 2024,
DOI: 10.36227/techrxiv.172926779.98914732/v1.
```
