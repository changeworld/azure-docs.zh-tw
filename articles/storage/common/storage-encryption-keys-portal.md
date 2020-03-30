---
title: 使用 Azure 門戶配置客戶管理的金鑰
titleSuffix: Azure Storage
description: 瞭解如何使用 Azure 門戶使用 Azure 金鑰保存庫配置客戶管理的金鑰以進行 Azure 存儲加密。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: bef3b53a160f17248c1a26e97bc85a86843cb3c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061231"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>使用 Azure 金鑰保存庫配置客戶管理的金鑰

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

本文演示如何使用[Azure 門戶](https://portal.azure.com/)使用客戶管理的金鑰配置 Azure 金鑰保存庫。 若要瞭解如何使用 Azure 門戶創建金鑰保存庫，請參閱[快速入門：使用 Azure 門戶從 Azure 金鑰保存庫設置和檢索機密](../../key-vault/quick-create-portal.md)。

## <a name="configure-azure-key-vault"></a>設定 Azure Key Vault

使用具有 Azure 存儲加密的客戶管理金鑰要求在金鑰保存庫上設置兩個屬性，**即"虛刪除****"和"不清除**"。 預設情況下，這些屬性不會啟用，但可以使用 PowerShell 或 Azure CLI 在新金鑰保存庫或現有金鑰保存庫上啟用。

要瞭解如何在現有金鑰保存庫中啟用這些屬性，請參閱以下文章中標題為 **"啟用虛刪除**和**啟用清除保護**"的部分：

- [如何使用虛刪除與PowerShell。](../../key-vault/key-vault-soft-delete-powershell.md)
- [如何使用虛刪除與CLI。](../../key-vault/key-vault-soft-delete-cli.md)

Azure 存儲加密僅支援大小為 2048 的 RSA 金鑰。 有關金鑰的詳細資訊，請參閱["關於 Azure 金鑰保存庫金鑰、機密和證書"](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)中的**金鑰保存庫金鑰**。

## <a name="enable-customer-managed-keys"></a>啟用客戶管理的金鑰

要在 Azure 門戶中啟用客戶管理的金鑰，請按照以下步驟操作：

1. 瀏覽至儲存體帳戶。
1. 在儲存體帳戶的 [設定]**** 刀鋒視窗上，按一下 [加密]****。 選擇 **"客戶託管金鑰"** 選項，如下圖所示。

    ![顯示加密選項的門戶螢幕截圖](./media/storage-encryption-keys-portal/portal-configure-encryption-keys.png)

## <a name="specify-a-key"></a>指定鍵

啟用客戶管理的金鑰後，您將有機會指定要與存儲帳戶關聯的金鑰。

### <a name="specify-a-key-as-a-uri"></a>以 URI 形式指定金鑰

要將鍵指定為 URI，請按照以下步驟操作：

1. 要在 Azure 門戶中查找金鑰 URI，請導航到金鑰保存庫，然後選擇 **"金鑰**"設置。 選擇所需的鍵，然後按一下該鍵以查看其版本。 選擇金鑰版本以查看該版本的設置。
1. 複製提供 URI 的 **"金鑰識別碼"** 欄位的值。

    ![顯示金鑰保存庫金鑰 URI 的螢幕截圖](media/storage-encryption-keys-portal/portal-copy-key-identifier.png)

1. 在存儲帳戶的**加密**設置中，選擇 **"輸入金鑰 URI"** 選項。
1. 將複製的 URI 粘貼到 **"金鑰 URI"** 欄位中。

   ![顯示如何輸入金鑰 URI 的螢幕截圖](./media/storage-encryption-keys-portal/portal-specify-key-uri.png)

1. 指定包含金鑰保存庫的訂閱。
1. 儲存您的變更。

### <a name="specify-a-key-from-a-key-vault"></a>從金鑰保存庫指定金鑰

要從金鑰保存庫指定金鑰，首先請確保具有包含金鑰的金鑰保存庫。 要從金鑰保存庫指定金鑰，請按照以下步驟操作：

1. 選擇 [從 Key Vault 選取]**** 選項。
1. 選擇包含要使用的金鑰的金鑰保存庫。
1. 從金鑰保存庫中選擇金鑰。

   ![顯示客戶託管金鑰選項的螢幕截圖](./media/storage-encryption-keys-portal/portal-select-key-from-key-vault.png)

1. 儲存您的變更。

## <a name="update-the-key-version"></a>更新金鑰版本

創建金鑰的新版本時，請更新存儲帳戶以使用新版本。 請遵循下列步驟：

1. 導航到存儲帳戶並顯示**加密**設置。
1. 輸入新金鑰版本的 URI。 或者，您可以選擇金鑰保存庫和金鑰以再次更新版本。
1. 儲存您的變更。

## <a name="use-a-different-key"></a>使用其他鍵

要更改用於 Azure 存儲加密的金鑰，請按照以下步驟操作：

1. 導航到存儲帳戶並顯示**加密**設置。
1. 輸入新金鑰的 URI。 或者，您可以選擇金鑰保存庫並選擇新金鑰。
1. 儲存您的變更。

## <a name="disable-customer-managed-keys"></a>禁用客戶管理的金鑰

禁用客戶管理的金鑰時，您的存儲帳戶將再次使用 Microsoft 管理的金鑰進行加密。 要禁用客戶管理的金鑰，請按照以下步驟操作：

1. 導航到存儲帳戶並顯示**加密**設置。
1. 取消選擇"**使用您自己的金鑰**設置"旁邊的核取方塊。

## <a name="next-steps"></a>後續步驟

- [靜態資料的 Azure 存儲加密](storage-service-encryption.md)
- [什麼是 Azure 金鑰保存庫](https://docs.microsoft.com/azure/key-vault/key-vault-overview)？
