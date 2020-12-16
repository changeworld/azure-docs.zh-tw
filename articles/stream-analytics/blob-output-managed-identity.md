---
title: 使用受控識別 Azure 串流分析來驗證 blob 輸出
description: 本文說明如何使用受控識別來向 Azure Blob 儲存體輸出驗證 Azure 串流分析作業。
author: cedarbaum
ms.author: sacedarb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: 62651e9ea4e1b02b276e7870163453bfa55c3909
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97587565"
---
# <a name="use-managed-identity-preview-to-authenticate-your-azure-stream-analytics-job-to-azure-blob-storage"></a>使用受控識別 (預覽版) 來驗證您的 Azure 串流分析作業，以 Azure Blob 儲存體

[受控識別驗證](../active-directory/managed-identities-azure-resources/overview.md) (預覽) 若要輸出至 Azure Blob 儲存體，可讓串流分析作業直接存取儲存體帳戶，而不是使用連接字串。 除了改進的安全性之外，此功能也可讓您將資料寫入虛擬網路中的儲存體帳戶， (Azure 中的 VNET) 。

本文說明如何透過 Azure 入口網站和 Azure Resource Manager 部署，為串流分析作業的 Blob 輸出 (s) 啟用受控識別。

## <a name="create-the-stream-analytics-job-using-the-azure-portal"></a>使用 Azure 入口網站建立串流分析作業

1. 建立新的串流分析作業，或在 Azure 入口網站中開啟現有的作業。 從位於畫面左側的功能表列中，選取位於 [**設定**] 下的 [**受控識別**]。 確定已選取 [使用系統指派的受控識別]，然後按一下畫面底部的 [ **儲存** ] 按鈕。

   ![設定串流分析受控識別](./media/common/stream-analytics-enable-managed-identity.png)

2. 在 Azure Blob 儲存體輸出接收的 [輸出屬性] 視窗中，選取 [驗證模式] 下拉式清單，然後選擇 [ **受控識別**]。 如需其他輸出屬性的相關資訊，請參閱 [瞭解 Azure 串流分析的輸出](./stream-analytics-define-outputs.md)。 當您完成後，請按一下 [儲存]。

   ![設定 Azure Blob 儲存體輸出](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-blob-output-blade.png)

