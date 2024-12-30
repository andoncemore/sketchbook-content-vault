---
title: creating a decision tree
subtitle: tutorial on creating a decision tree based discord bot
shortdesc: building a binary tree algorithm as a chatbot feels foundational to exploring algorithmic decision making, including this current era of LLM based systems
thumbnail: 
cssclasses: 
tags:
  - how-to
updated: 2024-02-15
---

This tutorial walks through the use of a binary tree algorithm in a discord chat bot by making the Animal Guessing Game. This tutorial assumes you have the Hello World discord bot from the [[building bots|building bots tutorial]]. 

You can see a working example of this code on replit here: https://replit.com/@andoncemore/Guessing-Bot-Example#main.py

## Create Node Class
1. To be able to represent a binary tree in code, all we need to do is to define a class to represent the node. 
```python
class Node:
    def __init__(self):
        # initialize our class
```

2. Next, let's define the attributes this class will have. Based on our diagraming, there are three attributes we want to track for each node:
    - the value of the node 
    - the "answer" that gets us there
    - the list of children nodes 

```python
class Node:
    def __init__(self):
        self.value = ""
        self.answer = ""
        self.children = []
```

3. Finally, let's specify the parameters to our class to initialize the attributes. We have to consider two special cases. For the root node, there is no "answer" because it is the top of the tree. And for the leaf node, there are no children nodes. So both the answer and children parameters should be optional parameters. 

```python
class Node:
    def __init__(self, value, answer="", children=[]):
        self.value = value
        self.answer = answer
        self.children = children
```

4. Let's now use our Node class to create an example binary tree. Let's start at the bottom of the tree and work up to our node. 

```python
# Our Node Class Code
class Node:
    def __init__(self, value, answer="", children=[]):
        self.value = value
        self.answer = answer
        self.children = children

# Create an example Binary Tree
option1a = Node("Dog", answer="Yes")
option1b = Node("Wolf", answer="No")

option1 = Node("Is it a pet", answer="Yes", children=[option1a, option1b])
option2 = Node("Lizard", answer="No")

root = Node("Is it a mammal?", children=[option1,option2])
```

## Ask First Question
Now that we have our Binary Tree represented in code, let's try to use the Root node to ask the first question. In the on_message function, we send a message with  the "value" attribute of root, which is the first question we need to ask. 

```python
@client.event
async def on_message(message):
  if message.author == client.user:
    return

  if(client.user in message.mentions):
    await message.channel.send(content=root.value)
```

## Create Button Responses
Next to let the user respond to the the question, let's create a view with the response options. 

1. Let's create a barebones view to start
```python
from discord.ui import View, Button

class GuessOptionsView(View):
    def __init__(self):
        super().__init__()

    # the callback function we will use to handle button press
    async def handleButtonPress(self, interaction):
        # do something once button is pressed
```

2. To determine what buttons to create (and their labels), our view needs to know the child nodes. Let's first add the current node as a parameter to our view.
3. Then, let's for loop through node.children to create a button for each child. 
```python
from discord.ui import View, Button

class GuessOptionsView(View):
    def __init__(self, node):
        super().__init__()
        for child in node.children:
            self.add_item(Button(label=child.answer))

    # the callback function we will use to handle button press
    async def handleButtonPress(self, interaction):
        # do something once button is pressed
```

We also need to link a callback function to each of the buttons. The easiest way to do this is to **create a custom Button class**. *(The reason we can't directly use the handleButtonPress as our callback is that it won't know which button was pressed.)*

5. Setup our GuessButton to accept a node as a parameter, and then use the node.answer attribute to initialize the Button. 
```python
class GuessButton(Button):
  def __init__(self, node):
    self.node = node;
    super().__init__(label=node.answer)
```

6. Next, let's define our callback function. Once the button is pressed, we can find our GuessOptionView, and call our "handleButtonPress" function, but now we can also tell the handleButtonPress which node corresponds with that button
```python
class GuessButton(Button):
  def __init__(self, node):
    self.node = node;
    super().__init__(label=node.answer)

  async def callback(self, interaction):
    await self.view.handleNode(interaction, self.node)
```

7. Now let's update our View code to use our newly created GuessButton. There are two changes we have to make - 1) Change `Button(label="")` to `GuessButton(child)` and 2) Update our handleButtonPress function to accept a node. 

```python
from discord.ui import View, Button

# Our Button Code from above
class GuessButton(Button):
  def __init__(self, node):
    self.node = node;
    super().__init__(label=node.answer)

  async def callback(self, interaction):
    await self.view.handleNode(interaction, self.node)

class GuessOptionsView(View):
    def __init__(self, node):
        super().__init__()
        for child in node.children:
            # 1. Replace Button with GuessButton
            self.add_item(GuessButton(child))

    # 2. Update our handleButtonPress to accept a Node
    async def handleButtonPress(self, interaction, node):
        # do something once button is pressed
```

