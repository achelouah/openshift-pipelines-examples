TASK_MANIFEST = task-params-array.yaml
TASK = $(shell grep "^  name:" $(TASK_MANIFEST) | awk '{print $$2}' )
PIPELINE_MANIFEST = pipeline-params-array.yaml
PIPELINE = $(shell grep "^  name:" $(PIPELINE_MANIFEST) | awk '{print $$2}' )

.PHONY: default task run task-delete taskrun-delete clean

default: pipeline-run

task:
	oc apply -f $(TASK_MANIFEST)

task-run: task
	tkn task start $(TASK) --use-param-defaults --showlog

task-delete:
	oc delete -f $(TASK_MANIFEST)

taskrun-delete:
	oc delete taskruns.tekton.dev --selector=tekton.dev/task=$(TASK)

pipeline: task
	oc apply -f $(PIPELINE_MANIFEST)

pipeline-run: pipeline
	tkn pipeline start $(PIPELINE) --use-param-defaults --showlog

pipeline-delete:
	oc delete -f $(PIPELINE_MANIFEST)

pipelinerun-delete:
	oc delete pipelineruns.tekton.dev --selector=tekton.dev/pipeline=$(PIPELINE)

clean-pipeline: pipelinerun-delete pipeline

clean-task: taskrun-delete task-delete

clean: clean-pipeline clean-task
