# Multistage Classification and Prognostic Prediction of Alzheimer’s Neuroimage Sequences with a Convolutional LSTM Network
A CNN-LSTM deep learning model for multistage classification and prognostic prediction of Alzheimer's MRI neuroimages.

## Abstract

Deep convolutional neural networks augmented with a recurrent LSTM mechanism offer a powerful solution for detecting, classifying, and predicting prognoses of Alzheimer’s in patients based on MRI scans.

Our project develops and trains a deep convolutional LSTM neural network on MRI neuroimage data of Alzheimer’s patients to yield predictive prognoses of future disease progression for individual patients based on previous MRI sequencing. The model takes in a sequence of MRI neuroimages and yields the likelihood of conversion from MCI to AD as a prognostic prediction.


## Model Architecture

Our prediction network combines a convolutional neural network (CNN), which compresses the MRI neuroimages by extracting learned features; and a single long short-term memory (LSTM) cell, which combines the extracted features with those of previously inputted MRI neuroimages. The output of the LSTM is fed through a single fully connected layer, to translate the multidimensional LSTM output into a single probability between 0 and 1. This network will be trained to produce the probability that a patient will develop Alzheimer’s within the next five years, weighed against a loss function that aggregates diagnoses for individual patients over time.

### Forward Pass

1. Convolutional Layers (CNN): Take 3-D neuroimage of standard dimensions and apply convolutions and max-pooling.    

    ![equation](https://latex.codecogs.com/svg.latex?%5Cdpi%7B150%7D%20%5Clarge%20%5Cbegin%7Balign*%7D%20%5Ctexttt%7BConv3d%3A%7D%20%26%5C%3B%20%5Cmathbb%7BR%7D%5E%7Bd_1%20%5Ctimes%20d_2%20%5Ctimes%20d_3%7D%20%5Cmapsto%20%5Cmathbb%7BR%7D%5E%7Bd_1%5E%5Cprime%20%5Ctimes%20d_2%5E%5Cprime%20%5Ctimes%20d_3%5E%5Cprime%7D%5C%5C%20%5Ctexttt%7BMaxPool%3A%7D%20%26%5C%3B%20%5Cmathbb%7BR%7D%5E%7Bd_1%5E%5Cprime%20%5Ctimes%20d_2%5E%5Cprime%20%5Ctimes%20d_3%5E%5Cprime%7D%20%5Cmapsto%20%5Cmathbb%7BR%7D%5E%7Bd_1%5E%7B%5Cprime%5Cprime%7D%20%5Ctimes%20d_2%5E%7B%5Cprime%5Cprime%7D%20%5Ctimes%20d_3%5E%7B%5Cprime%5Cprime%7D%7D%20%5Cend%7Balign*%7D)    
    
    This process repeats twice, for a total of three 3-D convolutions and three 3-D max-pooling operations.

2. Flatten output layers from CNN into a 1-D tensor of dimension *d*<sub>*l*</sub> for LSTM.    

    ![equation](https://latex.codecogs.com/svg.latex?%5Cdpi%7B150%7D%20%5Clarge%20%5Ctexttt%7BFlatten%3A%7D%20%5C%3B%20%5Cmathbb%7BR%7D%5E%7Bd_1%5E%7B%5Cprime%5Cprime%7D%20%5Ctimes%20d_2%5E%7B%5Cprime%5Cprime%7D%20%5Ctimes%20d_3%5E%7B%5Cprime%5Cprime%7D%7D%20%5Cmapsto%20%5Cmathbb%7BR%7D%5E%7Bd_l%7D)

3. LSTM: Combine feature encoding from CNN with feature encodings from past images for patient.    
    New memory *c*<sup>(*t*)</sup> is generated by using the input data *x*<sup>(*t*)</sup> and the past hidden state *h*<sup>(*t*-1)</sup>. (Note that *W* and *U* refer to weights and parameters.)    
    
    ![equation](https://latex.codecogs.com/svg.latex?%5Cdpi%7B150%7D%20%5Clarge%20%5Cwidetilde%7Bc%7D%5E%7B%28t%29%7D%20%26%3D%20%5Ctanh%5Cleft%28W%5E%7B%28c%29%7Dx%5E%7B%28t%29%7D%20&plus;%20U%5E%7B%28c%29%7Dh%5E%7B%28t-1%29%7D%5Cright%29)
    
    The input gate uses the input data and past hidden state to produce $i^{(t)}$ to gate the new memory, determining what information is valuable and should be remembered. 
    
    ![equation](https://latex.codecogs.com/svg.latex?%5Cdpi%7B150%7D%20%5Clarge%20i%5E%7B%28t%29%7D%20%26%3D%20%5Csigma%5Cleft%28W%5E%7B%28i%29%7Dx%5E%7B%28t%29%7D%20&plus;%20U%5E%7B%28i%29%7Dh%5E%7B%28t-1%29%7D%5Cright%29)
    
    The forget gate, similar to the input gate, uses the input data but determines whether or not the past memory is useful for computing the current memory, determining what information can be "forgotten."
    
    ![equation](https://latex.codecogs.com/svg.latex?%5Cdpi%7B150%7D%20%5Clarge%20f%5E%7B%28t%29%7D%20%26%3D%20%5Csigma%5Cleft%28W%5E%7B%28f%29%7Dx%5E%7B%28t%29%7D%20&plus;%20U%5E%7B%28f%29%7Dh%5E%7B%28t-1%29%7D%5Cright%29)
    
    Using the input gate, the forget gate, and the new memory, the final memory is generated.
    
    ![equation](https://latex.codecogs.com/svg.latex?%5Cdpi%7B150%7D%20%5Clarge%20c%5E%7B%28t%29%7D%20%26%3D%20f%5E%7B%28t%29%7D%20%5Ccirc%20%5Cwidetilde%7Bc%7D%5E%7B%28t-1%29%7D%20&plus;%20i%5E%7B%28t%29%7D%20%5Ccirc%20%5Cwidetilde%7Bc%7D%5E%7B%28t%29%7D)
    
    Finally, the output gate (or exposure gate) determines which portions of the memory need to be saved in the hidden state.
    
    ![equation](https://latex.codecogs.com/svg.latex?%5Cdpi%7B150%7D%20%5Clarge%20%5Cbegin%7Balign*%7D%20o%5E%7B%28t%29%7D%20%26%3D%20%5Csigma%5Cleft%28W%5E%7B%28o%29%7Dx%5E%7B%28t%29%7D%20&plus;%20U%5E%7B%28o%29%7Dh%5E%7B%28t-1%29%7D%5Cright%29%20%5C%5C%20h%5E%7B%28t%29%7D%20%26%3D%20o%5E%7B%28t%29%7D%20%5Ccirc%20%5Ctanh%5Cleft%28c%5E%7B%28t%29%7D%5Cright%29%20%5Cend%7Balign*%7D)

4. Linear Layer: Map from LSTM hidden space (with dimension $d_h$) to prediction space (with dimension $d_p$).    
    
    ![equation](https://latex.codecogs.com/svg.latex?%5Cdpi%7B150%7D%20%5Clarge%20%5Ctexttt%7BLinear%3A%7D%20%5C%3B%20%5Cmathbb%7BR%7D%5E%7Bd_h%7D%20%5Cmapsto%20%5Cmathbb%7BR%7D%5E%7Bd_p%7D)

5. Compute loss with cross-entropy.    
    
    ![equation](https://latex.codecogs.com/svg.latex?%5Cdpi%7B150%7D%20%5Clarge%20C%20%3D%20-%5Cfrac%7B1%7D%7Bn%7D%20%5Csum_x%20%5Cleft%5By%20%5Cln%20%5Chat%7By%7D%20&plus;%20%281%20-%20y%29%20%5Cln%20%281%20-%20%5Chat%7By%7D%29%5Cright%5D)

Thus, after training, the model takes as input an arbitrary number of MRI scans for a specific patient and outputs the described probability of a positive diagnosis within the next five years. A probability of 1 indicates present diagnosis. Lower probabilities may indicate the patient’s distance from incurring a positive diagnosis. 

<em>"Multistage Classification and Prognostic Prediction of Alzheimer’s Neuroimage Sequences with a Convolutional LSTM Network" is a project for CS 452/663: Deep Learning Theory and Applications, Spring 2020.</em>
