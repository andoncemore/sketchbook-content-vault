---
title: image classification with ml5js
subtitle: Learning to use the the image classification model in ml5.js
shortdesc: been exploring how to use image classifiers in the browser with ml5js, and especially playing with the teachable machine integration to bring in classifiers you train with your own datasets
thumbnail: 
cssclasses: 
tags:
  - how-to
updated: 2024-10-07
---

Through [[intro to inputs with p5| workshop 1]], you should be getting more familiar with the basics of making things in p5.js. We are now going to start playing with machine learning models you can implement in p5.js. 

For this workshop, instead of starting from scratch, we are going to start from the examples in the ml5.js documentation and remix them. 

## ImageClassifier

First, let's take a look at the documentation page for the [ImageClassifier](https://docs.ml5js.org/#/reference/image-classifier) model.  The description of this model tells us how we can use this to detect objects in images and from video. It also mentions how it comes with a pre-trained model, but we can load up other custom trained models to detect other objects too. 

### Working with Images

Lets start by loading up the example in the documentation and try to modify it. Under the "Quick Start" section of the documentation, click on the `Open in p5.js Web Editor` button to open the example in the web editor.  Try pressing play to make sure the example is working on your computer.

1. First, go to the "index.html" page, and notice line `17`. This connects our p5.js sketch to the ml5.js library. Without this line of code, nothing else would work. 

```html
<script src="https://unpkg.com/ml5@1/dist/ml5.min.js"></script>
```

2. Let's return to the "sketch.js" file. At the top of the code, there are four variables:
    - `let classifier`: Once we create our ImageClassifier model, we will store it in this variable. 
    - `let img`: This is where we will store the image we want to evaluate
    - `let label`: Once we evaluate the image, our "result", a category name, will be stored here. 
    - `let confidence`: Another result from our evaluation is how confident the model is in the result. We will store that in this variable. 

3. The preload function runs **once** before everything else starts. This is when we load up the imageClassifier model and load up our image. 
```javascript
function preload() {
  classifier = ml5.imageClassifier("MobileNet");
  img = loadImage("images/bird.jpg");
}
```

4. Now let's look at the setup function. After creating our canvas like usual, we classify the image and draw the image to the screen. 
    - `classifier.classify(img, gotResult)`: This line of code tells our `classifier` variable (that we loaded in the preload function) to classify the `img` variable. We also tell the classifier to call the `gotResult` function when it has an answer. (we will create the gotResult function next)
    - `image(img,0,0,width, height)`: This simply draws the image we loaded at the position 0,0 and sets the height and width of the image to be the height and width of the canvas. 

5. You might notice **there is no draw function** that we are typically used to seeing in a p5js sketch. Because we are evaluating a single image, one time, we only need to use the setup function. The draw function is called many time, which would be a waste in this scenario. 

6. Finally, lets look at the `gotResults` function at the bottom of the file. This is the function that is called once there are results from our classifier.
    - This function is sent a variable called `results` which has all the results data. In the first line of the function, notice that we are printing the results via `console.log(results)`. Look at the console section of the p5js editor, and use the arrows to look at what the results variable looks like. Results contains a list of possible labels for the image. For each possibility, there is a label and a confidence score, and it looks like the highest confidence label is first in the list. 
    - The next lines of code prepare to write text to the screen:

```javascript
  fill(255);
  stroke(0);
  textSize(18);
```

- To access the most confident label from the results variable, we need to get the first one, which is at the 0th spot in the list. We can store the label and the confidence into the variables we created earlier. 

```javascript
label = "Label: " + results[0].label;
confidence = "Confidence: " + nf(results[0].confidence, 0, 2);
```

- The `nf(...)` function being used to store the confidence is a built-in function in p5 to turn a number into a string. In this case, it says to turn it into a string with only 2 decimal points. 

- The final step of the gotResults function is to draw the text to the screen. 
```javascript
  text(label, 10, 360);
  text(confidence, 10, 380);
```

### Working with Video

Let's see what you would have to change to work with video. Go back to the ml5.js reference and go to the '[Examples](https://docs.ml5js.org/#/reference/image-classifier?id=examples)' section. Under there, click on 'ImageClassifier Video'. Click run to make sure the code works in your browser. 

1. Let's look at the variables at the top again. The only difference you should notice is that instead of image, we are creating a variable called `video` to store our webcam video. 
2. In the preload function, we still instantiate our classifier. We no longer need to load an image here because we will be using the webcam. 

```javascript
function preload() {
  classifier = ml5.imageClassifier("MobileNet");
}
```

3. In the setup function, after setting up the usual canvas and background, we see the code to setup our webcam. We set the size of the video to the same size we have the canvas, and then we hide the video. (By default, the video is drawn onto the screen, but we want to do that manually.)

```javascript
  video = createCapture(VIDEO);
  video.size(640, 480);
  video.hide();
```

4. In the last step of the setup function, we begin the classification. This time, we are continuously classifying frames of the video (not just a single image), so we use the `classifyStart` function. We pass in the `video` variable we just setup and then the same `gotResult` function that will be called once the classifier finds results. 

```javascript
  classifier.classifyStart(video, gotResult);
```

5. Now, let's move onto the draw function. When using video, we want to use the draw function because the video frame is constantly changing, and we want to update the labels as the classifier updates the results. 
    - `image(video, 0,0)`: draw the current frame of the video to the position 0,0. We don't se the size because we defined the default size of the image when creating the video feed. 
    - `fill(255); textSize(32)`: Format our text size and color
    - `text(label, 20,50)`: Draw the current label, stored in the `label` variable to the canvas. 

6. Finally, let's look at the `gotResults` function. The results object being passed to this function will take the exact same format as when we were doing static images. Results will be a list of possible labels - we can look at the first item in the list and save the label to our `label` variable. If we wanted, we could also save and use the `results[0].confidence`.

```javascript
function gotResult(results) {
  label = results[0].label;
}
```


### Working with Other Models

When we created the classifiers in the two examples above, you'll notice that we specified `MobileNet`. MobileNet is one of many pre-trained ImageClassification models you could use. MobileNet is trained on the ImageNet dataset, which results in a specific set of categories which you can find a list of [here](https://raw.githubusercontent.com/pytorch/hub/master/imagenet_classes.txt). Other models will offer different categories, and you can also train your own model to identify categories that you define. 

The other options that come out of the box with ml5.js are:
- **DoodleNet**: This was created by Google and trained on a dataset of doodles called Quick Draw. You can build from this [p5js example](https://editor.p5js.org/ima_ml/sketches/IbXlN6voN) and take a look at the [original dataset](https://quickdraw.withgoogle.com/data) to see all the possible categories. 
- **DarkNet and tinyDarkNet**: This is another standard image classification model. It has a different architecture to MobileNet and can sometimes be faster. You'll have to explore to figure out what categories are included in the pre-trained model. 


## Ideas for Remixing

Now that you are comfortable with using the ml5js ImageClassifier examples, try to remix the code. Here's some things to try:

- Use the *confidence* score to change how you display the label in the p5js sketch. Maybe less confidence should be less visible? 
- Use the webcam example and walk around with your laptop to see how the model responds to different places. Where is does it do well? Where is it bad? What is the best confidence score you can get?
- Try classifying different kinds of images: What if you tried to classify a satellite image?  What do you think it would see?
- Try to use the canvas as your image that you are classifying (use the doodlenet example)
- Do something creative with the text labels - don't just display them as text in the corner. 

