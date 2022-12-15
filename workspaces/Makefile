MANIFEST_TASK = task-helloworld-workspaces.yaml
MANIFEST_TASKRUN = taskrun-helloworld-workspaces.yaml
TASKRUN_NAME = $(shell grep "^  name:" $(MANIFEST_TASKRUN) | awk '{print $$2}' )
CONFIGMAP_FILE = workspace-configmap.yaml
SECRET_FILE = workspace-secret.yaml

get-project:
	@oc project

configmap:
	oc apply -f $(CONFIGMAP_FILE)

secret:
	oc apply -f $(SECRET_FILE)

task:
	oc apply -f $(MANIFEST_TASK)

taskrun: configmap secret task
	oc apply -f $(MANIFEST_TASKRUN)

logs:
	@echo TASKRUN_NAME: $(TASKRUN_NAME)
	tkn tr logs $(TASKRUN_NAME) -f

clean-taskrun:
	oc delete -f $(MANIFEST_TASKRUN)

clean-secret:
	oc delete -f $(SECRET_FILE)

clean-configmap:
	oc delete -f $(CONFIGMAP_FILE)

clean-task: clean-taskrun
	oc delete -f $(MANIFEST_TASK)

clean-all: clean-taskrun clean-task clean-secret clean-configmap



