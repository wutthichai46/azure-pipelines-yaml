# Resources in YAML

Any external service that is consumed as part of your pipeline is a resource.

An example of a resource can be another CI/CD pipeline that produces artifacts (say Azure pipelines, Jenkins etc.), code repositories (GitHub, Azure Repos, Git), container image registries (ACR, Docker hub etc.) or package feeds (Azure Artifact feed, Artifactory package etc.).  

## Why resources?

Resources are defined at one place and can be consumed anywhere in your pipeline. Resources provide you the full traceability of the services consumed in your pipeline including the branch, version, tags, associated commits and work-items. You can fully automate your DevOps workflow by subscribing to trigger events on your resources.

Resources in YAML represent sources of types pipelines, builds, repositories, containers and packages. 


### Schema

```yaml
resources:
  pipelines: [ pipeline ]  
  builds: [ build ]
  repositories: [ repository ]
  containers: [ container ]
  packages: [ package ]
```

---

## Resources: `pipelines`
 

An Azure Pipeline that produces artifacts can be consumed by defining a `pipelines` resource. `pipelines` is a dedicated resource only for Azure Pipelines. A new pipeline is triggered automatically whenever a new run of the `pipeline` resource is succesfully completed. However, you can provide specific conditions to override the `trigger`.

### Schema

```yaml
resources:        # types: pipelines | builds | repositories | containers | packages
  pipelines:
  - pipeline: string  # identifier for the pipeline resource
    connection: string  # service connection for pipelines from other Azure DevOps organizations
    project: string # project for the source; optional for current project
    source: string  # source definition of the pipeline
    version: string  # the pipeline run number to pick the artifact, defaults to Latest pipeline successful across all stages
<<<<<<< HEAD
    branch: string  # branch to pick the artifact, optional; defaults to master branch
    tags: string # picks the artifacts on from the pipeline with given tag, optional; defaults to no tags
=======
    branch: string  # branch to pick the artiafct, optional; defaults to master branch
    tag: string # picks the artifacts on from the pipeline with given tag, optional; defaults to any tag or no tag.
    trigger:     # Optional; Triggers are enabled by default.
      branches:  # branch conditions to filter the events, optional; Defaults to all branches.
        include: [ string ]  # branches to consider the trigger events, optional; Defaults to all branches.
        exclude: [ string ]  # branches to discard the trigger events, optional; Defaults to none.
      stages: [ string ]  # trigger after completion of given stage, optional; Defaults to all stage completion.
      tags: [ string ]  # tags on which the trigger events are considered, optional; Defaults to any tag or no tag.
>>>>>>> origin/ashokirla-resource-triggers
```

### Examples

<<<<<<< HEAD
If you need to consume artifacts from another azure pipeline from the current project and if you don't require setting branch, version and tags etc., this can be shortened to:
=======
You can consume artifacts from another azure pipeline from current project with simple syntax.
>>>>>>> origin/ashokirla-resource-triggers

```yaml
resources:
  pipelines:
  - pipeline: SmartHotel
    source: SmartHotel-CI # name of the pipeline source definition
```A

You can consume a azure pipeline from another project.

```yaml
resources:
  pipelines:
  - pipeline: SmartHotel
    project: DevOpsProject
    source: SmartHotel-CI
    branch: releases/M142
```

### `trigger` for pipelines

Triggers are enabled by default unless expliciltly opted out. You can disable the triggers on the `pipeline` resource.

```yaml
resources:
  pipelines:
  - pipeline: SmartHotel
    source: SmartHotel-CI 
    trigger: none
    
```

