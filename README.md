## Dreem One Shot Event Detector (DOSED)

This repository contains a functional implementation of DOSED, a deep learning method proposed in:

"A deep learning architecture to detect events in EEG signals during sleep" by Stanislas Chambon, Valentin Thorey, Pierrick J. Arnal, Emmanuel Mignot, Alexandre Gramfort.

"DOSED: a deep learning approach to detect multiple sleep micro-events in EEG signal" by Stanislas Chambon, Valentin Thorey, Pierrick J. Arnal, Emmanuel Mignot, Alexandre Gramfort.

### Introduction

DOSED in a deep learning approach to jointly predicts locations, durations and types of events in time series.
It was inspired by computer vision object detectors such as YOLO and SSD and relies on a convolutional neural network that builds a feature representation from raw input signals,
 as well as two modules performing localization and classification respectively. DOSED can be easily adapt to detect events of any sort.
 
 ![dosed_detection_image](https://github.com/Dreem-Organization/dosed/blob/master/dosed_detection.png)

### Citing DOSED


        @inproceedings{chambon2018deep,
          title={A deep learning architecture to detect events in EEG signals during sleep},
          author={Chambon, Stanislas and Thorey, Valentin and Arnal, Pierrick J and Mignot, Emmanuel and Gramfort, Alexandre},
          booktitle={2018 IEEE 28th International Workshop on Machine Learning for Signal Processing (MLSP)},
          pages={1--6},
          year={2018},
          organization={IEEE}
        }

        @article{chambon2018dosed,
          title={DOSED: a deep learning approach to detect multiple sleep micro-events in EEG signal},
          author={Chambon, Stanislas and Thorey, Valentin and Arnal, Pierrick J and Mignot, Emmanuel and Gramfort, Alexandre},
          journal={arXiv preprint arXiv:1812.04079},
          year={2018}
        }
 
### Minimum example  

The folder */minimum_example* contains all necessary code to train a spindle detection model on EEG signals.

We provide a dataset composed of 21 recordings with two EEG central channels downsampled at 64Hz on which spindles have been annotated. The data was collected at Dreem (dreem.com) with a Polysomnography device.

The example works out-of-the-box given the following considerations.

#### 1. Package requirements
Packages detailed in *requirements.txt* need to be installed for the example to work.

#### 2. Data

Running the script *download\_and\_format\_data.sh* from the */minimum\_example* directory automatically downloads, pre-processes and saves the EEG data in the correct format for training and testing DOSED.

Furthemore, the jupyter notebook *download\_and\_data\_format\_explanation.ipynb* provides detailed explanation about the aforementioned steps and about the data formats used to store the information, together with visualizations of the events under consideration.

In principle, signals and annotations provided in any data format can be processed by adapting the function *to\_h5.py*, which saves the information in individual .h5 files (one per record).

Required structure for the .h5 files is the following:

```javascript
        h5_file/
            /signal_1
            /signal_2
            /signal_3
            ... # add as many signals as desired
            /event_1/
                /start  # array_like: start position of events with respect to the beginning of the recording (in seconds).
                /duration  # array_like: duration events (in seconds).
            /event_2/
                /start
                /duration
            ... # add as many events as desired
```

The *to_memmap.py* script extracts data from the .h5 files, normalizes them and saves them in memmap files. Two variables define this process. The variable *signals* allows to configurate the minimun and maximum clipping boundaries for each signal. Additionally, variable *events* permits to specify the name of the event types under consideration and the ground truth (note that .h5 files can contain several ground truth versions).

Configuration of variable *signals* . e.g.

```javascript
    signals = {
        [
            "name": "signals",  # memmap save name
                "h5_paths": [
                    {
                        'path': '/h5_path_to_signal_1',
                        'processing': {
                            "type": "clip_and_normalize",
                            "args": {
                                "min_value": -100,
                                "max_value": 100,
                            }
                        }
                    },
                    {
                        'path': '/h5_path_to_signal_2',
                        'processing': {
                            "type": "clip_and_normalize",
                            "args": {
                                "min_value": -100,
                                "max_value": 100,
                            }
                        }
                    },
                    ...  # add as many signals as desired
                ],

            "fs": 256., # (in Hz) all signals in the set have to be sampled at the same frequency
        ],
        ... # add as many sets of signals as desired
    }
```

and of variable *events*. e.g.

```javascript
    events = [
        {
            "name": "my_events",  # name of the events
                    "h5_path": "/event_1",  # annotations path in the .h5 file
        },
    ]
```
  
#### 3. Training and testing

The jupyter notebook *train\_and\_evaluate\_dosed.ipynb* goes through the training process in detail, describing all important training parameters. It also explains how to generate predictions, and provides a plot of a spindle detection.
