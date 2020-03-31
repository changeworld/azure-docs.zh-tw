---
title: 使用託管標識 Azure 流分析對 Blob 輸出進行身份驗證
description: 本文介紹如何使用託管標識將 Azure 流分析作業驗證為 Azure Blob 存儲輸出。
author: cedarbaum
ms.author: sacedarb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 13f48a9e0bc3ed8f8c4d5f1b7da4b6c03f54cdf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129966"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-azure-blob-storage-output"></a>使用託管標識將 Azure 流分析作業驗證為 Azure Blob 存儲輸出

用於輸出到 Azure Blob 存儲的[託管標識身份驗證](../active-directory/managed-identities-azure-resources/overview.md)使流分析作業直接存取存儲帳戶，而不是使用連接字串。 除了提高安全性外，此功能還使您能夠將資料寫入 Azure 中的虛擬網路 （VNET） 中的存儲帳戶。

本文演示如何通過 Azure 門戶和 Azure 資源管理器部署為流分析作業的 Blob 輸出啟用託管標識。

## <a name="create-the-stream-analytics-job-using-the-azure-portal"></a>使用 Azure 門戶創建流分析作業

1. 創建新的流分析作業或在 Azure 門戶中打開現有作業。 從位於螢幕左側的功能表列中，選擇位於 **"配置**"下的**託管標識**。 確保選擇"使用系統分配的託管標識"，然後按一下螢幕底部的 **"保存**"按鈕。

   ![配置流分析託管標識](./media/common/stream-analytics-enable-managed-identity.png)

2. 在 Azure Blob 存儲輸出接收器的輸出屬性視窗中，選擇"身份驗證模式下拉"並選擇**託管標識**。 有關其他輸出屬性的資訊，請參閱瞭解[Azure 流分析的輸出](./stream-analytics-define-outputs.md)。 當您完成後，請按一下 [儲存]****。

   ![配置 Azure Blob 存儲輸出](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-blob-output-blade.png)

