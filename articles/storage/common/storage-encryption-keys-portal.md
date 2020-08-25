---
title: 使用 Azure 入口網站設定客戶管理的金鑰
titleSuffix: Azure Storage
description: 瞭解如何使用 Azure 入口網站設定 Azure 儲存體加密 Azure Key Vault 的客戶管理金鑰。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/31/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: a506f59d3f2d331e4c7680565f3c110b9cd12956
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88799159"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>在 Azure 入口網站中使用 Azure Key Vault 設定客戶自控金鑰

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

本文說明如何使用 [Azure 入口網站](https://portal.azure.com/)，以客戶管理的金鑰來設定 Azure Key Vault。 若要瞭解如何使用 Azure 入口網站來建立金鑰保存庫，請參閱 [快速入門：使用 Azure 入口網站從 Azure Key Vault 設定和取出秘密](../../key-vault/secrets/quick-create-portal.md)。

## <a name="configure-azure-key-vault"></a>設定 Azure Key Vault

使用客戶管理的金鑰搭配 Azure 儲存體加密，需要在金鑰保存庫、虛 **刪除** 和 **請勿清除**兩個屬性。 這些屬性預設不會啟用，但可使用 PowerShell 或 Azure CLI 在新的或現有的金鑰保存庫上啟用。

若要了解如何在現有的金鑰保存庫上啟用這些屬性，請參閱下列其中一篇文章中的**啟用虛刪除**和**啟用清除保護**小節：

- [如何搭配 PowerShell 使用虛刪除](../../key-vault/general/soft-delete-powershell.md)。
- [如何搭配 CLI 使用虛刪除](../../key-vault/general/soft-delete-cli.md)。

Azure 儲存體加密支援2048、3072和4096大小的 RSA 和 RSA HSM 金鑰。 如需有關金鑰的詳細資訊，請參閱[關於 Azure Key Vault 金鑰、秘密和憑證](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)的**Key Vault 金鑰**。

## <a name="enable-customer-managed-keys"></a>啟用客戶管理的金鑰

若要在 Azure 入口網站中啟用客戶管理的金鑰，請遵循下列步驟：

1. 瀏覽至儲存體帳戶。
1. 在儲存體帳戶的 [設定]**** 刀鋒視窗上，按一下 [加密]****。 選取 [ **客戶管理的金鑰** ] 選項，如下圖所示。

    ![顯示加密選項的入口網站螢幕擷取畫面](./media/storage-encryption-keys-portal/portal-configure-encryption-keys.png)

## <a name="specify-a-key"></a>指定金鑰

啟用客戶管理的金鑰之後，您將有機會指定與儲存體帳戶相關聯的金鑰。 您也可以指定 Azure 儲存體是否應該自動將客戶管理的金鑰更新為最新版本，或您是否要手動更新金鑰版本。

### <a name="specify-a-key-from-a-key-vault"></a>從金鑰保存庫指定金鑰

當您從金鑰保存庫選取客戶管理的金鑰時，會啟用金鑰版本的自動更新。 若要手動管理金鑰版本，請改為指定金鑰 URI，並包含金鑰版本。 如需詳細資訊，請參閱將索引 [鍵指定為 URI](#specify-a-key-as-a-uri)。

若要指定金鑰保存庫中的金鑰，請遵循下列步驟：

1. 選擇 [從 Key Vault 選取]**** 選項。
1. 選取 [ **選取金鑰保存庫與金鑰**]。
1. 選取包含您要使用之金鑰的金鑰保存庫。
1. 選取金鑰保存庫中的金鑰。

   ![顯示如何選取金鑰保存庫和金鑰的螢幕擷取畫面](./media/storage-encryption-keys-portal/portal-select-key-from-key-vault.png)

1. 儲存您的變更。

### <a name="specify-a-key-as-a-uri"></a>以 URI 形式指定金鑰

Azure 儲存體可以自動更新用來加密的客戶管理金鑰，以使用最新的金鑰版本。 在 Azure Key Vault 中輪替客戶管理的金鑰時，Azure 儲存體會自動開始使用最新版本的金鑰進行加密。

> [!NOTE]
> 若要輪替金鑰，請在 Azure Key Vault 中建立新版本的金鑰。 Azure 儲存體不會在 Azure Key Vault 中處理金鑰的輪替，因此您必須手動輪替金鑰，或建立函式以依排程輪替。

當您指定金鑰 URI 時，請從 URI 中省略金鑰版本，以啟用自動更新為最新的金鑰版本。 如果您在金鑰 URI 中包含金鑰版本，則不會啟用自動更新，您必須自行管理金鑰版本。 如需更新金鑰版本的詳細資訊，請參閱 [手動更新金鑰版本](#manually-update-the-key-version)。

若要將金鑰指定為 URI，請遵循下列步驟：

1. 若要在 Azure 入口網站中找出金鑰 URI，請流覽至您的金鑰保存庫，然後選取 [ **金鑰** ] 設定。 選取所需的金鑰，然後按一下金鑰以查看其版本。 選取金鑰版本以查看該版本的設定。
1. 複製 [ **金鑰識別碼** ] 欄位的值，以提供 URI。

    ![顯示 key vault 金鑰 URI 的螢幕擷取畫面](media/storage-encryption-keys-portal/portal-copy-key-identifier.png)

1. 在儲存體帳戶的 [ **加密金鑰** ] 設定中，選擇 [ **輸入金鑰 URI** ] 選項。
1. 將您複製的 URI 貼到 [ **金鑰 URI** ] 欄位中。 從 URI 中省略金鑰版本，以啟用金鑰版本的自動更新。

   ![顯示如何輸入金鑰 URI 的螢幕擷取畫面](./media/storage-encryption-keys-portal/portal-specify-key-uri.png)

1. 指定包含金鑰保存庫的訂用帳戶。
1. 儲存您的變更。

當您指定金鑰之後，Azure 入口網站會指出金鑰版本的自動更新是否已啟用，並顯示目前用於加密的金鑰版本。

:::image type="content" source="media/storage-encryption-keys-portal/portal-auto-rotation-enabled.png" alt-text="螢幕擷取畫面，顯示已啟用金鑰版本的自動更新":::

## <a name="manually-update-the-key-version"></a>手動更新金鑰版本

依預設，當您在 Key Vault 中建立客戶管理金鑰的新版本時，Azure 儲存體會自動使用新版本進行以客戶管理的金鑰加密，如先前章節所述。 如果您選擇自行管理金鑰版本，則每次建立新版本的金鑰時，都必須更新與儲存體帳戶相關聯的金鑰版本。

若要將儲存體帳戶更新為使用新的金鑰版本，請遵循下列步驟：

1. 流覽至您的儲存體帳戶，並顯示 **加密** 設定。
1. 輸入新版本金鑰的 URI。 或者，您可以再次選取金鑰保存庫和金鑰，以更新版本。
1. 儲存您的變更。

## <a name="switch-to-a-different-key"></a>切換至不同的金鑰

若要變更 Azure 儲存體加密所使用的金鑰，請遵循下列步驟：

1. 流覽至您的儲存體帳戶，並顯示 **加密** 設定。
1. 輸入新金鑰的 URI。 或者，您也可以選取金鑰保存庫，然後選擇新的金鑰。
1. 儲存您的變更。

## <a name="disable-customer-managed-keys"></a>停用客戶管理的金鑰

當您停用客戶管理的金鑰時，您的儲存體帳戶會再次使用 Microsoft 管理的金鑰進行加密。 若要停用客戶管理的金鑰，請遵循下列步驟：

1. 流覽至您的儲存體帳戶，並顯示 **加密** 設定。
1. 取消選取 [ **使用您自己的金鑰** ] 設定旁的核取方塊。

## <a name="next-steps"></a>後續步驟

- [待用資料的 Azure 儲存體加密](storage-service-encryption.md)
- [什麼是 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)？
