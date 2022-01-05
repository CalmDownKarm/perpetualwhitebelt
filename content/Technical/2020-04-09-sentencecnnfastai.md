---
date: 2020-04-09T00:17:04+05:30
lastmod: 2020-04-09T00:17:04+05:30
title: "Hacking Fastai v1, Sentence CNNs for Sentiment Classification"
tags:
  - Technical
slug: sentence-cnns-fast-ai

---

I've recently started a personal challenge to read and implement 20 papers inside of a month. This is mainly to get used to writing deep learning models again (I've spent most of the last few months doing web development for a couple of projects for the Delhi Government). Part of this is also to become more familiar with the PyTorch ecosystem and the tooling around it. Primarily - I'm trying to find the pytorch equivalent of Keras, wherein it removes a lot of the boilerplate I would traditionally have to write, but still giving me the transparency that Pytorch does. 

To that end, I'm looking at 2 libraries primarily - Fastai and Pytorch Lightening. I have very little experience with both of these, so as I write these models, I'm going to swap off between each. In the little experience I do have - I really like the Pytorch Lightning method of having 1 class that encapsulates everything - but prefer the conveniences that fastai brings - I don't need to use tensorboard, or grid search for learning rates or write my own triangular learning rate scheduler.

## The Sentence CNN

The [paper]([https://arxiv.org/abs/1408.5882](https://arxiv.org/abs/1408.5882)) was published in EMNLP 2014 and is a very straightforward technique to document/sentence classification. The basic idea is that you convert each word in your sentence/document to its corresponding vector - stack them vertically on top of each other, stick them into a convolutional layer and use max-pooling to reduce each 'feature' that the layers create before using a fully connected layer to do the actual classification.

The original paper uses 3 sets of filters, with region sizes of 3,4,5 with a 100 feature maps each, before passing into ReLU for a non-linearity and a maxpool layer. it then concatenates all three pooled vectors and then passes it into a fully connected layer that reduces the 300 sized vector into however many output classes we need. It uses L2 weight regularization on the fully connected layer. 

Subsequent to it's original release, there's been two interesting papers - the first is [A Sensitivity Analysis of (and Practitioners’ Guide to) Convolutional Neural Networks for Sentence Classification]([https://arxiv.org/pdf/1510.03820.pdf](https://arxiv.org/pdf/1510.03820.pdf)) which mentions that L2 regularization isn't that useful at all, and that the filter sizes need to be tuned to each particular dataset and that the width of the filter region should be between 1 and 10. To me this seems intuitive - the convolutional filters act like a N-gram window over the input sentence. The second paper is [Decoupled Weight Decay Regularization]([https://arxiv.org/pdf/1711.05101.pdf](https://arxiv.org/pdf/1711.05101.pdf)) which I haven't read very closely, but I think it implies that the default pytorch implementation of weight decay in the adam optimizer is actually l2 regularization. (Pytorch offers the AdamW optimizer with the weight decay algorithm that this paper suggests) but taking these two together, I decided not to use weight normalization.

The fastai library is built upon the idea of having different layers - the higher layer api does very specific things intended for use with their prebuilt models. The lower layers are intended to be sort of lego blocks that can be used to assemble custom pipelines. Technically I should be using their lower-level apis, but the higher level apis are just too convenient to not use.

The default TextClasDataBunch for fastai v1 uses the spacy tokenizer and has some default preprocessing rules for text. It also shuffles the dataset and collects equal length sentences in order to minimize the amount of padding per batch. This works great for an RNN, where I can have variable input sequences, however, for a CNN, the input size for every batch needs to be the same. So the typical approach is to figure out the most common sequence length in the dataset (histograms are great for this) and padding/trimming each of the sentences that aren't that length. To do this, I added another rule. The post tokenization rules are all functions that take in an iterable/collection of tokens and return another collection/iterable of the transformed tokens.

    # Create the rule
    def pad_to_length(in_seq):
    # PAD is the fastai constant that represents the string xxpad
        N = 250
        return (in_seq + N * [PAD])[:N]
    
    # Append it to the list of default rules
    defaults.text_post_rules.append(pad_to_length)

Each of the data bunches has a bunch of factory methods that similar to pandas, specify how the data is stored on disk. 

In this case, my data (the IMDB movie review sentiment dataset - specifically, a subset of the dataset so that I can test and run the model relatively quickly on a CPU) was stored was a csv file with the labels being the first column and the text being the second. However, using the .from_csv factory method was automatically converting the labels to LongTensors instead of FloatTensors. Perhaps the 'correct' thing to do would be to use a callback to my fit function and manually cast the y tensors to Floats but it was easier to just use the from_df constructor and manually map the labels to floats

    map_labels = {'negative': 0.0, 'positive': 1.0}
    frame = pd.read_csv(path/'texts.csv')
    frame['label'] = frame['label'].map(map_labels)
    train_frame = frame[~frame['is_valid']]
    valid_frame = frame[frame['is_valid']]
    print(train_frame.shape, valid_frame.shape)
    # create the databunch
    dc = TextClasDataBunch.from_df(path, train_df=train_frame,
                                   valid_df=valid_frame,
                                   test_df=valid_frame,
                                   max_vocab=16864, min_freq=1, bs=32)

A slightly curious thing about this entire process is that the vocabulary object that fastai creates has two properties - an itos property which is just a list of the tokens in your dataset and a stoi property which is a dictionary that maps each word to the index in itos. The strange thing is that if you call length on the itos and stoi object, you get different numbers. At first, I thought this was because you can set a minimum frequency of tokens on the data - and that stoi would first map the entire dataset using a defaultdict/counter and that itos just kept the ones that crossed that threshold. The source, however, specifies that stoi is populated after itos is. Even weirder, if I call max(vocab.stoi.values) or len(vocab.stoi.values) I actually get a number that matches the length of itos. Not really sure what's going on here, but it doesn't particularly matter? the mappings in stoi all match the indexes in itos correctly.

Anyway, an important part of sentence CNNs is to use vectors for every word - these can be 1 hot vectors, however, the standard practice is to use pretrained word vectors like Glove, Fasttext and Word2Vec. People typically use libraries like gensim to get these vectors - but I find the gensim api very confusing. I much prefer this lesser-known library called [pymagnitude]([https://github.com/plasticityai/magnitude](https://github.com/plasticityai/magnitude)) which essentially stores vectors in SQLite files and you can query vectors from the words. The github page for the library also has download links for a bunch of pretrained word vectors. The way the vectors are used is that you create an embedding layer in pytorch and that acts as the first layer of the network. You pass this embedding layer a matrix that has the same index as your vocabulary and is the size of the vector. This means that if you have a vocabulary of a thousand words and vectors of 300 dimensions, you'd pass it a matrix of 1000 x 300.  Pymagnitude makes creating this really easy - 

    vectors = Magnitude('TextDatasets/wiki-news-300d-1M-subword.magnitude')
    weights = torch.from_numpy(vectors.query(dc.vocab.itos))

so my model then looks like 

    class CNN(nn.Module):
        def __init__(self, embedding_dim, n_filters, filter_sizes, output_dim, weights, sentence_len, dropout):
            super().__init__()
            self.embedding = nn.Embedding.from_pretrained(weights, freeze=False,)
            self.conv_layers = [
                nn.Sequential(
                    nn.Conv2d(in_channels=1, out_channels=n_filters, kernel_size=(filter_size, embedding_dim)),
                    nn.ReLU())
                for filter_size in filter_sizes
            ]
            self.pooling_layers = [nn.MaxPool1d(sentence_len - fs + 1) for fs in filter_sizes]
            self.fc = nn.Linear(len(filter_sizes) * n_filters, output_dim)
            self.dropout = nn.Dropout(dropout)
            
        def forward(self, x):
            # [batch size, sent len]
            embedded = self.embedding(x)
            # [batch size, sent len, emb dim]
            embedded = embedded.unsqueeze(1)
            # [batch size, 1, sent len, emb dim]
            feature_maps = [conv(embedded).squeeze(3) for conv in self.conv_layers]
            # 3 [batch size, n_filters, sent len - filter_sizes[n] + 1]
            pooled_layers = [pool(feature_map).squeeze(2) 
                for pool, feature_map in zip(self.pooling_layers, feature_maps)]
            # [batch size, n_filters]
            cat = self.dropout(torch.cat((pooled_layers), dim = 1))
            # [batch size, n_filters * len(filter_sizes)]  
            return self.fc(cat).squeeze()
    
    net = CNN(embedding_dim=300, 
              n_filters=3, filter_sizes=[3,4,5], output_dim=1,
              weights=weights, sentence_len=250, dropout=0.5)

Another strange thing is that if I want to move the model to a GPU - just calling net.cuda() to net.to('cuda') didn't work - I had to cast each layer in the net to cuda. Not sure why this is the case,  I suspect it's because nn.Sequential isn't really meant to be used inside of a class that inherits nn.Module - but this is a wild guess.

Finally, we declare a learner, passing it the databunch, loss function and custom model.

    learner = Learner(dc, net, loss_func=loss_func, path=path)

We can then use the typical fastai tools - LR Finder and Fit One Cycle to train the model, learner.save and export to persist the models to disk and any of the fastai callbacks for checkpointing, accuracy calculation etc.

    learner.lr_find()
    learner.recorder.plot()
    learner.fit_one_cycle(10, 5e-3)