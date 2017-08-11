def BranchToPort = [
    'master'   : 15565,
    'Release'  : 15566,
    'Feature'  : 15567,
    'Prototype': 15568,
    'HotFix'   : 15569
]

def StartContainer() {
    def BranchToPort = [
        'master'   : 15565,
        'Release'  : 15566,
        'Feature'  : 15567,
        'Prototype': 15568,
        'HotFix'   : 15569
    ]    
    
    def SqlPort = BranchToPort[env.BRANCH_NAME]
    print "Debug point 1"
    print ${SqlPort}
    print "Debug point 2"
    bat "docker run -e \"ACCEPT_EULA=Y\" -e \"SA_PASSWORD=P@ssword1\" --name SQLLinux${env.BRANCH_NAME} -d -i -p {SqlPort}:1433 microsoft/mssql-server-linux"
}

def DeployDacpac() {
    def SqlPackage = "C:\\Program Files\\Microsoft SQL Server\\140\\DAC\\bin\\sqlpackage.exe"
    def SourceFile = "SelfBuildPipeline\\bin\\Release\\SelfBuildPipeline.dacpac"
    def ConnString = "server=localhost,15565;database=SsdtDevOpsDemo;user id=sa;password=P@ssword1"
    
    unstash 'theDacpac'
    bat "\"${SqlPackage}\" /Action:Publish /SourceFile:\"${SourceFile}\" /TargetConnectionString:\"${ConnString}\" /p:ExcludeObjectType=Logins"
}

node {
    stage('git checkout') {
        git 'https://github.com/chrisadkin/SelfBuildPipeline'
    }
    stage('build dacpac') {
        bat "\"${tool name: 'Default', type: 'msbuild'}\" /p:Configuration=Release"
        stash includes: 'SelfBuildPipeline\\bin\\Release\\SelfBuildPipeline.dacpac', name: 'theDacpac'
    }
    stage('start container') {
        StartContainer()
    }
    stage('deploy dacpac') {
        try {
            DeployDacpac()
        }
        catch (error) {
            throw error
        }
        finally {
            bat "docker rm -f SQLLinux${env.BRANCH_NAME}"
        }
    }
}
