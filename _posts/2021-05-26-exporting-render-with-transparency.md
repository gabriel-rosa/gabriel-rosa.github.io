---
published: false
---
## Exporting a render with transparency in a way that supports blending

Recently I ran into an interesting issue at work which led to a pretty neat solution. The renderer I was working with was capable of exporting the contents of the viewport to an image by rendering them to a buffer and saving the resulting color and alpha to disk. There was an issue with this approach though: if the background was fully transparent (black background with $$\alpha = 0$$) and some of the rendered meshes were semi-transparent, the resulting image would not look correct if it was later blended with anything other than a black layer. If we embedded the image in a document with a white background, for instance, the result would look very strange.

![Incorrectly blended image]({{site.baseurl}}/img/problem.png)

To understand and correct this behavior I decided to look at how the colors were produced both for the renderer and for the exported image. First lets remind ourselves how alpha blending works. If we want to blend a transparent color (called the source color) with a background color (called the destination) which is assumed to be opaque, we first define an $$\alpha$$ parameter that determines how transparent the source is ($$\alpha = 0$$ is fully transparent, $$\alpha = 1$$ is fully opaque). Then we use the so called over operator to compute the final image (its a linear interpolation):

$$O = \alpha * C_s + (1 - \alpha) * C_d$$

![Alpha blending with the over operator]({{site.baseurl}}/img/over_operator.png)


Say our renderer exported an image with color $$C_i$$ and alpha $$\alpha_i$$, if we blend the image with a background layer of color $$B_i$$ the result is

$$O_i = \alpha_i * C_i + (1 - \alpha_i) * B_i$$

Lets see how our renderer arrives at $$C_i$$ and $$\alpha_i$$. Typically in a scene with overlapping objects with different levels of transparency, objects are rendered back to front and blended with the over operator. So if we have a background of color $$B_r$$ and we render the first transparent object ($$C_0$$, $$\alpha_0$$) over it, the resulting color will be

$$O_0 = \alpha_0 * C_0 + (1 - \alpha_0) * B_r$$

This result is then placed in the backbuffer. Rendering a second object ($$C_1$$, $$\alpha_1$$) will result in it being blended with the previous result

$$O_1 = \alpha_1 * C_1 + (1- \alpha_1) * O_0$$

$$O_1 = \alpha_1 * C_1 + (1- \alpha_1) * (\alpha_0 * C_0 + (1 - \alpha_0) * B_r)$$

$$O_1 = [\alpha_1 * C_1 + (1- \alpha_1) * (\alpha_0 * C_0)] + [(1- \alpha_1) * (1 - \alpha_0)] * B_r$$

$$O_1 = X_1 + K_1 * B_r$$

![Multiple layers being blended]({{site.baseurl}}/img/alpha_blending.png)

If we repeat this N times we'll get 

$$O_n = X_n + K_n * B_r$$

where $$X_n$$ is a constant term and

$$K_n = (1 - \alpha_n) (1 - \alpha_{n-1}) ... (1 - a\alph_0)$$

Since each rendering assumes the destination color is opaque, every alpha is discarded but the last one ($$\alpha_n$$), which is written to the backbuffer along with color $$O_n = X_n + K_n * B_r$$. 

In the case of a fully transparent background we have $$B_r = 0$$, so the final color is $$O_n = X_n$$. In order to present the backbuffer to the monitor we "blend" it with the default state which is black, so the presented color is

$$O_r = \alpha_n * O_n$$

$$O_r = \alpha_n * (X_n + K_n * B_r)$$

$$O_r = \alpha_n * X_n$$

Lets get back to the saved image. We now know that the colors that get exported are $$C_i = O_n = X_n$$ with alpha $$a_i = a_n$$, which we can substitute in the equation for the blended image color to get

$$O_i = a_n * X_n + (1 - a_n) * B_n$$

As we can see, for any value of $$B_n$$ other than 0 we will have $$O_i != O_r$$. In order to fix this we have to find a different $$C_i$$ and $$a_i$$ such that blending the image with any background color will give the same result as rendering the objects over the same background color. So we begin by setting both background colors to an arbitrary value $$B_n = B_r = B$$. Then we make the image color equal the rendered color

$$O_i = O_r$$

$$a_i * C_i + (1 - \alpha_i) * B = \alpha_n * (X_n + K_n * B)$$

$$C_i = X_n * \frac{\alpha_n}{\alpha_i} + \frac{B}{\alpha_i} * (\alpha_n * K_n + \alpha_i - 1)$$

Since we can't solve the equation with two unknowns ($$C_i$$ and $$\alpha_i$$), we set $$\alph_i$$ in a way that simplifies the equation

$$\alpha_n * K + \alpha_i - 1 = 0$$

$$\alpha_i = 1 - \alpha_n * K$$

so

$$C_i = X_n * \frac{\alpha_n}{\alpha_i}$$

![Correctly blended image]({{site.baseurl}}/img/fixed.png)

And that's it. Instead of writing the color of our rendered buffer directly to disk, we compute $$K$$ and modify the color $$C_i$$ and alpha $$\alpha_i$$ before writing to an image. Now, you might recall that $$K$$ involves the product of a bunch of different alpha values. We could figure out all the alphas involved and carry out the multiplications, or we could use a little trick. If we first render the scene with $$B = 0$$, we get

$$O_{B0} = X_n + K * B$$

$$O_{B0} = X_n$$

If we then render again with $$B = 1$$, we get

$$O_{B1} = X_n + K * B$$

$$O_{B1} = X_n + K$$

meaning we can get the value of $$K$$ by subtracting these two colors

$$K = O_{B1} - O_{B0}$$

And now we're done. Rendering the scene twice might be expensive in some cases, but it simplifies things so much that I still chose to go with this solution.

To summarize, in order to save an image of a render with transparency in a way that it can still be blended afterwards we do the following:

1. Render the scene with a white background to buffer 1
2. Render the scene with a black background to buffer 2
3. For each pixel, get the value of $$K$$ by subtracting the color of in buffer 1 from the color in buffer 2
4. For each pixel of buffer 1 with alpha $$\alpha$$, calculate a new alpha $$\alpha_i$$ using the formula $$\alpha_i = 1 - \alpha * K$$
5. For each pixel of buffer 1 with color $$C$$, calculate a new color $$C_i$$ using the formula $$C_i = C * \frac{\alpha}{\alpha_i}$$
6. Save $$C_i$$ and $$\alpha_i$$ to disk