3. 創建作業後，請參閱本文[的"授予流分析作業"作業對存儲帳戶的存取權限](#give-the-stream-analytics-job-access-to-your-storage-account)。

## <a name="azure-resource-manager-deployment"></a>Azure Resource Manager 部署

使用 Azure 資源管理器可以完全自動部署流分析作業。 可以使用 Azure PowerShell 或[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)部署資源管理器範本。 以下示例使用 Azure CLI。


1. 您可以通過在資源管理器範本的資源部分中包括以下屬性來創建具有託管標識的**Microsoft.StreamAnalytics/流式作業**資源：

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   此屬性告訴 Azure 資源管理器為流分析作業創建和管理標識。 下面是一個示例資源管理器範本，該範本部署了啟用託管標識的流分析作業和使用託管標識的 Blob 輸出接收器：

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "MyStreamingJob",
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
                                "serialization": {
                                    "type": "JSON",
                                    "properties": {
                                        "encoding": "UTF8"
                                    }
                                },
                                "datasource":{
                                    "type":"Microsoft.Storage/Blob",
                                    "properties":{
                                        "storageAccounts": [
                                            { "accountName": "MyStorageAccount" }
                                        ],
                                        "container": "test",
                                        "pathPattern": "segment1/{date}/segment2/{time}",
                                        "dateFormat": "yyyy/MM/dd",
                                        "timeFormat": "HH",
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

    可以使用以下 Azure CLI 命令將上述作業部署到資源組**示例組**：

    ```azurecli
    az group deployment create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. 創建作業後，可以使用 Azure 資源管理器檢索作業的完整定義。

    ```azurecli
    az resource show --ids /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/StreamingJobs/{RESOURCE_NAME}
    ```

    上述命令將返回如下回應：

    ```json
    {
        "id": "/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/streamingjobs/{RESOURCE_NAME}",
        "identity": {
            "principalId": "{PRINCIPAL_ID}",
            "tenantId": "{TENANT_ID}",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "{RESOURCE_NAME}",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "{JOB_ID}",
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
        "resourceGroup": "{RESOURCE_GROUP}",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

   請注意作業定義中的**主體 Id，該定義**標識 Azure 活動目錄中作業的託管標識，並將在下一步中使用，以授予流分析作業對存儲帳戶的存取權限。

3. 創建作業後，請參閱本文[的"授予流分析作業"作業對存儲帳戶的存取權限](#give-the-stream-analytics-job-access-to-your-storage-account)。


## <a name="give-the-stream-analytics-job-access-to-your-storage-account"></a>授予流分析作業對存儲帳戶的存取權限

您可以選擇提供流分析作業的兩個級別：

1. **容器級別訪問：** 此選項允許作業訪問特定現有容器。
2. **帳戶級別訪問：** 此選項允許作業對存儲帳戶的一般存取權限，包括創建新容器的能力。

除非需要作業代表您創建容器，否則應選擇**容器級別訪問**，因為此選項將為作業授予所需的最低存取層級。 下面對 Azure 門戶和命令列說明了這兩個選項。

### <a name="grant-access-via-the-azure-portal"></a>通過 Azure 門戶授予存取權限

#### <a name="container-level-access"></a>容器級訪問

1. 導航到存儲帳戶中的容器配置窗格。

2. 選擇左側的訪問**控制 （IAM）。**

3. 在"添加角色指派"部分下，按一下"**添加**"。

4. 在角色指派窗格中：

    1. 將**角色**設置為"存儲 Blob 資料參與者"
    2. 確保 **"分配對**下拉清單的訪問"設置為"Azure AD 使用者、組或服務主體"。
    3. 在搜索欄位中鍵入流分析作業的名稱。
    4. 選擇您的流分析作業，然後按一下"**保存**"。

   ![授予容器訪問](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-container-access-portal.png)

#### <a name="account-level-access"></a>帳戶級別訪問

1. 瀏覽至儲存體帳戶。

2. 選擇左側的訪問**控制 （IAM）。**

3. 在"添加角色指派"部分下，按一下"**添加**"。

4. 在角色指派窗格中：

    1. 將**角色**設置為"存儲 Blob 資料參與者"
    2. 確保 **"分配對**下拉清單的訪問"設置為"Azure AD 使用者、組或服務主體"。
    3. 在搜索欄位中鍵入流分析作業的名稱。
    4. 選擇您的流分析作業，然後按一下"**保存**"。

   ![授予帳戶存取權限](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-account-access-portal.png)

### <a name="grant-access-via-the-command-line"></a>通過命令列授予存取權限

#### <a name="container-level-access"></a>容器級訪問

要授予對特定容器的存取權限，請使用 Azure CLI 運行以下命令：

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
   ```

#### <a name="account-level-access"></a>帳戶級別訪問

要授予對整個帳戶的存取權限，請使用 Azure CLI 運行以下命令：

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
   ```

## <a name="enable-vnet-access"></a>啟用 VNET 訪問

配置存儲帳戶的**防火牆和虛擬網路**時，可以選擇允許來自其他受信任的 Microsoft 服務的網路流量。 當流分析使用託管標識進行身份驗證時，它會提供請求源自受信任服務的證據。 下面是啟用此 VNET 訪問異常的說明。

1.  導航到存儲帳戶配置窗格中的"防火牆和虛擬網路"窗格。
2.  確保啟用"允許受信任的 Microsoft 服務訪問此存儲帳戶"選項。
3.  如果啟用了它，請按一下"**保存**"。

   ![啟用 VNET 訪問](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-vnet-exception.png)

## <a name="limitations"></a>限制
以下是此功能的當前限制：

1. 經典 Azure 存儲帳戶。

2. 沒有 Azure 活動目錄的 Azure 帳戶。

3. 不支援多租戶訪問。 為給定流分析作業創建的服務主體必須駐留在創建作業的同一 Azure 活動目錄租戶中，並且不能與駐留在其他 Azure 活動目錄租戶中的資源一起使用。

4. 不支援[使用者分配標識](../active-directory/managed-identities-azure-resources/overview.md)。 這意味著使用者無法輸入自己的服務主體，以便其流分析作業使用。 服務主體必須由 Azure 流分析生成。

## <a name="next-steps"></a>後續步驟

* [了解來自 Azure 串流分析的輸出](./stream-analytics-define-outputs.md)
* [Azure 串流分析自訂 Blob 輸出資料分割](./stream-analytics-custom-path-patterns-blob-storage-output.md)