3. 現在已建立作業，請參閱本文的「 [授與串流分析作業對您儲存體帳戶的存取權](#give-the-stream-analytics-job-access-to-your-storage-account) 」一節。

## <a name="azure-resource-manager-deployment"></a>Azure Resource Manager 部署

使用 Azure Resource Manager 可讓您將串流分析作業的部署作業完全自動化。 您可以使用 Azure PowerShell 或 [Azure CLI](/cli/azure/)來部署 Resource Manager 範本。 下列範例會使用 Azure CLI。


1. 您可以在 Resource Manager 範本的資源區段中包含下列屬性，以使用受控識別建立 **>mslearn-streamanalytics/streamingjobs** 資源：

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   此屬性會告知 Azure Resource Manager 建立和管理串流分析作業的身分識別。 以下是範例 Resource Manager 範本，此範本會部署已啟用受控識別的串流分析作業，以及使用受控識別的 Blob 輸出接收：

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

    您可以使用下列 Azure CLI 命令，將上述作業部署至資源群組 **ExampleGroup** ：

    ```azurecli
    az group deployment create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. 建立作業之後，您可以使用 Azure Resource Manager 來取出作業的完整定義。

    ```azurecli
    az resource show --ids /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/StreamingJobs/{RESOURCE_NAME}
    ```

    上述命令會傳回如下所示的回應：

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

   請記下作業定義中的 **principalId** ，以識別 Azure Active Directory 內的作業受控識別，並將在下一個步驟中用來授與串流分析作業存取儲存體帳戶的許可權。

3. 現在已建立作業，請參閱本文的「 [授與串流分析作業對您儲存體帳戶的存取權](#give-the-stream-analytics-job-access-to-your-storage-account) 」一節。


## <a name="give-the-stream-analytics-job-access-to-your-storage-account"></a>將串流分析作業存取權授與您的儲存體帳戶

您可以選擇兩種層級的存取權來提供您的串流分析作業：

1. **容器層級存取：** 此選項可讓您將作業存取權授與特定的現有容器。
2. **帳戶層級存取：** 此選項可讓您一般存取儲存體帳戶的作業，包括建立新容器的能力。

除非您需要代表您建立容器的作業，否則您應該選擇 **容器層級的存取權** ，因為此選項會將所需的最低存取層級授與作業。 以下將針對 Azure 入口網站和命令列說明這兩個選項。

### <a name="grant-access-via-the-azure-portal"></a>透過 Azure 入口網站授與存取權

#### <a name="container-level-access"></a>容器層級存取

1. 在您的儲存體帳戶中流覽至容器的設定窗格。

2. 選取左側的 [ **存取控制] (IAM)** 。

3. 在 [新增角色指派] 區段下，按一下 [ **新增**]。

4. 在 [角色指派] 窗格中：

    1. 將 **角色** 設定為「儲存體 Blob 資料參與者」
    2. 確定 [ **將存取權指派** 給] 下拉式清單設定為 [Azure AD 使用者、群組或服務主體]。
    3. 在 [搜尋] 欄位中輸入串流分析作業的名稱。
    4. 選取您的串流分析作業，然後按一下 [ **儲存**]。

   ![授與容器存取權](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-container-access-portal.png)

#### <a name="account-level-access"></a>帳戶層級存取

1. 瀏覽至儲存體帳戶。

2. 選取左側的 [ **存取控制] (IAM)** 。

3. 在 [新增角色指派] 區段下，按一下 [ **新增**]。

4. 在 [角色指派] 窗格中：

    1. 將 **角色** 設定為「儲存體 Blob 資料參與者」
    2. 確定 [ **將存取權指派** 給] 下拉式清單設定為 [Azure AD 使用者、群組或服務主體]。
    3. 在 [搜尋] 欄位中輸入串流分析作業的名稱。
    4. 選取您的串流分析作業，然後按一下 [ **儲存**]。

   ![授與帳戶存取權](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-account-access-portal.png)

### <a name="grant-access-via-the-command-line"></a>透過命令列授與存取權

#### <a name="container-level-access"></a>容器層級存取

若要授與特定容器的存取權，請使用 Azure CLI 執行下列命令：

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
   ```

#### <a name="account-level-access"></a>帳戶層級存取

若要授與整個帳戶的存取權，請使用 Azure CLI 執行下列命令：

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
   ```

## <a name="enable-vnet-access"></a>啟用 VNET 存取

設定儲存體帳戶的 **防火牆和虛擬網路** 時，您可以選擇性地允許來自其他受信任 Microsoft 服務的網路流量。 當串流分析使用受控識別進行驗證時，它會提供要求源自受信任服務的證明。 以下是啟用此 VNET 存取例外狀況的指示。

1.    流覽至儲存體帳戶的 [設定] 窗格中的 [防火牆和虛擬網路] 窗格。
2.    確定已啟用 [允許信任的 Microsoft 服務存取此儲存體帳戶] 選項。
3.    如果您已啟用它，請按一下 [ **儲存**]。

   ![啟用 VNET 存取](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-vnet-exception.png)

## <a name="remove-managed-identity"></a>移除受控識別

只有在作業刪除時，才會刪除為串流分析作業建立的受控識別。 沒有任何方法可刪除受控識別，而不刪除工作。 如果您不想再使用受控識別，您可以變更輸出的驗證方法。 受控識別會持續存在，直到刪除作業為止，如果您決定再次使用受控識別驗證，則會使用此身分識別。

## <a name="limitations"></a>限制
以下是這項功能目前的限制：

1. 傳統 Azure 儲存體帳戶。

2. 沒有 Azure Active Directory 的 Azure 帳戶。

3. 不支援多租使用者存取。 針對指定的串流分析作業所建立的服務主體必須位於建立作業的相同 Azure Active Directory 租使用者中，且不能與位於不同 Azure Active Directory 租使用者中的資源搭配使用。

4. 不支援[使用者指派](../active-directory/managed-identities-azure-resources/overview.md)的身分識別。 這表示使用者無法輸入其本身的服務主體，以供其串流分析作業使用。 服務主體必須由 Azure 串流分析產生。

## <a name="next-steps"></a>後續步驟

* [了解來自 Azure 串流分析的輸出](./stream-analytics-define-outputs.md)
* [Azure 串流分析自訂 Blob 輸出資料分割](./stream-analytics-custom-path-patterns-blob-storage-output.md)
