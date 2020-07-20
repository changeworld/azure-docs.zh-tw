---
title: 使用 Azure 入口網站來設定客戶管理的金鑰
titleSuffix: Azure Storage
description: 瞭解如何使用 Azure 入口網站，以 Azure 儲存體加密的 Azure Key Vault 來設定客戶管理的金鑰。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 4af70a4e2a698bd280c8c41018bc5aaa1bfa27f8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85512557"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>使用 Azure 入口網站以 Azure Key Vault 設定客戶管理的金鑰

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

本文說明如何使用[Azure 入口網站](https://portal.azure.com/)，以客戶管理的金鑰設定 Azure Key Vault。 若要瞭解如何使用 Azure 入口網站建立金鑰保存庫，請參閱[快速入門：使用 Azure 入口網站從 Azure Key Vault 設定和取出秘密](../../key-vault/secrets/quick-create-portal.md)。

## <a name="configure-azure-key-vault"></a>設定 Azure Key Vault

若要使用客戶管理的金鑰搭配 Azure 儲存體加密，需要在金鑰保存庫上設定兩個屬性： [虛**刪除**] 和 [**不要清除**]。 這些屬性預設不會啟用，但可以使用 PowerShell 或 Azure CLI 在新的或現有的金鑰保存庫上啟用。

若要了解如何在現有的金鑰保存庫上啟用這些屬性，請參閱下列其中一篇文章中的**啟用虛刪除**和**啟用清除保護**小節：

- [如何使用 PowerShell](../../key-vault/general/soft-delete-powershell.md)進行虛刪除。
- [如何使用 CLI](../../key-vault/general/soft-delete-cli.md)進行虛刪除。

Azure 儲存體加密支援 RSA 和 RSA-大小為2048、3072和4096的 HSM 金鑰。 如需金鑰的詳細資訊，請參閱[關於 Azure Key Vault 金鑰、秘密和憑證](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)中的**Key Vault 金鑰**。

## <a name="enable-customer-managed-keys"></a>啟用客戶管理的金鑰

若要在 Azure 入口網站中啟用客戶管理的金鑰，請遵循下列步驟：

1. 瀏覽至儲存體帳戶。
1. 在儲存體帳戶的 [設定]**** 刀鋒視窗上，按一下 [加密]****。 選取 [**客戶管理的金鑰**] 選項，如下圖所示。

    ![顯示加密選項的入口網站螢幕擷取畫面](./media/storage-encryption-keys-portal/portal-configure-encryption-keys.png)

## <a name="specify-a-key"></a>指定金鑰

啟用客戶管理的金鑰後，您將有機會指定與儲存體帳戶相關聯的金鑰。

### <a name="specify-a-key-as-a-uri"></a>以 URI 形式指定金鑰

若要指定金鑰做為 URI，請遵循下列步驟：

1. 若要找出 Azure 入口網站中的金鑰 URI，請流覽至您的金鑰保存庫，然後選取 [**金鑰**] 設定。 選取所需的金鑰，然後按一下金鑰以查看其版本。 選取金鑰版本以查看該版本的設定。
1. 複製提供 URI 的 [**金鑰識別碼**] 欄位的值。

    ![顯示金鑰保存庫金鑰 URI 的螢幕擷取畫面](media/storage-encryption-keys-portal/portal-copy-key-identifier.png)

1. 在儲存體帳戶的 [**加密**設定] 中，選擇 [**輸入金鑰 URI** ] 選項。
1. 將您複製的 URI 貼到 [**金鑰 URI** ] 欄位中。

   ![顯示如何輸入金鑰 URI 的螢幕擷取畫面](./media/storage-encryption-keys-portal/portal-specify-key-uri.png)

1. 指定包含金鑰保存庫的訂用帳戶。
1. 儲存您的變更。

### <a name="specify-a-key-from-a-key-vault"></a>從金鑰保存庫指定金鑰

若要從金鑰保存庫指定金鑰，請先確定您有包含金鑰的金鑰保存庫。 若要從金鑰保存庫指定金鑰，請遵循下列步驟：

1. 選擇 [從 Key Vault 選取]**** 選項。
1. 選取包含您要使用之金鑰的金鑰保存庫。
1. 選取金鑰保存庫中的金鑰。

   ![顯示客戶管理的金鑰選項的螢幕擷取畫面](./media/storage-encryption-keys-portal/portal-select-key-from-key-vault.png)

1. 儲存您的變更。

## <a name="update-the-key-version"></a>更新金鑰版本

當您建立新版本的金鑰時，請將儲存體帳戶更新為使用新的版本。 請遵循下列步驟：

1. 流覽至您的儲存體帳戶，並顯示**加密**設定。
1. 輸入新金鑰版本的 URI。 或者，您可以再次選取金鑰保存庫和金鑰，以更新版本。
1. 儲存您的變更。

## <a name="use-a-different-key"></a>使用不同的金鑰

若要變更 Azure 儲存體加密所使用的金鑰，請遵循下列步驟：

1. 流覽至您的儲存體帳戶，並顯示**加密**設定。
1. 輸入新金鑰的 URI。 或者，您可以選取金鑰保存庫，然後選擇新的金鑰。
1. 儲存您的變更。

## <a name="disable-customer-managed-keys"></a>停用客戶管理的金鑰

當您停用客戶管理的金鑰時，您的儲存體帳戶會再次使用 Microsoft 管理的金鑰進行加密。 若要停用客戶管理的金鑰，請遵循下列步驟：

1. 流覽至您的儲存體帳戶，並顯示**加密**設定。
1. 取消選取 [**使用您自己的金鑰**] 設定旁的核取方塊。

## <a name="next-steps"></a>後續步驟

- [待用資料的 Azure 儲存體加密](storage-service-encryption.md)
- [什麼是 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)？
