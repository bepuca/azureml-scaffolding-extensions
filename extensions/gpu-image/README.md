# GPU Docker image

Blueprint Dockerfile for images that need acces to NVIDIA GPUs. It makes sure CUDA drivers are installed.

You might have to change the version of the base CUDA image depending on the drivers installed in the machine that you intend to run it.

*Note: This Dockerfile is based on the one used by [Accelerate](https://github.com/huggingface/accelerate) developers.*