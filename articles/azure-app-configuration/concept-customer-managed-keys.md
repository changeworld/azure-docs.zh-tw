---
title: 使用客戶管理的金鑰來加密您的設定資料
description: 使用客戶管理的金鑰來加密您的設定資料
author: AlexandraKemperMS
ms.author: alkemper
ms.date: 07/28/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 46a1e99a7bc75efa85fcb5eff649c14af5abd17b
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96930495"
---
# <a name="use-customer-managed-keys-to-encrypt-your-app-configuration-data"></a>使用客戶管理的金鑰來加密您的應用程式設定資料
Azure 應用程式組態 [加密待用的機密資訊](../security/fundamentals/encryption-atrest.md)。 使用客戶管理的金鑰可讓您管理加密金鑰，以提供增強的資料保護。  使用受控金鑰加密時，應用程式設定中的所有機密資訊都會以使用者提供的 Azure Key Vault 金鑰進行加密。  這可讓您視需要輪替加密金鑰。  它也可撤銷應用程式設定實例對金鑰的存取權，以撤銷 Azure 應用程式組態存取機密資訊的能力。

## <a name="overview"></a>總覽 
Azure 應用程式組態使用 Microsoft 提供的256位 AES 加密金鑰來加密待用的機密資訊。 每個應用程式設定實例都有它自己的加密金鑰，可由服務管理，並用來加密機密資訊。 機密資訊包括在索引鍵/值組中找到的值。  啟用客戶管理的金鑰功能時，應用程式設定會使用指派給應用程式設定實例的受控識別，向 Azure Active Directory 進行驗證。 受控識別接著會呼叫 Azure Key Vault，並包裝應用程式設定實例的加密金鑰。 然後會儲存已包裝的加密金鑰，並在應用程式設定中快取未包裝的加密金鑰一小時。 應用程式設定會每小時重新整理應用程式設定實例之加密金鑰的解除包裝版本。 這可確保正常運作狀況下的可用性。 

>[!IMPORTANT]
> 若指派給應用程式設定實例的身分識別已不再授權解除包裝實例的加密金鑰，或如果已永久刪除受控金鑰，則將無法再解密儲存在應用程式設定實例中的機密資訊。 使用 Azure Key Vault 的虛 [刪除](../key-vault/general/soft-delete-overview.md) 函數可減少意外刪除加密金鑰的機會。

當使用者在其 Azure 應用程式組態實例上啟用客戶管理的金鑰功能時，會控制服務存取其機密資訊的能力。 受控金鑰會作為根加密金鑰。 使用者可以藉由變更其 key vault 存取原則，來撤銷其應用程式設定實例對其受控金鑰的存取權。 撤銷此存取權時，應用程式設定將會失去在一小時內解密使用者資料的能力。 此時，應用程式設定實例將禁止所有的存取嘗試。 這種情況可以透過將受控金鑰的存取權授與服務，來復原。  在一小時內，應用程式設定將能夠解密使用者資料，並在正常狀況下運作。

>[!NOTE]
>所有 Azure 應用程式組態資料都會儲存在隔離備份中最多24小時的時間。 這包括未包裝的加密金鑰。 這項資料不會立即提供給服務或服務小組使用。 萬一發生緊急狀況還原，Azure 應用程式組態將會從受控金鑰資料重新撤銷本身。

## <a name="requirements"></a>規格需求
若要成功啟用 Azure 應用程式組態客戶管理的金鑰功能，需要下列元件：
- 標準層 Azure 應用程式組態實例
- 啟用虛刪除和清除保護功能的 Azure Key Vault
- Key Vault 內的 RSA 或 RSA HSM 金鑰
    - 金鑰不得過期、必須啟用，而且必須同時啟用包裝和解除包裝功能

設定好這些資源之後，仍有兩個步驟可讓 Azure 應用程式組態使用 Key Vault 機碼：
1. 將受控識別指派給 Azure 應用程式組態實例
2. 授與 `GET` `WRAP` `UNWRAP` 目標 Key Vault 存取原則中的身分識別、和許可權。

## <a name="enable-customer-managed-key-encryption-for-your-azure-app-configuration-instance"></a>為您的 Azure 應用程式組態實例啟用客戶管理的金鑰加密
若要開始，您將需要適當設定的 Azure 應用程式組態實例。 如果您還沒有可用的應用程式設定實例，請遵循下列其中一個快速入門來設定一個：
- [使用 Azure 應用程式組態建立 ASP.NET Core 應用程式](quickstart-aspnet-core-app.md)
- [使用 Azure 應用程式組態建立 .NET Core 應用程式](quickstart-dotnet-core-app.md)
- [使用 Azure 應用程式設定建立 .NET Framework 應用程式](quickstart-dotnet-app.md)
- [使用 Azure 應用程式組態建立 Java Spring 應用程式](quickstart-java-spring-app.md)

