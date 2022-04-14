help:
	@echo "    endpoint-create   Create specified endpoint in Azure ML. Req: ENDPOINT.
	@echo "    endpoint-update   Update specified endpoint in Azure ML. Req: ENDPOINT."
	@echo "    endpoint-delete   Delete specified endpoint in Azure ML. Req: ENDPOINT."
	@echo "    endpoint-token    Get auth token for endpoint in Azure ML. Req: ENDPOINT."
	@echo "    endpoint-url      Get url of endpoint in Azure ML. Req: ENDPOINT."

# `deploy-base` extension <<<<<<<<
check-arg-ENDPOINT: ARG=ENDPOINT
check-arg-ENDPOINT: check-arg

endpoint-create: check-arg-ENDPOINT
	az ml online-endpoint create -f ./deploy/$(ENDPOINT).yaml \
		--resource-group $(RESOURCE_GROUP) --workspace-name $(WORKSPACE)

endpoint-update: check-arg-ENDPOINT
	az ml online-endpoint update -f ./deploy/$(ENDPOINT).yaml \
		--resource-group $(RESOURCE_GROUP) --workspace-name $(WORKSPACE)

endpoint-delete: check-arg-ENDPOINT
	az ml online-endpoint delete --name $(ENDPOINT) \
		--resource-group $(RESOURCE_GROUP) --workspace-name $(WORKSPACE)

endpoint-token: check-arg-ENDPOINT
	az ml online-endpoint get-credentials \
		--name $(ENDPOINT) \
		--workspace-name $(WORKSPACE) --resource-group $(RESOURCE_GROUP) \
		--query accessToken -o tsv

endpoint-url: check-arg-ENDPOINT
	az ml online-endpoint show \
		--name $(ENDPOINT) \
		--workspace-name $(WORKSPACE) --resource-group $(RESOURCE_GROUP) \
		--query scoring_uri -o tsv
# >>>>>>>> `deploy-base` extension