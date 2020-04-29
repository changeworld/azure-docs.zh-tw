---
title: 使用客戶管理的金鑰來加密您的設定資料
description: 使用客戶管理的金鑰來加密設定資料
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/18/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: ace34cf4a72b871ba6646b279007b8ce21c03e9b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457428"
---
# <a name="use-customer-managed-keys-to-encrypt-your-app-configuration-data"></a>使用客戶管理的金鑰來加密您的應用程式組態資料
Azure 應用程式組態[加密待用的機密資訊](../security/fundamentals/encryption-atrest.md)。 使用客戶管理的金鑰可讓您管理您的加密金鑰，以提供增強的資料保護。  使用受控金鑰加密時，應用程式組態中的所有機密資訊都會以使用者提供的 Azure Key Vault 金鑰進行加密。  這可讓您視需要輪替加密金鑰。  它也會撤銷應用程式組態實例的存取權，以撤銷 Azure 應用程式組態對機密資訊存取權的能力。

## <a name="overview"></a>總覽 
Azure 應用程式組態使用 Microsoft 所提供的256位 AES 加密金鑰，加密待用的機密資訊。 每個應用程式組態實例都有其自己的加密金鑰（由服務管理），並用來加密機密資訊。 敏感性資訊包括在索引鍵/值組中找到的值。  啟用客戶管理的金鑰功能時，應用程式組態會使用指派給應用程式組態實例的受控識別，以 Azure Active Directory 進行驗證。 受控識別接著會呼叫 Azure Key Vault 並包裝應用程式組態實例的加密金鑰。 然後會儲存已包裝的加密金鑰，並在應用程式組態中快取已解除包裝的加密金鑰一小時。 應用程式組態會每小時重新整理應用程式組態實例之加密金鑰的未包裝版本。 這可確保在正常操作條件下的可用性。 

>[!IMPORTANT]
> 如果指派給應用程式組態實例的身分識別已不再被授權解除包裝實例的加密金鑰，或永久刪除了受控金鑰，則將無法再解密儲存在應用程式組態實例中的機密資訊。 使用 Azure Key Vault 的虛[刪除](../key-vault/general/overview-soft-delete.md)功能可減輕不小心刪除加密金鑰的機會。

當使用者在其 Azure 應用程式組態實例上啟用客戶管理的金鑰功能時，他們會控制服務存取其敏感性資訊的能力。 受控金鑰會作為根加密金鑰。 使用者可以藉由變更其金鑰保存庫存取原則，撤銷其應用程式組態實例對其受控金鑰的存取權。 撤銷此存取權時，應用程式組態將會失去在一小時內解密使用者資料的能力。 此時，應用程式組態實例將禁止所有的存取嘗試。 這種情況可復原，方法是再次將服務存取權授與受控金鑰。  在一小時內，應用程式組態將能夠在正常情況下解密使用者資料和操作。

>[!NOTE]
>所有 Azure 應用程式組態資料在隔離備份中最多儲存24小時。 這包括未包裝的加密金鑰。 此資料不會立即提供給服務或服務小組。 在緊急還原的事件中，Azure 應用程式組態會從受管理的金鑰資料重新撤銷其本身。

## <a name="requirements"></a>需求
若要成功啟用客戶管理的金鑰功能以進行 Azure 應用程式組態，需要下列元件：
- 標準層 Azure 應用程式組態實例
- 已啟用虛刪除和清除保護功能的 Azure Key Vault
- Key Vault 內的 RSA 或 RSA HSM 金鑰
    - 金鑰不得過期、必須啟用，而且必須啟用包裝和解除包裝功能

設定這些資源之後，仍會有兩個步驟可讓 Azure 應用程式組態使用 Key Vault 金鑰：
1. 將受控識別指派給 Azure 應用程式組態實例
2. 在目標 Key Vault `GET`的`WRAP`存取原則`UNWRAP`中，授與身分識別、和許可權。

## <a name="enable-customer-managed-key-encryption-for-your-azure-app-configuration-instance"></a>為您的 Azure 應用程式組態實例啟用客戶管理的金鑰加密
若要開始，您將需要已正確設定 Azure 應用程式組態實例。 如果您還沒有可用的應用程式組態實例，請遵循下列其中一個快速入門來設定一個：
- [使用 Azure 應用程式組態建立 ASP.NET Core 應用程式](quickstart-aspnet-core-app.md)
- [使用 Azure 應用程式組態建立 .NET Core 應用程式](quickstart-dotnet-core-app.md)
- [使用 Azure 應用程式設定建立 .NET Framework 應用程式](quickstart-dotnet-app.md)
- [使用 Azure 應用程式組態建立 Java Spring 應用程式](quickstart-java-spring-app.md)

