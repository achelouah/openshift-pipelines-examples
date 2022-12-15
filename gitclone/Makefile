MANIFEST_PIPELINE = pipeline-clone-read.yaml
MANIFEST_PIPELINERUN = pipelinerun-clone-read.yaml
MANIFEST_TASK_SHOW_README = task-show-readme.yaml
PIPELINERUN_NAME = $(shell grep "^  name:" $(MANIFEST_PIPELINERUN) | awk '{print $$2}' )

.PHONY: get-project task-show-readme pipeline pipelinerun logs clean-pipelinerun clean-pipeline clean-task-show-readme clean

default: pipelinerun logs

get-project:
	@oc project

task-show-readme:
	oc apply -f $(MANIFEST_TASK_SHOW_README)

pipeline: task-show-readme
	oc apply -f $(MANIFEST_PIPELINE)

pipelinerun: pipeline
	oc apply -f $(MANIFEST_PIPELINERUN)

logs:
	@echo PIPELINERUN_NAME: $(PIPELINERUN_NAME)
	tkn pr logs $(PIPELINERUN_NAME) -f

clean-pipelinerun:
	oc delete -f $(MANIFEST_PIPELINERUN)

clean-pipeline: clean-pipelinerun
	oc delete -f $(MANIFEST_PIPELINE)

clean-task-show-readme:
	oc delete -f $(MANIFEST_TASK_SHOW_README)

clean: clean-pipelinerun clean-pipeline clean-task-show-readme 
