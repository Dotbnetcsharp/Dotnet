- task: UseDotNet@2
  displayName: 'Use .NET 8 SDK'
  inputs:
    packageType: 'sdk'
    version: 8.0.x
    includePreviewVersions: false
