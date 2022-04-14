help:
	@echo "    help             Shows this help."
	@echo "    torchserve-archive   Archive scripted PyTorch model. Req: EXP, VERSION; opt XARGS.
	@echo "    torchserve-image     Build & push TorchServe image. Req: EXP.
	@echo "    torchserve-local     Start TorchServe image locally. Req: EXP, VERSION; opt RUN_XARGS.
	@echo "    torchserve-register  Register TorchServe model as experiment:version. Req: EXP, VERSION; opt XARGS.
	@echo "    torchserve-deploy    Deploy a registered TorchServe model. Req: EXP, VERSION, ENDPOINT, DEPLOYMENT.
	@echo "    torchserve-delete    Delete deployment under endpoint. Req: ENDPOINT, DEPLOYMENT.


# `deploy-base` extension <<<<<<<<
check-arg-VERSION: ARG=VERSION
check-arg-VERSION: check-arg

check-arg-DEPLOYMENT: ARG=DEPLOYMENT
check-arg-DEPLOYMENT: check-arg

torchserve-archive: check-arg-EXP check-arg-VERSION
	torch-model-archiver \
		--model-name $(EXP)-$(VERSION) --version $(VERSION) \
		--serialized-file ./models/$(EXP).pt \
		--handler $(CODE_PATH)/$(EXP)/deploy/handler.py \
		--export-path ./models \
		$(XARGS)

torchserve-image: check-arg-EXP
	docker build $(CODE_PATH)/$(EXP)/deploy \
		--tag $(ACR_NAME).azurecr.io/torchserve:8080
	az acr login -n $(ACR_NAME)
	docker push $(ACR_NAME).azurecr.io/torchserve:8080

torchserve-local: check-arg-EXP check-arg-VERSION
	docker run --rm -it -p 8080:8080 $(RUN_XARGS) \
		--mount type=bind,source="$(PWD)/models",target="/home/model-server/model-store/$(EXP)/$(VERSION)" \
		-e MODEL_BASE_PATH=/home/model-server/model-store \
		-e MODEL_NAME=$(EXP) \
		-e MODEL_VERSION=$(VERSION) \
		$(ACR_NAME).azurecr.io/torchserve:8080

torchserve-register: check-arg-EXP check-arg-VERSION
	az ml model create --name $(EXP) \
		--path ./models/$(EXP)-$(VERSION).mar \
		--version $(VERSION) \
		--resource-group $(RESOURCE_GROUP) --workspace-name $(WORKSPACE) \
		$(XARGS)

torchserve-deploy: check-arg-EXP check-arg-ENDPOINT check-arg-VERSION check-arg-DEPLOYMENT
	az ml online-deployment create \
		--name $(DEPLOYMENT) \
		--endpoint-name $(ENDPOINT) \
		--file $(CODE_PATH)/$(EXP)/deploy/torchserve-deployment.yaml \
		--resource-group $(RESOURCE_GROUP) --workspace $(WORKSPACE) \
		--set model=azureml:$(EXP):$(VERSION) \
			environment_variables.MODEL_NAME=$(EXP) \
			environment_variables.MODEL_VERSION=$(VERSION) \
			environment.name=torchserve-$(EXP) \
			environment.version=1 \
			environment.image=$(ACR_NAME).azurecr.io/torchserve:8080

torchserve-delete: check-arg-ENDPOINT check-arg-DEPLOYMENT
	az ml online-deployment delete \
	--endpoint-name $(ENDPOINT) --name $(DEPLOYMENT) \
	--resource-group $(RESOURCE_GROUP) --workspace $(WORKSPACE)
# >>>>>>>> `deploy-torchserve` extension