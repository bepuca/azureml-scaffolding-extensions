# TorchServe Deployment

Add a TorchServe deployment to an existing online managed endpoint in Azure Machine Learning.

**Dependencies**: deploy-base

## Assumption

This extension works under the assumption that there will be at maximum one deployment per
experiment live at any given time.

That is, if you wanted to have two or more trained models from the same experiment simultaneously,
this would not work and you would need to do extra work.

## Set-up

If it is the first torchserve deployment you add to your project, follow all steps. If not, you only
need to follow steps 1 to 4. After the set-up outlined here, refer to the `README.md` inside
`deploy` for instructions on how tu use the extension.

1. Copy the `deploy` folder inside the experiment folder that creates the model you want to deploy.
2. Modify the `name` key in the `torchserve-deployment.yaml` to the name you want to give this
    deployment. A suggestion: `torchserve-<experiment_name>`.
3. Modify the keys `instance_count` and `instance_type` to reflect the number and type of machines
    you want to use for the deployment.
4. Modify the `endpoint_name` key in the `torchserve-deployment.yaml` to point to the managed
    online endpoint you want to add this deployment to. The endpoint should be created already.
5. Go to the YAML file for the endpoint the deployment now points to. Add a the following key-value
    pair under the `traffic` key (with `<experiment_name> modifed to your experiment name):

    ```yaml
    torchserve-<experiment_name>: 0
    ```

    Also uncomment the key `traffic` if it is commented out.

6. Add `torch-model-archiver` to the `requirements.txt` at the root of your project. That is, to
    the one containing the formatting tools.
7. Update your formatting environment to have `torch-model-archiver` installed.
8. If `ACR_NAME` is not yet present in your `config.env`, run the following command in your shell
    to find the Azure Container Registry associated with your Azure ML workspace.
    Substitute the workspace and resource group for the names of your project:

    ```bash
    az ml workspace show --query container_registry -o tsv \
		--name <your_workspace> --resource-group <your_resource_group> \
		| grep -o '[^/]*$'
    ```

    Then add the result to your `config.env` like so:

    ```.env
    # Azure Container Registry associated with the ML workspace
    ACR_NAME=<name_you_just_found>
    ```
9. Copy what all files in `docs/makefile` inside this folder to the `docs/makefile` of your
    project.
10. Copy the all the contents of the `Makefile` in this folder at the end of the `Makefile` in your
    project.
