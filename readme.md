# CI/CD Description

Pipelines are implemented using github actions. 

<hr />


## Files

The steps are described in the files in the ```.github/workflows``` folder:

### Main files

Main files describing trigger, enviroment parameters and steps for the workflow:

#### ```prod.yaml```

Trigger on push tag event with mask ```vX.X.X```
Run steps:
* ```docker-build-n-push.yml```
* ```run-ansible-playbook.yml```

#### ```staging.yaml```

Trigger on push tag event with mask ```vX.X.X-rc.X```
Run steps:
* ```docker-build-n-push.yml```
* ```run-ansible-playbook.yml```


### Pipeline files
Files describing pipeline steps:

#### ```docker-build-n-push.yml```

Build container and publish to registry

##### Variables
* ```DOCKER_DIR``` - Location of  Dockerfile file
* ```APP_ENV_PREFIX``` - The prefix used to pass github secrets as env vars to the docker build environment.
* ```AWS_REGION``` - AWS region
* ```ECR_REPOSITORY``` - Target ECR for pushing image

#### run-ansible-playbook.yml

Deploy conteiners to hosts via ansible

##### Variables

* ```DEVOPS_BRANCH``` - git branch with ansible
* ```DEVOPS_REPO``` - git repository with ansible
* ```ENV``` - working environment (origin-stage/origin-worker)
* ```APP_ENV_PREFIX``` - The prefix used to pass github secrets as env vars to the application working environment
* ```IMAGE_TAG``` - Image tag to deploy
* ```ecr_info``` - ECR repository credentials
* ```nat_ip``` - IP of NAT gateway instance (used as gateway)
* ```lb``` - Is the service using load balancer (used for additions steps)

<hr />

## Environment variables:

### Github action variables 

Pipeline environment variables defined in main files: ```prod.yaml```, ```staging.yaml```.

### Github action secrets

In addition to the set variables in the main files, the following secrets must be specified in github repository:

1. ```AWS_ACCESS_KEY_ID``` - AWS Access Key
2. ```AWS_SECRET_ACCESS_KEY``` - AWS Secret Key
3. ```AWS_REGION``` - AWS region 
2. ```SPOTINST_ACCOUNT``` -  A valid Spotinst account ID
2. ```SPOTINST_TOKEN``` - A Personal API Access Token issued by Spotinst
2. ```DEPLOY_KEY``` - SSH private key to pull [devops repo](https://github.com/joe-systems/devops). 


### Application environment variables


Github secrets with prefix **$APP_ENV_PREFIX** defined in ```staging.yaml``` and ```prod.yaml``` 
will be passed to app without prefixes. 
Prefixes : ```APP_STAGE_``` and ```APP_PROD_``` is used by default.

Example:
1. Github secret ```APP_PROD_ENVIRONMENT``` will be passed to application environment as ```ENVIRONMENT``` variable in production environment.

2. Github secret ```APP_STAGE_ENVIRONMENT``` will be passed to application environment as ```ENVIRONMENT``` variable in staging environment.

<hr />



