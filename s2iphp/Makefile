MANIFEST_SECRET_WORKSPACE_REGISTRY = secret-workspace-registry.yaml
MANIFEST_TASK_DIPLAY_IMAGE_DIGEST = task-display-image-digest.yaml
MANIFEST_PIPELINE = pipeline-s2i-php.yaml
MANIFEST_PIPELINERUN = pipelinerun-s2i-php.yaml
PIPELINE_NAME = $(shell grep "^  name:" $(MANIFEST_PIPELINE) | awk '{print $$2}' )
PIPELINERUN_NAME = $(shell grep "^  name:" $(MANIFEST_PIPELINERUN) | awk '{print $$2}' )

.PHONY: get-project pipeline pipelinerun logs clean-pipelinerun clean-pipeline clean

defaut: pipelinerun

get-project:
	@oc project

secret:
	oc apply -f $(MANIFEST_SECRET_WORKSPACE_REGISTRY)

task:
	oc apply -f $(MANIFEST_TASK_DIPLAY_IMAGE_DIGEST)

pipeline: secret task
	oc apply -f $(MANIFEST_PIPELINE)

pipelinerun: pipeline
	@#oc apply -f $(MANIFEST_PIPELINERUN)
	tkn pipeline start $(PIPELINE_NAME) \
		-p IMAGE=dune.registry.arrakis.kc:8443/tekton/scaling-version:1.0 \
		-p repo-url=https://github.com/achelouah/scaling-version-priv.git \
		-p repo-revision=main \
		-w name=shared-workspace,volumeClaimTemplateFile=workspace-template.yaml \
		-w name=registry-credential,secret=secret-workspace-registry \
		--use-param-defaults \
		--showlog

logs:
	@echo PIPELINERUN_NAME: $(PIPELINERUN_NAME)
	tkn pr logs $(PIPELINERUN_NAME) -f

clean-pipelinerun:
	@#oc delete -f $(MANIFEST_PIPELINERUN)
	oc delete pipelinerun --selector tekton.dev/pipeline=$(PIPELINE_NAME)

clean-pipeline: clean-pipelinerun
	oc delete -f $(MANIFEST_PIPELINE)

clean-task:
	oc delete -f $(MANIFEST_TASK_DIPLAY_IMAGE_DIGEST)

clean-secret:
	oc delete -f $(MANIFEST_SECRET_WORKSPACE_REGISTRY)

clean: clean-pipelinerun clean-pipeline clean-secret
