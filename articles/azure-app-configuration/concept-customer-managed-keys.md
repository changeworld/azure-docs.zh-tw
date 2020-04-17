---
title: 使用客戶管理的金鑰加密設定資料
description: 使用客戶管理的金鑰加密設定資料
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/18/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: ace34cf4a72b871ba6646b279007b8ce21c03e9b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457428"
---
# <a name="use-customer-managed-keys-to-encrypt-your-app-configuration-data"></a>使用客戶管理的金鑰加密應用設定資料
Azure 應用程式設定[在靜態時加密敏感資訊](../security/fundamentals/encryption-atrest.md)。 使用客戶管理的金鑰允許您管理加密密鑰,從而增強數據保護。  使用託管金鑰加密時,應用配置中的所有敏感資訊都使用使用者提供的 Azure 密鑰保管庫密鑰進行加密。  這提供了按需旋轉加密密鑰的能力。  它還提供了通過撤銷應用配置實例對金鑰的訪問來撤消 Azure 應用配置對敏感資訊的存取的能力。

## <a name="overview"></a>概觀 
Azure 應用配置使用 Microsoft 提供的 256 位元 AES 加密密鑰對靜態敏感資訊進行加密。 每個應用配置實例都有自己的加密密鑰,由服務管理,並用於加密敏感資訊。 敏感資訊包括鍵值對中找到的值。  啟用客戶託管金鑰功能後,應用配置將使用分配給應用配置實例的託管標識對 Azure 活動目錄進行身份驗證。 然後,託管標識調用 Azure 密鑰保管庫並包裝應用配置實例的加密密鑰。 然後存儲包裝的加密密鑰,並在應用配置中緩存未包裝的加密密鑰一小時。 應用配置每小時刷新應用配置實例加密密鑰的未包裝版本。 這可確保在正常操作條件下的可用性。 

>[!IMPORTANT]
> 如果分配給應用配置實例的標識不再授權解包實例的加密密鑰,或者託管密鑰被永久刪除,則將不再能夠解密存儲在應用配置實例中的敏感資訊。 使用 Azure 金鑰保存庫的[軟刪除](../key-vault/general/overview-soft-delete.md)功能可減少意外刪除加密金鑰的可能性。

當使用者在其 Azure 應用配置實例上啟用客戶託管密鑰功能時,他們控制服務訪問其敏感資訊的能力。 託管金鑰用作根加密金鑰。 使用者可以通過更改其金鑰保管庫存取策略來撤消其應用配置實例對其託管金鑰的訪問許可權。 撤銷此訪問許可權后,應用配置將失去在一小時內解密用戶數據的能力。 此時,應用配置實例將禁止所有訪問嘗試。 此情況可以通過再次授予對託管密鑰的服務訪問許可權來恢復。  在一小時內,應用配置將能夠解密用戶數據並在正常條件下運行。

>[!NOTE]
>所有 Azure 應用配置數據在隔離備份中存儲長達 24 小時。 這包括未包裝的加密密鑰。 此數據不能立即提供給服務或服務團隊。 在發生緊急還原時,Azure 應用配置將從託管密鑰數據中重新撤銷自身。

## <a name="requirements"></a>需求
成功開啟 Azure 應用設定的客戶管理金鑰功能需要以下元件:
- 標準層 Azure 應用設定實體
- 開啟了軟刪除及清除保護功能的 Azure 金鑰保存庫
- 金鑰保存的 RSA 或 RSA-HSM 金鑰
    - 金鑰不能過期,必須啟用,並且必須同時啟用換行和解包功能

設定這些資源後,將保留兩個步驟,以允許 Azure 應用配置使用金鑰保管庫金鑰:
1. 將託管識別分配給 Azure 應用設定實體
2. 在目標金鑰`GET`保`WRAP`管 庫`UNWRAP`的存取策略中授予標識和許可權。

## <a name="enable-customer-managed-key-encryption-for-your-azure-app-configuration-instance"></a>為 Azure 應用設定實體管理的金鑰加密
首先,您需要一個正確配置的 Azure 應用配置實例。 如果尚未提供應用配置實例,請按照以下快速入門之一設置一個實例:
- [使用 Azure 應用設定建立ASP.NET核心應用](quickstart-aspnet-core-app.md)
- [使用 Azure 應用設定建立 .NET 核心應用](quickstart-dotnet-core-app.md)
- [使用 Azure 應用程式設定建立 .NET Framework 應用程式](quickstart-dotnet-app.md)
- [使用 Azure 應用程式組態建立 Java Spring 應用程式](quickstart-java-spring-app.md)

