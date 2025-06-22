**Azure DevOps Pipeline for Java Web App on Linux**

This pipeline automates the process of building a Maven-based Java project, performing static code analysis with SonarQube, packaging artifacts, and deploying to an Azure Web App running on Linux.

**🛠️ Prerequisites**
Before using this pipeline:

✔️ Azure DevOps organization with a project.
✔️ Java Maven project with a valid pom.xml.
✔️ Service connection named ado-azure-spn to authenticate to Azure.
✔️ SonarQube service connection named ado-sonarqube-connection.
✔️ Self-hosted or Microsoft-hosted agent pool (ch14pool) with Java and Maven installed.
✔️ Azure Web App (Linux) named ch14webapp already created.

**🔁 Trigger**
trigger:
- main
Triggers the pipeline on changes pushed to the main branch.

**🔧 Pipeline Variables**
variables:
  azureSubscription: 'ado-azure-spn' # The service connection name between Azure portal and Azure DevOps
  webAppName: 'ch14webapp' # The webapp created on Azure portal
  environmentName: 'dev'
  vmImageName: 'ch14pool' # The agent pool, which is a dedicated VM, with all installed applications to run the pipeline
azureSubscription: Name of your Azure service connection.
webAppName: Target Web App in Azure for deployment.
environmentName: Logical environment for deployment slot management and approvals.
vmImageName: Agent pool or image (e.g., ubuntu-latest or your self-hosted pool).

**🔍 Stage 1: SonarQube Code Scan**
- stage: CodeScan
  displayName: Sonar Code Scan
Performs static code analysis using SonarQube:

- task: SonarQubePrepare@6
- task: Maven@4
This stage scans your Java project for code quality issues and security vulnerabilities.

**🧪 Stage 2: Build and Publish**
- stage: Build
  displayName: Build stage
Runs mvn clean install.
Copies the generated *.jar or *.war files to the artifact staging directory.
Publishes them as build artifacts.
- task: Maven@3
- task: CopyFiles@2
- upload: $(Build.ArtifactStagingDirectory)

**🚀 Stage 3: Deploy to Azure Web App**
- stage: Deploy
  displayName: Deploy stage
Deploys the packaged artifact to the specified Azure Linux Web App using the AzureWebApp@1 task.
- task: AzureWebApp@1
This task reads the artifact from the previous stage and pushes it to Azure.

**📌 Notes**
📦 Artifact Packaging: Only .jar or .war files in /target are uploaded and deployed.
🔒 Security: Keep secrets in Azure DevOps Library or use Azure Key Vault integration.
✅ Environment Approvals: You can enforce manual approval gates for the dev environment.
📈 SonarQube Setup: Make sure your project key and name are consistent with what’s registered on your SonarQube server.

🙋‍♂️ Author Chinwe Ebube Onaifoh 📫 onaifohchinwe094@gmail.com 📞 +1 (437) 473-4649 📍 Ajax, Ontario, Canada
