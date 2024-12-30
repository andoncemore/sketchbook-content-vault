---
title: "everything as input: workshop 1"
subtitle: Getting familiar with p5 and prototyping cursors, buttons, and text fields
shortdesc: 
thumbnail: 
cssclasses: 
tags:
  - how-to
updated: 2024-09-17
---

In this workshop, we will explore p5.js as a tool to prototype with basic digital inputs. In future workshops, we will build upon the foundation to use machine learning models.
- Setting up p5.js
- Cursors
- Buttons
- Text Fields

# Setting up p5.js

1. Follow the instructions on the [Setting Up Your Environment](https://p5js.org/tutorials/setting-up-your-environment/) guide. Specifically follow the instructions to setup the p5.js web editor. 
2. If you would like your p5.js sketches to take up the full screen, use the following code:
```javascript
function setup() {
    // windowWidth and windowHeight are variables available in p5. We will set our canvas size to take up the entire available space. 
    createCanvas(windowWidth, windowHeight);
}

function draw() {
    background(220);
}

// When you resize the window, p5 will call this function. We will use this as an opportunity to resize the canvas to the new window width and height.  
function windowResized(){
    resizeCanvas(windowWidth, windowHeight)
}
```


# Cursor
<iframe style="aspect-ratio: 4 / 3" src="https://editor.p5js.org/andoncemore/full/4ejOnixEO"></iframe>

To prototype cursor interactions in p5, we can hide the mouse default mouse cursor and draw a cursor on the canvas at the mouse position. 

Let's start by drawing a circle at the location of your cursor. The current mouse position on the canvas is given by `mouseX` and `mouseY`. In the draw function:

```javascript
function draw() {
    //if we draw a background every frame, we basically are clearing the canvas.
    background(220);
    
    //the circle function takes an x position, y position, radius
    circle(mouseX, mouseY, 10)
}
```

1. Try removing the `background(220)` line from your draw function. What happens?

To prototype new types of cursors, we most likely would want to hide the default system cursor. We can do this with the p5 function `noCursor()`. We can set this in the setup function of our code. 

```javascript
function setup() {
    createCanvas(windowWidth, windowHeight);
    noCursor();
}
```


### Prototyping different cursors
Now that we have the basic cursor setup, try modifying things to play with different forms of input. The[ Interactivity page](https://p5js.jp/learn/interactivity.html) on the p5 reference has a bunch of demos that you can copy+paste into your code and modify. 

1. **Change Color:** You can change the color of your cursor by setting the stroke prior to drawing the circle. See p5.js reference on [stroke](https://p5js.org/reference/p5/stroke/) for more examples.

```javascript
function draw() {
    background(220);
    stroke('rgb(100,50,20)');
    circle(mouseX, mouseY, 10)
}
```

2. **Change Size:** We can change the radius of the circle. For example, based on the mouse position, we can make the circle bigger or smaller.

```javascript
function draw() {
    background(220);
    stroke(rgb(100,50,20));
    circle(mouseX, mouseY, 10*mouseX/500);
}
```

3. **Change Frame Rate:** p5 re-draws the canvas at a certain frame rate. We can change the frame rate to make the cursor feel more "choppy." See the [p5 documentation](https://p5js.org/reference/p5/frameRate/) for more details.  In the setup function:

```javascript
function setup() {
    createCanvas(windowWidth, windowHeight);
    noCursor();
    frameRate(12);
}
```

4. **Add Randomness:** p5js has a function called random which you can use to generate random numbers between two numbers. We can use this to create something like a mouse jitter. 

```javascript
function draw(){
  background(220);
  circle(mouseX+random(-50, 50), mouseY+random(-50, 50), 10);
}
```

6. **Use Previous Mouse Position:** p5.js tracks the position of the mouse position from the previous frame. We can use this to draw a line between the previous position and current position to highlight movement.  (instead of drawing a circle).

```javascript
function draw(){
  background(220);
  line(mouseX, mouseY, pmouseX, pmouseY);
}
```

7. **Fade Cursor:** Each loop of the draw function, we clear everything from the canvas using the `background()` function. Instead of clearing everything, we can use a transparent background to slowly erase the background. If we remove the `background(220)` line entirely, we can create a permanent trace of the cursor. 

```javascript
function draw(){
  background('rgba(220, 220, 220, 0.2)');
  circle(mouseX, mouseY, 20);
}

```

8. **Other Shapes:** We don't have to represent the cursor with just a circle. p5 provides functions to draw other [shapes](https://p5js.org/examples/shapes-and-color-shape-primitives/), replace your [cursor with an image](https://p5js.org/examples/imported-media-copy-image-data/), [draw text](https://p5js.org/examples/imported-media-words/) and more. Explore the p5.js reference to see what all you can do. 

### Other Examples
Here are some other examples to get inspiration from and to borrow code from:

- [Assorted examples from p5 documentation](https://p5js.jp/learn/interactivity.html)
- [Cursor Spinning in a Circle](https://editor.p5js.org/qc822/sketches/2ulNQOe5Y)
- [Changing Cursor Icon](https://editor.p5js.org/wisemonkey/sketches/ByIdMHABm)
- [Triangle Cursor](https://editor.p5js.org/jonathanheckerman/sketches/CEwf5ZQE5)
- [Mouse Following](https://medium.com/@maxswann/p5-js-mouse-following-tutorial-using-vectors-188481008b99)
- [Computer Mouse Conference & Research](https://inbetweenhumansandcomputers.net/)
- [Mouse Cursor are.na board](https://www.are.na/maxim-leyzerovich/mouse-cursor)
- [Unusual Cursors are.na board](https://www.are.na/unusual-internet/unusual-cursors)

# Button

<iframe style="aspect-ratio: 3 / 4" src="https://editor.p5js.org/andoncemore/full/CZfl8JNNv"></iframe>

There are two ways we can prototype buttons with p5:
1. We could draw buttons onto the canvas. (Ex. draw a rectangle). This would give you the freedom to imagine buttons that take non-traditional shapes and forms. 
2. We could use the standard html button, and style it using css and define with p5.

### Prototyping with HTML button

In p5, we can create a html button by using the [`createButton()`](https://p5js.org/reference/p5/createButton/) function. 

```javascript
function setup() {
    noCanvas();
    let button = createButton('click me');
}

function draw() {
}
```

Because we don't need a canvas, we don't use the `createCanvas()` function, and instead specify `noCanvas()`.

We can start to customize our button using a variety of functions provided to us from p5. This [p5 reference page](https://p5js.org/reference/p5/p5.Element/) highlights all your options. 

1. **Set css styles**: We can create the styles of our button directly in p5
```javascript
function setup() {
    noCanvas();
    let button = createButton('click me');
    button.style('color', 'blue');
    button.style('font-size', '18px');
}
```

2. **Set class name**: Rather than setting each individual style in p5, we can specify a class name and then apply styles in the css file:
```javascript
function setup() {
    noCanvas();
    let button = createButton('click me');
    button.class('mybutton');
}
```
and in your css file:
```css
.mybutton{
    color: blue;
    font-size: 18px;
    padding: 8px;
}
```

3. **Clear Existing Button Styles:** Buttons have a default style applied, and to get rid of those styles, here is some css you can use as a starting point:

```css
button {
    display: inline-block;
    border: none;
    padding: 1rem 2rem;
    margin: 0;
    text-decoration: none;
    font-size: 1rem;
    cursor: pointer;
    text-align: center;
    background: white;
    color: black;
}
```

4. **Events**: We can also define what happens in response to how people interact with your buttons. What happens when someone clicks on the button? How does the button respond once you release the button? Below is a simple example of using the `mouseClicked` and `mouseOver`. Other events you have available to you from p5:
    - doubleClicked
    - mouseWheel
    - mouseReleased
    - mouseOut
    - touchStart
    - touchEnd

```javascript

let button;
function setup() {
    noCanvas();
    button = createButton('click me');
    button.mouseClicked(buttonPressed);
    button.mouseOver(moveButton)
}

function buttonPressed(){
    button.style('background-color', 'lightblue');
}

function moveButton(){
    button.position(random(0, 100), random(0, 100));
}
```

5. **Moving Position:** We can move the position of the button by using the position attribute

```javascript
function setup() {
    noCanvas();
    let button = createButton('click me');
    button.position(100, 200);
}
```

6. **Change the button text:** We can change the main text of the button at any time by setting the "value" of the button. 

```javascript
function setup() {
    noCanvas();
    let button = createButton('click me');
    button.value('New Word')
}
```

7. **Draggable**: You can make any element in p5 draggable 
```javascript
function setup() {
    noCanvas();
    let button = createButton('click me');
    button.draggable();
}
```

Here's another example of how you might mockup a button in a specific context by using a screenshot in the background:
<iframe style="aspect-ratio: 4 / 3"  src="https://editor.p5js.org/andoncemore/full/6RFI2Wfr0"></iframe>


### Prototyping by drawing on canvas
Another approach to prototyping buttons will be to use the p5 canvas. The advantage of using this approach is that there are less constraints. However, that also means there are less convenience functions like `button.mousePressed()` for you to use. 

1. Let's start by drawing a shape for our button
```javascript
function setup() {
    createCanvas(windowWidth, windowHeight);
}

function draw() {
  background(220);
  fill('blue');
  noStroke();
  rect(20,20, 300, 300*2/3)
}

function windowResized(){
    resizeCanvas(windowWidth, windowHeight)
}
```

2. For this example, lets store our button size in a variable so we can change it once the button is clicked.
```javascript
let size = 300;
function draw() {
  background(220);
  fill('blue');
  noStroke();
  rect(20,20, size, size*2/3)
}
```

3. To figure out when someone clicks on our button, we can use the `mouseClicked()` function. This function will be called whenever someone clicks on the canvas. When someone clicks, let's decrease the size of our button. 
```javascript
let size = 300;
function draw() {
  background(220);
  fill('blue');
  noStroke();
  rect(20,20, size, size*2/3)
}

function mouseClicked(){
    size = size - 10;
}
```

4. If we try the code, we'll see that the button does get smaller, but we can click anywhere on the canvas, not just the button. To know that the user clicked on the button, we will have to look at the mouse position and check whether it is where we drew the button on the canvas. 
```javascript
function mouseClicked(){
    if(mouseX > 20 && mouseX < 20+size && mouseY > 20 && mouseY < 20 + size*2/3){
        size = size - 10;
    }
}
```

5. Let's try to create a hover effect for our button. We can do the same thing we did in mouseClicked, but using the `mouseMoved()` function:

```javascript
function mouseMoved(){
    if(mouseX > 20 && mouseX < 20+size && mouseY > 20 && mouseY < 20 + size*2/3){
        cursor(CROSS);
    }
    else{
        cursor('default');
    }
}
```

<iframe style="aspect-ratio: 4 / 3" src="https://editor.p5js.org/andoncemore/full/CZfl8JNNv"></iframe>


6. What other interaction options do you have?
    - mouseDragged()
    - mouseWheel()
    - mouseReleased()
    - mousePressed()
    - doubleClicked()


### Other Examples and References
- [100 CSS Button Designs](https://ui-buttons.web.app/)
- [Unusual Buttons are.na board](https://www.are.na/unusual-internet/unusual-buttons)
- [Interface and buttons are.na board](https://www.are.na/mechagear-moon/interface-and-buttons)

# Text Input

Just like with the buttons, we can prototype text inputs using the built-in HTML element or we can implement text inputs by drawing directly onto the canvas. 

### Prototyping with HTML text input

In p5, we can create an input using the [`createInput()`](https://p5js.org/reference/p5/createInput/) function. 

```javascript
let inp;
function setup() {
    noCanvas();
    inp = createInput();
}

function draw() {
}
```

Once we create the function, just like with the button, we can style the input by applying styles in your p5 sketch, or by applying a class.

```javascript
let inp;
function setup() {
    noCanvas();
    inp = createInput();
    inp.class('myInput');
}

function draw() {
}

```

In our css, we can style the input however we want. In this case, I just increased the font size and set the width to 24ch (24 characters wide). 

```css
.myInput{
  font-size: 24px;
  width: 24ch;
  font-family: monospace;
}
```

1. The first thing we can do is to track what people people are typing by using either the `changed()` function or `input()`:
    - The **changed()** function is called whenever a person finishes filling out the field
    - The **input()** function is called whenever a person presses any key in the field. 

```javascript
let inp;
function setup() {
    noCanvas();
    let inp = createInput();
    inp.class('myInput');
    inp.input(onTyping)
    //try switching 'input' to 'changed' and see what happens.
}

function draw() {
}

function onTyping(){
    console.log(inp.value())
}
```

2. Based on the text someone types, we can do something. Say for example I wanted to try to create a typewriter effect: When the input string is above 24 characters, write the line of text permanently to the screen, and clear the input. 

```javascript
let inp;
function setup() {
    noCanvas();
    let inp = createInput();
    inp.class('myInput');
    inp.input(onTyping)
    //try switching 'input' to 'changed' and see what happens.
}

function draw() {
}

function onTyping(){
    // Check if the length of text fills the whole input
    if(inp.value().length > 23){
        // Create a permenent record of that line
        createDiv(inp.value());
        // Clear the input
        inp.value("");
    }
}
```

<iframe style="aspect-ratio: 4 / 3"  src="https://editor.p5js.org/andoncemore/full/UYMmpyHUt"></iframe>

### Other Examples and References

- [Tutorial on Text and Type in p5](https://creative-coding.decontextualize.com/text-and-type/)


# Assignment

**Reading:**
[Too much Information](https://www.e-flux.com/architecture/intelligence/310403/too-much-information/)
[Trust as Social Reality](https://drive.google.com/file/d/1vc9tknu6C5715ZU85CKIqJMDblOxOcfX/view)


**Secondary Research:** Research a form of sensing or new sensor and bring it to class next week for discussion. 

**Technology-Centered Research**: Pick a specific context and prototype 2-3 different inputs (buttons, cursors, text fields, or other). Be prepared to show/demo and discuss what you did, what you learned and your opinions. No presentations

- **Context**: Select a space, relationship, behavior, or activity
- 
- **Document:** Document your sketches, insights and reflections in a "notebook" of your choosing - make sure it is shareable. (Ex. a website, google doc, google slides). Record your work's process through screenshots, recordings, and links. 

Use the workshop on prototyping cursors, buttons, and text fields, and last weeks readings to assist you in the investigation.

 Be prepared to show/demo and discuss what you did, what you learned and your opinions. No presentations

- **Context**: Select a space, relationship, behavior, or activity
- **Input States:** Identify 2-3 states
- **Output Sketches:** What is communicated to user(s) when a certain state is reached?
- **Document:** Document your sketches, insights and reflections in a studio notebook of your choice. Record your work's process through screenshots, recordings, and links. 


# Discussion Questions

- What is one of your takeaways from the reading?

- The reading talks about the God's Eye View asserting itself through simulations. What other perspectives can machines take? What context do they take those perspectives?

- What are different dimensions by which machines "see"

- Tool making as metaphors

- Being "typed" and how we take on different states. 

- Computation projects a future that is like the past, which makes it incapable of dealing with the present which is never stable

- If you were to fill out a god's eye view of society, what bodies do you imagine in it? What do you look like in this simulation? What exactly is the model of your body moving through time? What does this simulation account for, or not account for? What hidden or not sensible qualities are erased? What are you able to name easily? What are your blind spots? What should the machine eye visualize that you cannot? What is the simulation of America in which a person of color lived a full and healthy life? In which the mentally ill were cared for? In which debt slavery was abolished? In which racialized capitalism was acknowledged as real and accounted for in all aspects of society? What could technology look like if it were not built around efficiency alone, if history and narrative context were not costly aspects to be erased, but in fact essential to a complete simulation? How would our seeing, naming, and knowing change, if the practice of technology was not framed so relentlessly as constituting objective observation of phenomena, but instead as an active creator of an illusion of empirical, measurable, stable, and separate world?