TASK = helloworld-onerror
TASK_MANIFEST = task-helloworld-onerror.yaml

.PHONY: task run task-delete taskrun-delete clean

task:
	oc apply -f $(TASK_MANIFEST)

run: task
	tkn task start $(TASK) --showlog

task-delete:
	oc delete -f $(TASK_MANIFEST)

taskrun-delete:
	oc delete taskruns.tekton.dev --selector=tekton.dev/task=$(TASK)

clean: taskrun-delete task-delete
