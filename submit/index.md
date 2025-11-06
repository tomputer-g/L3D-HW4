# Q1

## 1.1.5. 3D Gaussian Rasterization. Perform Splatting

Here is the rendered result:

<image src="images/q1/q1_1_render.gif" />

## 1.2.2. Training 3D Gaussian Representations 

The learning rates I used for the parameters are the following:

* opacities: 0.05
* scales: 0.05
* colors: 0.05
* means: 0.01
* optimizer default: 0.05

I trained this for 1000 iterations.

The resulting mean PSNR is 28.195, and the mean SSIM score is 0.922.

Here is the training progress of the splats visualized:

<image src="images/q1/q1_2_training_progress.gif" />

Here is the rendered final result after training: 

<image src="images/q1/q1_2_training_final_renders.gif" />

## 1.3.1. Rendering using Spherical Harmonics


Rendered with spherical harmonics:

<image src="images/q1/q1_3_render.gif" />

Rendered without spherical harmonics (From q1.1.5 above):

<image src="images/q1/q1_1_render.gif" />

Taking some specific frames, we can see how view dependance affects the rendering process:

| View | Without spherical harmonics (Q1.1) | With spherical harmonics (Q1.3) | Observation Notes |
|---|:---:|:---:|:---|
| 000 | ![000](images/q1/q1_1_render/000.png) | ![000](images/q1/q1_3_render/000.png) | Note a subtle transition of the shadow when using spherical harmonics instead of the dull and discontinuous shadow on the seat. |
| 010 | ![010](images/q1/q1_1_render/010.png) | ![010](images/q1/q1_3_render/010.png) | The material of the seatback and seat clearly has a sheen that is not present from the scene without spherical harmonics, due to view dependance. |


# Q2

## 2.1. Diffusion-guided Image Optimization

**All diffusion results obtained with 2000 iterations of training.**

| Prompt | Result Without Guidance | Result With Guidance |
|---|:---:|:---:|
| `a hamburger` | ![img](images/q2/image/a_hamburger_unguided.png) | ![img](images/q2/image/a_hamburger_guided.png) |
| `a standing corgi dog` | ![img](images/q2/image/a_standing_corgi_dog_unguided.png) | ![img](images/q2/image/a_standing_corgi_dog_guided.png) |
| `a humanoid robot` | ![img](images/q2/image/a_humanoid_robot_unguided.png) | ![img](images/q2/image/a_humanoid_robot_guided.png) |
| `a sports car` |  ![img](images/q2/image/a_sports_car_unguided.png) | ![img](images/q2/image/a_sports_car_guided.png) |

It is worthy to note that all results without using guidance over SDS loss always collapses into some white-beige scene overall within a few hundred steps, while using guidance always produces non-empty results. All prompts were trained five times with identical results.

## 2.2. Texture Map Optimization for Mesh

### Prompt: "A hamburger"

<image src="images/q2/mesh/a_hamburger.gif" />

### Prompt: "A standing corgi dog"

<image src="images/q2/mesh/a_standing_corgi_dog.gif" />

### Prompt: "A pizza slice"

<image src="images/q2/mesh/a_pizza_slice.gif" />

## 2.3. NeRF Optimization

| Prompt | Result depth map | Result rendered RGB |
|---|:---:|:---:|
| `a standing corgi dog` | <video width="128" height="128" controls><source src="images/q2/nerf/a_standing_corgi_dog/depth_ep_99.mp4></video> | <video width="128" height="128" controls><source src="images/q2/nerf/a_standing_corgi_dog/rgb_ep_99.mp4></video> |
| `a hamburger` | <video width="128" height="128" controls><source src="images/q2/nerf/a_hamburger/depth_ep_99.mp4></video> | <video width="128" height="128" controls><source src="images/q2/nerf/a_standing_corgi_dog/rgb_ep_99.mp4></video> |
| `a potted plant` | <video width="128" height="128" controls><source src="images/q2/nerf/a_potted_plant/depth_ep_80.mp4></video> | <video width="128" height="128" controls><source src="images/q2/nerf/a_potted_plant/rgb_ep_80.mp4></video> |
| `a sports car` | <video width="128" height="128" controls><source src="images/q2/nerf/a_sports_car/depth_ep_99.mp4></video>   | <video width="128" height="128" controls><source src="images/q2/nerf/a_sports_car/rgb_ep_99.mp4></video> |

Note that the sports car does not converge during training. This is to show that view-independent text embedding would try to optimize every view independently and not consider the viewing angle. 


## 2.4 Bonus: View Dependant text embedding


| Prompt | Result depth map | Result rendered RGB |
|---|:---:|:---:|
| `a standing corgi dog` | <video width="128" height="128" controls><source src="images/q2/nerf_viewdep/a_standing_corgi_dog/depth_ep_99.mp4></video> | <video width="128" height="128" controls><source src="images/q2/nerf_viewdep/a_standing_corgi_dog/rgb_ep_99.mp4></video> |
| `a sports car` | <video width="128" height="128" controls><source src="images/q2/nerf_viewdep/a_sports_car/depth_ep_99.mp4></video> | <video width="128" height="128" controls><source src="images/q2/nerf_viewdep/a_sports_car/rgb_ep_99.mp4></video> |

Using view dependent text embedding as given in the utils python script, we see that the standing corgi dog prompt actually performs worse on the view dependant text embedding, and the training fails to converge. However, when the view dependent text embedding is used on the sports car prompt, it performs better than in Q2.3 and converges to a mostly accurate sports car shape with one of the front pillars missing, but at least converges during training.

This shows that the primitive view dependance of the text embedding is not as robust, though it did help the sports car prompt converge. In the code, currently, the view dependance directions are only from "front", "side", and "back", while the paper also mentions "overhead" and "underneath" views. If the set of embeddings were expanded as per the DreamFusion paper, it may significantly improve the output render quality.