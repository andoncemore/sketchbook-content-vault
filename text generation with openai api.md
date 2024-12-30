---
title: text generation with openai api
subtitle: Learning to use text generation models with the openai api
shortdesc: 
thumbnail: 
cssclasses: 
tags:
  - how-to
updated: 2024-10-07
---

In this tutorial, we'll explore how to integrate text generation into our p5.js projects through the openai api. 

Important Note: to use the openai api, you will likely have to put in a credit card, and monitor your usage. You also need to be careful with your personal API key - if someone gets ahold of your api key, they can run queries that will get charged to your credit card. For this reason, **we won't be using the p5.js web editor for this tutorial**. Instead, you will have to use p5.js via VSCode on your local computer. 

1. Setup VSCode and p5js using this [guide](https://p5js.org/tutorials/setting-up-your-environment/#vscode). Follow steps 1-4 in to get p5js setup in your local environment. 

You should now have the basic p5.js code in a VSCode window and your canvas showing up in your browser. 

2. Let's copy over the example code from [this github repo](https://github.com/troglodisme/OpenAI-p5.js/blob/main/1-basic-chat.js) replacing all the code in our sketch.js file. 
3. Setup an OpenAI account and get an API Key by following steps 1 and 2 in this [tutorial](https://github.com/troglodisme/OpenAI-p5.js/). You will get some free credits to get started, but eventually you will need to setup a payment method. I uncheck the option to automatically buy credits so that I can keep track of costs. 
4. Once you have an API key, paste it into line 7 of the example code. Then save your code, and take a look at the browser. If everything is working, you should be able to type a prompt into the text field and hit submit, and after a couple of seconds, you should see a response. 

```javascript
const API_KEY = "PASTE YOUR KEY HERE";
```


## Stepping through the code

Let's walk through the example code:

1. At the top, we start by creating some variables for communicating with the OpenAI API
    - `API_KEY`: This is where we store our specific API key so the OpenAI can identify who we are
    - `url`: This is the url to communicate with specifically with the ChatGPT model. If we wanted to use different parts of the OpenAI API, we might use a different url
    - `options`: This is an object that we would use when communicating with the ChatGPT API. 
2. Next, we define some variables for our p5.js sketch:
    - `myButton`: This is where we will store a reference to submit button
    - `myInput`: This is where we will store a reference to our text input
    - `myOutput`: This is the html element where we put the results from our 
3. In the setup function, we first do our basic setup. In this case we don't use a canvas, so we specify `noCanvas()`. After that, we setup all the GUI elements and place them on the page. We place the button, input field, and the output paragraph using the normal p5.js functions. 

```javascript
function setup() {

noCanvas();
background(200);

myButton = createButton("Submit");
myButton.position(540, 20);
myButton.mousePressed(getText);

myInput = createInput("Enter prompt");
myInput.position(20, 20);
myInput.size(500);

myOutput = createElement("p", "Results:");
myOutput.position(20, 80);

}
```

4. For the button in particular, notice the line where we specify that on `mousePressed`, we specify that the getText function should be called. We'll look at that function next. 

```javascript
myButton.mousePressed(getText);
```

5. The `getText()` function is where we talk to the OpenAI API. First, we get the text within the text input. The if statement checks to see if the text input is blank. If it is blank, then we skip everything else. 

```javascript
function getText(){
    var inputValue = myInput.value();
    console.log("myinput", inputValue);

    if (!inputValue) {
        return
    }
```

6. Next, we have to format the AI prompt. To do this, we create a series of messages. Imagine we are coding the messages we would send the GUI chat interface. The first message is a system message, which we use to tell the chatbot to take on a specific role. Then we send a message as a "user" role, and we send in the value from our input.  

```javascript
options.body = JSON.stringify({
    model: "gpt-3.5-turbo", // Specify the model to use
    messages: [
        {
        "role": "system",
        "content": "You are a helpful assistant."
        },
    
        {
        "role": "user",
        "content": inputValue
        }
    ]
});
```

- You can explore different ways of formatting the prompts using the "[Playgrounds](https://platform.openai.com/playground/chat)" feature in the OpenAI developer area. In Playgrounds, there is a GUI that can help you create and test prompts. You can then export those as code that you can paste into your sketch. 

7. Finally, the `fetch` portion of the code sends our message to the OpenAI API, and then handles the results. We start by calling `fetch`, passing in the `url` variable and the `options` variable that stores all of the information about our prompt. 

```javascript
fetch(url, options)
```

8. Next, we use `then` to specify what should happen once the API responds. The first step will be to reformat the response to json.

```javascript
.then(function(response){
    return response.json();
})
```

9. After this, we do another `then` to decide what to do with the response. First, we look through the responses, make sure there are options and then grab the latest one:

```javascript
.then(function(response){
    if(response.choices && response.choices[0]) {
        var assistantMessage = response.choices[0].message.content;
    }
```

10. Finally, we can save the assistantMessage to our `myOutput` html element so the user can see the response from the API. 

```javascript
console.log("response:", assistantMessage);
myOutput.html(assistantMessage);
```

## Ideas for Remixing

- Explore different prompts to chatGPT, try out different models
- Rework the input method to not just be a text field
- Try visualizing outputs in different ways, and style it using css
- Make use of the canvas to display outputs
