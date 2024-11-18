---
title: Elevate access to manage all Azure subscriptions and management groups
description: Describes how to elevate access for a Global Administrator to manage all subscriptions and management groups in Microsoft Entra ID using the Azure portal or REST API.
author: rolyon
manager: amycolannino
ms.service: role-based-access-control
ms.topic: how-to
ms.date: 02/16/2024
ms.author: rolyon
ms.custom: devx-track-azurecli
---
# Elevate access to manage all Azure subscriptions and management groups

As a Global Administrator in Microsoft Entra ID, you might not have access to all subscriptions and management groups in your directory. This article describes the ways that you can elevate your access to all subscriptions and management groups.

[!INCLUDE [gdpr-dsr-and-stp-note](~/reusable-content/ce-skilling/azure/includes/gdpr-dsr-and-stp-note.md)]

## Why would you need to elevate your access?

If you are a Global Administrator, there might be times when you want to do the following actions:

- Regain access to an Azure subscription or management group when a user has lost access
- Grant another user or yourself access to an Azure subscription or management group
- See all Azure subscriptions or management groups in an organization
- Allow an automation app (such as an invoicing or auditing app) to access all Azure subscriptions or management groups

## How does elevated access work?

Microsoft Entra ID and Azure resources are secured independently from one another. That is, Microsoft Entra role assignments do not grant access to Azure resources, and Azure role assignments do not grant access to Microsoft Entra ID. However, if you are a [Global Administrator](../active-directory/roles/permissions-reference.md#global-administrator) in Microsoft Entra ID, you can assign yourself access to all Azure subscriptions and management groups in your directory. Use this capability if you don't have access to Azure subscription resources, such as virtual machines or storage accounts, and you want to use your Global Administrator privilege to gain access to those resources.

When you elevate your access, you will be assigned the [User Access Administrator](built-in-roles.md#user-access-administrator) role in Azure at root scope (`/`). This allows you to view all resources and assign access in any subscription or management group in the directory. User Access Administrator role assignments can be removed using Azure PowerShell, Azure CLI, or the REST API.

You should remove this elevated access once you have made the changes you need to make at root scope.

![Elevate access](./media/elevate-access-global-admin/elevate-access.png)

## Perform steps at root scope

# [Azure portal](#tab/azure-portal)

### Step 1: Elevate access for a Global Administrator

Follow these steps to elevate access for a Global Administrator using the Azure portal.

1. Sign in to the [Azure portal](https://portal.azure.com) as a Global Administrator.

    If you are using Microsoft Entra Privileged Identity Management, [activate your Global Administrator role assignment](../active-directory/privileged-identity-management/pim-how-to-activate-role.md).

1. Open **Microsoft Entra ID**.

1. Under **Manage**, select **Properties**.

   ![Select Properties for Microsoft Entra properties - screenshot](./media/elevate-access-global-admin/azure-active-directory-properties.png)

1. Under **Access management for Azure resources**, set the toggle to **Yes**.

   ![Access management for Azure resources - screenshot](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   When you set the toggle to **Yes**, you are assigned the User Access Administrator role in Azure RBAC at root scope (/). This grants you permission to assign roles in all Azure subscriptions and management groups associated with this Microsoft Entra directory. This toggle is only available to users who are assigned the Global Administrator role in Microsoft Entra ID.

   When you set the toggle to **No**, the User Access Administrator role in Azure RBAC is removed from your user account. You can no longer assign roles in all Azure subscriptions and management groups that are associated with this Microsoft Entra directory. You can view and manage only the Azure subscriptions and management groups to which you have been granted access.

    > [!NOTE]
    > If you're using [Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md), deactivating your role assignment does not change the **Access management for Azure resources** toggle to **No**. To maintain least privileged access, we recommend that you set this toggle to **No** before you deactivate your role assignment.

1. Click **Save** to save your setting.

   This setting is not a global property and applies only to the currently signed in user. You can't elevate access for all members of the Global Administrator role.

1. Sign out and sign back in to refresh your access.

    You should now have access to all subscriptions and management groups in your directory. When you view the Access control (IAM) pane, you'll notice that you have been assigned the User Access Administrator role at root scope.

   ![Subscription role assignments with root scope - screenshot](./media/elevate-access-global-admin/iam-root.png)

1. Make the changes you need to make at elevated access.

    For information about assigning roles, see [Assign Azure roles using the Azure portal](role-assignments-portal.yml). If you are using Privileged Identity Management, see [Discover Azure resources to manage](../active-directory/privileged-identity-management/pim-resource-roles-discover-resources.md) or [Assign Azure resource roles](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md).

1. Perform the steps in the following section to remove your elevated access.

### Step 2: Remove elevated access

To remove the User Access Administrator role assignment at root scope (`/`), follow these steps.

1. Sign in as the same user that was used to elevate access.

1. In the navigation list, click **Microsoft Entra ID** and then click **Properties**.

1. Set the **Access management for Azure resources** toggle back to **No**. Since this is a per-user setting, you must be signed in as the same user as was used to elevate access.

    If you try to remove the User Access Administrator role assignment on the Access control (IAM) pane, you'll see the following message. To remove the role assignment, you must set the toggle back to **No** or use Azure PowerShell, Azure CLI, or the REST API.

    ![Remove role assignments with root scope](./media/elevate-access-global-admin/iam-root-remove.png)

1. Sign out as Global Administrator.

    If you are using Privileged Identity Management, deactivate your Global Administrator role assignment.

    > [!NOTE]
    > If you're using [Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md), deactivating your role assignment does not change the **Access management for Azure resources** toggle to **No**. To maintain least privileged access, we recommend that you set this toggle to **No** before you deactivate your role assignment.

# [PowerShell](#tab/powershell)

### Step 1: Elevate access for a Global Administrator

Use the Azure portal or REST API to elevate access for a Global Administrator.

### Step 2: List role assignment at root scope (/)

Once you have elevated access, to list the User Access Administrator role assignment for a user at root scope (`/`), use the [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) command.

```azurepowershell
Get-AzRoleAssignment | where {$_.RoleDefinitionName -eq "User Access Administrator" `
  -and $_.SignInName -eq "<username@example.com>" -and $_.Scope -eq "/"}
```

```Example
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111
Scope              : /
DisplayName        : username
SignInName         : username@example.com
RoleDefinitionName : User Access Administrator
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9
ObjectId           : 22222222-2222-2222-2222-222222222222
ObjectType         : User
CanDelegate        : False
```

### Step 3: Remove elevated access

To remove the User Access Administrator role assignment for yourself or another user at root scope (`/`), follow these steps.

1. Sign in as a user that can remove elevated access. This can be the same user that was used to elevate access or another Global Administrator with elevated access at root scope.

1. Use the [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) command to remove the User Access Administrator role assignment.

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName <username@example.com> `
      -RoleDefinitionName "User Access Administrator" -Scope "/"
    ```

# [Azure CLI](#tab/azure-cli)

### Step 1: Elevate access for a Global Administrator

Use the following basic steps to elevate access for a Global Administrator using the Azure CLI.

1. Use the [az rest](/cli/azure/reference-index#az-rest) command to call the `elevateAccess` endpoint, which grants you the User Access Administrator role at root scope (`/`).

    ```azurecli
    az rest --method post --url "/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01"
    ```

1. Make the changes you need to make at elevated access.

    For information about assigning roles, see [Assign Azure roles using the Azure CLI](role-assignments-cli.md).

1. Perform the steps in a later section to remove your elevated access.

### Step 2: List role assignment at root scope (/)

Once you have elevated access, to list the User Access Administrator role assignment for a user at root scope (`/`), use the [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list) command.

```azurecli
az role assignment list --role "User Access Administrator" --scope "/"
```

```Example
[
  {
    "canDelegate": null,
    "id": "/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
    "name": "11111111-1111-1111-1111-111111111111",
    "principalId": "22222222-2222-2222-2222-222222222222",
    "principalName": "username@example.com",
    "principalType": "User",
    "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "roleDefinitionName": "User Access Administrator",
    "scope": "/",
    "type": "Microsoft.Authorization/roleAssignments"
  }
]

```

### Step 3: Remove elevated access

To remove the User Access Administrator role assignment for yourself or another user at root scope (`/`), follow these steps.

1. Sign in as a user that can remove elevated access. This can be the same user that was used to elevate access or another Global Administrator with elevated access at root scope.

1. Use the [az role assignment delete](/cli/azure/role/assignment#az-role-assignment-delete) command to remove the User Access Administrator role assignment.

    ```azurecli
    az role assignment delete --assignee username@example.com --role "User Access Administrator" --scope "/"
    ```

# [REST API](#tab/rest-api)

### Prerequisites

You must use the following versions:

- `2015-07-01` or later to list and remove role assignments
- `2016-07-01` or later to elevate access
- `2018-07-01-preview` or later to list deny assignments

For more information, see [API versions of Azure RBAC REST APIs](/rest/api/authorization/versions).

### Step 1: Elevate access for a Global Administrator

Use the following basic steps to elevate access for a Global Administrator using the REST API.

1. Using REST, call `elevateAccess`, which grants you the User Access Administrator role at root scope (`/`).

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. Make the changes you need to make at elevated access.

    For information about assigning roles, see [Assign Azure roles using the REST API](role-assignments-rest.md).

1. Perform the steps in a later section to remove your elevated access.

### Step 2: List role assignments at root scope (/)

Once you have elevated access, you can list all of the role assignments for a user at root scope (`/`).

- Call [Role Assignments - List For Scope](/rest/api/authorization/role-assignments/list-for-scope) where `{objectIdOfUser}` is the object ID of the user whose role assignments you want to retrieve.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2022-04-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

### Step 3: List deny assignments at root scope (/)

Once you have elevated access, you can list all of the deny assignments for a user at root scope (`/`).

- Call [Deny Assignments - List For Scope](/rest/api/authorization/deny-assignments/list-for-scope) where `{objectIdOfUser}` is the object ID of the user whose deny assignments you want to retrieve.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2022-04-01&$filter=gdprExportPrincipalId+eq+'{objectIdOfUser}'
   ```

### Step 4: Remove elevated access

When you call `elevateAccess`, you create a role assignment for yourself, so to revoke those privileges you need to remove the User Access Administrator role assignment for yourself at root scope (`/`).

1. Call [Role Definitions - Get](/rest/api/authorization/role-definitions/get) where `roleName` equals User Access Administrator to determine the name ID of the User Access Administrator role.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2022-04-01&$filter=roleName+eq+'User Access Administrator'
    ```

    ```json
    {
      "value": [
        {
          "properties": {
      "roleName": "User Access Administrator",
      "type": "BuiltInRole",
      "description": "Lets you manage user access to Azure resources.",
      "assignableScopes": [
        "/"
      ],
      "permissions": [
        {
          "actions": [
            "*/read",
            "Microsoft.Authorization/*",
            "Microsoft.Support/*"
          ],
          "notActions": []
        }
      ],
      "createdOn": "0001-01-01T08:00:00.0000000Z",
      "updatedOn": "2016-05-31T23:14:04.6964687Z",
      "createdBy": null,
      "updatedBy": null
          },
          "id": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
          "type": "Microsoft.Authorization/roleDefinitions",
          "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"
        }
      ],
      "nextLink": null
    }
    ```

    Save the ID from the `name` parameter, in this case `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9`.

1. You also need to list the role assignment for the directory administrator at directory scope. List all assignments at directory scope for the `principalId` of the directory administrator who made the elevate access call. This will list all assignments in the directory for the objectid.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2022-04-01&$filter=principalId+eq+'{objectid}'
    ```

    >[!NOTE]
    >A directory administrator should not have many assignments, if the previous query returns too many assignments, you can also query for all assignments just at directory scope level, then filter the results:
    > `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2022-04-01&$filter=atScope()`

1. The previous calls return a list of role assignments. Find the role assignment where the scope is `"/"` and the `roleDefinitionId` ends with the role name ID you found in step 1 and `principalId` matches the objectId of the directory administrator.

    Sample role assignment:

    ```json
    {
      "value": [
        {
          "properties": {
            "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
            "principalId": "{objectID}",
            "scope": "/",
            "createdOn": "2016-08-17T19:21:16.3422480Z",
            "updatedOn": "2016-08-17T19:21:16.3422480Z",
            "createdBy": "22222222-2222-2222-2222-222222222222",
            "updatedBy": "22222222-2222-2222-2222-222222222222"
          },
          "id": "/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
          "type": "Microsoft.Authorization/roleAssignments",
          "name": "11111111-1111-1111-1111-111111111111"
        }
      ],
      "nextLink": null
    }
    ```

    Again, save the ID from the `name` parameter, in this case 11111111-1111-1111-1111-111111111111.

1. Finally, Use the role assignment ID to remove the assignment added by `elevateAccess`:

    ```http
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111?api-version=2022-04-01
    ```

---

## View elevate access log entries in the Directory Activity logs

When access is elevated, an entry is added to the logs. As a Global Administrator in Microsoft Entra ID, you might want to check when access was elevated and who did it. Elevate access log entries do not appear in the standard activity logs, but instead appear in the Directory Activity logs. This section describes different ways that you can view the elevate access log entries.

### View elevate access log entries using the Azure portal

1. Sign in to the [Azure portal](https://portal.azure.com) as a Global Administrator.

1. Open **Monitor** > **Activity log**.

1. Change the **Activity** list to **Directory Activity**.

1. Search for the following operation, which signifies the elevate access action.

    `Assigns the caller to User Access Administrator role`

    ![Screenshot showing directory activity logs in Monitor.](./media/elevate-access-global-admin/monitor-directory-activity.png)

### View elevate access log entries using Azure CLI

1. Use the [az login](/cli/azure/reference-index#az-login) command to sign in as Global Administrator.

1. Use the [az rest](/cli/azure/reference-index#az-rest) command to make the following call where you will have to filter by a date as shown with the example timestamp and specify a filename where you want the logs to be stored.

    The `url` calls an API to retrieve the logs in Microsoft.Insights. The output will be saved to your file.

    ```azurecli
    az rest --url "https://management.azure.com/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2021-09-10T20:00:00Z'" > output.txt
    ```

1. In the output file, search for `elevateAccess`.

    The log will resemble the following where you can see the timestamp of when the action occurred and who called it.

    ```json
      "submissionTimestamp": "2021-08-27T15:42:00.1527942Z",
      "subscriptionId": "",
      "tenantId": "33333333-3333-3333-3333-333333333333"
    },
    {
      "authorization": {
        "action": "Microsoft.Authorization/elevateAccess/action",
        "scope": "/providers/Microsoft.Authorization"
      },
      "caller": "user@example.com",
      "category": {
        "localizedValue": "Administrative",
        "value": "Administrative"
      },
    ```

### Delegate access to a group to view elevate access log entries using Azure CLI

If you want to be able to periodically get the elevate access log entries, you can delegate access to a group and then use Azure CLI.

1. Open **Microsoft Entra ID** > **Groups**.

1. Create a new security group and note the group object ID.

1. Use the [az login](/cli/azure/reference-index#az-login) command to sign in as Global Administrator.

1. Use the [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) command to assign the [Reader](built-in-roles.md#reader) role to the group who can only read logs at the directory level, which are found at `Microsoft/Insights`.

    ```azurecli
    az role assignment create --assignee "{groupId}" --role "Reader" --scope "/providers/Microsoft.Insights"
    ```

1. Add a user who will read logs to the previously created group.

A user in the group can now periodically run the [az rest](/cli/azure/reference-index#az-rest) command to view elevate access log entries.

```azurecli
az rest --url "https://management.azure.com/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2021-09-10T20:00:00Z'" > output.txt
```

## Next steps

- [Understand the different roles](rbac-and-directory-admin-roles.md)
- [Assign Azure roles using the REST API](role-assignments-rest.md)
