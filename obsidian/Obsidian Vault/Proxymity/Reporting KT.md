## Repo: reporting-managemant
report API (generic, aka unified report generator)

build pipeline: `reporting-management`
deploy pipeline: 
* deploy-reporting-management-lambda-dev02
* deploy-reporting-management-lambda-uat
* deploy-lambda-reporting-management-prod

Uses *pipelineBuildSAM* and *pipelineDeploySAMPackage*

## Repo: automatic-reports
Automatic reports generation, has 2 step functions:
* Executor: run children in parallel
	automatic-reports-state-machine-child-error.asl.yaml
	name: **automatic-reports-state-machine**
* Child: generate the report and ship it
	automatic-reports-state-machine-executor.asl.yaml
	name: **automatic-reports-executor**
	uses reporting-management in the step: Call unified reports API
	6 Lambdas:
	* Check status (DB)
	* Call unified reports
	* Update status (DB)
	* Email ship
	* Sftp ship
	* S3 ship



## KT
**Can you show how you test this?**
I can try...

**Which Jenkins pipeline deploys the cloudformation?**
`template.yml` in `automatic-reports` build and deploy CF, lambdas and step functions. I used to run it locally from my machine.
```sh
sam build
sam deploy --guided --profile aws-dev
```

The jenkins build pipeline is: `automatic-reports` is uses old build method (not pipelineBuildSAM) which builds only the lambdas code.

There are pipelines to deploy only configuration changes:
* deploy-automatic-reports-config-files-dev02
* deploy-automatic-reports-config-files-uat
* deploy-automatic-reports-config-files-prod
they use the `Jenkinsfile_config` from the deploy folders.

There is old deploy pipeline:
deploy-automatic-reports-dev02
it uses Jenkinsfile from the deploy folder.

Also old deploy pipelines:
* deploy-lambda-automatic-reports-dev02
* deploy-lambda-automatic-reports-uat
* deploy-udpate-automatic-reports-lambda-prod
* deploy-udpate-automatic-reports-lambda-uat
* deploy-udpate-automatic-reports-lambda-dev02
(uses same Jenkinsfile as above)

**I think the relevant deploy pipeline is:**
deploy-lambdas-packages-generic-job
which let you select automatic-reports builds (but I think only deploy the code, but this needs to be taken with DevOps)

***There is a mess here and we need to make some order in all those pipelines...***


**Why are there 2 state machines?**
See above.

**What are the 2 different folders in the repo for?**
I assume this refers to `automatic-reports` and `report-generator`.
The 1st is the automatic reports relevant code.
the 2nd was the initial coding effort to separate generation into a general purpose API. It was later moved to its own repo (see above) and was never cleaned up here.

**Does everything begin with child error?**
Yes, this is what is triggered by event bridge every 30 min. Naming is confusing. it was `automatic-reports-state-machine.asl.yaml` originally, but renamed to `automatic-reports-state-machine-child-error.asl.yaml` when we added handling of the inner step functions errors.

**Why are the Jenkins pipelines different between environments?**
Because it is a mess...

**Why is there a db_script?**
This script was used to create the `report_execution` table in the PG db (used for status and auditing). This is also used for auto recovery from generation errors - used in the check status step and update status step.

**Why is Liquibase and liquidb referenced in the Jenkins pipeline?**
If you refer to the generic one, because it also deploy projects with liquibase

**Why does the Jenkins deploy pipeline not let you pick a deployed artifact for the Lambda deployment? Does it always pick the latest for a branch?**
                •                              •              https://jenkins.proxymity.io/job/deploy-lambda-automatic-reports-dev02/build?delay=0sec 
 I think this one is very old and not sure if working.
 `deploy-lambdas-packages-generic-job` will let you pick artifact.

	
	
