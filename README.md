# StyleTTS-VC: One-Shot Voice Conversion by Knowledge Transfer from Style-Based TTS Models

### Yinghao Aaron Li, Cong Han, Nima Mesgarani

> One-shot voice conversion (VC) aims to convert speech from any source speaker to an arbitrary target speaker with only one reference speech audio from the target speaker, which relies heavily on disentangling the speaker’s identity and speech content, a task that still remains challenging. Here, we propose a novel approach to learning disentangled speech representation by transfer learning from style-based text-to-speech (TTS) models. With cycle consistent and adversarial training, the style-based TTS models can perform transcription-guided one-shot VC with high fidelity and similarity. By learning an additional mel-spectrogram encoder through a teacher-student knowledge transfer and novel data augmentation scheme, our approach results in disentangled speech representation without needing the input text. The subjective evaluation shows that our approach can significantly outperform the previous state-of-the-art one-shot voice conversion models in both naturalness and similarity.

Paper: [https://arxiv.org/abs/2212.14227](https://arxiv.org/abs/2212.14227)

Audio samples: [https://styletts-vc.github.io/](https://styletts-vc.github.io/)

***Our work has been awarded [SLT 2022 Best Papers Award](https://slt2022.org/best-papers.php)**

## Pre-requisites
1. Python >= 3.7
2. Clone this repository:
```bash
git clone https://github.com/yl4579/StyleTTS-VC.git
cd StyleTTS
```
3. Install requirements: 
```bash
pip install SoundFile torchaudio munch torch pydub pyyaml librosa phonemizer attrdict git+https://github.com/resemble-ai/monotonic_align.git
sudo apt-get install espeak-ng
```
4. Download and extract the [VCTK dataset](https://datashare.ed.ac.uk/handle/10283/3443). You need to downsample all files in `wav48_silence_trimmed` to 24 kHz and name the folder `wav24_silence_trimmed`. The vocoder, text aligner and pitch extractor are pre-trained on 24 kHz data, but you can easily change the preprocessing and re-train them using your own preprocessing. I will provide more receipes and pre-trained models later if I have time. If you are willing to help, feel free to work on other preprocessing methods. 

## Training
First stage training:
```bash
python train_first.py --config_path ./Configs/config.yml
```
Second stage training:
```bash
python train_second.py --config_path ./Configs/config.yml
```
The repo is self-contained and you do not need to train the first stage model with the [StyleTTS](https://github.com/yl4579/StyleTTS) repo. You can run both commands consecutively and it will train both the first and second stage. The model will be saved in the format "epoch_1st_%05d.pth" and "epoch_2nd_%05d.pth". Checkpoints and Tensorboard logs will be saved at `log_dir`. 

The data list format needs to be `filename.wav|transcription|speaker`, see [val_list.txt](https://github.com/yl4579/StyleTTS-VC/blob/main/Data/val_list.txt) as an example. The speaker information is needed in order to perform speaker-dependent adversarial training. 

## Inference

### Colab

Please refer to this [colab notebook](https://colab.research.google.com/drive/1Y7WxQVfQkl0pPSxSXiN0MJsJwKARg6y-?usp=sharing) which runs end to end. A copy of this notebook is in the demo folder.

### Pretrained Models

The pretrained StyleTTS-VC on VCTK and Hifi-GAN on LibriTTS corpus in 24 kHz can be downloaded at [StyleTTS-VC Link](https://drive.google.com/file/d/1bJbj3alOSu51riHUQl4G1GOjlzulyg6M/view?usp=sharing) and [Hifi-GAN Link](https://drive.google.com/file/d/1RDxYknrzncGzusYeVeDo38ErNdczzbik/view?usp=sharing). 

## Preprocessing

The pretrained text aligner and pitch extractor models are provided under the `Utils` folder. Both the text aligner and pitch extractor models are trained with melspectrograms preprocessed using [meldataset.py](https://github.com/yl4579/StyleTTS-VC/blob/main/meldataset.py). 

You can edit the [meldataset.py](https://github.com/yl4579/StyleTTS-VC/blob/main/meldataset.py) with your own melspectrogram preprocessing, but the provided pretrained models will no longer work. You will need to train your own text aligner and pitch extractor with the new preprocessing. 

The code for training new text aligner model is available [here](https://github.com/yl4579/AuxiliaryASR) and that for training new pitch extractor models is available [here](https://github.com/yl4579/PitchExtractor).

I will provide more recepies with existing preprocessing like those in official [HifiGAN](https://github.com/jik876/hifi-gan) and [ESPNet](https://github.com/espnet/espnet) in the future if I have extra time. If you are willing to help, feel free to make receipes with [ESPNet](https://github.com/espnet/espnet). 
