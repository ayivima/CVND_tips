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

+ The Embedding Layer: Language is complex. And, it becomes necessary that we make the model's work easier. The first step to making the work easier is to help it understand the relationships between words. So that if it identifies a `ski` it must know that it has something to do with `snow`. If it identifies a `bowl`, it must know that it has something to do with `food`. This is
what makes the embedding layer important. It lets the model get relationships between words.

+ LSTM layer: The LSTM layer(s) will do the actual word predictions. During training or a forward pass, it must receive as input, a combination of the image features and the captions which have been passed through the embedding layer. The way we combine the image features and the embedding of captions, is to concatenate them...literally. Example of concatenations: 
`[0.1,0.2,0.3]` and `[0.4,0.5,0.6]` become `[0.1,0.2,0.3,0.4,0.5,0.6]`. Alternatively, `[[0.1,0.2,0.3]]` and `[[0.4,0.5,0.6]]` become `[[0.1,0.2,0.3],[0.4,0.5,0.6]]` after concatenation.

+ Linear layer(s): The linear layer of the `DecoderRNN` serves as an intermediary between the LSTM and the Embedding layer. Unlike the usual LSTM, we will not pass the states of the LSTM back to itself directly. We need to pass the output, which is the caption, back through the embedding layer before it gets concatenated with image features and gets passed through the LSTM. Evidently, the output size of the Linear Layer must match the input size of the Embedding layer.

##### The Forward Function

+ Retrieve the captions, making sure to remove all fullstops. Then we will pass the captions through the embedding layer.

+ Concatenate the embedded captions and the image feature vectors(from the EncoderCNN).

+ Push the concatenation of embedded captions and image feature vectors to the LSTM layer

+ Return the prediction(out put from the LSTM layer)

Note: Note that for efficiency of operations we train on batches of images and captions at a time. This is an efficient way as the neural network gets to use Matrix operations to save time and effort compared to performing operations one at a time.


### Some FAQs

##### What is Perplexity?

You may look at it this way. PERPLEXITY in english can mean CONFUSION. Thus, if the model has high PERPLEXITY, it is HIGHLY CONFUSED...And it will start calling "a bowl of rice" as "a man holding a frisbee in the field". Consequently, the model will give better captions if PERPLEXITY is low.

##### What perplexity is best?

It is recommended that your model PERPLEXITY goes beyond 10. For very good results, you should be aiming at PERPLEXITY that is lower than 6.

##### The model Perplexity goes up and down. Is this normal?

It is normal that you will have the PERPLEXITY go up and down, so far as the range of fluctuation keeps decreasing. If the range of fluctuations is stagnant for unusually long, thats when you might want to think of retraining and tuning your hyperparameters. For example, it could be that the learning rate is too small and the descent is getting stuck in local minima instead of the global minimum, or you might have a very high learning rate that the descent cannot settle in the minimum and keeps oscillating past it.

##### Is batch size really important?

It is easy to think that batch size does not really have a significant impact on accuracy. Thus, we may set it just anyhow. But, batch size really does affect accuracy and must be carefully selected. As we fiddle with the other hyperparameters, we should also dedicate a
trial round for getting a good batch size.

##### Which batch size is good for me?

There is really no fixed rule. You must experiment and find a batch size that works for your use case. That said, it is recommended that if you choose a large batch size, your learning rate must be high too. A large batch size and a low learning rate is almost certainly set for doom. If you start with a low batch size and a low learning rate, and later choose to increase the batch size,
you should remember to adjust the learning rate appropriately especially for a big change. If you
are increasing batch size significantly, it is more likely that an increase in learning rate will
help too and vice versa.


##### Why is the model giving the same captions after training?

+ The first thing to check is the PERPLEXITY. High PERPLEXITIES may cause this.

+ If the PERPLEXITY is good enough, there may be a problem with the forward and sample functions of the decoderRNN class.


##### What is the best vocabulary threshold?

There is no fixed threshold. Generally, a low vocabulary threshold means there are more words for the model to learn from and may be better. As we grow, we are likely to get better with language because we get to know more words. In the same way, the more words the model learns, the higher the likelihood it can generate accurate captions.


##### How long should I expect to train for the models on GPU?

The image captioning captioning models take time to train. On average, colleagues have taken about 3 hours to train one epoch.
The time will vary with different parameters like batch size. Generally, expect to train in excess of 8 hours for 3 epochs and above.

##### What is the hidden_size?

You can look at the hidden_size as the number of nodes or learnable parameters for the LSTM. 


##### What are the states of the LSTM?

Generally, an LSTM has two outputs:

+ Short term memory(hidden state): This is the same as previous output of the LSTM for a given sequence of words it is predicting. In simple terms, because we are predicting a sequence of words like "a sweet ice cream", when the LSTM predicts "a", it becomes the hidden state at the time it has to predict "sweet". Then, "sweet" becomes the hidden state for when it has to predict "ice". 

+ Long term memory(cell state): This keeps some form of memory for all the parts of the sentence predicted so far.
Once again, given "a sweet ice cream", when the model has to predict "cream", the long term represents something that somehow points to "a sweet ice". It gives context to what the finished sentence should be.

