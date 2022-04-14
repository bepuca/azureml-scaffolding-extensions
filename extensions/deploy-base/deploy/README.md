# Deploy on Azure ML Online Endpoint

A very convenient way of deploying a machine learning model is through a managed online endpoint in Azure Machine Learning. The main idea is a REST endpoint where the underlying infrastructure is managed for you.

Each YAML file in this folder will map to one endpoint. You may need only one endpoint for your project or multiple endpoints for multiple tasks.

The endpoint alone is not sufficient. We will need to assign deployments to these endpoints. Deployments will be defined inside each experiment, as different libraries deploy differently. We favor deployments
through custom containers to allow more clarity on what is actually deployed.

For more information and context:
- [Deploy a machine learning model by using and online endpoint](https://docs.microsoft.com/en-us/azure/machine-learning/how-to-deploy-managed-online-endpoints)
- [Deploy a custom container with an online endpoint](https://docs.microsoft.com/en-us/azure/machine-learning/how-to-deploy-custom-container)
- [Safe rollout for online endpoints](https://docs.microsoft.com/en-us/azure/machine-learning/how-to-safely-rollout-managed-endpoints)
- [CLI online endpoint YAML schema](https://docs.microsoft.com/en-us/azure/machine-learning/reference-yaml-endpoint-online)

## User Guide

1. Before being able to make any deployment, we need to create the endpoint in Azure ML. To do so,
    we can run:

    ```make endpoint-create ENDPOINT=<your_endpoint_name>```

    Note that for this to succeed, you need to make sure your YAML file has the `name` key specified
    (and the `description` too for sanity). The name of the YAML file should be `<endpoint_name>.YAML`. Additionally, the key `traffic` should be commented out when it is empty (which is how it is
    the first time we create and endpoint).

2. An endpoint can have one or multiple deployments. For adding a deployment, refer to the
    instructions of a specific deployment on one of your models. The Azure MLOps Scaffolding extensions
    repository offers some examples (e.g. deploy-torchserve). Once a deployment is added, it has
    no traffic to it. To add traffic we need to uncomment the `traffic` key in the endpoint YAML,
    and add a `<deployment_name>: 100` key-value pair inside the `traffic`. This directs all
    traffic of the endpoint (100%) to that deployment. If we already had one or more deployments
    and we want to do a safe rollout to a subset of users, we can set the keys to whatever
    percentage we want. The only constraint is they all add to 100.

3. Once the YAML is modified, we need to make the updates live in the endpoint. To do so, run:

    ```make endpoint-update ENDPOINT=<your_endpoint_name>```

    After that, all changes are live. We recommend then commiting the YAML so that it reflects the
    current state of things.

4. To make calls to the endpoint, we will need the URL and the authorization token.
    - For the URL, run `make endpoint-url ENDPOINT=<your_endpoint_name>`. Note that the URL ends
        in `/score`. If using custom containers (which is recommended), you should strip that and
        add whatever your custom container adds.
    - For the token, run `make endpoint-token ENDPOINT=<your_endpoint_name>`.
    - You can call the endpoint with something like this:

        ```curl POST <endoint_url> --header "Authorization: Bearer <endpoint_token>"```

    Note the command is missing the payload, which could be a JSON or a file or something else.
    This is for illustrative purposes on how to call and get authorized. Feel free to use the tool
    that best works for you and your use case.

5. If you want to delete the endpoint at some point. You can run the following command and the
    endpoint and all related deployments will be deleted:

    ```make endpoint-delete ENDPOINT=<your_endpoint_name>```

