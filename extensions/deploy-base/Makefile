# `deploy-base` extension <<<<<<<<
check-arg-endpoint: arg=endpoint
check-arg-endpoint: check-arg

check-arg-version: arg=version
check-arg-version: check-arg

check-arg-filetype: arg=filetype
check-arg-filetype: check-arg

endpoint-create: check-arg-endpoint
	az ml online-endpoint create -f ./endpoints/$(endpoint).yaml \
		--resource-group $(RESOURCE_GROUP) --workspace-name $(WORKSPACE)

endpoint-update: check-arg-endpoint
	az ml online-endpoint update -f ./endpoints/$(endpoint).yaml \
		--resource-group $(RESOURCE_GROUP) --workspace-name $(WORKSPACE)

endpoint-delete: check-arg-endpoint
	az ml online-endpoint delete --name $(endpoint) \
		--resource-group $(RESOURCE_GROUP) --workspace-name $(WORKSPACE)

endpoint-token: check-arg-endpoint
	az ml online-endpoint get-credentials \
		--name $(endpoint) \
		--workspace-name $(WORKSPACE) --resource-group $(RESOURCE_GROUP) \
		--query accessToken -o tsv

endpoint-url: check-arg-endpoint
	az ml online-endpoint show \
		--name $(endpoint) \
		--workspace-name $(WORKSPACE) --resource-group $(RESOURCE_GROUP) \
		--query scoring_uri -o tsv

register-model: check-arg-exp check-arg-version check-arg-filetype
	az ml model create --name $(exp) \
		--path ./models/$(exp)-$(version).$(filetype) \
		--version $(version) \
		--resource-group $(RESOURCE_GROUP) --workspace-name $(WORKSPACE) \
		$(xargs)
# >>>>>>>> `deploy-base` extension