>[!TIP]
> Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的命令列指令。  其中已預先安裝常用的 Azure 工具，包括 .NET Core SDK。 如果您已登入 Azure 訂用帳戶，請從 shell.azure.com 啟動您的 [Azure Cloud Shell](https://shell.azure.com) \(英文\)。  您可以[閱讀我們的文件](../cloud-shell/overview.md)，以深入了解 Azure Cloud Shell

### <a name="create-and-configure-an-azure-key-vault"></a>建立並設定 Azure Key Vault
1. 使用 Azure CLI 建立 Azure Key Vault。  請注意， `vault-name` 和 `resource-group-name` 都是由使用者提供，而且必須是唯一的。  `contoso-vault`在這些範例中，我們會使用和 `contoso-resource-group` 。

    ```azurecli
    az keyvault create --name contoso-vault --resource-group contoso-resource-group
    ```
    
1. 啟用 Key Vault 的虛刪除和清除保護。 `contoso-vault`以步驟1中建立的 Key Vault () 和資源群組的名稱取代 (`contoso-resource-group`) 。

    ```azurecli
    az keyvault update --name contoso-vault --resource-group contoso-resource-group --enable-purge-protection --enable-soft-delete
    ```
    
1. 建立 Key Vault 金鑰。 提供此索引 `key-name` 鍵的唯一，並以 `contoso-vault` 在步驟1中建立的 Key Vault () 名稱取代。 指定您想要 `RSA` 還是 `RSA-HSM` 加密。

    ```azurecli
    az keyvault key create --name key-name --kty {RSA or RSA-HSM} --vault-name contoso-vault
    ```
    
    此命令的輸出會顯示所產生金鑰的金鑰識別碼 ( 「小孩」 ) 。  記下金鑰識別碼，以便稍後在此練習中使用。  金鑰識別碼的格式如下： `https://{my key vault}.vault.azure.net/keys/{key-name}/{Key version}` 。  金鑰識別碼有三個重要元件：
    1. Key Vault URI： ' HTTPs：//{我的金鑰保存庫]。
    1. Key Vault 機碼名稱： {Key Name}
    1. Key Vault 金鑰版本： {Key version}

1. 使用 Azure CLI 建立系統指派的受控識別，並以您在先前步驟中使用的應用程式設定實例和資源群組的名稱取代。 受控識別將用來存取受管理的金鑰。 我們使用 `contoso-app-config` 來說明應用程式設定實例的名稱：
    
    ```azurecli
    az appconfig identity assign --name contoso-app-config --resource-group contoso-resource-group --identities [system]
    ```
    
    此命令的輸出包含主體識別碼 ( "principalId" ) 和租使用者識別碼 ( "tenandId" ) 系統指派的身分識別。  這些識別碼將用來授與受控金鑰的身分識別存取權。

    ```json
    {
    "principalId": {Principal Id},
    "tenantId": {Tenant Id},
    "type": "SystemAssigned",
    "userAssignedIdentities": null
    }
    ```

1. Azure 應用程式組態實例的受控識別需要存取金鑰，才能執行金鑰驗證、加密和解密。 需要存取的一組特定動作包括： `GET` 、 `WRAP` 和索引 `UNWRAP` 鍵。  授與存取權需要應用程式設定實例之受控識別的主體識別碼。 此值是在上一個步驟中取得。 它會顯示如下 `contoso-principalId` 。 使用命令列將許可權授與受控金鑰：

    ```azurecli
    az keyvault set-policy -n contoso-vault --object-id contoso-principalId --key-permissions get wrapKey unwrapKey
    ```

1. 一旦 Azure 應用程式組態實例可以存取受控金鑰之後，我們就可以使用 Azure CLI，在服務中啟用客戶管理的金鑰功能。 請記得在金鑰建立步驟期間記錄的下列屬性： `key name` `key vault URI` 。

    ```azurecli
    az appconfig update -g contoso-resource-group -n contoso-app-config --encryption-key-name key-name --encryption-key-version key-version --encryption-key-vault key-vault-Uri
    ```

您的 Azure 應用程式組態實例現在已設定為使用儲存在 Azure Key Vault 中的客戶管理金鑰。

## <a name="next-steps"></a>後續步驟
在本文中，您已將 Azure 應用程式組態實例設定為使用客戶管理的金鑰進行加密。  瞭解如何 [整合您的服務與 Azure 受控](howto-integrate-azure-managed-service-identity.md)識別。
