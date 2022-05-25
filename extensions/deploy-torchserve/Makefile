help:
	@echo ""
	@if [ -z "$(cmd)" ]; then { \
		echo "For a more detailed help of a command, run 'make help cmd=<cmd>'.\n"; \
		echo "Commands:"; \
		for file in `ls ./docs/makefile`; do cat ./docs/makefile/$$file | head -2 | tail -1; done; \
	} else { \
		cat ./docs/makefile/$(cmd); \
	} fi
	@echo "";

# `deploy-torchserve` extension <<<<<<<<
check-arg-deployment: arg=deployment
check-arg-deployment: check-arg

torchserve-archive: check-arg-exp check-arg-version
	torch-model-archiver \
		--model-name $(exp)-$(version) --version $(version) \
		--serialized-file ./models/$(exp).pt \
		--handler $(CODE_PATH)/$(exp)/deploy/handler.py \
		--export-path ./models \
		$(xargs)

torchserve-image: check-arg-exp
	# Build TorchServe docker image
	docker build --tag $(ACR_NAME).azurecr.io/torchserve-$(exp):8080 $(build-xargs) \
		$(CODE_PATH)/$(exp)/deploy

	# Login to Azure Container Registry to be able to push
	az acr login -n $(ACR_NAME)

	# Push image to the ACR of the Azure ML workspace
	docker push $(ACR_NAME).azurecr.io/torchserve:8080

torchserve-local: port=8080
torchserve-local: check-arg-exp check-arg-version
	docker run --rm -it -p 8080:$(port) $(run_xargs) \
		--mount type=bind,source="$(PWD)/models",target="/home/model-server/model-store/$(exp)/$(version)" \
		-e MODEL_BASE_PATH=/home/model-server/model-store \
		-e MODEL_NAME=$(exp) \
		-e MODEL_version=$(version) \
		$(ACR_NAME).azurecr.io/torchserve-$(exp):8080


torchserve-deploy: check-arg-exp check-arg-endpoint check-arg-version check-arg-deployment
	az ml online-deployment create \
		--name $(deployment) \
		--endpoint-name $(endpoint) \
		--file $(CODE_PATH)/$(exp)/deploy/torchserve-deployment.yaml \
		--resource-group $(RESOURCE_GROUP) --workspace $(WORKSPACE) \
		--set model=azureml:$(exp):$(version) \
			environment_variables.MODEL_NAME=$(exp) \
			environment_variables.MODEL_VERSION=$(version) \
			environment.name=torchserve-$(exp) \
			environment.version=1 \
			environment.image=$(ACR_NAME).azurecr.io/torchserve-$(exp):8080

torchserve-delete: check-arg-endpoint check-arg-deployment
	az ml online-deployment delete \
	--endpoint-name $(endpoint) --name $(deployment) \
	--resource-group $(RESOURCE_GROUP) --workspace $(WORKSPACE)
# >>>>>>>> `deploy-torchserve` extension