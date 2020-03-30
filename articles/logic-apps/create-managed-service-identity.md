---
title: 使用受控識別進行驗證
description: 使用託管標識訪問其他 Azure 活動目錄租戶中的資源，而無需使用憑據或機密登錄
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: 82710a66cdf7874c745070e49b2c7aff7bc8816d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77117200"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>使用 Azure 邏輯應用中的託管標識對 Azure 資源的訪問進行身份驗證

要訪問其他 Azure 活動目錄 （Azure AD） 租戶中的資源並在未登錄的情況下對標識進行身份驗證，邏輯應用可以使用[託管標識](../active-directory/managed-identities-azure-resources/overview.md)（以前託管服務標識或 MSI），而不是憑據或機密。 Azure 會為您管理此身分識別，並協助保護您的認證，因為您不需要提供或輪替使用祕密。

Azure 邏輯應用同時支援[*系統分配*](../active-directory/managed-identities-azure-resources/overview.md)和[*使用者分配的*](../active-directory/managed-identities-azure-resources/overview.md)託管標識。 邏輯應用可以使用系統分配的標識或*單個*使用者分配的標識，您可以在一組邏輯應用之間共用，但不能同時共用這兩個標識。 目前，只有[特定的內置觸發器和操作](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)支援託管標識，不支援託管連接器或連接，例如：

* HTTP
* Azure Functions
* Azure API 管理
* Azure App Service

本文演示如何為邏輯應用設置這兩種類型的託管標識。 如需詳細資訊，請參閱下列主題：

