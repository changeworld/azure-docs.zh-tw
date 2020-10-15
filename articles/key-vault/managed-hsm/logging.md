---
title: Azure 受控 HSM 記錄
description: 本教學課程可協助您開始使用受控 HSM 記錄。
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: mbaldwin
ms.openlocfilehash: 22abd38ead1257b49eeae98acfcd74349f563811
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90992102"
---
# <a name="managed-hsm-logging"></a>受控 HSM 記錄 

在建立一或多個受控 HSM 之後，您可能會想要監視 HSM 的存取方式、時間和存取者。 為此，您可以啟用記錄，以在您提供的 Azure 儲存體帳戶中儲存這方面的資訊。 系統會自動為您指定的儲存體帳戶建立名為 **insights-logs-auditevent** 的新容器。 您可以使用這個相同的儲存體帳戶來收集多個受控 HSM 的記錄。

在受控 HSM 作業完成 10 分鐘 (最多) 後，您就可以存取記錄資訊。 但大多不用這麼久。  儲存體帳戶中的記錄由您全權管理：

* 請使用標準的 Azure 存取控制方法限制可存取記錄的人員，藉此來保護記錄。
* 刪除不想繼續保留在儲存體帳戶中的記錄。

本教學課程可協助您開始使用受控 HSM 記錄。 您將會建立儲存體帳戶、啟用記錄及解譯收集到的記錄資訊。  

> [!NOTE]
> 本教學課程不會指示如何建立受控 HSM 或金鑰。 本文提供更新診斷記錄的 Azure CLI 指示。

## <a name="prerequisites"></a>必要條件

若要完成本文中的步驟，您必須具有下列項目︰

