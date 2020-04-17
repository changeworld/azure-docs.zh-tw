---
title: 使用 Azure 閘戶設定客戶管理的金鑰
titleSuffix: Azure Storage
description: 瞭解如何使用 Azure 門戶使用 Azure 密鑰保管庫配置客戶管理的密鑰以進行 Azure 儲存加密。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 192e58b101b824ca0cc0c732e02647838be6dc35
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81456476"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>使用 Azure 金鑰保存式庫組態客戶管理的金鑰

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

本文演示如何使用[Azure 門戶](https://portal.azure.com/)使用客戶管理的密鑰配置 Azure 密鑰保管庫。 若要瞭解如何使用 Azure 門戶建立金鑰保管庫,請參閱[快速入門:使用 Azure 門戶從 Azure 金鑰保管庫設定和檢索機密](../../key-vault/secrets/quick-create-portal.md)。

## <a name="configure-azure-key-vault"></a>設定 Azure Key Vault

使用具有 Azure 儲存加密的客戶管理金鑰要求在密鑰保管庫上設置兩個屬性,**即「軟刪除****」和「不清除**」 。 默認情況下,這些屬性不會啟用,但可以使用 PowerShell 或 Azure CLI 在新密鑰保管庫或現有金鑰保管庫上啟用。

要瞭解如何在現有金鑰保管庫中啟用這些屬性,請參閱以下文章中標題為 **「啟用軟刪除**和**啟用清除保護**」的部分:

- [如何使用軟刪除與PowerShell。](../../key-vault/general/soft-delete-powershell.md)
- [如何使用軟刪除與CLI。](../../key-vault/general/soft-delete-cli.md)

Azure 儲存加密僅支援 2048 位 RSA 和 RSA-HSM 密鑰。 關於金鑰的詳細資訊,請參考[「關於 Azure 金鑰保管庫金鑰、機密和憑證」](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)的**金鑰保管庫金鑰**。

## <a name="enable-customer-managed-keys"></a>開啟客戶管理的金鑰

要在 Azure 門戶開啟客戶管理的金鑰,請按照以下步驟操作:

1. 瀏覽至儲存體帳戶。
1. 在儲存體帳戶的 [設定]**** 刀鋒視窗上，按一下 [加密]****。 選擇 **「客戶託管金鑰」** 選項,如下圖所示。

    ![顯示加密選項的門戶螢幕截圖](./media/storage-encryption-keys-portal/portal-configure-encryption-keys.png)

## <a name="specify-a-key"></a>指定鍵

啟用客戶管理的密鑰後,您將有機會指定要與存儲帳戶關聯的密鑰。

### <a name="specify-a-key-as-a-uri"></a>以 URI 形式指定金鑰

要將鍵指定為 URI,請按照以下步驟操作:

1. 要在 Azure 門戶中查找密鑰 URI,請導航到密鑰保管庫,然後選擇 **「密鑰**」設置。 選擇所需的鍵,然後單擊該鍵以查看其版本。 選擇金鑰版本以查看該版本的設置。
1. 複製提供 URI 的 **「密鑰識別符」** 欄位的值。

    ![顯示金鑰保存金鑰的網鑰的網形](media/storage-encryption-keys-portal/portal-copy-key-identifier.png)

1. 在儲存帳戶的**加密**設定中,選擇 **「輸入金鑰 URI」** 選項。
1. 將複製的 URI 貼上到 **「密鑰 URI」** 欄位中。

   ![顯示如何輸入金鑰的網址的螢幕擷取](./media/storage-encryption-keys-portal/portal-specify-key-uri.png)

1. 指定包含密鑰保管庫的訂閱。
1. 儲存您的變更。

### <a name="specify-a-key-from-a-key-vault"></a>從金鑰保存庫指定金鑰

要從密鑰保管庫指定密鑰,首先請確保具有包含密鑰的密鑰保管庫。 要從金鑰保管庫指定金鑰,請按照以下步驟操作:

1. 選擇 [從 Key Vault 選取]**** 選項。
1. 選擇包含要使用的金鑰的金鑰保管庫。
1. 從金鑰保管庫中選擇密鑰。

   ![顯示客戶託管金鑰選項的螢幕擷取](./media/storage-encryption-keys-portal/portal-select-key-from-key-vault.png)

1. 儲存您的變更。

## <a name="update-the-key-version"></a>更新金鑰版本

建立金鑰的新版本時,請更新存儲帳戶以使用新版本。 請遵循下列步驟：

1. 導航到存儲帳戶並顯示**加密**設置。
1. 輸入新密鑰版本的 URI。 或者,您可以選擇金鑰保管庫和密鑰以再次更新版本。
1. 儲存您的變更。

## <a name="use-a-different-key"></a>使用其他鍵

要更改用於 Azure 儲存加密的金鑰,請按照以下步驟操作:

1. 導航到存儲帳戶並顯示**加密**設置。
1. 輸入新密鑰的 URI。 或者,您可以選擇金鑰保管庫並選擇新密鑰。
1. 儲存您的變更。

## <a name="disable-customer-managed-keys"></a>關閉客戶管理的金鑰

禁用客戶管理的密鑰時,您的儲存帳戶將再次使用 Microsoft 管理的密鑰進行加密。 要關閉客戶管理的金鑰,請按照以下步驟操作:

1. 導航到存儲帳戶並顯示**加密**設置。
1. 取消選擇 **「 使用您自己的金鑰**」 設定「旁邊的複選框」。

## <a name="next-steps"></a>後續步驟

- [靜態資料的 Azure 儲存加密](storage-service-encryption.md)
- [什麼是 Azure 金鑰保存嗎](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
