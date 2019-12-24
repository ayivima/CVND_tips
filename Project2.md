# IMAGE CAPTIONING

### OVERVIEW

In the image captioning project, we are going to use two models in tandem to describe images. This is one of the interesting projects you will get to boast of. 


+ One model is responsible for detecting key objects in the image. Image descriptions are more appreciated when key objects like a human, horse, beach, dog etc. in an image are mentioned in description. This is why we need a good model to first detect these objects, and provide clues for how they are related. For instance, if there is a human and horse, we need to know if the human is sitting on the horse or just standing by it?

+ A second model must look at the objects detected by the first model and generate words based on the relationships between them.

+ The first model, which assesses images and detects important objects, is a Convolutional Neural Network(CNN). It is called `EncoderCNN`.

+ The second model, which is responsible for finding the right words to describe images, is a Recurrent Neural Network(RNN) - specifically a Long Short Term Memory architecture. it is called `DecoderRNN`


### The EncoderCNN

+ Because of the complexity of this project, it is recommended that a pretrained model is used for the `EncoderCNN`. While you may be able to build your own model, it will take time and a lot of trial and error. A pre-trained model like `Resnets` and `VGGs` will save a lot of headache and help achieve good accuracy pretty quickly.

+ We do not need the EncoderCNN to classiy images, we only need it to extract the features of important objects. Therefore, we do not need a `softmax`, we only need a linear layer without activation, which will transform the size of feature vector to a size that is expected by the DecoderRNN.

### The DecoderRNN

The DecoderRNN receives the important features of an image and generates words to describe them.

##### We identify three important layers:

+ The Embedding Layer: Language is complex. And, it becomes necessary that we make the model's work easier. The first step to making the work easier is to help it understand the relationships between words. So that if it identifies a ski it must know that it has something to do with snow. If it identifies a bowl, it must know that it has something to do with food. This is
what makes the embedding layer important. It lets the model get relationships between words.

+ LSTM layer: The LSTM layer(s) will do the actual word predictions. During training or a forward pass, it must receive as input, a combination of the image features and the captions which have been passed through the embedding layer. The way we combine the image features and the embedding of captions, is to concatenate them...literally. Example of concatenations: 
`[0.1,0.2,0.3]` and `[0.4,0.5,0.6]` become `[0.1,0.2,0.3,0.4,0.5,0.6]`. Alternatively, `[[0.1,0.2,0.3]]` and `[[0.4,0.5,0.6]]` become `[[0.1,0.2,0.3],[0.4,0.5,0.6]]` after concatenation.

+ Linear layer(s): The linear layer of the `DecoderRNN` serves as an intermediary between the LSTM and the Embedding layer. Unlike the usual LSTM, we will not pass the states of the LSTM back to itself directly. We need to pass the output, which is the caption, back through the embedding layer before it gets concatenated with image features and gets passed through the LSTM. Evidently, the output size of the Linear Layer must match the input size of the Embedding layer.

##### The Forward Function

+ We will retrieve the captions, making sure to remove all fullstops. Then we will pass the captions through the embedding layer.

+ Then we will concatenate the embedded captions and the feature vector from the EncoderCNN
