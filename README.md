# PainDetection
Utilizing AI in fMRI Scans to Detect Nociceptive Intensity Ratings in Patients

# Formats of data
  - fMRI
    - pros: spatial mappings
    - cons: non-temporal state does not allow for immediate processing
  - EEG
    - pros: time-based
    - cons: does not show activity in specific regions of the brain
  - fNIRS
  - Spinal cord (experimental, undecided)

# Preprocessing
  - 4D array fMRI scans taken from https://openfmri.org/dataset/
  - dimensionality reduction of 2D data and 3D data
  - large amounts of RAM needed
  - preallocation of numpy arrays
  - considering lazy loading with tf.data
  - pain ratings on a scale from 0-200 are extracted from the dataset
  - binary classification
    - one hot encode labels
    - threshold = 50
    - Verdict: > 50 = pain, < 50 = no pain

# Architecture
  - Convolution2D/3D + AveragePooling + LeakyRelu (alpha = .3) + BatchNormalization + Dropout
  - Dropout used for first block of convolutional layers, tends to drop performance when used in further blocks succeeding the first block
  - Sigmoid activation for binary classification, linear actiation for regression
  
# Training
  - Grid search used for hyperparameter optimization (ran for 36 hours)
    - AveragePooling shown to have a better performance (+ ~10% accuracy) than MaxPooling
    - Lesser values of Dropout (.1, .15)
  - Explainability
    - Saliency maps show significance of pixels relative to the derivative of the weights during backprop (visualization tool: keras-vis)
    - Surrogate models
    - Connectivity correlation for understanding ROI for nociceptive input in the brain
  - RAM exceeds limit during training, possible solutions:
    - swap space w/ virtual ram from SSD
    - AWS or Google Cloud servers
    - remote desktop access
    - recurrently load new data in batches + train on new batches, then save the model and load it back up in a new runtime
  - Do not use LSTM, requires significantly more data to store in Many2Many
  - Accuracy: ~64%
  
# Evaluation
  - When training on a specific portion of the dataset, the model evaluates very poorly (~30% accuracy) on other portions of the dataset
  - Distribution is not equal due to hardware restrictions (memory)

# Prediction
  - shows direct correlation between pain/no pain and pain ratings during prediction process