>[!TIP]
> Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的命令列指令。  其中已預先安裝常用的 Azure 工具，包括 .NET Core SDK。 如果您已登入 Azure 訂用帳戶，請從 shell.azure.com 啟動您的 [Azure Cloud Shell](https://shell.azure.com) \(英文\)。  您可以[閱讀我們的文件](../cloud-shell/overview.md)，以深入了解 Azure Cloud Shell

### <a name="create-and-configure-an-azure-key-vault"></a>建立和設定 Azure Key Vault
1. 使用 Azure CLI 建立 Azure Key Vault。  請注意， `vault-name`和`resource-group-name`都是由使用者提供，而且必須是唯一的。  在這些`contoso-vault`範例`contoso-resource-group`中，我們會使用和。

    ```azurecli
    az keyvault create --name contoso-vault --resource-group contoso-resource-group
    ```
    
1. 啟用 Key Vault 的虛刪除和清除保護。 替代在步驟1中建立的`contoso-vault`Key Vault （）和資源`contoso-resource-group`群組（）的名稱。

    ```azurecli
    az keyvault update --name contoso-vault --resource-group contoso-resource-group --enable-purge-protection --enable-soft-delete
    ```
    
1. 建立 Key Vault 金鑰。 提供此索引`key-name`鍵的唯一，並替代步驟1中所建立的`contoso-vault`Key Vault （）名稱。 指定您偏好`RSA`或`RSA-HSM`加密。

    ```azurecli
    az keyvault key create --name key-name --kty {RSA or RSA-HSM} --vault-name contoso-vault
    ```
    
    此命令的輸出會顯示所產生金鑰的金鑰識別碼（「小孩」）。  記下金鑰識別碼，以供稍後在本練習中使用。  金鑰識別碼的格式為： `https://{my key vault}.vault.azure.net/keys/{key-name}/{Key version}`。  金鑰識別碼有三個重要元件：
    1. Key Vault URI： ' HTTPs：//{我的金鑰保存庫. azure。
    1. Key Vault 索引鍵名稱： {金鑰名稱}
    1. Key Vault 金鑰版本： {金鑰版本}

1. 使用 Azure CLI 建立系統指派的受控識別，並以先前步驟中使用的應用程式組態實例和資源群組的名稱取代。 受控識別將用來存取受管理的金鑰。 我們使用`contoso-app-config`來說明應用程式組態實例的名稱：
    
    ```azurecli
    az appconfig identity assign --na1. me contoso-app-config --group contoso-resource-group --identities [system]
    ```
    
    此命令的輸出包含系統指派之身分識別的主體識別碼（"principalId"）和租使用者識別碼（"tenandId"）。  這會用來授與受控金鑰的身分識別存取權。

    ```json
    {
    "principalId": {Principal Id},
    "tenantId": {Tenant Id},
    "type": "SystemAssigned",
    "userAssignedIdentities": null
    }
    ```

1. Azure 應用程式組態實例的受控識別需要金鑰的存取權，才能執行金鑰驗證、加密和解密。 其需要存取的一組特定動作包括： `GET`、和`WRAP` `UNWRAP` （針對金鑰）。  授與存取權需要應用程式組態實例受控識別的主體識別碼。 這個值是在上一個步驟中取得。 如下所示`contoso-principalId`。 使用命令列授與受控金鑰的許可權：

    ```azurecli
    az keyvault set-policy -n contoso-vault --object-id contoso-principalId --key-permissions get wrapKey unwrapKey
    ```

1. 一旦 Azure 應用程式組態實例可以存取受控金鑰，我們就可以使用 Azure CLI 在服務中啟用客戶管理的金鑰功能。 請記得在金鑰建立步驟期間記錄的下列屬性`key name` `key vault URI`：。

    ```azurecli
    az appconfig update -g contoso-resource-group -n contoso-app-config --encryption-key-name key-name --encryption-key-version key-version --encryption-key-vault key-vault-Uri
    ```

您的 Azure 應用程式組態實例現在已設定為使用儲存在 Azure Key Vault 中的客戶管理金鑰。

## <a name="next-steps"></a>後續步驟
在本文中，您已將 Azure 應用程式組態實例設定為使用客戶管理的金鑰進行加密。  瞭解如何將[您的服務與 Azure 受控識別整合](howto-integrate-azure-managed-service-identity.md)。