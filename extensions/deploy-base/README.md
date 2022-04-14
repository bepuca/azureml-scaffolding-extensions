# Deploy Base

Manage an online endpoint in Azure Machine Learning ready to receive model deployments

## Set-Up

The following set up instructions are only needed for the first endpoint you want to deploy. If you want to add more endpoints to your project, you just need to do steps 2 to 4 with a new YAML file.

1. Copy the `deploy` folder in the root folder of your repository.
2. Change the `name` key in the `azure-ml-endpoint.yaml` to a meaningful name. It has to be unique
in the Azure region where your project lives.
3. Rename the `azure-ml-endpoint.yaml` to the exact same name you just defined. That is,
    `<name_key_in_yaml>.yaml`.
4. Change the `description` key to something meaningful and illustrative.
5. Copy what is inside of the `help` command of the `Makefile` in this folder inside the `help`
    command on your project `Makefile`.
6. Copy the all the contents except the `help` command of the `Makefile` in this folder to the
    `Makefile` on your project.
7. Copy the following in the Commands section under the Makefile section of your project `README.md`.

    ```Markdown
    - `endpoint-create`

        Create a new managed online endpoint in your Azure Machine Learning workspace using the specifications in `./deploy/<ENDPOINT>.yaml`. Requires the `ENDPOINT` argument.

        > Example: `make create-endpoint ENDPOINT=my-super-endpoint`

    - `endpoint-update`

        Update the managed online endpoint in your Azure Machine Learning workspace created with the file
        `./deploy/<ENDPOINT>.yaml` to ensure it matches. Requires the `ENDPOINT` argument.

        > Example: `make update-endpoint ENDPOINT=my-super-endpoint`

    - `endpoint-delete`

        Delete the managed online endpoint in your Azure Machine Learning workspace named `ENDPOINT`. Requires the `ENDPOINT` argument.

        > Example: `make delete-endpoint ENDPOINT=my-super-endpoint`

    - `endpoint-url`

        Obtain the URL where the endpoint can be called. Note that the returned URL ends with
        `/score`. This is the standard when not using custom containers. We favor custom containers
        so you may need to change this last bit to whatever your container needs.
        Requires the `ENDPOINT` argument.

        > Example: `make endpoint-url ENDPOINT=my-super-endpoint`

    - `endpoint-token`

        Obtain the authorization token for the specified managed endpoint. To be able to call this
        endpoint, the token needs to be passed in a header `Authorization: Bearer <token>`.
        Requires the `ENDPOINT` argument.

        > Example: `make endpoint-token ENDPOINT=my-super-endpoint`
    ```
8. See the `README.md` inside the `deploy` folder you copied on step 1 for the user guide of this
    extension.