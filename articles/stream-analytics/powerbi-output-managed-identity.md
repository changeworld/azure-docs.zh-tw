---
title: 使用受控識別來驗證您的 Azure 串流分析作業，以 Power BI 輸出
description: 本文說明如何使用受控識別來驗證您的 Azure 串流分析作業，以 Power BI 輸出。
author: cedarbaum
ms.author: sacedarb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/10/2020
ms.openlocfilehash: 31a5195038ef25acadc08e2acbedf8471b25833c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81261409"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-power-bi"></a>使用受控識別來驗證您的 Azure 串流分析作業，以 Power BI

輸出至 Power BI 的[受控識別驗證](../active-directory/managed-identities-azure-resources/overview.md)可讓串流分析作業直接存取您 Power BI 帳戶內的工作區。 這項功能可讓串流分析作業的部署完全自動化，因為使用者不再需要透過 Azure 入口網站以互動方式登入 Power BI。 此外，現在更能支援寫入 Power BI 的長時間執行作業，因為您不需要定期重新授權作業。

本文說明如何透過 Azure 入口網站以及透過 Azure Resource Manager 部署，為串流分析作業的 Power BI 輸出啟用受控識別。

## <a name="prerequisites"></a>先決條件

使用此功能需要下列各項：

- 具有[Pro 授權](https://docs.microsoft.com/power-bi/service-admin-purchasing-power-bi-pro)的 Power BI 帳戶。

- Power BI 帳戶內已升級的工作區。 如需詳細資訊，請參閱這項功能[Power BI 的公告](https://powerbi.microsoft.com/blog/announcing-new-workspace-experience-general-availability-ga/)。

## <a name="create-a-stream-analytics-job-using-the-azure-portal"></a>使用 Azure 入口網站建立串流分析作業

1. 建立新的串流分析作業，或在 Azure 入口網站中開啟現有的作業。 從位於畫面左側的功能表列中，選取位於 [**設定**] 底下的 [**受控識別**]。 確定已選取 [使用系統指派的受控識別]，然後選取畫面底部的 [**儲存**] 按鈕。

   ![設定串流分析受控識別](./media/common/stream-analytics-enable-managed-identity.png)

2. 設定輸出之前，請依照本文的[授與串流分析作業存取 Power BI 權](#give-the-stream-analytics-job-access-to-your-power-bi-workspace)一節中的指示，為串流分析作業提供 Power BI 工作區的存取權。

3. 流覽至串流分析作業的 [**輸出**] 區段，選取 [ **+ 新增**]，然後選擇 [ **Power BI**]。 然後，選取 [**授權**] 按鈕，並使用您的 Power BI 帳戶登入。

   ![使用 Power BI 帳戶進行授權](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-authorize-powerbi.png)

4. 授權之後，下拉式清單就會填入您有權存取的所有工作區。 選取您在上一個步驟中授權的工作區。 然後選取 [**受控識別**] 做為 [驗證模式]。 最後，選取 [**儲存**] 按鈕。

   ![使用受控識別設定 Power BI 輸出](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-configure-powerbi-with-managed-id.png)

## <a name="azure-resource-manager-deployment"></a>Azure Resource Manager 部署

Azure Resource Manager 可讓您將串流分析作業的部署作業完全自動化。 您可以使用 Azure PowerShell 或[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)來部署 Resource Manager 範本。 下列範例使用 Azure CLI。


1. 您可以在 Resource Manager 範本的資源區段中包含下列屬性，以建立具有受控識別的**mslearn-streamanalytics/streamingjobs**資源：

    ```json
    "identity": {
        "type": "SystemAssigned",
    }
    ```

   這個屬性會告訴 Azure Resource Manager 建立和管理串流分析作業的身分識別。 以下範例 Resource Manager 範本，其會部署已啟用受控識別的串流分析作業，以及使用受控識別的 Power BI 輸出接收：

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "pbi_managed_id",
                "location": "[resourceGroup().location]",
                "type": "Microsoft.StreamAnalytics/StreamingJobs",
                "identity": {
                    "type": "systemAssigned"
                },
                "properties": {
                    "sku": {
                        "name": "standard"
                    },
                    "outputs":[
                        {
                            "name":"output",
                            "properties":{
                                "datasource":{
                                    "type":"PowerBI",
                                    "properties":{
                                        "dataset": "dataset_name",
                                        "table": "table_name",
                                        "groupId": "01234567-89ab-cdef-0123-456789abcdef",
                                        "authenticationMode": "Msi"
                                    }
                                }
                            }
                        }
                    ]
                }
            }
        ]
    }
    ```

    使用下列 Azure CLI 命令，將上述作業部署到資源群組**ExampleGroup** ：

    ```azurecli
    az group deployment create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. 建立作業之後，請使用 Azure Resource Manager 來取出作業的完整定義。

    ```azurecli
    az resource show --ids /subscriptions/<subsription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/StreamingJobs/<resource-name>
    ```

    上述命令會傳回如下所示的回應：

    ```json
    {
        "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/streamingjobs/<resource-name>",
        "identity": {
            "principalId": "<principal-id>",
            "tenantId": "<tenant-id>",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "<resource-name>",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "<job-id>",
            "jobState": "Created",
            "jobStorageAccount": null,
            "jobType": "Cloud",
            "outputErrorPolicy": "Stop",
            "package": null,
            "provisioningState": "Succeeded",
            "sku": {
                "name": "Standard"
            }
        },
        "resourceGroup": "<resource-group>",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

    如果您打算使用 Power BI 的 REST API，將串流分析作業新增至 Power BI 工作區，請記下傳回的 "principalId"。

3. 現在已建立作業，請繼續閱讀本文的將[串流分析作業存取權授與您的 Power BI 工作區](#give-the-stream-analytics-job-access-to-your-power-bi-workspace)一節。


## <a name="give-the-stream-analytics-job-access-to-your-power-bi-workspace"></a>提供 Power BI 工作區的串流分析作業存取權

現在已建立串流分析作業，可以取得 Power BI 工作區的存取權。

### <a name="use-the-power-bi-ui"></a>使用 Power BI UI

   > [!Note]
   > 若要使用 UI 將串流分析作業新增至您的 Power BI 工作區，您也必須在 Power BI 系統管理員入口網站中的**開發人員設定**中啟用服務主體存取。 如需詳細資訊，請參閱[開始使用服務主體](https://docs.microsoft.com/power-bi/developer/embed-service-principal)。

1. 流覽至工作區的 [存取設定]。 如需詳細資訊，請參閱這篇文章：[授與您工作區的存取權](https://docs.microsoft.com/power-bi/service-create-the-new-workspaces#give-access-to-your-workspace)。

2. 在文字方塊中輸入串流分析作業的名稱，然後選取 [**參與者**] 做為存取層級。

3. 選取 [**新增**]，然後關閉窗格。

   ![將串流分析作業新增至 Power BI 工作區](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-add-job-to-powerbi-workspace.png)

### <a name="use-the-power-bi-powershell-cmdlets"></a>使用 Power BI PowerShell Cmdlet

1. 安裝 Power BI `MicrosoftPowerBIMgmt`的 PowerShell Cmdlet。

   > [!Important]
   > 請確定您使用的是1.0.821 或更新版本的 Cmdlet。

```powershell
Install-Module -Name MicrosoftPowerBIMgmt
```

2. 登入 Power BI。

```powershell
Login-PowerBI
```

3. 將您的串流分析作業新增為工作區的參與者。

```powershell
Add-PowerBIWorkspaceUser -WorkspaceId <group-id> -PrincipalId <principal-id> -PrincipalType App -AccessRight Contributor
```

### <a name="use-the-power-bi-rest-api"></a>使用 Power BI REST API

您也可以直接使用「新增群組使用者」 REST API，將串流分析作業新增為工作區的參與者。 您可以在這裡找到此 API 的完整檔：[群組-新增群組使用者](https://docs.microsoft.com/rest/api/power-bi/groups/addgroupuser)。

**範例要求**
```http
POST https://api.powerbi.com/v1.0/myorg/groups/{groupId}/users
```
要求本文
```json
{
    "groupUserAccessRight": "Contributor",
    "identifier": "<principal-id>",
    "principalType": "App"
}
```

## <a name="limitations"></a>限制
以下是這項功能的限制：

- 不支援傳統 Power BI 工作區。

- 沒有 Azure Active Directory 的 Azure 帳戶。

- 不支援多租使用者存取。 針對指定串流分析作業所建立的服務主體必須位於建立作業所在的相同 Azure Active Directory 租使用者中，且不能與位於不同 Azure Active Directory 租使用者中的資源搭配使用。

- 不支援[使用者指派](../active-directory/managed-identities-azure-resources/overview.md)的身分識別。 這表示您無法輸入自己的服務主體，以供其串流分析作業使用。 服務主體必須由 Azure 串流分析產生。

## <a name="next-steps"></a>後續步驟

* [Azure 串流分析的 Power BI 儀表板整合](./stream-analytics-power-bi-dashboard.md)
* [了解來自 Azure 串流分析的輸出](./stream-analytics-define-outputs.md)
