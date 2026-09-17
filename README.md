# Neural Radiance Field

Implementing a Neural Radiance Field from scratch. Newer methods like Gaussian Splatting
have made things faster and improved the quality of rendering, but building this one from
scratch is what makes it click.

**Part 1 is in [`nerf1.ipynb`](nerf1.ipynb), Part 2 is in [`nerf3.ipynb`](nerf3.ipynb).**
There is a `requirements.txt` for all the libraries we need. I have saved the results of
the tasks into the folder called `output`.

## Part 1: Fit a neural field to a 2D image

Before we jump into implementing a real NeRF, we will first try it out on a 2D image. NeRF
is basically a neural network that has learned a 3D scene.

Since we are starting with 2D, we first create a model that only takes 2D input, so the xy
coordinates of the image. It then returns the RGB color of the pixel.

Our architecture is a simple feedforward neural network with 3 hidden layers. We use ReLU
as our activation function, but we also use a Sinusoidal Positional Encoding (PE). We keep
the original input in the PE.

We implement a dataloader that randomly selects N samples at every iteration during
training, giving us the coordinates of the pixel as Nx2 and also the RGB values as Nx3.

Hyperparameters used: learning rate 1e-2, hidden size 256, L = 10, batch size 10.000,
trained for 1000 iterations.

Setting L to a very high value (L = 40) does not improve the result. Setting our hidden
dimension to 32 instead of 256 also does not improve it.

## Part 2: Fit a neural radiance field from multi-view images

Now that we are familiar with the basics, we can transition to 3D, using the Lego
Bulldozer dataset from the original NeRF paper.

First we implement the conversion of 3D camera coordinates into 3D world coordinates, then
the conversion of 2D image coordinates into 3D camera coordinates, then turning pixel
coordinates into rays. We find the camera origin and the ray direction, then adjust our
dataloader to sample rays from the images.

We can use viser to visualize our cameras, rays and points on our rays, to make sure we
have the right setup.

This new architecture is very similar to our old network we used in 2D, but we extend it
to accept 3D input: a 3D point in our space and a 3D ray direction. We make it output a 3D
RGB color and also a density value. We use Sigmoid to set the output color within range
(0, 1), and ReLU to set the output density to be non negative.

Once we have set up our network we create our volume rendering function. We have to use
the discrete version of this function, as we can not integrate over the continuous space.
We can understand this through an analogy, where the ray travels through the volume and
depending on the density and color picks up Color and ends up with a saturated final
color.

We train 1000 iterations with a batch size of 10.000 pixels and a learning rate of 1e-3.

I have used the camera intrinsics from our test dataset to generate images and create a
spinning 3D gif view of our Lego Bulldozer. Finally we also display the depth, which is
very similar to the volume rendering part, only that we output our sigma.
