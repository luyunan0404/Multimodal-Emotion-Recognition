# Multimodal-Emotion-Recognition

## Introduction
In this project, I would present an analysis of audio and visual information, especially facial video to recognize human emotions. The whole process can be divided into two stages. First, two separate models are trained to perform audio and facial emotion recognition tasks respectively on the corresponding labeled speech and face data. Second, the outputs of these two models are integrated into a fusion network constructed by a number of fully-connected layers. The fusion network is trained to obtain a joint audio-visual feature representation for emotion recognition. The performance of emotion recognition algorithm is measured by the prediction accuracy of the testing set.


## Dataset
- The Ryerson Audio-Visual Database of Emotional speech and Song - RAVDESS

  - Video_Speech_actors_01-24.zip
  - It contains 1440 video files: 60 trials per actor * 24 actors where 12 female, 12 male actors. Each of them is a short video around 3 seconds of a actor standing in front of a white wall and speaking one of two sentences ("Kids are talking by the door" or "Dogs are sitting by the door") in six basic emotions (angry, sad, happy, surprise, fear and disgust) and neural(calm) expressed in both face and tone. 

- Surrey Audio-Visual Expressed Emotion - SAVEE

  - The SAVEE database was recorded from four native English male speakers (identified as DC, JE, JK, KL). Emotion has been described psychologically in discrete categories: anger, disgust, fear, happiness, sadness, surprise and neutural. The text material consisted of 15 TIMIT sentences per emotion: 3 common, 2 emotion-specific and 10 generic sentences that were different for each emotion and phonetically-balanced. The 3 common and 2 × 6 = 12 emotion-specific sentences were recorded as neutral to give 30 neutral sentences. This resulted in a total of 120 utterances per speaker.


## Models
- Visual Network

  - Data Pre-Processing

      - Extract the frames from each video by using cv2.VideoCapture.read() function.
      - For each frame, crop the face from the complete image. Then resize it to 224*224 which is the input size for Alexnet that I used for training.
      - Store one frame every ten frames in the corresponding video name folder.

  - The AlexNet network as pretrained model because of its promising performance and generalization ability in various vision tasks. The AlexNet model contains "features" layer and "classifier" layer. The "feature" layer contains five convolutional layers(Conv.), three of which are followed by max-pooling layers, and "classifier" layer contains three fully connected (FC.) layers, in which the first two FC. layers consist of 4096 units, respectively, and the last FC. layer has 1000 dimension corresponding to class labels. So in this visual network, I currently change the last FC. layer output to 7 which correspond to 7 emotion classes and add an Softmax() layer as the activation layer to represent the probability of the image corresponding to each category.

  - The test accuracy is 48.89%.

- Audio Network
  - Data Pre-processing

      - Extract the audio track from the videos.
      - Load the audio files and extract the MFCC features from the log scale of the melspectrogram by using librosa.feature.mfcc() function.  
      - Zero-pad the MFCC of each audio to the same length for network input. In my case, the longest time frame is 282, so pad all the MFCC features to (20,282) size.

  - Networks

    - CNN - Similar to the AlexNet
    - LSTM - two 128 hidden layers with Softmax() as the activation function for 7 outputs.

  - The test accuracy for CNN is 53.33% and for LSTM is 37.68%.
  
## Fusion Netowrk
- Model 1 - CNN+CNN(8192)

The outputs from the second last layer of audio and visual network are concatenated together and further trained through three fully connected layers and get the final 7 classes.​

- Model 2 - SingleInput

Use single frame from each video as the input for the visual network model, then the result is weighted average with the outputs from the audio network model to get the final classification.​ The network is trained on both CNN and LSTM model for the audio network.​

- Model 3 - MultipleInput

Use multiple frames as inputs for the visual network model and average their prediction percentage of 7 classes as the output of the visual network, then weighted average with the outputs from the audio network model as second method using fully connected layer to learn those weights. The network also trained on both CNN and LSTM model for the audio network.​

#### The project Website for more information: https://luyunan0404.wixsite.com/emotionrecognition