* [支援託管標識的觸發器和操作](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [出站呼叫上受支援的身份驗證類型](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [邏輯應用的託管標識限制](../logic-apps/logic-apps-limits-and-config.md#managed-identity)
* [支援具有託管標識的 Azure AD 身份驗證的 Azure 服務](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有訂用帳戶，請[註冊一個免費的 Azure 帳戶](https://azure.microsoft.com/free/)。 託管標識和目標 Azure 資源（需要訪問）都必須使用相同的 Azure 訂閱。

* 要授予對 Azure 資源的託管標識存取權限，需要向該標識的目標資源添加角色。 要添加角色，需要[Azure AD 管理員許可權](../active-directory/users-groups-roles/directory-assign-admin-roles.md)，這些許可權可以將角色指派給相應 Azure AD 租戶中的標識。

* 要訪問的目標 Azure 資源。 在此資源上，您將為託管標識添加一個角色，這有助於邏輯應用對目標資源的存取權限進行身份驗證。

* 要使用[支援託管標識的觸發器或操作](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)的邏輯應用

## <a name="enable-managed-identity"></a>啟用受控識別

要設置要使用的託管標識，請按照該標識的連結操作：

* [系統指派的身分識別](#system-assigned)
* [使用者指派的身分識別](#user-assigned)

<a name="system-assigned"></a>

### <a name="enable-system-assigned-identity"></a>啟用系統分配的標識

與使用者分配的身份不同，您不必手動創建系統分配的標識。 要為邏輯應用設置系統分配的標識，可以使用以下選項：

* [Azure 門戶](#azure-portal-system-logic-app)
* [Azure 資源管理員範本](#template-system-logic-app)

<a name="azure-portal-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-portal"></a>在 Azure 門戶中啟用系統分配的標識

1. 在 [Azure 入口網站](https://portal.azure.com)的邏輯應用程式設計工具中，開啟邏輯應用程式。

1. 在邏輯應用程式功能表的 [設定]**** 下，選取 [身分識別]****。 選擇 > **在** > **保存**時**分配的系統**。 當 Azure 提示您確認時，請選擇"**是**"。

   ![啟用系統分配的標識](./media/create-managed-service-identity/enable-system-assigned-identity.png)

   > [!NOTE]
   > 如果收到一個錯誤，即只能具有單個託管標識，則邏輯應用已與使用者分配的標識關聯。 在添加系統分配的標識之前，必須首先從邏輯應用*中刪除*使用者分配的標識。

   邏輯應用現在可以使用系統分配的標識，該標識在 Azure 活動目錄中註冊，並由物件識別碼 表示。

   ![系統分配標識的物件識別碼](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | 屬性 | 值 | 描述 |
   |----------|-------|-------------|
   | **物件識別碼** | <*標識資源識別碼*> | 表示 Azure AD 租戶中邏輯應用的系統分配標識的全域唯一識別碼 （GUID） |
   ||||

1. 現在，請按照本主題稍後將[授予該標識對資源存取權限的步驟](#access-other-resources)進行操作。

<a name="template-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>在 Azure 資源管理器範本中啟用系統分配的標識

要自動創建和部署 Azure 資源（如邏輯應用），可以使用[Azure 資源管理器範本](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)。 要在範本中為邏輯應用啟用系統分配的託管標識，請`identity`將物件和`type`子屬性添加到範本中的邏輯應用的資源定義中，例如：

```json
{
   "apiVersion": "2016-06-01",
   "type": "Microsoft.logic/workflows",
   "name": "[variables('logicappName')]",
   "location": "[resourceGroup().location]",
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {
      "definition": {
         "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
         "actions": {},
         "parameters": {},
         "triggers": {},
         "contentVersion": "1.0.0.0",
         "outputs": {}
   },
   "parameters": {},
   "dependsOn": []
}
```

當 Azure 創建邏輯應用資源定義時`identity`，物件將獲取以下其他屬性：

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Property (JSON) | 值 | 描述 |
|-----------------|-------|-------------|
| `principalId` | <*本金 ID*> | 表示 Azure AD 租戶中邏輯應用的託管標識的服務主體物件的全域唯一識別碼 （GUID）。 此 GUID 有時顯示為"物件識別碼"`objectID`或 。 |
| `tenantId` | <*Azure-AD-租戶-ID*> | 表示邏輯應用現在是其成員的 Azure AD 租戶的全域唯一識別碼 （GUID）。 在 Azure AD 租用戶中，服務主體會有與邏輯應用程式執行個體相同的名稱。 |
||||

<a name="user-assigned"></a>

### <a name="enable-user-assigned-identity"></a>啟用使用者分配的標識

要為邏輯應用設置使用者分配的託管標識，必須首先將該標識創建為單獨的獨立 Azure 資源。 以下是您可以使用的選項：

* [Azure 門戶](#azure-portal-user-identity)
* [Azure 資源管理員範本](#template-user-identity)
* Azure PowerShell
  * [創建使用者分配的標識](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
  * [新增角色指派](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* Azure CLI
  * [創建使用者分配的標識](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
  * [新增角色指派](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)
* Azure REST API
  * [創建使用者分配的標識](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)
  * [新增角色指派](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-user-identity"></a>

#### <a name="create-user-assigned-identity-in-the-azure-portal"></a>在 Azure 門戶中創建使用者分配的標識

1. 在[Azure 門戶](https://portal.azure.com)中，在任何頁面上的搜索框中輸入`managed identities`，然後選擇**託管標識**。

   ![查找並選擇"託管標識"](./media/create-managed-service-identity/find-select-managed-identities.png)

1. 在**託管標識**下，選擇 **"添加**"。

   ![添加新託管標識](./media/create-managed-service-identity/add-user-assigned-identity.png)

1. 提供有關託管標識的資訊，然後選擇 **"創建**"，例如：

   ![創建使用者分配的託管標識](./media/create-managed-service-identity/create-user-assigned-identity.png)

   | 屬性 | 必要 | 值 | 描述 |
   |----------|----------|-------|-------------|
   | **資源名稱** | 是 | <*使用者分配標識名稱*> | 要提供使用者分配的標識的名稱。 此示例使用"Fabrikam-使用者分配標識"。 |
   | **訂閱** | 是 | <*Azure 訂閱名稱*> | 要使用的 Azure 訂用帳戶的名稱 |
   | **資源組** | 是 | <*Azure 資源組名稱*> | 要使用的資源組的名稱。 創建新組，或選擇現有組。 本示例創建一個名為"fabrikam 管理身份-RG"的新組。 |
   | **位置** | 是 | <*Azure 區域*> | 要存儲有關資源資訊的 Azure 區域。 此範例使用「美國西部」。 |
   |||||

   現在，您可以將使用者分配的標識添加到邏輯應用。 不能向邏輯應用添加多個使用者分配的標識。

1. 在 Azure 入口網站的邏輯應用程式設計工具中，尋找並開啟邏輯應用程式。

1. 在邏輯應用功能表中，在 **"設置"** 下，選擇 **"標識**"，然後選擇 **"使用者分配** > **添加**"。

   ![添加使用者分配的託管標識](./media/create-managed-service-identity/add-user-assigned-identity-logic-app.png)

1. 在"**添加使用者分配的託管標識**"窗格中，從 **"訂閱"** 清單中選擇 Azure 訂閱（如果尚未選擇）。 從顯示該訂閱*中所有*託管標識的清單中，查找並選擇所需的使用者分配的標識。 要篩選清單，請在 **"使用者分配"託管標識**搜索框中輸入標識或資源組的名稱。 完成後，選擇 **"添加**"。

   ![選擇要使用的使用者分配標識](./media/create-managed-service-identity/select-user-assigned-identity.png)

   > [!NOTE]
   > 如果收到一個錯誤，即只能具有單個託管標識，則邏輯應用已與系統分配的標識相關聯。 在添加使用者分配的標識之前，必須首先禁用邏輯應用中的系統分配標識。

   邏輯應用現在與使用者分配的託管標識相關聯。

   ![與使用者分配的標識關聯](./media/create-managed-service-identity/added-user-assigned-identity.png)

1. 現在，請按照本主題稍後將[授予該標識對資源存取權限的步驟](#access-other-resources)進行操作。

<a name="template-user-identity"></a>

#### <a name="create-user-assigned-identity-in-an-azure-resource-manager-template"></a>在 Azure 資源管理器範本中創建使用者分配的標識

要自動創建和部署 Azure 資源（如邏輯應用），可以使用[Azure 資源管理器範本](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)，支援[使用者分配的標識進行身份驗證](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)。 在範本部分`resources`中，邏輯應用的資源定義需要以下項：

* `type`屬性設置為 的物件`identity``UserAssigned`

* 指定標識`userAssignedIdentities`的資源識別碼 的子物件，該 ID 是具有 和`principalId``clientId`屬性的另一個子物件

此示例顯示 HTTP PUT 請求的邏輯應用資源定義，並包含非參數化`identity`物件。 對 PUT 請求和後續 GET 操作的回應`identity`也具有以下物件：

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicappName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user-assigned-identity-name>": {
                  "principalId": "<principal-ID>",
                  "clientId": "<client-ID>"
               }
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": []
      },
   ],
   "outputs": {}
}
```

| Property (JSON) | 值 | 描述 |
|-----------------|-------|-------------|
| `principalId` | <*本金 ID*> | Azure AD 租戶中使用者分配的託管標識的全域唯一識別碼 （GUID） |
| `clientId` | <*用戶端 ID*> | 邏輯應用的新標識的全球唯一識別碼 （GUID），用於運行時的調用 |
||||

如果範本還包括託管標識的資源定義，則可以對`identity`物件進行參數化。 此示例顯示子`userAssignedIdentities`物件如何引用您在範本`userAssignedIdentity``variables`部分中定義的變數。 此變數引用使用者分配的標識的資源識別碼。

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "Template_LogicAppName": {
         "type": "string"
      },
      "Template_UserAssignedIdentityName": {
         "type": "securestring"
      }
   },
   "variables": {
      "logicAppName": "[parameters(`Template_LogicAppName')]",
      "userAssignedIdentityName": "[parameters('Template_UserAssignedIdentityName')]"
   },
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicAppName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": [
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]"
         ]
      },
      {
         "apiVersion": "2018-11-30",
         "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
         "name": "[parameters('Template_UserAssignedIdentityName')]",
         "location": "[resourceGroup().location]",
         "properties": {
            "tenantId": "<tenant-ID>",
            "principalId": "<principal-ID>",
            "clientId": "<client-ID>"
         }
      }
  ]
}
```

| Property (JSON) | 值 | 描述 |
|-----------------|-------|-------------|
| `tenantId` | <*Azure-AD-租戶-ID*> | 表示 Azure AD 租戶的全域唯一識別碼 （GUID），使用者分配的標識現在是其成員。 在 Azure AD 租戶中，服務主體的名稱與使用者分配的標識名稱相同。 |
| `principalId` | <*本金 ID*> | Azure AD 租戶中使用者分配的託管標識的全域唯一識別碼 （GUID） |
| `clientId` | <*用戶端 ID*> | 邏輯應用的新標識的全球唯一識別碼 （GUID），用於運行時的調用 |
||||

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>授予對資源的標識存取權限

在可以使用邏輯應用的託管標識進行身份驗證之前，請在計畫使用該標識的 Azure 資源上設置該標識的存取權限。 要完成此任務，請為目標 Azure 資源上的該標識分配適當的角色。 以下是您可以使用的選項：

* [Azure 門戶](#azure-portal-assign-access)
* [Azure 資源管理器範本](../role-based-access-control/role-assignments-template.md)
* Azure 電源外殼[（New-AzRole 分配](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment)） - 有關詳細資訊，請參閱[使用 Azure RBAC 和 Azure PowerShell 添加角色指派](../role-based-access-control/role-assignments-powershell.md)。
* Azure CLI（az[角色指派創建](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create)） - 有關詳細資訊，請參閱[使用 Azure RBAC 和 Azure CLI 添加角色指派](../role-based-access-control/role-assignments-cli.md)。
* [Azure REST API](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-assign-access"></a>

### <a name="assign-access-in-the-azure-portal"></a>在 Azure 門戶中分配存取權限

1. 在[Azure 門戶](https://portal.azure.com)中，轉到 Azure 資源，希望託管標識具有存取權限。

1. 從資源的功能表中選擇 **"存取控制 （IAM）** > **角色指派"，** 您可以在其中查看該資源的當前角色指派。 在工具列上，選擇 **"添加** > **角色指派**"。

   ![選擇"添加">"添加角色指派"](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > 如果禁用"**添加角色指派"** 選項，則很可能沒有許可權。 有關允許您管理資源角色的許可權的詳細資訊，請參閱[Azure 活動目錄中的管理員角色許可權](../active-directory/users-groups-roles/directory-assign-admin-roles.md)。

1. 在 **"添加角色指派**"下，選擇一個角色，該**角色**使您的身份能夠對目標資源進行必要的存取權限。

   在本主題示例中，標識需要一個角色[，該角色可以訪問 Azure 存儲容器中的 blob。](../storage/common/storage-auth-aad.md#assign-rbac-roles-for-access-rights)

   ![選擇"存儲 Blob 資料參與者"角色](./media/create-managed-service-identity/select-role-for-identity.png)

1. 按照以下步驟進行託管標識：

   * **系統指派的身分識別**

     1. 在"**分配對訪問的訪問**"框中，選擇**邏輯應用**。 顯示 **"訂閱"** 屬性時，選擇與您的標識關聯的 Azure 訂閱。

        ![選擇系統分配標識的訪問](./media/create-managed-service-identity/assign-access-system.png)

     1. 在 **"選擇"** 框中，從清單中選擇邏輯應用。 如果清單太長，請使用 **"選擇"** 框篩選清單。

        ![為系統分配的標識選擇邏輯應用](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

   * **使用者指派的身分識別**

     1. 在"**分配對存取權限"** 框中，選擇 **"使用者分配"託管標識**。 顯示 **"訂閱"** 屬性時，選擇與您的標識關聯的 Azure 訂閱。

        ![選擇使用者分配標識的訪問](./media/create-managed-service-identity/assign-access-user.png)

     1. 在 **"選擇"** 框中，從清單中選擇您的身份。 如果清單太長，請使用 **"選擇"** 框篩選清單。

        ![選擇使用者分配的標識](./media/create-managed-service-identity/add-permissions-select-user-assigned-identity.png)

1. 完成時，選取 [儲存]****。

   目標資源的角色指派清單現在顯示所選的託管標識和角色。 此示例演示如何將系統分配的標識用於一個邏輯應用，以及如何將使用者分配的標識用於一組其他邏輯應用。

   ![添加託管標識和角色以定位資源](./media/create-managed-service-identity/added-roles-for-identities.png)

   有關詳細資訊，請使用[Azure 門戶將託管標識存取權限分配給資源](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)。

1. 現在，按照以下步驟對支援託管標識的觸發器或操作中的[標識進行身份驗證](#authenticate-access-with-identity)。

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>使用託管標識驗證存取權限

[為邏輯應用啟用託管標識](#azure-portal-system-logic-app)並[授予對目標資源或實體的標識存取權限](#access-other-resources)後，可以在[支援託管標識的觸發器和操作](logic-apps-securing-a-logic-app.md#managed-identity-authentication)中使用該標識。

> [!IMPORTANT]
> 如果具有要使用系統分配的標識的 Azure 函數，則首先[為 Azure 函數啟用身份驗證](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions)。

這些步驟演示如何通過 Azure 門戶將託管標識與觸發器或操作一起使用。 要在觸發器或操作的基礎 JSON 定義中指定託管標識，請參閱[託管標識身份驗證](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication)。

1. 在[Azure 門戶](https://portal.azure.com)中，在邏輯應用設計器中打開邏輯應用。

1. 如果尚未這樣做，則添加[支援託管標識的觸發器或操作](logic-apps-securing-a-logic-app.md#managed-identity-authentication)。

   例如，HTTP 觸發器或操作可以使用為邏輯應用啟用的系統分配的標識。 通常，HTTP 觸發器或操作使用這些屬性來指定要訪問的資源或實體：

   | 屬性 | 必要 | 描述 |
   |----------|----------|-------------|
   | **方法** | 是 | 要運行的操作使用的 HTTP 方法 |
   | **URI** | 是 | 用於訪問目標 Azure 資源或實體的終結點 URL。 URI 語法通常包括 Azure[資源或服務的資源識別碼。](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) |
   | **標頭** | 否 | 需要或想要包含在傳出請求中的任何標頭值，如內容類型 |
   | **查詢** | 否 | 需要或想要包含在請求中的任何查詢參數，例如特定操作的參數或要運行的操作的 API 版本 |
   | [驗證]**** | 是 | 用於驗證對目標資源或實體的存取權限的身份驗證類型 |
   ||||

   作為特定示例，假設您希望在 Azure 存儲帳戶中的 Blob 上運行[快照 Blob 操作](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob)，而以前在 Azure 存儲帳戶中為標識設置存取權限。 但是[，Azure Blob 存儲連接器](https://docs.microsoft.com/connectors/azureblob/)當前不提供此操作。 相反，您可以使用[HTTP 操作](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)或其他 Blob 服務 REST [API 操作](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs)來運行此操作。

   > [!IMPORTANT]
   > 要使用 HTTP 要求和託管標識訪問防火牆後面的 Azure 存儲帳戶，請確保也設置存儲帳戶[，但允許受信任的 Microsoft 服務進行訪問的除外](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service)。

   要運行[快照 Blob 操作](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob)，HTTP 操作指定以下屬性：

   | 屬性 | 必要 | 範例值 | 描述 |
   |----------|----------|---------------|-------------|
   | **方法** | 是 | `PUT`| 快照 Blob 操作使用的 HTTP 方法 |
   | **URI** | 是 | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | Azure 全域（公共）環境中 Azure Blob 存儲檔的資源識別碼，該環境使用此語法 |
   | **標頭** | 是，對於 Azure 存儲 | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` | Azure `x-ms-blob-type` `x-ms-version`存儲操作所需的 和 標頭值。 <p><p>**重要提示**：在 Azure 存儲的傳出 HTTP 觸發器和操作請求`x-ms-version`中，標頭需要要運行的操作的屬性和 API 版本。 <p>如需詳細資訊，請參閱下列主題： <p><p>- [請求標頭 - 快照 Blob](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob#request) <br>- [Azure 存儲服務的版本控制](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
   | **查詢** | 是，對於此操作 | `comp` = `snapshot` | 快照 Blob 操作的查詢參數名稱和值。 |
   |||||

   下面是顯示所有這些屬性值的示例 HTTP 操作：

   ![添加 HTTP 操作以訪問 Azure 資源](./media/create-managed-service-identity/http-action-example.png)

1. 現在，將**身份驗證**屬性添加到 HTTP 操作中。 從 **"添加新參數**"清單中，選擇 **"身份驗證**"。

   ![將"身份驗證"屬性添加到 HTTP 操作](./media/create-managed-service-identity/add-authentication-property.png)

   > [!NOTE]
   > 並非所有觸發器和操作都支援添加身份驗證類型。 有關詳細資訊，請參閱[將身份驗證添加到出站調用](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)。

1. 從**身份驗證類型**清單中，選擇**託管標識**。

   ![對於"身份驗證"，請選擇"託管標識"](./media/create-managed-service-identity/select-managed-identity.png)

1. 從託管識別欄位表中，根據方案從可用選項中進行選擇。

   * 如果設置系統分配的標識，請選擇 **"系統分配託管標識**"（如果尚未選擇）。

     ![選擇"系統分配的託管標識"](./media/create-managed-service-identity/select-system-assigned-identity-for-action.png)

   * 如果設置了使用者分配的標識，請選擇該標識（如果尚未選擇）。

     ![選擇使用者分配的標識](./media/create-managed-service-identity/select-user-assigned-identity-for-action.png)

   此示例繼續與**系統分配的託管標識**。

1. 在某些觸發器和操作上，還會顯示 **"訪問群體"** 屬性，以便您設置目標資源識別碼。 將 **"訪問群體"** 屬性設置為[目標資源或服務的資源識別碼。](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) 否則，預設情況下，**訪問群體**屬性使用`https://management.azure.com/`資源識別碼，這是 Azure 資源管理器的資源識別碼。

   > [!IMPORTANT]
   > 確保目標資源識別碼 與 Azure 活動目錄 （AD） 期望的值*完全符合*，包括任何必需的尾隨斜杠。 例如，所有 Azure Blob 存儲帳戶的資源識別碼 都需要尾隨斜杠。 但是，特定存儲帳戶的資源識別碼 不需要尾隨斜杠。 檢查[支援 Azure AD 的 Azure 服務的資源標識](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。

   本示例將 **"訪問群體"** 屬性集，`https://storage.azure.com/`以便用於身份驗證的訪問權杖對所有存儲帳戶都有效。 但是，您還可以為特定存儲帳戶指定根服務`https://fabrikamstorageaccount.blob.core.windows.net`URL。

   ![將"訪問"屬性設置為目標資源識別碼](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   有關使用 Azure 存儲授權使用 Azure AD 進行訪問的詳細資訊，請參閱以下主題：

   * [使用 Azure 活動目錄授權對 Azure Blob 和佇列的訪問](../storage/common/storage-auth-aad.md)
   * [使用 Azure 活動目錄授權訪問 Azure 存儲](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. 繼續以所需的方式構建邏輯應用。

<a name="remove-identity"></a>

## <a name="disable-managed-identity"></a>禁用託管標識

要停止對邏輯應用使用託管標識，您有以下選項：

* [Azure 門戶](#azure-portal-disable)
* [Azure 資源管理員範本](#template-disable)
* Azure PowerShell
  * [刪除角色指派](../role-based-access-control/role-assignments-powershell.md)
  * [刪除使用者分配的標識](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* Azure CLI
  * [刪除角色指派](../role-based-access-control/role-assignments-cli.md)
  * [刪除使用者分配的標識](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
* Azure REST API
  * [刪除角色指派](../role-based-access-control/role-assignments-rest.md)
  * [刪除使用者分配的標識](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)

如果刪除邏輯應用，Azure 將自動從 Azure AD 中刪除託管標識。

<a name="azure-portal-disable"></a>

### <a name="disable-managed-identity-in-the-azure-portal"></a>禁用 Azure 門戶中的託管標識

在 Azure 門戶中，首先刪除標識對[目標資源](#disable-identity-target-resource)的訪問。 接下來，關閉系統分配的標識，或從[邏輯應用](#disable-identity-logic-app)中刪除使用者分配的標識。

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>從資源中刪除身份訪問

1. 在[Azure 門戶](https://portal.azure.com)中，轉到要刪除託管標識存取權限的目標 Azure 資源。

1. 從目標資源的功能表中，選擇**存取控制 （IAM）。** 在工具列下，選擇**角色指派**。

1. 在角色清單中，選擇要刪除的託管標識。 在工具列上，選擇 **"刪除**"。

   > [!TIP]
   > 如果禁用 **"刪除**"選項，則很可能沒有許可權。 有關允許您管理資源角色的許可權的詳細資訊，請參閱[Azure 活動目錄中的管理員角色許可權](../active-directory/users-groups-roles/directory-assign-admin-roles.md)。

託管標識現已被刪除，不再有權訪問目標資源。

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app"></a>禁用邏輯應用上的託管標識

1. 在 [Azure 入口網站](https://portal.azure.com)的邏輯應用程式設計工具中，開啟邏輯應用程式。

1. 在邏輯應用功能表中，在 **"設置"** 下，選擇 **"標識**"，然後按照標識的步驟操作：

   * 選擇 > **在** > **保存**時**分配的系統**。 當 Azure 提示您確認時，請選擇"**是**"。

     ![禁用系統分配的標識](./media/create-managed-service-identity/disable-system-assigned-identity.png)

   * 選擇**使用者分配**和託管標識，然後選擇 **"刪除**"。 當 Azure 提示您確認時，請選擇"**是**"。

     ![刪除使用者分配的標識](./media/create-managed-service-identity/remove-user-assigned-identity.png)

託管標識現在在邏輯應用中被禁用。

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>禁用 Azure 資源管理器範本中的託管標識

如果使用 Azure 資源管理器範本創建了邏輯應用的託管標識，則將`identity`物件的`type`子屬性設置為`None`。 對於系統託管標識，此操作還會從 Azure AD 中刪除主體 ID。

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>後續步驟

* [Azure 邏輯應用中的安全訪問和資料](../logic-apps/logic-apps-securing-a-logic-app.md)