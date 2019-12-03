# Basic (chit-chat) chatbot using Sequence to Sequence (seq2seq) model and Word Embeddings

Use Microsoft BotBuilder Personality Chat Datasets, which includes three different personality chat (professional, friend, comics) datasets. 
https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets

# Instuctions
Download the [IPython file](basic_chatbot.ipynb) and upload to Google Drive then open on Colab and run everything from beginning.

## Data Preprocessing
Text pre-processing techniques includes tokenisation, removing numbers, converting to lowercase, removing stop words, stemming, etc.

An typical approach for normalising words is to decapitalising words(to lower case), removing non-alphabet, removing stop-word, stemming, lemmatization etc.

I have tried all of above and I found the most effectives are decapitalisation(to lower case) and removing non-alphabet(e.g. puntuation, numebrs...) so I only used these 2.

Lowering case of the words does not chage the meaning of a given word, and it is not costly to create a extra vocab for 2 words with exactly same meaning. Removing non-alphabet also an effective step since t they themself are not word and has no meaning at all.

Stop-word removal may actually harm the model as the questions set contains too many stopwords such as what, when, how... are, do, our, you... by removing stopwords the whole question set contains only single or even empty tokens list of a sentence. And i did tried apply removing stops words and it actually increase difficulty to converge the models(training loss stops reducing in an very early iteration no matter what setting of hyper parameters such as leanring rate).

stemming, lemmatization do not help much for training the model or chatbot evaluation(observe if chatting content making sense)


## Model Implementation

### Word Embeddings
My initial goal is to train a **skip-gram** word embedding model such that the training loss is as small as possible.

word_size(vocab size) is set to 100 since{} 100, 200, 300} are most common proven effective setting for industries and research works. And I found 100 is the best in my cases in terms of converge speed. 

Learning rate have been tested from 0.001-1 and I found the best is 1 since it converge fast and the training loss is stably decresing as shown below.

Unlike other learning rate such as 0.001, 0.01, 0.5 the loss is flutuated or converge slowly.(e.g. 0.01 even start with >70000 loss and suddently drop to 20000 then increse to 50000...)

But eventually I have applied **Facebook Pretrain Fasttext** [wiki.simple.bin](https://fasttext.cc/docs/en/pretrained-vectors.html) instead of using self trained **skip-gram**.


### Sequence Modelling 
I trained 3 different individual sequence models (using three different personality datasets) and those models should be individually applied.

I mainly focus on tuning learning rate, changing number hidden units, and #iterations. 

I found that models with proper learning rate will converge to low training loss such as below 0.5, with inappropreiate learning rate such as ... professional model would stuck on training loss of 1-2 and never fall down.

I also experiences 128, 256 number of hidden layer, in the case of professional model , 256 may lower down the training loss in less iteration but at the same time more training time and it seems does not have much differnt interms of chatbot chatting ability after all. So I stick with 256.

The most cretical hyperparameter is learning rate as mention a inappropraite learning rate could cause model stuck in a training loss that never fall down regardless of increasing number of iterations.

From experience, I tried range 0.000001 - 0.01, and I found for all 3 models(professional, friend and comic), 0.0005-0.001 worked for the best for all 3 models. The models converge to low traning loss ~=0.3 in 3000-5000 iterations. The model is also robust in Prediction stage in chatbot in temrs of performance of how chatbot answering user's questions, it gives reasonable responses and with similar input the chatbot gives same output.

I also tried multiple attemptions for the same set of hyper parameters values and each time the training loss could end up quite differnce, probably due to the random initialisation so I chose the one which converge better.

### Running Chatbot

**Change personality:** 
Users are able to change chatbot personality  during the conversation (runtime). The default personality should be “Professional”.

**Save chat log:**
All chat log (chat history) can be printed on the console and saved in the ‘chat_log.txt’ file on Google Drive.

**Examples:** 
please have a look [chat_log.txt](chat_log.txt)
