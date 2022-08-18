# Deploy TorchServe on Azure ML Online Endpoint

Once we have a trained PyTorch model and an online endpoint running, we are ready to deploy
the model there and start getting predictions.

It is important to note that by design, only one deployment per experiment is allowed. The reason
for this is that deployment code in git should match what is deployed. Otherwise things can
get quite confusing. Of course, commits could be tagged to obtain the same results, but I believe
it is less transparent.

## User Guide

1. The first thing we need is the trained PyTorch model. In order to avoid the need of a python
    definition file for the model, it needs to be saved using torchscript. You can do so, using
    the following python code (note the file must be named `<experiment_name>.pt`):

    ```python
    import torch

    model = ...  # This has to be a trained nn.Module for this to work.
    # Ensure model is saved on eval mode
    model.eval()
    # Compile the model to TorchScript
    scripted_model = torch.jit.script(model)
    # Save the scripted model with a ".pt" extension
    scripted_model.save("<experiment_name>.pt")
    ```

2. Once we have a model `.pt` artifact, we need to put it inside the `./models` folder in the
    project. For instance, if you trained a model on Azure ML and saved the model to outputs, you
    need to go inside the experiments outputs in the Azure ML studio, download it and move it.
    The model will only exist in the developer machine that puts it in the `models` folder, since
    everything inside that folder is ignored.

3. The way Azure ML deployments work, we need to have a built image in the Azure ML container
    registry to be able to deploy in a custom container. For deploying using TorchServe, we are
    going to use the `pytorch/torchserve` base image and a `CMD` on top that lets us specify the
    model and where it is located when the image is used by Azure ML managed endpoint.
    To build that image and push it to the Azure container registry, run:

    ```make torchserve-build exp=<experiment_name>```

    A few things to note:
    - The default Dockerfile uses the cpu version of torchserve image. A GPU version is also
        available if you need to. In that case, you need to make sure the `instance_type`
        referenced in the `torchserve-deployment.yaml` does have a GPU.
    - The reason there is one image (and Dockerfile) per experiment is that TorchServe allows
        more configuration (like extra requirements) that would be model specific. That would
        require to modify the Dockerfile. For details on TorchServe, visit
        https://pytorch.org/serve/index.html. Note that the TorchServe base images have the pytorch
        suite installed (pytorch, torchvision, etc).

4. For TorchServe to be able to run predictions, we need to define a handler. A handler is simply
    a python file that has the necessary code to run predictions. TorchServe offers some
    out-of-the-box handlers that you can use or extend to match your use case. You must define
    the `handler.py` to fit your use case. For details, visit
    https://github.com/pytorch/serve/blob/master/model-archiver/README.md#handler

5. Once we have the `handler.py` is filled in and the model `.pt` artifact in the `models` folder,
    TorchServe requires we "archive" the model and the handler together in a single artifact with
    extension `.mar`. We do that by running the following command:

    ```make torchserve-archive exp=<experiment_name> version=<version>```

6. Before passing things to Azure ML, it is a good idea to spin the TorchServe server locally and
    test that the model and the handler interact nicely and we get predictions as expected. To do
    so, first we spin the server with the command:

    ```make torchserve-local exp=<experiment_name>```

    Then, on another terminal, we could call it using the next command (assuming it is a vision
    model):

    ```curl -X POST http://localhost:8080/predictions/model -T /path/to/image```

    There is a high chance there is some error in the hanlder on your first try. In the terminal
    where TorchServe is running you will see the logs. Then, you should follow an iterative process
    of steps 4,5 and 6. That is, modify the handler, archive it with the model and run this step
    again. When you are able to get the prediction you would expect, move to the following step.

7. Now that we know our model-handler bundle works as expected, it is time to register the
    `.mar` artifact as a model in Azure ML. We need to do this because otherwise Azure ML has
    nowhere to look for the model that TorchServe requires. To do so, run the following command:

    ```make register-model exp=<experiment_name> version=<version> filetype=mar```

    The model will be registered under the name `<experiment_name>` and version `<version>`.

8. At this point we are ready to create our deployment. First make sure that the specs
    `instance_count` and `instance_type` in `torchserve-deployment.yaml` match your expectations.
    After that, run the following command. It will add the deployment to the specified endpoint.

    ```make torchserve-deploy exp=<experiment_name> version=<version> endpoint=<endpoint-name> deployment=<deployment-name>```

9. After the previous command succeeds (which may take a while because machines have to be
    provisioned) the deployment is created but no traffic is routed to it. To allocate traffic,
    follow the instructions in steps 2 & 3 in the `README.md` inside the `endpoints` folder.

10. To make calls to the new deployment (through the endpoint), follow step 4 in the `README.md`
    inside the `endpoints` folder. For TorchServe, the URL has to end in `/predictions/model`. If it
    is not the case, change everything after the `/` to `/predictions/model` in the URL.

11. Finally, if you want to delete a deployment, you will need to set the traffic to 0 and then
    run the following command:

    ```make torchserve-delete endpoint=<endpoint-name> deployment=<deployment-name>```
