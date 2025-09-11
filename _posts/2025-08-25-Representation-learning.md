---
title: "Representation learning "
date: 2025-08-25 10:00:00 +0800
categories: [AI] #[Top_category, sub_category]
tags: [ai, representation learning , blog] ## TAG names should always be lowercase
# author: 
---
## Representation learning 
Representation learning is a set of methods that allows a system to automatically discover representations from raw data. These representations are useful、low dimensional values. An analogy is that transforming those easy-understaning for human while difficult to process in machine/computors, like images、texts、audio、tactile info... into effective math format, and they are usually vectors or embeddings(NLP).

Image example: convolutional neural networks (CNNs) are a typical example of representation learning models. Each layer of the network transforms images from low-level features (such as edges and colors) into increasingly higher-level features (such as eyes and noses), ultimately forming a representation vector that represents the entire image.

In the early age of AI, people want to "teach" AI what is a cat, AI researchers did a very tedious job about handcrafting features of cats. A programmer has to sit down and painstakingly set up a check list of a cat's features: a tail, 4 legs and etc. 

Forget the checklist! representation learning changes this. it uses models to obeserve a sea of data(images) and automatically learn how to categorize features of cat and other items. These features are encoded as a series of numbers, which is "representation". It can capture the most fundamental rules and patterns behind the data.

### Characteristics and Advantages of Representation Learning
From raw data to abstract concepts: It can compress complex, high-dimensional raw data (such as pixels) into refined, low-dimensional abstract concepts (such as the “concept” of a cat).

Avoiding manual feature engineering: It greatly reduces reliance on human expertise, enabling machine learning models to process data more efficiently.

Capturing deep semantics and relationships: The learned representations are not merely a simple compression of data; they also capture deep relationships between data points. For example, in natural language processing, representation learning can map words like “king” and “queen” to nearby positions in a vector space, while the relationship between ‘king’ and “queen” (gender) can be represented using vector addition and subtraction.

Foundation for transfer learning: Once a model has learned an effective representation of a certain type of data, this representation can be transferred to different, even unrelated, tasks. For example, a model trained on a large dataset of images can use the learned representations to help a new model trained on a small dataset better recognize objects.

