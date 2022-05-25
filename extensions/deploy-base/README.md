# Deploy Base

Manage an online endpoint in Azure Machine Learning ready to receive model deployments.

## Set-Up

The following set up instructions are only needed for the first endpoint you want to deploy. If you want to add more endpoints to your project, you just need to do steps 2 to 4 with a new YAML file.

1. Copy the `endpoints` folder in the root folder of your repository.
2. Change the `name` key in the `azure-ml-endpoint.yaml` to a meaningful name. It has to be unique
in the Azure region where your project lives.
3. Rename the `azure-ml-endpoint.yaml` to the exact same name you just defined. That is,
    `<name_key_in_yaml>.yaml`.
4. Change the `description` key to something meaningful and illustrative.
5. Copy the all the contents of the `Makefile` in this folder to the `Makefile` on your project.
6. Copy all files inside `docs/makefile` of this folder inside the `docs/makefile` of your project.
7. See the `README.md` inside the `endpoints` folder you copied on step 1 for the user guide of this
    extension.