---
title: designing within the black box
subtitle: a 3-session workshop on AI, focused on making simple bots
shortdesc: 
thumbnail: 
cssclasses: 
tags:
  - how-to
  - wip
updated: 2024-02-15
---

This workshop has been developed over a couple of iterations with the goal of teaching non-technical designers an approach to working with AI that actually engages with how the technology works.

Within the "design with AI" space, I too often see designers taking the technology as black box, just speculating (sometimes critically) on how a particular AI technology will fit into various contexts in society. I think that designers have a responsibility to critically examine that black box, and then treat the algorithms itself as a design opportunity.

The goal of this workshop is to piece together an understanding of how neural networks work, specifically in the context of large language models like ChatGPT, and to gain some experience designing **within** the black-box.

_This workshop pulls together resources created by other designers, technologists and scholars, which are all cited, and linked to in the library section._

## Contents
1. Large Language Models (LLM) and GPT
2. Chatbots as an Interface
    1. **Tutorial**: [[building bots]]
    2. **Tutorial**: [[creating a decision tree]]
    3. **Tutorial**: ELIZA
3. Generative text using probability
    1. **Tutorial**: [[markov chains]]
    2. **Tutorial**: Gathering Text Data
4. Introduction to Neural Networks
    1. What is a model?
    2. Machine learning to train neural networks
5. Representing Text as Numbers
    1. **Tutorial**: Word Vectors/Embeddings
    2. **Tutorial**: Semantic Similarity
6. Fine-tuning Large Language Models
    1. **Tutorial**: Formatting text data

## What is GPT?

## Interface

ChatGPT is a specific variation of GPT-3.5 that makes it usable as a conversational interface. It could be argued that the recent public facination/hype around ChatGPT is due to the re-packaging of GPT interface from an "autocomplete" interface to a "conversational bot" interface. We will dig into the details of how OpenAI turned GPT into ChatGPT later, but for now lets focus on chatbots as an interface.

Tutorial: [[building bots|building discord bots]]


## Using Probability to Write a Sentence
Before jumping into neural networks, lets play with **Markov Chains**, a simpler algorithm to use probability to write setences.

### Collecting Data

## Neural networks

### Models

### Training Models

## Representing Text as Numbers

The way neural networks are currently setup, they work best with numbers. When you train a neural network on images, you are manipulating pixel values. When working with text, we similarly need a way to represent text with numbers. How we do this representation is known as **word embeddings**.

Once you have this representation of words as numbers, you now have a neural network friendly format for your text. But there are plenty of creative opportunities for simply using these word embeddings as a way to modify text, without using a neural network at all.

## Finetuning Large Models

ChatGPT is a fine-tuning of GPT-3.5 using an approach called Reinforcement Learning from Human Feedback. What OpenAI has done is give GPT examples of a conversation: for a given prompt, what would a response look like. 

Building any large language model like GPT is nearly impossible without the resources of one of the large tech companies. However, as something like ChatGPT has shown, it is very feasible for you to be able to fine-tune one of these models yourself. 