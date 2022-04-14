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
3. Modify the `endpoint_name` key in the `torchserve-deployment.yaml` to point to the managed
    online endpoint you want to add this deployment to.
4. Go to the YAML file for the endpoint the deployment now points to. Add a the following key-value
    pair under the `traffic` key (with `<experiment_name> modifed to your experiment name):

    ```yaml
    torchserve-<experiment_name>: 0
    ```

    Also uncomment the key `traffic` if it is commented out.

5. Add `torch-model-archiver` to the `requirements.txt` at the root of your project. That is, to
    the one containing the formatting tools.
6. Update your formatting environment to have `torch-model-archiver` installed.
7. If `ACR_NAME` is not yet present in your `config.env`, run the following command in your shell
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
8. Copy what is inside of the `help` command of the `Makefile` in this folder inside the `help`
    command on your project `Makefile`.
9. Copy the all the contents except the `help` command of the `Makefile` in this folder to the
    `Makefile` on your project.
10. Copy the following in the Commands section under the Makefile section of your project `README.md`.

    ```Markdown
    - `torchserve-archive`

        Use torch model archiver to create a `.mar` file ready for deployment in TorchServe.
        Expects a `<experiment_name>-<version>.pt` file in `./models` folder. The saved model
        should be saved using torchscript so a definition file is not defined. It also expects
        a `handler.py` inside the `deploy` folder inside your experiment folder. Outputs a
        `<experiment_name>-<version>.mar` file in the `./models` folder.
        Requires the `EXP`, `VERSION` arguments. Accepts `XARGS` extra arguments.

        > Example: `make torchserve-archive EXPERIMENT=my_experiment VERSION=2`

    - `torchserve-image`

        Build the TorchServe docker image needed for deployment and uploads it to the Azure
        Container Registry associated with the Azure ML workspace.
        Requires the `EXP` argument. Accepts `XARGS` extra arguments.

        > Example: `make torchserve-image EXPERIMENT=my_experiment`

    - `torchserve-local`

        Start the TorchServe docker image locally. Useful to confirm everything is working.
        Requires the `EXP`, `VERSION` arguments. Accepts `RUN_XARGS` extra arguments.

        > Example: `make torchserve-image EXPERIMENT=my_experiment VERSION=3`

    - `torchserve-register`

        Register a `.mar` file in Azure ML as `MODEL=<experiment_name>` and `VERSION=<passed_version>`.
        Requires the `EXP`, `VERSION` arguments. Accepts `XARGS` extra arguments.

        > Example: `make torchserve-register EXPERIMENT=my_experiment VERSION=3`

    - `torchserve-deploy`

        Deploy a registered model in the specified model and version under the specifided endpoint
        with the deployment name specified.
        Requires the `EXP`, `VERSION`, `ENDPOINT`, `DEPLOYMENT` arguments.

        > Example: `make torchserve-deploy EXPERIMENT=my_experiment VERSION=3 ENDPOINT=my-endpoint DEPLOYMENT=my-experiment-3`

    - `torchserve-delete`

        Delete the specified deployment under the specified endpoint.
        Requires the `ENDPOINT`, `DEPLOYMENT` arguments.

        > Example: `make torchserve-delete ENDPOINT=my-endpoint DEPLOYMENT=my-experiment-3`




    ```


