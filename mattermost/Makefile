MATTERMOST_URL = http://mattermost.apps.example.com:8065
USERNAME = username
PASSWORD = secret
AUTH = {"login_id":"$(USERNAME)","password":"$(PASSWORD)"}
TOKEN = $(shell curl -si -d '$(AUTH)' $(MATTERMOST_URL)/api/v4/users/login | grep "Token:" | awk '{print $$2}' )
SECRET_MM_TOKEN_MANIFEST = mm-secret-token.yaml
SECRET_MM_TOKEN_NAME = mm-secret-token
#
TASK_MM_GENLOG_MANIFEST = mm-task-mm-genlog.yaml
TASK_MM_GENLOG_NAME = $(shell grep "^  name:" $(TASK_MM_GENLOG_MANIFEST) | awk '{print $$2}' )
TASK_MM_NOTIFICATION_MANIFEST = mm-task-mm-notification.yaml
TASK_MM_NOTIFICATION_NAME = $(shell grep "^  name:" $(TASK_MM_NOTIFICATION_MANIFEST) | awk '{print $$2}' )
PIPELINE_MANIFEST = mm-pipeline.yaml
PIPELINE_NAME = $(shell grep "^  name:" $(PIPELINE_MANIFEST) | awk '{print $$2}' )

.PHONY: beare token auth secret-token task-genlog task-mm-notification pipeline pipelinerun show

default: pipelinerun

show:
	@echo "SECRET_MM_TOKEN_MANIFEST: $(SECRET_MM_TOKEN_MANIFEST)"
	@echo "SECRET_MM_TOKEN_NAME: $(SECRET_MM_TOKEN_NAME)"
	@echo "TASK_MM_GENLOG_MANIFEST: $(TASK_MM_GENLOG_MANIFEST)"
	@echo "TASK_MM_GENLOG_NAME: $(TASK_MM_GENLOG_NAME)"
	@echo "TASK_MM_NOTIFICATION_MANIFEST: $(TASK_MM_NOTIFICATION_MANIFEST)"
	@echo "TASK_MM_NOTIFICATION_NAME: $(TASK_MM_NOTIFICATION_NAME)"
	@echo "PIPELINE_MANIFEST: $(PIPELINE_MANIFEST)"
	@echo "PIPELINE_NAME: $(PIPELINE_NAME)"

bearer:
	@curl -i -d '$(AUTH)' $(MATTERMOST_URL)/api/v4/users/login

token:
	@echo "Token: $(TOKEN)"

auth:
	@curl -i -H 'Authorization: Bearer $(TOKEN)' $(MATTERMOST_URL)/api/v4/users/me

secret-token:
	@oc create secret generic $(SECRET_MM_TOKEN_NAME) --from-literal=token=$(TOKEN) --dry-run=client -o yaml > $(SECRET_MM_TOKEN_MANIFEST)
	@oc apply -f $(SECRET_MM_TOKEN_MANIFEST)

task-mm-genlog:
	@oc apply -f $(TASK_MM_GENLOG_MANIFEST)

task-mm-notification:
	@oc apply -f $(TASK_MM_NOTIFICATION_MANIFEST)

pipeline: secret-token task-mm-genlog task-mm-notification
	@oc apply -f $(PIPELINE_MANIFEST)

pipelinerun: pipeline
	@tkn pipeline start $(PIPELINE_NAME) \
		-p IMAGE=dune.registry.arrakis.kc:8443/ach/ubi8-jq:20220805 \
		-p MATTERMOST_APPNAME=redhat \
		-p MATTERMOST_SERVICE=airbus \
		-p MATTERMOST_FILENAME=logs.txt \
		-p MATTERMOST_CHANNEL_MESSAGE=MEP6902 \
		-p MATTERMOST_BASEURL=http://mattermost.apps.example.com:8065/api/v4 \
		-p MATTERMOST_SENDFILE=true \
		-p MATTERMOST_SECRET=$(SECRET_MM_TOKEN_NAME) \
		-w name=shared-data,volumeClaimTemplateFile=workspace-template.yaml \
		--use-param-defaults \
		--showlog

clean-pipelinerun:
	@oc delete pipelinerun --selector tekton.dev/pipeline=$(PIPELINE_NAME)

clean-pipeline:
	@oc delete -f $(PIPELINE_MANIFEST)

clean-secret-token:
	@oc delete -f $(SECRET_MM_TOKEN_MANIFEST)

clean-task-mm-genlog:
	@oc delete -f $(TASK_MM_GENLOG_MANIFEST)

clean-task-mm-notification:
	@oc delete -f $(TASK_MM_NOTIFICATION_MANIFEST)

clean: clean-pipelinerun clean-pipeline clean-task-mm-genlog clean-task-mm-notification 
