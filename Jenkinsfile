def PowerShell(psCmd) {
    bat "powershell.exe -NonInteractive -ExecutionPolicy Bypass -Command \"\$ErrorActionPreference='Stop';$psCmd;EXIT \$global:LastExitCode\""
}

def StartContainer() {
    println "Current branch ${env.BRANCH_NAME}"
    
    switch( env.BRANCH_NAME ) {
      case "master":
        sh 'docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=P@ssword1" --name SQLLinuxMaster -d -i -p 15565:1433 microsoft/mssql-server-linux'
        break
      case "Release":
        sh 'docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=P@ssword1" --name SQLLinuxRelease -d -i -p 15566:1433 microsoft/mssql-server-linux'
        break
      case "Feature":
        sh 'docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=P@ssword1" --name SQLLinuxFeature -d -i -p 15567:1433 microsoft/mssql-server-linux'
        break
      case "Prototype":
        sh 'docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=P@ssword1" --name SQLLinuxPrototype -d -i -p 15568:1433 microsoft/mssql-server-linux'
        break
      default:
        sh 'docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=P@ssword1" --name SQLLinuxHotFix -d -i -p 15569:1433 microsoft/mssql-server-linux'
        break
    }
}

def DeployDacpac() {
    unstash 'theDacpac'

    switch(env.BRANCH_NAME) {
      case "master":
        bat "\"C:\\Program Files\\Microsoft SQL Server\\140\\DAC\\bin\\sqlpackage.exe\" /Action:Publish /SourceFile:\"SelfBuildPipeline\\bin\\Release\\SelfBuildPipeline.dacpac\" /TargetConnectionString:\"server=localhost,15565;database=SsdtDevOpsDemo;user id=sa;password=P@ssword1\" /p:ExcludeObjectType=Logins"
        break
      case "Release":
        bat "\"C:\\Program Files\\Microsoft SQL Server\\140\\DAC\\bin\\sqlpackage.exe\" /Action:Publish /SourceFile:\"SelfBuildPipeline\\bin\\Release\\SelfBuildPipeline.dacpac\" /TargetConnectionString:\"server=localhost,15566;database=SsdtDevOpsDemo;user id=sa;password=P@ssword1\" /p:ExcludeObjectType=Logins"
        break
      case "Feature":
        bat "\"C:\\Program Files\\Microsoft SQL Server\\140\\DAC\\bin\\sqlpackage.exe\" /Action:Publish /SourceFile:\"SelfBuildPipeline\\bin\\Release\\SelfBuildPipeline.dacpac\" /TargetConnectionString:\"server=localhost,15567;database=SsdtDevOpsDemo;user id=sa;password=P@ssword1\" /p:ExcludeObjectType=Logins"
        break
      case "Prototype":
        bat "\"C:\\Program Files\\Microsoft SQL Server\\140\\DAC\\bin\\sqlpackage.exe\" /Action:Publish /SourceFile:\"SelfBuildPipeline\\bin\\Release\\SelfBuildPipeline.dacpac\" /TargetConnectionString:\"server=localhost,15568;database=SsdtDevOpsDemo;user id=sa;password=P@ssword1\" /p:ExcludeObjectType=Logins"
        break
      default:
        bat "\"C:\\Program Files\\Microsoft SQL Server\\140\\DAC\\bin\\sqlpackage.exe\" /Action:Publish /SourceFile:\"SelfBuildPipeline\\bin\\Release\\SelfBuildPipeline.dacpac\" /TargetConnectionString:\"server=localhost,15569;database=SsdtDevOpsDemo;user id=sa;password=P@ssword1\" /p:ExcludeObjectType=Logins"
        break
    }
}

def CleanUp() {
    switch(env.BRANCH_NAME) {
      case "master":
        sh 'docker stop SQLLinuxMaster'
        sh 'docker rm SQLLinuxMaster'
        break
      case "Release":
        sh 'docker stop SQLLinuxRelease'
        sh 'docker rm SQLLinuxRelease'
        break
      case "Feature":
        sh 'docker stop SQLLinuxFeature'
        sh 'docker rm SQLLinuxFeature'
        break
      case "Prototype":
        sh 'docker stop SQLLinuxPrototype'
        sh 'docker rm SQLLinuxPrototype'
        break
      default:
        sh 'docker stop SQLLinuxHotFix'
        sh 'docker rm SQLLinuxHotFix'
        break
    }
}

node {
    stage('git checkout') {
        checkout scm
    }

    stage('build dacpac') {
        bat "\"${tool name: 'Default', type: 'msbuild'}\" /p:Configuration=Release"
        stash includes: 'SelfBuildPipeline\\bin\\Release\\SelfBuildPipeline.dacpac', name: 'theDacpac'
    }

    stage('start container') {
        StartContainer()
    }

    stage('deploy dacpac') {
        DeployDacpac()
    }

    stage('run tests') {
        PowerShell('Start-Sleep -s 5')
    }

    stage('cleanup') {
        CleanUp()
    }
}
