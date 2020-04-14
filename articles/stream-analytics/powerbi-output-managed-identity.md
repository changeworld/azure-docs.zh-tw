---
title: 使用託管識別對 Azure 串流分析作業進行身份驗證,以為 BI 輸出提供支援
description: 本文介紹如何使用託管標識對 Azure 流分析作業進行身份驗證以將 BI 輸出提供支援。
author: cedarbaum
ms.author: sacedarb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/10/2020
ms.openlocfilehash: 31a5195038ef25acadc08e2acbedf8471b25833c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261409"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-power-bi"></a>使用託管識別對 Azure 串流分析作業進行身份驗證,以為 BI 提供支援

用於輸出到 Power BI 的[託管識別身分驗證](../active-directory/managed-identities-azure-resources/overview.md)使流分析作業直接存取 Power BI 帳戶中的工作區。 此功能允許完全自動化流分析作業的部署,因為使用者不再需要通過 Azure 門戶以交互方式登錄到 Power BI。 此外,現在更好地支援寫入 Power BI 的長運行作業,因為您不需要定期重新授權該作業。

本文演示如何通過 Azure 門戶和 Azure 資源管理器部署為流分析作業的 Power BI 輸出啟用託管標識。

## <a name="prerequisites"></a>Prerequisites

使用此功能需要以下內容:

- 具有[Pro 許可證](https://docs.microsoft.com/power-bi/service-admin-purchasing-power-bi-pro)的 Power BI 帳戶。

- Power BI 帳戶中的升級工作區。 有關詳細資訊,請參閱 Power BI 的[此功能公告](https://powerbi.microsoft.com/blog/announcing-new-workspace-experience-general-availability-ga/)。

## <a name="create-a-stream-analytics-job-using-the-azure-portal"></a>使用 Azure 門戶建立流分析作業

1. 創建新的流分析作業或在 Azure 門戶中打開現有作業。 從位於螢幕左方的選單列中,選擇位於 **「設定**」下的**託管識別**。 確保選擇"使用系統分配的託管標識",然後選擇螢幕底部的 **「儲存**」按鈕。

   ![設定流分析託管識別](./media/common/stream-analytics-enable-managed-identity.png)

2. 在配置輸出之前,請按照本文的[Power BI 工作區的「授予流分析作業存取許可權](#give-the-stream-analytics-job-access-to-your-power-bi-workspace)」部分的說明,為流分析作業授予對 Power BI 工作區的訪問許可權。

3. 導覽到流分析作業的 **「輸出」** 部分,選擇 **'添加**",然後選擇 **「Power BI」。。** 然後,選擇 **「授權」** 按鈕,然後使用 Power BI 帳戶登入。

   ![使用 Power BI 帳號授權](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-authorize-powerbi.png)

4. 獲得授權後,將填充下拉清單,並填充您有權訪問的所有工作區。 選擇您在上一步中授權的工作區。 然後選擇**託管標識**作為"身份驗證模式」。 最後,選擇「**保存**」 按鈕。

   ![使用託管識別配置電源 BI 輸出](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-configure-powerbi-with-managed-id.png)

## <a name="azure-resource-manager-deployment"></a>Azure Resource Manager 部署

Azure 資源管理員允許您完全自動部署流分析作業。 可以使用 Azure PowerShell 或[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)部署資源管理器範本。 以下示例使用 Azure CLI。


1. 您可以透過資源管理員樣本的資源部分中包括以下屬性來建立具有託管識別的**Microsoft.StreamAnalytics/流式作業**資源:

    ```json
    "identity": {
        "type": "SystemAssigned",
    }
    ```

   此屬性告訴 Azure 資源管理器為流分析作業創建和管理標識。 下面是一個範例資源管理員樣本,該範本部署了啟用託管標識的流分析作業和使用託管標識的 Power BI 輸出接收器:

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

    使用以下 Azure CLI 指令將上述工作部署到資源群組**範例群組 :**

    ```azurecli
    az group deployment create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. 創建作業後,使用 Azure 資源管理器檢索作業的完整定義。

    ```azurecli
    az resource show --ids /subscriptions/<subsription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/StreamingJobs/<resource-name>
    ```

    上述命令將回回如下回應:

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

    如果您計劃使用 Power BI 的 REST API 將流分析作業添加到 Power BI 工作區,請記下返回的「主體 Id」。。

3. 創建作業後,請繼續為[流分析作業提供對](#give-the-stream-analytics-job-access-to-your-power-bi-workspace)本文 Power BI 工作區部分的訪問許可權。


## <a name="give-the-stream-analytics-job-access-to-your-power-bi-workspace"></a>為流分析作業授予對 Power BI 工作區的存取權限

現在,流分析作業已創建,可以授予它對 Power BI 工作區的訪問許可權。

### <a name="use-the-power-bi-ui"></a>使用電源 BI UI

   > [!Note]
   > 要使用 UI 將流分析作業添加到 Power BI 工作區,還必須在 Power BI 管理門戶中的**開發人員設定**中啟用服務主體訪問。 有關詳細資訊[,請參閱使用服務主體入門](https://docs.microsoft.com/power-bi/developer/embed-service-principal)。

1. 導航到工作區的訪問設置。 有關詳細資訊,請參閱本文:[授予對工作區的存取權限](https://docs.microsoft.com/power-bi/service-create-the-new-workspaces#give-access-to-your-workspace)。

2. 在文字框中鍵入流分析作業的名稱,然後選擇 **「參與者**」作為存取層級。

3. 選擇 **「新增**並關閉窗格」。

   ![將流分析工作新增 Power BI 工作區](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-add-job-to-powerbi-workspace.png)

### <a name="use-the-power-bi-powershell-cmdlets"></a>使用電源 BI 電源外殼 cmdlet

1. 安裝電源`MicrosoftPowerBIMgmt`BI 電源外殼 cmdlet。

   > [!Important]
   > 請確保您使用的是 1.0.821 版或更高版本的 cmdlet。

```powershell
Install-Module -Name MicrosoftPowerBIMgmt
```

2. 登錄到電源 BI。

```powershell
Login-PowerBI
```

3. 將流分析作業添加為工作區的"參與者"

```powershell
Add-PowerBIWorkspaceUser -WorkspaceId <group-id> -PrincipalId <principal-id> -PrincipalType App -AccessRight Contributor
```

### <a name="use-the-power-bi-rest-api"></a>使用電源 BI REST API

流分析作業還可以直接使用「添加組使用者」REST API 作為參與者添加到工作區。 此 API 的完整文件可在此處找到:[群組 - 加入群組使用者](https://docs.microsoft.com/rest/api/power-bi/groups/addgroupuser)。

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
以下是此功能的限制:

- 不支援經典 Power BI 工作區。

- 沒有 Azure 活動目錄的 Azure 帳戶。

- 不支援多租戶訪問。 為給定流分析作業創建的服務主體必須駐留在創建作業的同一 Azure 活動目錄租戶中,並且不能與駐留在其他 Azure 活動目錄租戶中的資源一起使用。

- 不支援[使用者分配識別](../active-directory/managed-identities-azure-resources/overview.md)。 這意味著您無法輸入自己的服務主體,以便其流分析作業使用。 服務主體必須由 Azure 流分析生成。

## <a name="next-steps"></a>後續步驟

* [Azure 串流分析的 Power BI 儀表板整合](./stream-analytics-power-bi-dashboard.md)
* [了解來自 Azure 串流分析的輸出](./stream-analytics-define-outputs.md)
