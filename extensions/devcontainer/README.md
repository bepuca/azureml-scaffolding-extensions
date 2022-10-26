# VSCode Dev Container

Provides a blueprint of a `devcontainer.json` you can use to leverage the [VSCode Dev Containers](https://code.visualstudio.com/docs/remote/containers).

That way, all the IDE functionalities VSCode provides for developing in Python use the exact same environment we use anywhere else and you do not need to define a specific environment for VSCode.

## Assumptions

You have read the documentation of the VSCode devcontainers and understand what extension you
need in VSCode and how to use it.

## Usage

1. Create a `.devcontainer` folder inside the experiment you intend to use this for.
2. Copy the `devcontainer.json` from this extension inside that folder.
3. Modify the file as instructed by the comments in it.
4. Open a VSCode project from inside the experiment (not the project root).
5. When VSCode prompts you, click "Reopen in container". If the project is git
tracked, VSCode will actually open the whole project. This is a bit hacky, but it is the only
way until [this issue](https://github.com/devcontainers/spec/issues/6) is resolved.