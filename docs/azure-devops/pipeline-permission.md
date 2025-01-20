# Pipeline Permissions

In this section, we will explain how to configure the necessary permissions in Azure Pipelines to ensure that the pipeline can successfully perform commit and push operations. This guide will help you understand how to use appropriate permissions and settings to enable the pipeline to automate these tasks.

If permissions are not configured correctly, you may encounter the following error message:

```plaintext
remote: TF401027: You need the Git 'GenericContribute' permission to perform this action. Details: identity 'Build\7be59e48-a479-XXXX-a453-XXXXXXXXXXXX', scope 'repository'.
```

In this case, you can copy the GUID (7be59e48-a479-XXXX-a453-XXXXXXXXXXXX) from the error message and search for it in Azure DevOps to configure the required permissions. Detailed steps on how to do this are included below.

## 1. Permissions Required

To automate commit and push operations, you typically need to configure specific permissions to allow the pipeline to make changes to the repository. Here are some essential steps and settings:

### 1.1 Build Service Account

The **{Project-Name} Build Service** account is a project-level service account automatically created by Azure DevOps for each project. When the pipeline performs operations related to the code, it usually uses this account. To allow the pipeline to perform commit and push operations, you need to configure the following permissions for this account:

- **Contribute**: Allows committing and pushing changes.
- **Bypass policies when pushing** (if branch protection policies are set): Allows bypassing branch policies to push changes directly.

### 1.2 PersistCredentials Setting

In the pipeline's checkout step, you also need to configure the following setting to ensure that subsequent Git operations can use the appropriate credentials:

```yaml
steps:
- checkout: self
  persistCredentials: true  # Keep credentials for subsequent Git operations
```

- **`persistCredentials: true`**: Retains the credentials for subsequent Git operations. This ensures that after the checkout is complete, the pipeline can still perform commit and push operations without needing to re-authenticate.

## 2. Setup Steps

### 2.1 Build Service Permissions

1. **Access Project Settings**:

   - In Azure DevOps, go to Project Settings and select **Repositories**.

2. **Select Security Settings**:

   - Under **Repositories > Security**, find the **{Project-Name} Build Service** account. If the account is not listed, use the search function to locate the Build Service account.

3. **Configure Contribute Permission**:

   - Set the **Contribute** and **Contribute to pull requests** permission to **Allow** to enable the pipeline to push and merge changes.

### 2.2 Persistent Credentials

In the pipeline configuration, use `persistCredentials: true` to retain credentials so that Git operations can continue after the checkout.

## 3. Summary

To enable Azure Pipelines to automate commit and push operations, you need to configure the **{Project-Name} Build Service** account with **Contribute** permission and set **`persistCredentials: true`** in the checkout step. These settings ensure that the pipeline has sufficient permissions to modify the repository during the automation process.
