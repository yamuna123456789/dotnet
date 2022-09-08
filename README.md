pool:
  name: Azure Pipelines

steps:
- task: DotNetCoreCLI@2
  displayName: Restore
  inputs:
    command: restore
    projects: '$(Parameters.RestoreBuildProjects)'

- task: DotNetCoreCLI@2
  displayName: Build
  inputs:
    projects: '$(Parameters.RestoreBuildProjects)'
    arguments: '--configuration $(BuildConfiguration)'

- task: DotNetCoreCLI@2
  displayName: Publish
  inputs:
    command: publish
    publishWebProjects: false
    projects: '$(Parameters.RestoreBuildProjects)'
    arguments: '--configuration $(BuildConfiguration) --output $(build.artifactstagingdirectory)'
    zipAfterPublish: false
    modifyOutputPath: false

- task: PublishBuildArtifacts@1
  displayName: 'Publish Artifact'
  inputs:
    PathtoPublish: '$(build.artifactstagingdirectory)'
  condition: succeededOrFailed()
