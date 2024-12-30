---
title: body tracking with ml5js
subtitle: Learning to use the body tracking models in ml5.js
shortdesc: exploring the body tracking models in the ml5js library by remixing the examples (rather than starting from scratch)
thumbnail: 
cssclasses: 
tags:
  - how-to
updated: 2024-10-07
---

Through [[intro to inputs with p5| workshop 1]], you should be getting more familiar with the basics of making things in p5.js. We are now going to start playing with machine learning models you can implement in p5.js. 

For this workshop, instead of starting from scratch, we are going to start from the examples in the ml5.js documentation and remix them. 


## HandPose
First take a look at the [documentation](https://docs.ml5js.org/#/reference/handpose) for this model. This model is trained to track hand points. We are going to start from the example in the documentation, and see how we can modify it 

Under the "Quick Start" section of the documentation, click on the `Open in p5.js Web Editor` button to open the example in the web editor.  Try pressing play to make sure the example is working on your computer.

1. First, go to the "index.html" page, and notice line `17`. This connects our p5.js sketch to the ml5.js library. Without this line of code, nothing else would work. 

```html
<script src="https://unpkg.com/ml5@1/dist/ml5.min.js"></script>
```

2. Let's return to the "sketch.js" file. At the top of the code, there are three variables defined, used throughout the code:
    - `let handPose`; Once we create the handPose model, it will be stored in this variable
    - `let video`: This example code detects hands from the webcam. This variable will store our webcam feed.
    - `let hands = []`: This variable will store the end result of the machine learning model, the points where the hands are located. 

3. The preload function runs **once** before everything else starts. This is when we load up the handPose model.

```javascript
function preload() {
  handPose = ml5.handPose();
}
```

4. Now let's look at the setup function. The first part of the code sets up the canvas and starts up the webcam feed:
    - You should be familiar with `createCanvas(640, 640)` already.
    - the [`createCapture(VIDEO)`](https://p5js.org/reference/p5/createCapture/) function gets the webcam feed. You can look at the documentation for how you could customize the video feed
    - After that, we use `video.size(640, 480)` to set the size and then do `video.hide()` because by default, the webcam is drawn onto the canvas. Instead, we will manually draw the webcam onto the screen ourselves. 

```javascript
function setup(){
    createCanvas(640, 480);
    video = createCapture(VIDEO);
    video.size(640, 480);
    video.hide();
```

5. Finally, as the last step of our setup function, we will start detecting hands from the webcam video. Here are the parameters we have to provide to the detectStart function. 
    - `video`: The first thing we send is the media we want to detect hands within. In this case, that is our video feed. However, we can also switch that out to be an image we load up OR even the drawing on the canvas. 
    - `gotHands`: This is a function we create ourselves. Once the model finds a result, it will call this function to give you the results. 

```javascript
handPose.detectStart(video, gotHands)
```

6. Now, let's move to the draw function. In the first line, we draw the video feed to our canvas. 
    - Try commenting out this line of code to see what happens.
    - Try replacing this line of code with a `background(240)`

```javascript
function draw(){
    image(video, 0,0, width, height);
```

7. The next section of code draws the all the hand points. Remember, the `hands` variable is where we will be storing the hand points. The ml5.js [documentation](https://docs.ml5js.org/#/reference/handpose?id=handposedetectstart) tells us what format the `hands` result will look like. 

 - Each hand is stored in a list. We can "loop" through that list to draw each hand. We create a variable called `hand` to store the specific hand.
```javascript
for (let i=0; i < hands.length; i++){
    let hand = hands[i];
}
```
- Next, we need to look at the keypoints in that hand. The keypoints are stored in `hand.keypoints`. This is **also** a list, so we will need to loop through all the keypoints to draw each one. We can store each keypoint in a variable called `keypoint` 
```javascript
for (let i=0; i < hands.length; i++){
    let hand = hands[i];
    for (let j=0; j<hand.keypoints.length; j++){
        let keypoint = hand.keypoints[j];
    }
}
```
- Finally, we can draw something on the canvas at the keypoint. Try customizing what is drawn at each point!
```javascript
fill(0,255,0);
noStroke();
circle(keypoint.x, keypoint.y, 10)
```

8. Before we explore some of the ways we could customize this more, let's look at the final part of the example code the `gotHands()` function. As a reminder, this is the function that we told ml5 to call whenever it detects a hand. Here, we just take the results and store it in our `hands` variable.
```javascript
function gotHands(results) {
  hands = results;
}
```

### Ideas for Remixing
Now that you have some intuition for what is happening in the code, here are some ideas for how to remix it:

- **Draw specific finger points**: Right now we are drawing all the fingers the same way. If we look at the [documentation](https://docs.ml5js.org/#/reference/handpose?id=handposedetectstart), each keypoint has a name. We can use an `if` statement to draw specific fingers. 
    - In the section of the code where we draw, lets only draw if we see the index_finger_tip. Now, the index finger works kind of like the mouseCursor. 
```javascript
let keypoint = hand.keypoints[j];
if(keypoint.name == 'index_finger_tip'){
    fill(0,255,0);
    noStroke();
    circle(keypoint.x, keypoint.y, 10)
}
```

- **Draw different shapes between finger points:** We can change what we draw with the keypoints. For example, lets draw a line between index finger and thumb. 
  
  Let's adjust our for loop to identify the index_finger_tip and thumb_tip. Instead of looping through all the keypoints, we can find the specific keypoints we need. Based on the documentation, thumb_tip is at position 4, and index_finger_tip is at position 8.

```javascript
for (let i=0; i < hands.length; i++){
    let hand = hands[i];
    let thumb;
    let thumb = hand.keypoints[4];
    let indexFinger = hand.keypoints[8];

    // Now draw the two finger points
    stroke(0, 255, 0);
    strokeWeight(4);
    line(thumb.x, thumb.y, indexFinger.x, indexFinger.y)
}
```

- **Confidence:** You might notice that the system is sometimes less accurate in identifying your hand position. We can check the confidence and only draw if it has a high enough confidence level

```javascript
  for (let i = 0; i < hands.length; i++) {
    let hand = hands[i];
    let thumb = hand.keypoints[4];
    let indexFinger = hand.keypoints[8];
    if(hand.confidence > 0.9){
      stroke(255, 255, 255);
      strokeWeight(10);
      line(thumb.x, thumb.y, indexFinger.x, indexFinger.y);
    }
  }


```

- **Mirror video feed** We already saw how we could turn off the webcam feed background, but once we turn if off, we also might want to mirror the video feed for better usability:
```javascript
function preload(){
      handPose = ml5.handPose({flipped: true});
}
function setup(){
    video = createCapture(VIDEO, {flipped:true});
    ...
}
function draw(){
    //image(video, 0, 0, width, height)
    background(240);
}
```


## FaceMesh

FaceMesh works the exact same way as HandPose. The one difference in the example code is the use of `options`

At the top of the code, we define some options for how we want the model to work, and then pass that into the preload function. 

```javascript
let options = { maxFaces: 1, refineLandmarks: false, flipHorizontal: false };

function preload() {
  // Load the faceMesh model
  faceMesh = ml5.faceMesh(options);
}
```

## BodyPose

BodyPose works the same way as the other keypoint identification models we've looked at. If we look over the code, there's only one thing that is different, this model has something called `connections`

```javascript
let connections;
...
function setup(){
...
connections = bodyPose.getSkeleton();
}
```

Connections help you connect the different keypoints together. It's a key that tells you which points should be connected together in a line. It will look something like this:

```
[[0, 1], [0, 4], [1, 2], ...[28, 32], [29, 31], [30, 32]];
```

We will need to loop through the list of all the connection, and draw a line between the each of the keypoints. Ex. draw a line between keypoint 0 and keypoint 1.

```javascript

//loop through each pose (if there are multiple people)
for (let i = 0; i < poses.length; i++) {
    let pose = poses[i];

    //Loop through each connection and draw a line
    for (let j = 0; j < connections.length; j++) {
      let pointAIndex = connections[j][0];
      let pointBIndex = connections[j][1];
      let pointA = pose.keypoints[pointAIndex];
      let pointB = pose.keypoints[pointBIndex];
      // Only draw a line if both points are confident enough
      if (pointA.confidence > 0.1 && pointB.confidence > 0.1) {
        stroke(255, 0, 0);
        strokeWeight(2);
        line(pointA.x, pointA.y, pointB.x, pointB.y);
      }
    }
}

```


## BodySegmentation

The Body Segmentation model works differently to the other body tracking in that it creates image masks that make it possible to mask a human subject out from the background. Feel free to read the documentation to figure out how it works. 