* Microsoft Azure 訂用帳戶。 如果您沒有帳戶，您可以註冊[免費試用](https://azure.microsoft.com/pricing/free-trial)。
* Azure CLI 2.12.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。
* 訂用帳戶中的受控 HSM。 請參閱[快速入門：使用 Azure CLI 佈建並啟動受控 HSM](quick-create-cli.md)，以佈建並啟動受控 HSM。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>連接到 Azure 訂用帳戶

設定金鑰記錄的第一個步驟，是將 Azure CLI 指向您要記錄的受控 HSM。

```azurecli-interactive
az login
```

若要進一步了解透過 CLI 的登入選項，請參閱[使用 Azure CLI 進行登入](/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true)

您可能必須指定用來建立受控 HSM 的訂用帳戶。 輸入下列命令以查看您帳戶的訂用帳戶：

## <a name="identify-the-managed-hsm-and-storage-account"></a>識別受控 HSM 和儲存體帳戶

```azurecli-interactive
hsmresource=$(az keyvault show --hsm-name ContosoMHSM --query id -o tsv)
storageresource=$(az storage account show --name ContosoMHSMLogs --query id -o tsv)
```

## <a name="enable-logging"></a>啟用記錄

若要啟用受控 HSM 的記錄，請使用 **az monitor diagnostic-settings create** 命令，以及我們為新的儲存體帳戶和受控 HSM 建立的變數。 我們也會將 **-Enabled** 旗標設定為 **$true**，並將類別設定為 **AuditEvent** (受控 HSM 記錄唯一適用的類別)：

此輸出可確認受控 HSM 的記錄現已啟用，而這會將資訊儲存到儲存體帳戶中。

您可以選擇性地設定記錄的保留原則，以便自動刪除較舊的記錄。 例如，藉由設定 **-RetentionEnabled** 旗標將保留原則設為 **$true** 並將 **-RetentionInDays** 參數設為 **90**，以便自動刪除超過 90 天的舊記錄。

```azurecli-interactive
az monitor diagnostic-settings create --name ContosoMHSM-Diagnostics --resource $hsmresource --logs '[{"category": "AuditEvent","enabled": true}]' --storage-account $storageresource
```

所記錄的內容：

* 所有已驗證的 REST API 要求，包括因為存取權限、系統錯誤或要求錯誤而發生的失敗要求。
* 對受控 HSM 本身執行的作業，包括建立、刪除及更新屬性 (例如標籤)。
* 安全性網域的相關作業，例如初始化和下載、初始化復原、上傳
* 完整 HSM 備份、還原和選擇性還原作業
* 金鑰的作業，包括：
  * 建立、修改或刪除金鑰。
  * 簽署、驗證、加密、解密、包裝和解除包裝金鑰，以及列出金鑰。
  * 金鑰備份、還原、清除
* 產生 401 回應的未經驗證要求。 例如，沒有持有人權杖的要求、格式不正確或已過期的要求，或具有無效權杖的要求。  

## <a name="access-your-logs"></a>存取記錄

受控 HSM 記錄儲存在您提供之儲存體帳戶的 **insights-logs-auditevent** 容器中。 若要查看記錄，您必須下載 Blob。 如需 Azure 儲存體的詳細資訊，請參閱[使用 Azure CLI 建立、下載及列出 Blob](../../storage/blobs/storage-quickstart-blobs-cli.md)。

個別的 Blob 會儲存為文字，並格式化為 JSON。 讓我們看看記錄項目範例。 下列範例說明當建立完整備份的要求傳送至受控 HSM 時的記錄項目。

```json
[
  {
    "TenantId": "766eaf62-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "time": "2020-08-31T19:52:39.763Z",
    "resourceId": "/SUBSCRIPTIONS/A1BA9AAA-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/MANAGEDHSMS/CONTOSOMHSM",
    "operationName": "BackupCreate",
    "operationVersion": "7.0",
    "category": "AuditEvent",
    "resultType": "Success",
    "properties": {
        "PoolType": "M-HSM",
        "sku_Family": "B",
        "sku_Name": "Standard_B1"
    },
    "durationMs": 488,
    "callerIpAddress": "X.X.X.X",
    "identity": "{\"claim\":{\"appid\":\"04b07795-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"http_schemas_microsoft_com_identity\":{\"claims\":{\"objectidentifier\":\"b1c52bf0-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"}},\"http_schemas_xmlsoap_org_ws_2005_05_identity\":{\"claims\":{\"upn\":\"admin@contoso.com\"}}}}",
    "clientInfo": "azsdk-python-core/1.7.0 Python/3.8.2 (Linux-4.19.84-microsoft-standard-x86_64-with-glibc2.29) azsdk-python-azure-keyvault/7.2",
    "correlationId": "8806614c-ebc3-11ea-9e9b-00155db778ad",
    "subnetId": "(unknown)",
    "httpStatusCode": 202,
    "PoolName": "mhsmdemo",
    "requestUri": "https://ContosoMHSM.managedhsm.azure.net/backup",
    "resourceGroup": "ContosoResourceGroup",
    "resourceProvider": "MICROSOFT.KEYVAULT",
    "resource": "ContosoMHSM",
    "resourceType": "managedHSMs"
  }
]
```

下表列出各個欄位的名稱和其描述：

| 欄位名稱 | 描述 |
| --- | --- |
| **TenantId** | 受控 HSM 建立所在之訂用帳戶的 Azure Active Directory 租用戶識別碼 |
| **time** |日期和時間 (UTC)。 |
| **resourceId** |Azure Resource Manager 資源識別碼。 針對受控 HSM 記錄，這一律是受控 HSM 資源識別碼。 |
| **operationName** |下一份表格所述作業的名稱。 |
| **operationVersion** |用戶端所要求的 REST API 版本。 |
| **類別** |結果的類型。 對於受控 HSM 記錄來說，**AuditEvent** 是其唯一可用的值。 |
| **resultType** |REST API 要求的結果。 |
| **properties** |根據作業 (**operationName**) 而有所不同的資訊|
| **resultSignature** |HTTP 狀態。 |
| **resultDescription** |結果的其他描述 (若有提供)。 |
| **durationMs** |服務 REST API 要求時所花費的時間，以毫秒為單位。 此時間不包括網路延遲，因此在用戶端所測量到的時間可能不符合此時間。 |
| **callerIpAddress** |提出要求用戶端的 IP 位址。 |
| **correlationId** |選擇性的 GUID，用戶端可傳遞此 GUID，讓用戶端記錄與服務端記錄相互關聯。 |
| **身分識別** |權杖中的身分識別，會在 REST API 要求中提供。 這通常是「使用者」，一個「服務主體」。 |
| **requestUri** | REST API 要求 URI |
| **clientInfo** | 

## <a name="use-azure-monitor-logs"></a>使用 Azure 監視器記錄

您可以使用 Azure 監視器記錄中的 Key Vault 解決方案來檢閱受控 HSM 的 **AuditEvent** 記錄。 在 Azure 監視器記錄中，您可以使用記錄查詢來分析資料，並取得所需的資訊。 

## <a name="next-steps"></a>下一步

- 了解佈建和使用受控 HSM 的[最佳做法](best-practices.md)
- 深入了解[如何備份和還原受控 HSM](backup-restore.md)
