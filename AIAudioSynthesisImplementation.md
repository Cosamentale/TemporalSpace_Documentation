## RAVE / nn~ Overview
[RAVE: Realtime Audio Variational autoEncoder](https://github.com/acids-ircam/RAVE) can be used to train models compatible with [nn~](https://github.com/acids-ircam/nn_tilde) to execute real-time AI audio processing

Can be used within Max, PD, [Supercollider](https://github.com/elgiano/nn.ar), [Tidal](https://github.com/jarmitage/tidal-rave), VST, and possibly other frameworks
#### Interactive functions:
- Audio timbre transfer (e.g. drum loop input -> vocal model -> beatbox output)
- Latent space navigation (e.g. 3D accelerometer data -> encode - decode -> continuous decoded output)
- Autonomous generation - Prior (Temperature control -> decoder -> generated output)
Functions can be combined for interesting results
#### Examples with nn~
- [€&)(€…..&$ // nn~ // RAVE](https://www.youtube.com/watch?v=YqCLOAvfDms)
- [Realtime Neural Audio Synthesis - RAVE + nn~ #1](https://www.youtube.com/watch?v=dMZs04TzxUI)
- [Realtime Neural Audio Synthesis - embedded RAVE #2](https://www.youtube.com/watch?v=jAIRf4nGgYI)
- https://iil.is/news/ravemodels
#### Training models
- A [Colab](https://colab.research.google.com/drive/1ih-gv1iHEZNuGhHPvCHrleLNXvooQMvI?usp=sharing ) has been published to train RAVe remotley
- However, it takes a lot of time and resources to train a model from scratch, depending on the size and content of the input data. One User reported 3 hours of training data with 3+ days of training (high-end GPU) was necessary for plausible results. Others claiming even more.
## Experimentation with Temporal Spaces System 

#### Survey of pre-trained models

**Sources**
https://acids-ircam.github.io/rave_models_download
RAVE discord
https://huggingface.co/Intelligent-Instruments-Lab/rave-models

| Name | Rave Model | Description |
| ---- | ---- | ---- |
| [Sol_ordinario_fast](https://play.forum.ircam.fr/rave-vst-api/get_model/sol_ordinario_fast) | RAVE V2 - Small | Trained On The Ordinario Sounds From Studio OnLine IRCAM Database, Prior Unavailable |
| [Sol_full](https://play.forum.ircam.fr/rave-vst-api/get_model/sol_full) | RAVE V2 | Trained On Sounds From The Entire Studio OnLine IRCAM Database, Prior Unavailable |
| [Sol_ordinario](https://play.forum.ircam.fr/rave-vst-api/get_model/sol_ordinario) | RAVE V2 | Trained On The Ordinario Sounds From Studio OnLine IRCAM Database, Prior Unavailable |
| [Musicnet](https://play.forum.ircam.fr/rave-vst-api/get_model/musicnet) | RAVE V2 | Trained On The Musicnet Database, Prior Available |
| [Isis](https://play.forum.ircam.fr/rave-vst-api/get_model/isis) | RAVE V2 | Trained On The Vocal ISiS Database For Analysis-Synthesis IRCAM Team (Https://Forum.ircam.fr/Projects/Detail/Isis/), Prior Unavailable |
| [Vintage](https://play.forum.ircam.fr/rave-vst-api/get_model/vintage) | RAVE V1 - Large | Trained On 80h Of Vintage Music, Prior Available |
| [Percussion](https://play.forum.ircam.fr/rave-vst-api/get_model/percussion) | RAVE V1 - Default | Trained On 8h Of Various Percussion Recordings, Prior Available |
| [Nasa](https://play.forum.ircam.fr/rave-vst-api/get_model/nasa) | RAVE V1 - Default | Trained On Recordings From The Apollo 11 Mission (Https://Www.youtube.com/Watch?V=DejhGSEu8wk), Prior Available |
| [Darbouka_onnx](https://play.forum.ircam.fr/rave-vst-api/get_model/darbouka_onnx) | RAVE V2 - Onnx | Trained On 8h Of Darbouka Recordings, Prior Unavailable |
| [VCTK](https://play.forum.ircam.fr/rave-vst-api/get_model/VCTK) | RAVE V1 - Default | Trained On The VCTK Dataset (Doi.org/10.7488/Ds/2645), Prior Available |