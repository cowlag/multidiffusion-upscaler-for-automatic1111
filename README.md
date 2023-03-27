# Large Image Generation via Tiled Diffusion & VAE

English｜[中文(待更新，欢迎帮助翻译)](README_CN.md)

The extension enables **large image drawing with limited VRAM** via the following techniques:

1. Two SOTA diffuion tiling algorithms: [Mixture of Diffusers](https://github.com/albarji/mixture-of-diffusers) and [MultiDiffusion](https://multidiffusion.github.io)
2. My original Tiled VAE algorithm.

## Features

****

### 🔥 Tiled VAE

- **It saves your VRAM at nearly no cost.**
- You may not need --lowvram or --medvram anymore.
- Take highres.fix as an example, if you can only do 1.5x upscale previously, you may do 2.0x upscale with it now. 
  - Normally you can use default settings without changing.
  - But if you see CUDA out of memory error, just lower down the two tile sizes.
- Screenshot：![TiledVAE](https://github.com/pkuliyi2015/multidiffusion-img-demo/blob/master/tiled_vae.png?raw=true)

****

### 🆕 Regional Prompt Control

Draw large images by fusing multiple regions together.

NOTE: we recommend you use custom regions to fill the whole canvas.

#### Example 1: draw multiple characters at a high resolution

- Params:

  - Ckpt: Anything V4.5,  1920 * 1280 (no highres), method=Mixture of Diffusers

  - Main prompt = masterpiece, best quality, highres, extremely clear 8k wallpaper, white room, sunlight

  - Negative prompt = ng_deepnegative_v1_75t EasyNegative

  - **The tile size parameters become useless; just ignore them.**

- Regions:
  - Region 1: Prompt = sofa, Type = Background
  - Region 2: Prompt = 1girl, gray skirt, (white sweater), (slim) waist, medium breast, long hair, black hair, looking at viewer, sitting on sofa, Type = Foreground, Feather = 0.2
  - Region 3: Prompt = 1girl, red silky dress, (black hair), (slim) waist, large breast, short hair, laughing, looking at viewer, sitting on sofa, Type = Foreground, Feather = 0.2

- Region Layout:![MultiCharacterRegions](https://github.com/pkuliyi2015/multidiffusion-img-demo/blob/master/multicharacter.png?raw=true)

- Result (2 out of 4)![MultiCharacter](https://github.com/pkuliyi2015/multidiffusion-img-demo/blob/master/multicharacter.jpeg?raw=true)

  ![MultiCharacter](https://github.com/pkuliyi2015/multidiffusion-img-demo/blob/master/multicharacter2.jpeg?raw=true)

#### Example 2: draw a full-body character

- Usually it is difficult to draw a full-body character at a high resolution (e.g., it may concatenates two bodies). 
- With this extension, **put your character in your background**, it becomes much easier.
- Params:
  - Ckpt: Anything V4.5, width = 1280, height = 1600 (no highres), method=MultiDiffusion
  - Main prompt: masterpiece, best quality, highres, extremely clear 8k wallpaper, beach, sea, forest
  - Neg prompt:  ng_deepnegative_v1_75t EasyNegative
- Regions:
  - Region 1 Prompt = 1girl, black bikini, (white hair), (slim) waist, giant breast, long hair, Type = Foreground, Feather: 0.2
  - Region 2 Prompt = (empty), Type: Background
- Region Layout: ![FullBodyRegions](https://github.com/pkuliyi2015/multidiffusion-img-demo/blob/master/fullbody_regions.png?raw=true)
- Result: 32s, 4729 MB on NVIDIA V100. I was lucky to get this at once without cherry picks.![FullBody](https://github.com/pkuliyi2015/multidiffusion-img-demo/blob/master/fullbody.jpeg?raw=true)
- Also works well for 2.5D characters. For exampe, the 1024*1620 image generation
- Great thanks to all settings from @辰熙. Click here for more her artworks: https://space.bilibili.com/179819685
- Cherry-picked from 20 generations.![FullBody2](https://github.com/pkuliyi2015/multidiffusion-img-demo/blob/master/fullbody2.jpeg?raw=true)

****
### Img2img upscale
- Leverage Tiled Diffusion to upscale & redraw large images

#### Example: 1024 * 800 -> 4096 * 3200 image, with default params

- Params:
  - denoise=0.4, steps=20, Sampler=Euler a, Upscaler=RealESRGAN++, Negative Prompts=EasyNegative,
  - Ckpt: Gf-style2 (4GB version), CFG Scale = 14, Clip Skip = 2
  - method = MultiDiffusion, tile batch size = 8, tile size height = 96, tile size width = 96, overlap = 32
  - Prompt = masterpiece, best quality, highres, extremely detailed 8k wallpaper, very clear, Neg prompt = EasyNegative.

- Before upscaling:![lowres](https://github.com/pkuliyi2015/multidiffusion-img-demo/blob/master/lowres.jpg?raw=true)
- After 4x upscale, No cherry-picking. 1min12s on NVIDIA Testla V100. (If 2x, it completes in 10s)![highres](https://github.com/pkuliyi2015/multidiffusion-img-demo/blob/master/highres.jpeg?raw=true)

****

### Ultra-Large image generation

- Please use simple positive prompts at the top of the page, as they will be applied to each tile.
- If you want to add objects to a specific position, use **regional prompt control** and enable **draw full canvas background** 

#### Example 1:  masterpiece, best quality, highres, city skyline, night.

- ![panorama](https://github.com/pkuliyi2015/multidiffusion-img-demo/blob/master/city_panorama.jpeg?raw=true)

#### Example 2: cooperate with ControlNet to convert ancient wide paintings

- 22020 x 1080 ultra-wide image conversion 
  - Masterpiece, best quality, highres, ultra-detailed 8k unity wallpaper, bird's-eye view, trees, ancient architectures, stones, farms, crowd, pedestrians
  - Before: [click for the raw image](https://github.com/pkuliyi2015/multidiffusion-img-demo/blob/master/ancient_city_origin.jpeg)
  - ![ancient city origin](https://github.com/pkuliyi2015/multidiffusion-img-demo/blob/master/ancient_city_origin_compressed.jpeg?raw=true)
  - After: [click for the raw image](https://github.com/pkuliyi2015/multidiffusion-img-demo/blob/master/ancient_city.jpeg)
  - ![ancient city origin](https://github.com/pkuliyi2015/multidiffusion-img-demo/blob/master/ancient_city_compressed.jpeg?raw=true)

#### Example 3: 2560 * 1280 large image drawing

- ControlNet canny edge![Your Name](https://github.com/pkuliyi2015/multidiffusion-img-demo/blob/master/yourname_canny.jpeg?raw=true)![yourname](https://github.com/pkuliyi2015/multidiffusion-img-demo/blob/master/yourname.jpeg?raw=true)

****

## Installation

#### Method 1: Official Market

- Open Automatic1111 WebUI -> Click Tab "Extensions" -> Click Tab "Available" -> Find "[MultiDiffusion with Tiled VAE]" -> Click "Install"

#### Method 2: URL Install

- Open Automatic1111 WebUI -> Click Tab "Extensions" -> Click Tab "Install from URL" -> type in https://github.com/pkuliyi2015/multidiffusion-upscaler-for-automatic1111.git -> Click "Install" 
- ![installation](https://github.com/pkuliyi2015/multidiffusion-img-demo/blob/master/installation.png?raw=true)

****

## Usage

### Tiled VAE

- The script will recommend settings for you when first use.
- So normally, you don't need to change the default params.![TiledVAE](https://github.com/pkuliyi2015/multidiffusion-img-demo/blob/master/tiled_vae.png?raw=true)
- You only need to change params in following cases
  1. When you see CUDA out of memory error before generation, or after generation, please low down the tile size.
  2. If you use too small tile size and the picture becomes gray and unclear, please enable Encoder Color Fix.

****

### Tiled Diffusion

- Main Part / Image tiling options

  The following part controls the tiling of the image: ![Tab](https://github.com/pkuliyi2015/multidiffusion-img-demo/blob/master/Tab.png?raw=true)

  Here is an illustration:

  ![Tab](https://github.com/pkuliyi2015/multidiffusion-img-demo/blob/master/tiles_explain.png?raw=true)

- From the illustration, you can see how is an image splitted into tiles. 
  - In each step, each tile in the latent space will be send to Stable Diffusion UNet.
  - The tiles are splited and fused over and over again, util all steps completed.
- What is a good tile size?
  - Larger tile size will increase the speed because it produces less tiles.
  - However, the optimal size depends on your checkpoint. The basic SD1.4 is only good at drawing 512 * 512 images (SD2.1 will be 768 * 768). And most checkpoints cannot generate good pictures larger than 1280 * 1280. So in latent space let's divide this by 8, and you will get 64 - 160.
  - Hence, you should pick a value between 64 - 160.
  - **Personally I recommend 96 or 128 for fast speed.**
- What is a good overlap?
  - The overlap reduces seams in fusion. Obviously, larger overlap means less seams, but will **significantly reduce the speed** as it brings much more tiles to redraw.
  - Comparing to MultiDiffusion, Mixture of Diffusers requires less overlap because it use Gaussian smoothing (and therefore can be faster).
  - **Personally I recommend 32 or 48 for MultiDiffusion, 16 or 32 for Mixture of Diffusers**
- **Upscaler** will appear in i2i. You can select one to upscale your image in advance.

****

### Region Prompt Control

- Normally, all tiles share the same main prompt.
  - So you can't draw meaningful objects with main prompt, it will draw your object everywhere and ruin your image.
- To handle this, we provide the powerful region prompt control tool.
- ![Tab](https://github.com/pkuliyi2015/multidiffusion-img-demo/blob/master/region_prompt_control.png?raw=true)
1. First, enable the region prompt control.
    - **NOTE: When you enable the control, the default tiling behavior will be disabled.**
    - If your custom regions can't fill the whole canvas, it will produce brown color (MultiDiffusion) or noises (Mixture of Diffusers) in those uncovered areas.
    - We recommend you use your own regions to fill the canvas, as it can be much faster when generation.
    - If you are lazy to draw, you can also enable the **Draw full canvas background.** However, this will be much slower in generation.
2. Upload an image or click the button to create an empty image as a reference.
3. Click the enable in Region 1, you will see a red rectangle appears in the image.
    - **Click and drag** the region with your mouse to move and resize them.
4. Selecte region type. If you want to draw object, select Foreground. Otherwise select Background.
    - **Feather** will appear if you select foreground.
    - The larger value will give you more smooth edges.
5. Type in positive prompt and negative prompt.
    -  **Note: your prompt will be appended to the prompt at the top of the page.**
    - You can leverage this to save your words, write common things like "masterpiece, best quality, highres..." at the top of the page
    - **You can also use Textual Inversion and LoRA in the prompt**

****

### Special tips for Upscaling
- **Recommend Parameters for Efficient Upscaling.**
  - Sampler = Euler a, steps = 20, denoise = 0.35, method = Mixture of Diffusers, Latent tile height & width = 128, overlap = 16, tile batch size = 8 (reduce tile batch size if see CUDA out of memory).
- We are compatible with masked inpainting
  - If you want to keep some parts, or the upscaler gives you weird results, just mask these areas.
- **The checkpoint is crucial.**. 
  - MultiDiffusion works very similar to highres.fix, so it highly relies on your checkpoint.
  - A checkpoint that good at drawing details (e.g., trained on high resolution images) can add amazing details to your image.
  - A **full checkpoint** instead of a pruned one yields much finer results.
- **Don't include any concrete objects in your main prompts.**  Otherwise the results get ruined.
  - Just use something like "highres, masterpiece, best quality, ultra-detailed 8k wallpaper, extremely clear".
  - And use regional prompt control for concrete objects if you like.
- You don't need too large tile size, large overlap and many denoising steps, **or it can be very slow**.
- **CFG scale can significantly affect the details.**
  - A large CFG scale (e.g., 14) gives you much more details.
- You can control how much you want to change the original image with **denoising strength from 0.1 - 0.6**.
- If your results are still not as satisfying as mine, [see our discussions here.](https://github.com/pkuliyi2015/multidiffusion-upscaler-for-automatic1111/issues/3)

****

## Technical Part

For those who want to know how this works.

### Tiled VAE

The core technique is to estimate GroupNorm params for seamless generation.

1. The image is split into tiles, which are then padded with 11/32 pixels' in decoder/encoder.
2. When Fast Mode is disabled:
   1. The original VAE forward is decomposed into a task queue and a task worker, which start to process each tile.
   2. When GroupNorm is needed, it suspends, stores current GroupNorm mean and var, send everything to RAM, and turns to the next tile.
   3. After all GroupNorm mean and var parameters are summarized, it applies group norm to tiles and continues. 
   4. A zigzag execution order is used to reduce unnecessary data transfer.
3. When Fast Mode is enabled:
   1. The original input is downsampled and passed to a separate task queue.
   2. Its group norm parameters are recorded and used by all tiles' task queues.
   3. Each tile is separately processed without any RAM-VRAM data transfer.
4. After all tiles are processed, tiles are written to a result buffer and returned.

Encoder color fix = only estimate GroupNorm before downsampling, i.e., run in a semi-fast mode.

****

### Tiled Diffusion

1. The latent image is split into tiles.
2. In MultiDiffusion:
   1. The UNet predict the noise of each tile.
   2. The tiles are denoised by the original sampler for one time step.
   3. The tiles are added together but divided by how many times each pixel is added.
3. In Mixture of Diffusers:
   1. The UNet predict the noise of each tile
   2. All noise are fused together with a gaussian weight mask.
   3. The denoiser denoise the whole image by one step using fused noises.
4. Repeat 2-3 until all timesteps are completed.

### Advantages

- Draw super large resolution (2k~8k) image in limited VRAM
- Seamless output without any post-processing

### Drawbacks

- It will be slower than normal generation.
- The gradient calculation is not compatible with this hack. It will break any backward() or torch.autograd.grad()

****

## Current Progress

- Chinese README
- Saving region info into image & read back is in progress.
- Tiled noise inverse for better upscaling

****

## License

These scripts are licensed under the MIT License. If you like the project, please give me a star!

Thank you!