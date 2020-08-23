---
layout: post
title: Past projects
categories: [projects]
- blog
---

* TOC
{:toc}

As a self taught graphics programmer, tutorials and personal projects were some of the best learning tools I had access to, especially personal projects. They are a fun and engaging way to learn, and at the end of the day you have something cool you can show other people. 

Here I've collected some of the personal projects I've developed ~~and abandoned~~ over the years.

## 2014

This is around the time I started to learn the basics of programming, mostly as a hobby.

#### Checkbox Renderer
![Checkbox Renderer](/img/checkbox_renderer.png){: .img-resized :}

After I taught myself the basics of javascript, I worked on this little project as a way to practice.

The name says it all, it's a very basic renderer using checkboxes as pixels. It can render points and lines (using Bresenham's line algorithm) and by default it renders a cube with perspective projection.

## 2015

This year I started getting more into computer graphics (again, as a hobby).

#### Raymarched Terrain
![Raymarched Terrain](/img/raymarched_terrain.jpg){: .img-resized :}

After reading some of [Inigo Quilez's articles](https://www.iquilezles.org/www/index.htm), I had to try my hand at implementing something similar. I don't think [Shadertoy](https://www.shadertoy.com/) was around during this time, but it would be perfect for this project.

The terrain rendering is done inside a fragment shader, which marches a ray from each pixel until they intersect (or not) a procedurally generated terrain.

#### Simulation of the Solar System
![Solar System](/img/galaxy_simulation.gif){: .img-resized :}

In this project I intended to simulate the gravitational pull between many objects (like a whole galaxy). In the end I only ended up simulating the orbit of the planets in the solar system.

#### 2D Gravity Simulation
![Gravity Simulation](/img/gravity.gif){: .img-resized :}

This is probably the first project I've made that has user interaction. Keeping in theme with gravity simulations, this project allows the user to place objects of different mass, direction of travel and velocity, and simulates the gravitational pull between them.

#### Electromagnetic Simulation with Finite Elements Method
![Finite Elements](/img/fem.png){: .img-resized :}

During one of my classes a professor showed how the Finite Elements Method could be used for electromagnetic simulations. Seeing the parallels between triangle rasterization and FEM, I decided to try and implement a simple EM simulation.

#### Maze Solver/Generator
![Maze](/img/maze.gif){: .img-resized :}

[](/maze){: .center-links .icon-link .fa .fa-eye .fa-2x :}
[](https://github.com/gabriel-rosa/maze){: .fa .icon-link .fa-github .fa-2x :}

After reading a post on stackoverflow about solving mazes with Breadth-First Search I just had to do my own implementation. The implementation also features a maze generator using Depth-First Search.

## 2016

During this time I still thought I was gonna work as an engineer after graduating. I'm glad I was wrong!

#### Endless Runner
![Endless Runner](/img/endless_runner.gif){: .img-resized :}

A very simple endless runner game I made when I was recovering from a hangover.

#### Image Filter Sandbox
![Filter Sandbox](/img/filter_sandbox.png){: .img-resized :}

As a way to teach myself some web development tools such as bootstrap and angular, I made this little single-page application to play around with image filters. The basic idea was to have a number of convolutional filters in a stack, but I ended up adding basic shader support after realizing how powerful that would be.

This is far from a finished project (you can't even add new filters, having to use and modify pre-existing ones), but at least it's in a state where you can play around.

## 2018

Nearing the end of my graduation, I started to focus on job searching and found that there **are** companies where I live that deal with computer graphics.

#### Tinyrenderer
![Tiny Renderer](/img/tinyrenderer.jpg){: .img-resized :}

[](/tinyrenderer){: .center-links .icon-link .fa .fa-eye .fa-2x :}
[](https://github.com/gabriel-rosa/tinyrenderer){: .fa .icon-link .fa-github .fa-2x :}

After seeing an open position at [Audaces](https://www.audaces.com), I decided to take on a rendering-focused project to put on my resume before applying. Luckily I came across the excellent [tutorial series on creating a software renderer](https://github.com/ssloy/tinyrenderer) by ssloy.

Following the tutorial series, I made my own implementation in javascript, so the render could run on the browser. It features line and triangle rasterization, texture mapping, a depth buffer, normal mapping, shadow mapping and SSAO! Rendering of course is really slow, and for that reason only a single frame is rendered.
