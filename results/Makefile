TASK_MANIFEST = task-helloworld-results.yaml
TASK_NAME = $(shell grep "^  name:" $(TASK_MANIFEST) | awk '{print $$2}' )

.PHONY: task taskrun clean-taskrun clean-task

default: taskrun

task:
	@oc apply -f $(TASK_MANIFEST)

taskrun: task
	@tkn task start $(TASK_NAME) --use-param-defaults --showlog

clean-task:
	@oc delete -f $(TASK_MANIFEST)

clean-taskrun:
	@oc delete taskrun --selector tekton.dev/task=$(TASK_NAME)

clean: clean-taskrun clean-task