8. Now the last step is to write our handleButtonPress function. Once a button has been pressed, we will want to use that node to send the next question. Along with that next question, we want to send a new set of response buttons. 

```python
async def handleButtonPress(self, interaction, node):
    await interaction.response.send_message(content=node.value, view= GuessOptionsView(node))
```

9. Now to tie it all together, we update our on_message code to send our GuessOptionsView along with the first root message. 

```python
@client.event
async def on_message(message):
  if message.author == client.user:
    return

  if(client.user in message.mentions):
    await message.channel.send(content=root.value, view= GuessOptionsView(root))
```

Before we continue, let's test what we have so far:
```python
from discord.ui import View, Button

# Our Node Class Code
class Node:
    def __init__(self, value, answer="", children=[]):
        self.value = value
        self.answer = answer
        self.children = children

# Create an example Binary Tree
option1a = Node("Dog", answer="Yes")
option1b = Node("Wolf", answer="No")

option1 = Node("Is it a pet", answer="Yes", children=[option1a, option1b])
option2 = Node("Lizard", answer="No")

root = Node("Is it a mammal?", children=[option1,option2])

# Our Button Code
class GuessButton(Button):
  def __init__(self, node):
    self.node = node;
    super().__init__(label=node.answer)

  async def callback(self, interaction):
    await self.view.handleNode(interaction, self.node)

# Our Guess Options View
class GuessOptionsView(View):
    def __init__(self, node):
        super().__init__()
        for child in node.children:
            self.add_item(GuessButton(child))
            
    async def handleButtonPress(self, interaction, node):
        await interaction.response.send_message(content=node.value, view= GuessOptionsView(node))

# ... Discord Bot Setup Code Goes Here
# ...
# ... 
# ... 

@client.event
async def on_message(message):
  if message.author == client.user:
    return

  if(client.user in message.mentions):
    await message.channel.send(content=root.value, view= GuessOptionsView(root))
```


## Make a Guess Code
When we get to the leaf node, we want to make a final guess instead of asking a question. We can edit our handleButtonPress function in the GuessOptionsView to do something different if we are at a leaf node. 

1. Let's add an if-statement to check if we are a leaf node. We can identify a leaf node by seeing if it has no children. 
```python
async def handleButtonPress(self, interaction, node):
    if(node.children == []):
        # If it's a leaf node, make a guess
    else:
        # Else ask the next question
        await interaction.response.send_message(content=node.value, view= GuessOptionsView(node))

```
2. Make the guess by sending a message
```python
async def handleButtonPress(self, interaction, node):
    if(node.children == []):
       await interaction.response.send_message(content=f'Are you thinking of a {node.value}?')
    else:
        # Else ask the next question
        await interaction.response.send_message(content=node.value, view= GuessOptionsView(node))
```
3. Once the guess is made, we need to get feedback to improve the decision tree if the guess is wrong. Let's start by making a view to hold a button to press if the guess is wrong. 
```python
class WrongView(View):
  def __init__(self, node):
    super().__init__()
    self.node = node

  @discord.ui.button(label="You are wrong")
  async def buttonCallback(self, interaction, button):
    # Do something if they are wrong
```
4. Once the button is pressed, let's open up a modal to find out the right answer. 
```python
class FeedbackModal(Modal):
  def __init__(self, node):
    self.node = node
    super().__init__(title='Machine Learning')
    self.newAnimal = TextInput(label="What animal were you thinking of?")
    self.newQuestion = TextInput(label=f'A question to distinguish from {self.node.value}')
    self.oldAnswer = TextInput(label=f'What answer gets {self.node.value}?')
    self.newAnswer = TextInput(label="What answer gets your animal?")
    self.add_item(newAnimal)
    self.add_item(newQuestion)
    self.add_item(oldAnswer)
    self.add_item(newAnswer)

  async def on_submit(self, interaction):
      await interaction.response.send_message(f'Thanks! Algorithm is updating....{self.newAnimal.value}')
```
5. When a user hits submit, we need to update our decision tree with the new data. Let's update our on_submit function
```python
async def on_submit(self, interaction):
    newChildren = [Node(self.node.value, answer=self.oldAnswer.value), Node(self.newAnimal.value, answer=self.newAnswer.value)]
    self.node.value = self.newQuestion.value
    self.node.children = newChildren
    await interaction.response.send_message(f'Thanks! Algorithm is updating....{self.newAnimal.value}')
```
6. Finally, we need to update our WrongView code to send this Feedback Modal when the button is pressed.
```python
class WrongView(View):
  def __init__(self, node):
    super().__init__()
    self.node = node

  @discord.ui.button(label="You are wrong")
  async def buttonCallback(self, interaction, button):
    await interaction.response.send_modal(FeedbackModal(self.node))
```

With everything put together, here's what you should have:
- Node Class - represents our Binary Tree
- Example Decision Tree Setup
- GuessOptionsView Class - The set of buttons we send when we ask a question
- GuessButton Class - The button we send in the GuessOptionsView
- WrongView Class - The buttons we send with a guess to get feedback
- FeedbackModal Class - The modal used to collect feedback and update the tree
- Discord Bot Basic Setup