>[!TIP]
> Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的命令列指令。  其中已預先安裝常用的 Azure 工具，包括 .NET Core SDK。 如果您已登入 Azure 訂用帳戶，請從 shell.azure.com 啟動您的 [Azure Cloud Shell](https://shell.azure.com) \(英文\)。  您可以[閱讀我們的文件](../cloud-shell/overview.md)，以深入了解 Azure Cloud Shell

### <a name="create-and-configure-an-azure-key-vault"></a>建立與設定 Azure 金鑰保存庫
1. 使用 Azure CLI 創建 Azure 金鑰保管庫。  請注意,這`vault-name`兩`resource-group-name`個和 都是使用者提供的,並且必須是唯一的。  我們使用這些`contoso-vault`示`contoso-resource-group`例 並在這些示例中使用。

    ```azurecli
    az keyvault create --name contoso-vault --resource-group contoso-resource-group
    ```
    
1. 為金鑰保管庫啟用軟刪除和清除保護。 取代步驟 1 中建立的`contoso-vault`金鑰 保`contoso-resource-group`管庫 ( ) 和資源群組 ( ) 的名稱。

    ```azurecli
    az keyvault update --name contoso-vault --resource-group contoso-resource-group --enable-purge-protection --enable-soft-delete
    ```
    
1. 建立金鑰保管庫金鑰。 為此鍵提供`key-name`唯一,並替換步驟 1 中創建的密`contoso-vault`鑰保管 庫 ( ) 的名稱。 指定是首選`RSA``RSA-HSM`還是 加密。

    ```azurecli
    az keyvault key create --name key-name --kty {RSA or RSA-HSM} --vault-name contoso-vault
    ```
    
    此命令的輸出顯示生成的密鑰的密鑰 ID("兒童")。  記下本練習稍後將使用的密鑰 ID。  金鑰 ID 的表`https://{my key vault}.vault.azure.net/keys/{key-name}/{Key version}`單為 :  金鑰 ID 有三個重要元件:
    1. 金鑰保管庫 URI:'HTTPs://[我的密鑰保管庫].vault.azure.net
    1. 金鑰保管庫金鑰名稱: [金鑰名稱]
    1. 金鑰保管庫金鑰版本:[金鑰版本]

1. 使用 Azure CLI 創建分配的託管識別的系統,替換應用配置實例和前面步驟中使用的資源組的名稱。 託管標識將用於訪問託管金鑰。 我們用於`contoso-app-config`說明應用設定實體的名稱:
    
    ```azurecli
    az appconfig identity assign --na1. me contoso-app-config --group contoso-resource-group --identities [system]
    ```
    
    此命令的輸出包括系統分配標識的主體 ID("主體 Id")和租戶 ID("tenandId")。  這將用於授予對託管金鑰的標識訪問許可權。

    ```json
    {
    "principalId": {Principal Id},
    "tenantId": {Tenant Id},
    "type": "SystemAssigned",
    "userAssignedIdentities": null
    }
    ```

1. Azure 應用配置實例的託管標識需要存取金鑰才能執行金鑰驗證、加密和解密。 它需要訪問的特定操作集包括:`GET``WRAP``UNWRAP`和 鍵。  授予訪問許可權需要應用配置實例的託管標識的主 ID。 此值在上一步中獲得。 這所表示`contoso-principalId`。 使用命令列向託管金鑰的權限:

    ```azurecli
    az keyvault set-policy -n contoso-vault --object-id contoso-principalId --key-permissions get wrapKey unwrapKey
    ```

1. Azure 應用配置實例可以存取託管金鑰後,我們可以使用 Azure CLI 在服務中啟用客戶管理的金鑰功能。 回想在金鑰建立步驟中記錄的以下屬性: `key name` `key vault URI`.

    ```azurecli
    az appconfig update -g contoso-resource-group -n contoso-app-config --encryption-key-name key-name --encryption-key-version key-version --encryption-key-vault key-vault-Uri
    ```

Azure 應用設定實例現在配置為使用存儲在 Azure 密鑰保管庫中的客戶管理金鑰。

## <a name="next-steps"></a>後續步驟
在本文中,您將 Azure 應用配置實例配置為使用客戶管理的密鑰進行加密。  瞭解如何[將服務與 Azure 託管識別整合](howto-integrate-azure-managed-service-identity.md)。