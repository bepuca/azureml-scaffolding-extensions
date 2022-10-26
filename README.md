# AzureML Scaffolding Extensions

This repository is meant to be a marketplace for extensions to the base
[AzureML Scaffolding](https://github.com/bepuca/azureml-scaffolding). These extensions aim to
provide plug and play solutions to solve scenarios that, while they are common in many projects,
they are not required for all of them. Our goal is to make the list of available extensions grow
over time and encourage to contribute to anyone that wishes to!

At the top level inside each extension, you will find a `README` explaining how to port it to
your project and how to use it.

- **deploy-base** - Manage an online endpoint in Azure Machine Learning ready to receive model
    deployments.
- **deploy-torchserve** - Add a TorchServe deployment to an existing online managed endpoint in
    Azure Machine Learning.
    - **Dependencies**: deploy-base
- **gpu-image** - A blueprint Dockerfile for building Docker images that need access to NVIDIA GPUs.