Take a look at [replit](https://replit.com/@andoncemore/Guessing-Bot-Example#main.py) for a working example. Here's all the code we just wrote, put together:
```python
import discord
from discord.ui import View, Button, Modal, TextInput
import os

## Node Class
class Node:
  def __init__(self, value, answer="", children=[]):
    self.answer = answer
    self.value = value
    self.children = children

## Setup Example Tree
option1a = Node("Dog", answer="Yes")
option1b = Node("Wolf", answer="No")

option1 = Node("Is it a pet", answer="Yes", children=[option1a, option1b])
option2 = Node("Lizard", answer="No")
root = Node("Is it a mammal?", children=[option1,option2])

## GuessOptionsView Class
class GuessOptionsView(View):
  def __init__(self, node):
    super().__init__()
    for option in node.children:
      self.add_item(GuessButton(option))

  async def handleNode(self, interaction, node):
    # if there's no children, then it's the last node. Make the guess
    if(node.children == []):
      await interaction.response.send_message(content=f'Is it a {node.value}?', view=WrongView(node))
    # otherwise, ask the next question.
    else:
      await interaction.response.send_message(content=node.value, view=GuessOptionsView(node))

## GuessButton Class
class GuessButton(Button):
  def __init__(self, node):
    self.node = node;
    super().__init__(label=node.answer)

  async def callback(self, interaction):
    await self.view.handleNode(interaction, self.node)

## WrongView Class
class WrongView(View):
  def __init__(self, node):
    super().__init__()
    self.node = node

  @discord.ui.button(label="Wrong")
  async def buttonCallback(self, interaction, button):
    await interaction.response.send_modal(FeedbackModal(self.node))

# FeedbackModal Class
class FeedbackModal(Modal):
  def __init__(self, node):
    self.node = node
    super().__init__(title='Machine Learning')
    self.question = TextInput(label=f'A question to distinguish from {node.value}')
    self.animal = TextInput(label="What animal were you thinking of?")
    self.newAnswer = TextInput(label="And what is the answer to the question?")
    self.currentAnswer = TextInput(label=f'What answer gets {node.value}?')
    self.add_item(self.question)
    self.add_item(self.animal)
    self.add_item(self.newAnswer)
    self.add_item(self.currentAnswer)

  async def on_submit(self, interaction):
      newChildren = [Node(self.node.value, answer=self.currentAnswer.value), Node(self.animal.value, answer=self.newAnswer.value)]
      self.node.value = self.question.value
      self.node.children = newChildren
      await interaction.response.send_message(f'Thanks! Algorithm is updating....{self.animal.value}')

## Discord Bot Basic Setup
intents = discord.Intents.default()
intents.message_content = True
client = discord.Client(intents=intents)

@client.event
async def on_ready():
  print(f'We have logged in as {client.user}')

@client.event
async def on_message(message):
  if message.content.startswith('$hello2'):
    await message.channel.send(content=root.value, view=GuessOptionsView(root))

token = os.getenv("DISCORD_BOT_SECRET")
client.run(token)
```

## Read/Save Decision Tree

It's a bit annoying to have to initialize our decision tree by manually creating Nodes like we did above for our testing purposes. I've created a helper function that can parse a "script" file and initialize the tree. 

*Caveat: This will only work if you use the same Node class as I've defined above.* 

The **script format** looks like this. The structure of the tree is parsed automatically based on the number of tabs - so each line MUST be tabbed over to the appropriate amount for the tree to be created. 
```
Is it an animal?
    Yes, Is it a mammal?
        Yes, Cheetah
        No, Snake
        Maybe, Coral
    No, Chair
```

The following is the **parse function** that can read in a script file:
```python
def parseScript(filename):
  # function that takes a list of lines, and returns the root node
  def createNode(lines):
    # if the length of lines is 1, Create an End Node
    if(len(lines) == 1):
      content = lines[0].strip('\t\n').split(',')
      return Node(content[1].strip(), answer=content[0].strip())

    # Create a list of all the children nodes
    level = lines[0].count("\t") + 1
    index = 1;
    children = []
    start = 1
    while(index+1 < len(lines)):
      index += 1
      if(lines[index].count("\t") <= level):
        children.append(lines[start:index])
        start = index
    children.append(lines[start: len(lines)])

    content = lines[0].strip('\t\n').split(',')
    if(len(content) == 1):
      # Create a Root Node
      return Node(content[0], children=[createNode(child) for child in children])
    else:
      # Create a Middle Node
      return Node(content[1].strip(), answer=content[0].strip(), children=[createNode(child) for child in children])
    
  file = open(filename, "r")
  content = file.readlines() 
  file.close()
  
  return createNode(content)
```

To use the function, just pass in the path to the script file like so:
```python
root = parseScript("script.txt")
```