You can control which branches get the triggers with a simple syntax.
```yaml
resources:
  pipelines:
  - pipeline: SmartHotel
    source: SmartHotel-CI 
    trigger: 
    - releases/*
    - master
 ```
 
 You can specify the full name of the branch (for example, master) or a prefix-matching wildcard (for example, releases/*). You cannot put a wildcard in the middle of a value. For example, releases/*2018 is invalid.
 

You can specify the branches to include and exclude.
```yaml
resources:
  pipelines:
  - pipeline: SmartHotel
    source: SmartHotel-CI 
    trigger: 
      branches:
        include: 
        - releases/*
        exclude:
        - master
 ```
 
 You can specify which tags to control the triggers.
 ```yaml
resources:
  pipelines:
  - pipeline: SmartHotel
    source: SmartHotel-CI 
    trigger: 
      branches:
        include: 
        - releases/*
      tags: 
      - Production
      - Signed
 ```
 
If you don't want to wait until all the stages of the run are completed for the `pipeline` resource. You can provide the stage to be completed to trigger you pipeline. 

```yaml
resources:
  pipelines:
  - pipeline: SmartHotel
    source: SmartHotel-CI 
    trigger: 
      branches:
        include: master
      stages: 
      - QA
 ```
### `download` for pipelines

All artifacts from the current pipeline and from all `pipeline` resources are automatically downloaded and made available at the beginning of each of the 'deployment' job. You can override this behavior: see [Pipeline Artifacts](pipeline-artifacts.md#default-and-named-artifacts) for more details. For regular 'job' artifacts are not automatically downloaded. You need to use `download` explicitly wherever needed.

```yaml
- job: deploy_windows_x86_agent
  steps:
  - download: SmartHotel   # pipeline resource identifier.
    artifact: WebTier1  # artifact to download, optional; defaults to all the artifacts from the resource.
    patterns: '**/*.zip'  # mini match pattern to download specific files, optional; defaults to all files.
```

Or to avoid downloading any of the artifacts at all:

```yaml
- download: none
```

<<<<<<< HEAD
Artifacts from the `pipeline` resource are downloaded to `$(PIPELINE.WORKSPACE)/<pipeline-identifier>/<artifact-identifier>` folder; see [artifact download location](https://github.com/Microsoft/azure-pipelines-yaml/blob/master/design/pipeline-artifacts.md#artifact-download-location) for more details.
=======
Artifacts from the `pipeline` resource are downloaded to `$PIPELINE.RESOURCESDIRECTORY/<pipeline-identifier>/<artifact-identifier>` folder; see [artifact download location](https://github.com/Microsoft/azure-pipelines-yaml/blob/master/design/pipeline-artifacts.md#artifact-download-location) for more details.


## Resources: `repositories`

If you have multiple repositories from which you need to sync the code into your pipeline, you can consume the repos by defining a `repository` resource. A repository can be another Azure Repo or any external repo like GitHub etc. 
>>>>>>> origin/ashokirla-resource-triggers

You can define triggers for your `repository` resource. Repository triggers can be of two types.
Normal triggers: Events raised upon commit activity on the `repository` resource. By default, triggers are enabled on the `repository` resource.
PR triggers: Events raised when a pull request is raised to merge into `repository` resource. By default, pr triggers are disabled and you can to explicitly opt in. 

You can take advantage of these two triggers to run your production and testing pipelines.


### Schema

```yaml
<<<<<<< HEAD
resources:        # types: pipelines | builds | repositories | containers | packages
  builds:
  - build: string   # identifier for the build resource
    type: string   # the type of your build service like Jenkins, circleCI etc.
    connection: string   # service connection for your build service.
    source: string   # source definition of the build
    version: string   # the build number to pick the artifact, defaults to Latest successful build
    branch: string   # branch to pick the artifact; defaults to master branch
=======
resources:          # types: pipelines | repositories | containers | packages
  repositories:
  - repository: string # identifier for the repository resource      
    type: enum # type of the repository source like AzureRepos, GitHub etc. In future this can extend to other source types
    connection: string # service connection to connect to the source, defaults to primary source connection
    source: string  # source repository to fetch
    ref: string  # ref name to use, defaults to 'refs/heads/master'
    trigger:  # Optional; Triggers are enabled by default
      batch: boolean 
      branches:  # branch conditions to filter the events, optional; Defaults to all branches.
        include: [ string ]  # branches to consider the trigger events, optional; Defaults to all branches.
        exclude: [ string ]  # branches to discard the trigger events, optional; Defaults to none.  
      paths:
        include: [ string ]  # file paths to consider the trigger events, optional; Defaults to all branches.
        exclude: [ string ]  # file paths to discard the trigger events, optional; Defaults to none.  
    pr:        # Optional; pr triggers are disabled by default
      branches:  # branch conditions to filter the events, optional; Defaults to all branches.
        include: [ string ]  # branches to consider the trigger events, optional; Defaults to all branches.
        exclude: [ string ]  # branches to discard the trigger events, optional; Defaults to none.  
      paths:
        include: [ string ]  # file paths to consider the trigger events, optional; Defaults to all branches.
        exclude: [ string ]  # file paths to discard the trigger events, optional; Defaults to none.
>>>>>>> origin/ashokirla-resource-triggers
```
The inputs for the `build` resource can change based on the `type` of the build service (i.e. Jenkins, TeamCity etc.). The publisher of each extension defines the inputs for the resource. During the pipeline authoring time, when user defines the resource, it is validated based on the inputs defined in the extension.

### Examples

<<<<<<< HEAD
=======
You can consume a github repo as a `repository` resource.
>>>>>>> origin/ashokirla-resource-triggers
```yaml
resources:         
  repositories:
  - repository: secondaryRepo      
    type: GitHub
    connection: myGitHubConnection
    source: Microsoft/alphaworz
```

<<<<<<< HEAD
### `downloadBuild` for builds
You can use `downloadBuild` task to download the artifacts available as part of the `build` resource.

### Schema

```yaml
- downloadBuild: string # identifier for the resource from which to download artifacts
  artifact: string # identifier for the artifact to download; if left blank, downloads all artifacts associated with the resource provided
  patterns: string | [ string ] # a minimatch path or list of [minimatch paths](tasks/file-matching-patterns.md) to download; if blank, the entire artifact is downloaded
  path: string # relative path from $(PIPELINE.WORKSPACE) to download the artifacts
```

The inputs for `downloadBuild` macro is fixed for all the build resources. The automatic artifact download and overriding behavior of `downloadBuild` is same as the `download` macro used for [pipeline artifacts](https://github.com/Microsoft/azure-pipelines-yaml/blob/master/design/pipeline-artifacts.md#downloading-artifacts-download).


### Examples
You can customize the download behavior for each deployment or job.

```yaml
- job: deploy_windows_x86_agent
  steps:
  - downloadBuild: Spaceworkz   # build resource identifier.
    artifact: WebTier1  # artifact to download, optional; defaults to all the artifacts from the resource.
    patterns: '**/*.zip'  # mini match pattern to download specific files, optional; defaults to all files.
=======
### `trigger` in repositories

Triggers are enabled by default and any new change to your repo will trigger a new pipeline run automatically.
You can disable triggers on your repository.

```yaml
resources:         
  repositories:
  - repository: secondaryRepo      
    type: GitHub
    connection: myGitHubConnection
    source: Microsoft/alphaworz
    trigger: none
```

You can control which branches to get triggers with simple syntax.
```yaml
resources:         
  repositories:
  - repository: myPHPApp      
    type: GitHub
    connection: myGitHubConnection
    source: ashokirla/phpApp
    trigger:
      - master
      - releases/*
>>>>>>> origin/ashokirla-resource-triggers
```

You can specify branches and paths to include and exclude.
```yaml
resources:         
  repositories:
  - repository: myPHPApp      
    type: GitHub
    connection: myGitHubConnection
    source: ashokirla/phpApp
    trigger:
      branches:
        include:
        - features/*
        exclude:
        - features/experimental/*
      paths:
        exclude:
        - README.md
```
Based on the type of build resource (Jenkins, TeamCity etc.) and the associated artifacts, appropriate task is used to download the artifacts in the job.

Artifacts from the `build` resource are downloaded to `$(PIPELINE.WORKSPACE)/<build-identifier>/` folder unless user specifies a path in which case artifacts are downloaded to the path provided. 

<<<<<<< HEAD
We provide full artifact traceability i.e which artifact is downloaded from which resource for every job in a pipeline.

## Resources: `repositories`
=======
>>>>>>> origin/ashokirla-resource-triggers

If you have a lot of team members uploading changes often, then you might want to reduce the number of builds you're running. If you set batch to true, when a build is running, the system waits until the build is completed, then queues another build of all changes that have not yet been built.

```yaml
resources:         
  repositories:
  - repository: myPHPApp      
    type: GitHub
    connection: myGitHubConnection
    source: ashokirla/phpApp
    trigger:
      batch: true
      branches:
        - master
```

### `pr` triggers for repositories
Unless you specify, `pr` triggers are disabled for your repository. You can enable pull request based pipeline runs.

```yaml
resources:         
  repositories:
  - repository: myPHPApp      
    type: GitHub
    connection: myGitHubConnection
    source: ashokirla/phpApp
    pr: true
```

You can control the target branches for your pull request based pipeline runs by simple syntax.
```yaml
resources:         
  repositories:
  - repository: myPHPApp      
    type: GitHub
    connection: myGitHubConnection
    source: ashokirla/phpApp
    pr: 
    - master
    - releases/*
```

You can specify the branches and file paths to include and exclude.
```yaml
resources:         
  repositories:
  - repository: myPHPApp      
    type: GitHub
    connection: myGitHubConnection
    source: ashokirla/phpApp
    pr:
      branches:
        include:
        - releases/*
        exclude:
        - releases/experimental/*
      paths:
        include:
        - web/*
        exclude:
        - web/README.md
```


### `checkout` your repository

Repos from the `repository` resources defined are automatically synced and made available for all the jobs in the pipeline. However, in any of the jobs, you can choose to override and sync only specific repository using `checkout` shortcut. 

Repos from `repository` resource and `self` repo are not automatically synced in 'deployment' jobs. If you required repo to be fetched in the deployment job, you need to explicitly `checkout`.

```yaml
- checkout: string  # identifier for your repository; for primary repository use the keyword self.
  clean: boolean  # whether to fetch clean each time
  fetchDepth: number  # the depth of commits to ask Git to fetch
  lfs: boolean  # whether to download Git-LFS files
  submodules: true | recursive  # set to 'true' for a single level of submodules or 'recursive' to get submodules of submodules
  persistCredentials: boolean  # set to 'true' to leave the OAuth token in the Git config after the initial fetch
  root: string        # directory to checkout the repo
```

The repo is checked-out to `$(PIPELINE.RESOURCESDIRECTORY)/<pipeline-identifier>/` folder.

### Example

```yaml
- checkout: secondaryRepo  
  clean: false
  fetchDepth: 5
  lfs: true
```

You can sync your primary repository as `self`.
```yaml
- checkout: self  
```

Or to avoid syncing any of the repository resources use:

```yaml
- checkout: none
```

When you use `checkout` to sync a specific repository resource, all the other repositories are not synced. 

`self` checkout directory: `$(PIPELINE.SOURCESDIRECTORY)`
other repositories' checkout directory: `$(PIPELINE.RESOURCESDIRECTORY)/<repository-identifier>/`



## Resources: `containers`

If you need to consume a container image as part of your CI/CD pipeline, you can achieve it using `container`. A container can be an Azure Container Registry or any external Docker registry. You can enable triggers on the `container` resource. A new pipeline run get triggered whenever an image is published.


If you need to consume images from external docker registries as part of your pipeline, you can define a generic container resource. A generic container resource requires a Docker registry service connection.

### Schema
```yaml
resources:          # types: pipelines | repositories | containers | packages
  containers:
<<<<<<< HEAD
  - container: string # identifier for the container resource   
    connection: string # service connection (Docker registry) to connect to the image registry;
    image: string # container image name, Tag/Digest is optional; defaults to latest image
    options: string  # arguments to pass to container at startup
    env: { string: string }  # list of environment variables to add
    ports: [ string ] # ports to expose on the container
    volumes: [ string ] # volumes to mount on the container
=======
  - container: string # identifier for the container resource      
    type: enum # type of the registry like ACR, DockerHub etc. 
    connection: string # service connection to connect to the image registry, defaults to ACR??
    image: string # container image name, Tag/Digest is optional; defaults to latest image
    options: string # arguments to pass to container at startup
    env: { string:string } # list of environment variables to add
    trigger:
      tags:
        include: [ string ]  # image tags to consider the trigger events, optional; defaults to any new tag
        exclude: [ string ]  # image tags on discard the trigger events, option; defaults to none
      location: [ string ]  # geo-location the image published to; ACR specific setting.
>>>>>>> origin/ashokirla-resource-triggers
```

### Examples

```yaml
resources:         
  containers:
  - container: smartHotel 
    connection: myDockerRegistry
    image: smartHotelApp 
```

<<<<<<< HEAD
We also provide a first class experience for Azure Container registry (ACR). You can create a container resource of type ACR. 

### Schema
```yaml
resources:          # types: pipelines | repositories | containers | packages
  containers:
  - container: string # identifier for the container resource      
    type: string # type of the registry like ACR, GCR etc. 
    subscription: string # Azure subscription (ARM service connection) for container registry;
    registry: string # registry for the container images
    image: string # container image name, Tag/Digest is optional; defaults to latest image
```
### Examples

```yaml
resources:         
  containers:
  - container: petStore
    type: ACR
    subscription: jPetsAzureSubscription 
    registry: myDockerRegistry
    image: jPetStoreImage 
```
ACR container resource enables you to use Azure service principal (ARM service connection) for authentication. You can disable admin user for the container registry in azure and enforce using service principal.
ACR container resource provides you with rich [triggers](https://github.com/microsoft/azure-pipelines-yaml/blob/master/design/pipeline-triggers.md#containers) experience with support for location based triggers and better traceability. 

Once you define a container as resource, container image metadata is passed to the pipeline in the form of variables. Information like image, registry and connection details are made accessible across all the jobs so that your kubernetes deploy tasks can extract the image pull secrets and pass it to the cluster.

## Resources: `packages`

If you need to consume a package from your package repository as part of your deployment you can define a `package` resource. It can be any package type like Nuget, NPM, Python or Gradle. And you can consume the package from any package repository like GitHub packages, Nuget org or NPMJS etc.

### Schema
```yaml
resources:        # types: pipelines | builds | repositories | containers | packages
  packages:
  - package: string   # identifier for the package resource
    type: string   # the type of your package like Nuget, NPM etc.
    connection: string   # service connection for your package repo.
    package: string   # package definition
    version: string   # optional; the version of the package to download, defaults to latest published vession.
    tag: string   # optional; branch to pick the artifact; defaults to master branch
```
The inputs for the `package` resource can change based on the `type` of the package (i.e. Nuget, NPM etc.). The publisher of each package type defines the inputs for the resource. 

### Examples

```yaml
resources:
  packages:
  - package: MyPackage
    type: Nuget
    connection: MyConnection # Here we support Nuget service connection and GitHub service connection 
    package: types/zookeper   # name of the package; For github packages it is repo/package
```

### `getPackage` for packages

All the package types defined in `package` resources can be downloaded as part of your pipeline jobs using the macro `getPackages`. Package resources are not automatically downloaded. You need to explicitly define this task in your job/deploy-job to download the package.

### Schema
```yaml
- getPackage: string # identifier for the package resource from which to download the package
  path: string # relative path from $(PIPELINE.WORKSPACE) to download the package
```

### Examples
```yaml
- job: deploy_windows_x86_agent
  steps:
  - getPackage: MyPackage   # package resource identifier.
```

packages from the `package` resource are downloaded to `$(PIPELINE.WORKSPACE)/<resource-identifier>/` folder. 
We provide full artifact traceability i.e which package is downloaded from which resource for every job in a pipeline.

# Triggers
Refer to the [spec](https://github.com/Microsoft/azure-pipelines-yaml/blob/master/design/pipeline-triggers.md) for resource level triggers.
=======
Triggers are enabled by default on the `container` resource. When a new image gets published to your image registry, pipeline run starts automatically. You can disable the triggers on your `container`.

```yaml
resources:         
  containers:
  - container: smartHotel 
    type: Docker
    connection: myDockerRegistry
    image: smartHotelApp 
    trigger: none
```

You can specify the image tag format to get the trigger by simple syntax.
```yaml
resources:         
  containers:
  - container: smartHotel 
    type: Docker
    connection: myDockerRegistry
    image: smartHotelApp 
    trigger:
    - version-*
```

You can specify the image tags to include and exclude.
```yaml
resources:         
  containers:
  - container: smartHotel 
    type: Docker
    connection: myDockerRegistry
    image: smartHotelApp 
    trigger:
      tags:
        include:
        - version-*
        exclude:
        - version-2017*
```

If you have an ACR `container` resource, you can specify the geo location to get the triggers.
```yaml
repositories:
  containers:
  - container: MyACR    
    connection: RMPM
    registry: contosodemo
    image: Microsoft/alphaworz
    trigger: 
      tags:
        include: 
        - production*
      location: 
      - east-US
      - west-EU
```

Once you define a container as resource, container image metadata passed to the pipeline in the form of variables. Information like image, registry and connection details are made accessible across all the jobs so that your kubernetes deploy tasks can extract the image pull secrets and pass it to the cluster.

>>>>>>> origin/ashokirla-resource-triggers
