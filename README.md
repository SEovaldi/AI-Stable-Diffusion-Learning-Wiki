# AI-Stable-Diffusion-Learning-Wiki

Guide Im working on to teach myself Stable Diffusion, will be an aggregate of all the resources I find and my own notes.

## Source Credits:

- Amazing work from Jay Alammar: https://jalammar.github.io/illustrated-stable-diffusion/
  <!-- @misc{alammar2022diffusion,
  title={The Illustrated Stable Diffusion},
  author={Alammar, J},
  year={2022},
  url={https://jalammar.github.io/illustrated-stable-diffusion/}
  } -->

## Components of Stable Diffusion

### High Level

#### Stable Diffusion Black Box:

![Alt text](/images/Stable_Explanation_1.png "Stable Diffusion Black Box")

- can also change existing images

![Alt text](/images/Stable_Explanation_2.png "Stable Diffusion Black Box Alter image")

### Stable Diffusion Components (Highest Level):

Lets first break Stable Diffusion into two parts

![Alt text](/images/Stable_Explanation_3.png "Text Encoder and Image Generator")

**Text Understander (Encoder)** - Special Transformer language model (technically: the text encoder of a CLIP model). It takes the input text and outputs a list of numbers representing each word/token in the text (a vector per token).

- **Input**: image prompt text
- **Output**: a list of numbers representing **each word/token** in the text (a vector per token).
  - ex: [0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9]

That information is then presented to the **Image Generator**, which is composed of a couple of components itself.

- **Image Information Center:** This component runs for multiple steps to generate image information. This is the steps parameter in Stable Diffusion interfaces and libraries which often defaults to 50 or 100

  - works completely in the image information space (or latent space).
  - **Input**: output from the text encoder
  - **Output**: image information

- **Image Decoder:** paints a picture from the information it got from the information creator. It runs only once at the end of the process to produce the final pixel image.

![Alt text](/images/Stable_Explanation_4.png "Stable Diffusion Black Box Alter image")

### The Three

With this we come to see the three main components (each with its own neural network) that make up Stable Diffusion:

![Alt text](/images/Stable_Explanation_5.png "Three components")

**ClipText** for text encoding.

- Input: text.
- Output: 77 token embeddings vectors, each in 768 dimensions.

**UNet + Scheduler** to gradually process/diffuse information in the information (latent) space.

- Input: text embeddings and a starting multi-dimensional array (structured lists of numbers, also called a **tensor**) made up of noise.
- Output: A processed information array

**Autoencoder Decoder** that paints the final image using the processed information array.

- Input: The processed information array (dimensions: (4,64,64))
- Output: The resulting image (dimensions: (3, 512, 512) which are (red/green/blue, width, height))

![Alt text](/images/Stable_Explanation_6.png "Three components Detailed")

## So What is Diffusion?

Needed Definition:

- **Tensor: Array<Latent> (Image Information Array)**: a data object that can be passed to a decoder to generate an image at any time.

![Alt text](/images/Stable_Explanation_7.png "Diffusion")

So the Image Information Center is passed a latents array AND it spits out a latents array. Both could be Decoded to images. However the one passed to the info center is **random** noise (random pixels). Its over a step by step process in the info center that the relevant information is set in the latent that is spit out.

![Alt text](/images/Stable_Explanation_8.png "initial vs final Image info array")

![Alt text](/images/Stable_Explanation_9.png "Cycle of diffusion")

Diffusion: happens in multiple steps, each step operates on the previous input latents array, and produces another latents array **that better resembles:**

- **the input text** AND
- **all the visual information the model picked up from all images the model was trained on**.

![Alt text](/images/Stable_Explanation_10.png "Visualization of steps")

We can visualize a set of these latents to see what information gets added at each step.

![Alt text](/images/Stable_Explanation_11.png "Visualization of steps")
