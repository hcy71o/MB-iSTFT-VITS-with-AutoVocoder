# MB-iSTFT-VITS with AutoVocoder

## Motivation for implementation
Starting from [VITS](https://arxiv.org/abs/2106.06103), [MB-iSTFT-VITS](https://arxiv.org/abs/2210.15975) improves the synthesis speed using below techniques:
1. Multi-band parallel generation strategy by decomposing speech signals into sub-band signals
2. iSTFT based waveform generation process<br>

Based on this well-designed framework, this repository aims to further improve sound quality and inference speed with [Autovocoder](https://github.com/hcy71o/AutoVocoder).<br> This repo is based on [MB-iSTFT-VITS](https://github.com/MasayaKawamura/MB-iSTFT-VITS), and the expected modifications and enhancements are below:
- [x] 1. Replace the iSTFTNet-based decoder to AutoVocoder-based decoder.<br>

- [x] 2. In iSTFT operation, use Real/Imaginary instead of Phase/Magnitude components to construct complex spectrogram. Add time-domain reconstruction loss.

- [x] 3. Revise the posterior encoder to accept 4 complex components instead of linear spectrogram.<br>

* Owing to nature of VITS that models powerful latents, AutoVocoder can be proper application due to its autoencoder architecture. Also it has fast inference speed 
by directly generating waveform with `(1024, 256, 1024)` fft/hop/win size without upsmpling modules. (Multi-band startegy will be maintained)
* Conventional TTS models including VITS, modeling phase information has been entirely the role of a decoder (vocoder). In `Mod 3.`, by providing phase information to latents, we test whether prior can reliably approx these latents.

`Disclaimer : This repo is built for testing purpose. Performance is not guaranteed. Welcome your contributions.`

## Note
* For easy comparison, we did not change the whole architecture of the posterior encoder. Instead, we only used group convolution in the front part to process revised inputs (4 complex components).
* In current, this repo tries to implement MB-iSTFT-VITS based model. Application to mini, MS, w/o MB might be future work.

## Explanation (from [MB-iSTFT-VITS](https://github.com/MasayaKawamura/MB-iSTFT-VITS))

### 0. Baseline: MB-iSTFT-VITS

<p align="center"><img src="./fig/proposed_model.png" width="85%"></p>

### 1. Pre-requisites

0. Python >= 3.6
0. Clone this repository
0. Install python requirements. Please refer [requirements.txt](requirements.txt)
    1. You may need to install espeak first: `apt-get install espeak`
0. Download datasets
    1. Download and extract the [LJ Speech dataset](https://keithito.com/LJ-Speech-Dataset/), then rename or create a link to the dataset folder: `ln -s /path/to/LJSpeech-1.1/wavs DUMMY1`
0. Build Monotonic Alignment Search and run preprocessing if you use your own datasets.
```sh
# Cython-version Monotonoic Alignment Search
cd monotonic_align
mkdir monotonic_align
python setup.py build_ext --inplace
```

### 2. Training
In the case of MB-iSTFT-VITS training, run the following script
```sh
python train_latest.py -c configs/ljs_mb_istft_vits.json -m ljs_mb_istft_vits

```

After the training, you can check inference audio using [inference.ipynb](inference.ipynb)

## References
- MB-iSTFT-VITS: [Paper](https://arxiv.org/abs/2210.15975) / [Code](https://github.com/MasayaKawamura/MB-iSTFT-VITS)
- AutoVocoder: [Paper](https://arxiv.org/abs/2211.06989) / [Code](https://github.com/hcy71o/AutoVocoder) (unofficial)