# Exploring Stroke-Level Modifications for Scene Text Editing

## Introduction
This is a pytorch implementation for paper [MOSTEL](https://arxiv.org/abs/2212.01982). It edits scene text at stroke level and can be trained using both labeled synthetic images and unpaired real scene text images.

## ToDo List

- [x] Release code
- [x] Release evaluation datasets
- [x] Document for Installation
- [x] Trained models
- [x] Document for training and testing 


## Installation

### Requirements
- Python==3.7
- Pytorch==1.7.1
- CUDA==10.1

```bash
https://github.com/qqqyd/MOSTEL.git
cd MOSTEL/

conda create --name MOSTEL python=3.7 -y
conda activate MOSTEL
pip install torch==1.7.1+cu101 torchvision==0.8.2+cu101 -f https://download.pytorch.org/whl/torch_stable.html
pip install mmcv-full==1.6.0 -f https://download.openmmlab.com/mmcv/dist/cu101/torch1.7/index.html
pip install -r requirements.txt
```

## Training
Prepare the datasets and put them in ```datasets/```. Our training data uses synthetic data generated by [SRNet-Datagen](https://github.com/youdao-ai/SRNet-Datagen) and real scene text datasets. You can download our datasets [here](https://rec.ustc.edu.cn/share/81ddc320-b05b-11ed-b4bc-5f690f426d88)(password: t6bq).

To get better performance, Background Reconstruction Module can be pre-trained on [SCUT-EnsText](https://github.com/HCIILAB/SCUT-EnsText), and recognizer can be pre-trained on 50k synthetic data generated by [SRNet-Datagen](https://github.com/youdao-ai/SRNet-Datagen). You can also use our [models](https://rec.ustc.edu.cn/share/56198940-b05c-11ed-a0b3-69e6f6e19d65)(password: 85b5).


```bash
python train.py --config configs/mostel-train.py
```

## Testing and evaluation
Prepare the models and put them in ```models/```. You can download our models [here](https://rec.ustc.edu.cn/share/56198940-b05c-11ed-a0b3-69e6f6e19d65)(password: 85b5).

Generating the predicted results using following commands:
```bash
python predict.py --config configs/mostel-train.py --input_dir datasets/evaluation/Tamper-Syn2k/i_s/ --save_dir results-syn2k --checkpoint models/mostel.pth --slm
python predict.py --config configs/mostel-train.py --input_dir datasets/evaluation/Tamper-Scene/i_s/ --save_dir results-scene --checkpoint models/mostel.pth --slm
```

For synthetic data, the evaluation metrics are MSE, PSNR, SSIM and FID.
```bash
python evaluation.py --gt_path datasets/evaluation/Tamper-Syn2k/t_f/ --target_path results-syn2k/
```
For real data, the evaluation metric is recognition accuracy.
```bash
python eval_real.py --saved_model models/TPS-ResNet-BiLSTM-Attn.pth --gt_file datasets/evaluation/Tamper-Scene/i_t.txt --image_folder results-scene/
```

Or you can use ```eval_2k.sh``` and ```eval_scene.sh``` for testing and evaluation.
```bash
bash eval_2k.sh configs/mostel-train.py models/mostel.pth
bash eval_scene.sh configs/mostel-train.py models/mostel.pth
```

In our experiments, we found that SLM will improve the quantitative performance while leaving some text outline traces, which is not good for visualization. You can add ```--dilate``` for better visualization when generating predicted results.

## Citing the related works

If you find our method useful for your research, please cite

    @article{qu2022mostel,
      title={Exploring Stroke-Level Modifications for Scene Text Editing},
      author={Qu, Yadong and Tan, Qingfeng and Xie, Hongtao and Xu, Jianjun  and Wang, Yuxin and Zhang, Yongdong},
      journal={arXiv preprint arXiv:2212.01982},
      year={2022}}

## References

[Niwhskal/SRNet](https://github.com/Niwhskal/SRNet)

[youdao-ai/SRNet-Datagen](https://github.com/youdao-ai/SRNet-Datagen)

[clovaai/deep-text-recognition-benchmark](https://github.com/clovaai/deep-text-recognition-benchmark)