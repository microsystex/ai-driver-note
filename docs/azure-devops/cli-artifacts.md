# CLI Deployment

Using a CLI for AI-assisted software development is an effective approach. It not only allows local execution but also facilitates broader use through CI/CD pipelines or as a VS Code extension.

This article primarily documents the process of deploying the CLI to Azure DevOps Artifacts, with special emphasis on properly configuring permissions.

## 1. Deployment Overview

Deploying a CLI to Azure DevOps Artifacts allows it to be shared and used by multiple teams or projects. This process requires careful permission management to ensure that the deployment and access are both secure and efficient.

## 2. Required Permissions

### 2.1 Feed Permissions

To deploy the CLI to Azure DevOps Artifacts, you need to properly configure permissions for the feed where the artifacts will be published. Here are the necessary steps:

- **Manage Feed Permissions**: Navigate to **Artifacts** in Azure DevOps, select the desired feed, and click on the gear icon to enter **Feed Settings**. In the **Permissions** tab, add the **Project Collection Build Service** account if it is not already listed, and grant it **Contributor** permissions to allow publishing and managing packages.

### 2.2 Pipeline Configuration

In the pipeline configuration, use the following steps to ensure the CLI is successfully deployed to Azure DevOps Artifacts:

1. **Access Feed Settings**:

   - Navigate to **Artifacts** in Azure DevOps, select the desired feed, and configure permissions using the gear icon to allow the **Project Collection Build Service** to publish packages.

2. **Pipeline Task for Deployment**:

   - Use the following YAML task to publish the CLI to Azure Artifacts:

   ```yaml
   steps:
   - task: npm@1
     inputs:
       command: 'publish'
       publishRegistry: 'useFeed'
       publishFeed: 'your-feed-id'
       workingDir: '$(System.DefaultWorkingDirectory)'
   ```

   - Explanation of each input parameter:
     - command: Sets the npm command. Here, 'publish' is used to indicate publishing the package to the registry.
     - publishRegistry: Specifies the registry to use. 'useFeed' here indicates using an internal registry in Azure Artifacts.
     - publishFeed: Specifies the ID of the registry to publish to. This needs to be replaced with the actual registry identifier.
     - workingDir: Sets the working directory. `$(System.DefaultWorkingDirectory)` is the default working directory of the pipeline, usually containing the generated package files.

## 3. Summary

Deploying the CLI to Azure DevOps Artifacts involves setting up the correct permissions for the **Project Collection Build Service** account and configuring the pipeline to publish the package. Make sure the appropriate permissions are configured at the feed level to allow successful deployment. Proper permission management is crucial to ensure a smooth deployment process and secure access to the published CLI. Feed permissions are managed through the **Artifacts** section by accessing the feed settings and configuring permissions through the gear icon.
