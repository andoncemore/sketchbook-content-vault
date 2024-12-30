---
title: making blobs
subtitle: 
shortdesc: I've been really interested in making computational blobs, and exploring the different mathematical approaches of generating them
thumbnail: 
cssclasses: 
tags:
  - wip
  - sketch
updated: 2023-12-15
---

I've been doing a deep dive into making blobs computationally for the free-form canvas "sketchbook" portion of my website. Being able to group items based on spatial relationship and then draw boundaries around those items automatically is what I'm most interested in achieving. 

# SVG Blobs
The keyword to search for to make blobs turns out to be "metaballs." The most common approach seems to be a shader or pixel based approach, but I wanted to start with a shape based approach. Being able to draw the blob boundary with something like an SVG would give me the flexibility to easily create hover effects, customize the stroke and more. 

I found this article on [SVG metaballs by Varun Vachhar](https://varun.ca/metaballs/). The approach shared in that article was inspired from the implementation in Paper.js which is also worth checking out: http://paperjs.org/examples/meta-balls/

Reimplementing the math shared in that article works pretty well. In the example below, I determined the connections between circles by a simple distance calculation. 

<iframe style="aspect-ratio: 4 / 3" src="https://editor.p5js.org/andoncemore/full/0KlzZv-gl"></iframe>

## Gravity Approach

One thing that didn't feel right in that exploration was that with different size circles, the distance calculation wouldn't make the right connections. So to incorporate the size of the circle into the calculation, I decided to go back to an equation I remembered from mechanics about planetary orbit calculations. 

F = (G * m1 * m2) / r^2

<iframe style="aspect-ratio: 4 / 3" src="https://editor.p5js.org/andoncemore/full/ECdKEUUpQ"></iframe>

## Grouped Shape

One downside of this approach of determining connections on an individual node level is that you can end up with weird gaps in what otherwise looks like one cluster/blob. If rather than a per-node connection system, I used a cluster approach where you separately calculate the cluster, and then for those given set of nodes, you figure out a way to draw a blob that encompasses all of them. 

For this approach, I need to be able to draw an exterior shape that encompasses all the points. The algorithm that seems to be able to draw the right connections between all the points in such a way that creates an encompassing shape was the [delaunay triangulation](https://en.wikipedia.org/wiki/Delaunay_triangulation). 

<iframe style="aspect-ratio: 4 / 3"  src="https://editor.p5js.org/andoncemore/full/qMW_35lLV"></iframe>

This blob shape gets more of the feeling I want as you drag elements around a screen, but requires some kind of grouping calculation to be done beforehand, which will require further investigation. (The above example treats all nodes as part of one group)


# Grouping Algorithms
Next thing I want to do is to look at various grouping algorithms I could ideally implement on the client side. 