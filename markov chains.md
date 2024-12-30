---
title: markov chains
subtitle: basic introduction to implementing a markov chain
shortdesc: making your own markov chains feels so relevant to understanding how LLMs work because they help you get a feel for probabilistic text generation at a more manageable scale
thumbnail: 
cssclasses: 
tags:
  - how-to
updated: 2024-02-15
---

Through this tutorial, we'll walk through how to generate text using a Markov Chain. First, to get a feel for the calculations that happen, we'll make a simple one ourselves. Then, we'll see how to use a python library to do the same thing, in a more robust fashion. 

You can integrate this generated text code into a [[building bots|discord bot]] to make it an interactive experience. 

## Markov Chain from Scratch

All of the code explained here is covered in the excellent [Coding Train video](https://www.youtube.com/watch?v=eGFJ8vugIWA) on the topic. (the only difference is this is python, and that was in p5 javascript)

1. Let's start by finding a source text to base our generation on. In this case, I found a lorem ipsum generator of dialog from the tv show Futurama
```python
txt = "Oh yeah, good luck with that. Who's brave enough to fly into something we all keep calling a death sphere? Tell her you just want to talk. It has nothing to do with mating. Fry, you can't just sit here in the dark listening to classical music.Take me to your leader! Anyhoo, your net-suits will allow you to experience Fry's worm infested bowels as if you were actually wriggling through them. Okay, it's 500 dollars, you have no choice of carrier, the battery can't hold the charge and the reception isn't very… Then we'll go with that data file! Do a flip! Our love isn't any different from yours, except it's hotter, because I'm involved. I barely knew Philip, but as a clergyman I have no problem telling his most intimate friends all about him. Leela, are you alright? You got wanged on the head. We'll go deliver this crate like professionals, and then we'll go home. No, she'll probably make me do it. Yes! In your face, Gandhi! Then throw her in the laundry room, which will hereafter be referred to as the brig. Bite my shiny metal ass. You'll have all the Slurm you can drink when you're partying with Slurms McKenzie! I saw you with those two ladies of the evening at Elzars. Explain that."
```
2. Now, we need to process the source text and create a dictionary of sorts that tells us for an arbitrary section of text (word, character, 3 characters, etc.), what are all the possible words that could follow. That would look something like this:
```
'Oh ': [y, w, a, g]
'the': [a, r, i]
'hro': [a, a, r, i, i, w, w, w]
...
```

So let's make a variable called ngrams to hold our dictionary, and a variable called order to define how long each of the "words" in our dictionary should be. 

```python
ngrams = {}
order = 3
```
3. Next, let's loop through all the characters in the source text. We stop "order" number of characters before the end because that is where the last "word"/ngram is. 
```python
ngrams = {}
order = 3

for i in range(0, len(txt)-order)):
    # do something
```
4. Now, lets find the ngram at that location, find the character that follows it, and add it to our dictionary
```python
ngrams = {}
order = 3

for i in range(0, len(txt)-order)):
    # 1. get the "word" for our dictionary
    gram = txt[i:i+order]
    # 2. If the word doesn't exist in our dictionary already, let's add it
    if(gram not in ngrams):
        ngrams[gram] = []
    # 3. Let's add the following character to the list stored in the dictionary
    ngrams[gram].append(txt[i+order])

```
5. Now that we have our dictionary calculated, we can use it to generate new text. Let's start by finding a "word" to begin with. To make it easy, let's just use the first word in our source text. We'll also make a variable to store our generated text. 
```python
currentGram = txt[0:order]
result = currentGram
```
6. Next, let's find all the possibilities for the next character in our generated text and pick one at random. 
```python
import random

currentGram = txt[0:order]
result = currentGram

possibilities = ngrams[currentGram]
result += random.choice(possibilities)

print(result)
```
7. We've now generated a single character, but let's use a loop to generate many more characters
```python
import random

currentGram = txt[0:order]
result = currentGram

# range sets number of generated characters
for i in range(100):
    possibilities = ngrams[currentGram]
    result += random.choice(possibilities)
    # update currentGram to be the last three characters
    currentGram = result[-order:] 

print(result)
```
8. One final polish we need to add is what happens if a n-gram doesn't exist in our dictionary? In that case, we should just end the generated text. 
```python
import random

currentGram = txt[0:order]
result = currentGram

# range sets number of generated characters
for i in range(100):
    # Make sure the currentGram is in our dictionary
    if currentGram not in ngrams:
        break
        
    possibilities = ngrams[currentGram]
    result += random.choice(possibilities)
    # update currentGram to be the last three characters
    currentGram = result[-order:] 

print(result)
```

All of the code together is below. Play around with different "order" values and see what kinds of results you get. There are clearly limitations with out simple implementation - for example treating uppercase and lowercase letters differently. 
```python
import random

ngrams = {}
order = 3

for i in range(0, len(txt)-order)):
    gram = txt[i:i+order]
    if(gram not in ngrams):
        ngrams[gram] = []
    ngrams[gram].append(txt[i+order])

currentGram = txt[0:order]
result = currentGram


for i in range(100):
    if currentGram not in ngrams:
        break
        
    possibilities = ngrams[currentGram]
    result += random.choice(possibilities)
    currentGram = result[-order:] 

print(result)
```

## Markovify Library
There are many libraries that implement Markov Chains for us that we can use. One such library is called [Markovify](https://github.com/jsvine/markovify). Install the package with pip (or in Replit using the package manager). Below is the basics of the library, but explore the documentation for more. 

### Simple Example
```python
import markovify

text_model = markovify.Text(txt, state_size=1)
print(text_model.make_sentence())
```
By default, Markovify doesn't split the text into ngrams. Instead, it splits by words. The state_size parameter changes how many words are split (default is 2 words, but for small amounts of text, you get more interesting results using 1)

### Provide a starting point
If we want to make sentences but with a starting point, Markovify has another function called `make_sentence_with_start`. You have to make sure that the "start" you provide is definitely in the source text. 
```python
import markovify

text_model = markovify.Text(txt, state_size=1)
print(text_model.make_sentence_with_start(beginning="Oh"))
```