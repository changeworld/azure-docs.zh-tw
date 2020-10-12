---
title: 使用 Azure 入口網站設定匯入/匯出服務的客戶管理金鑰
description: 瞭解如何使用 Azure 入口網站為 Azure 匯入/匯出服務的 Azure Key Vault 設定客戶管理的金鑰。 客戶管理的金鑰可讓您建立、輪替、停用及撤銷存取控制。
services: storage
author: alkohli
ms.service: storage
ms.topic: how-to
ms.date: 05/06/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: d0a1826dafd1e6ce6202dc4f29417a1ce100e54f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "83195244"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-importexport-service"></a>在 Azure Key Vault 中使用客戶管理的金鑰進行匯入/匯出服務

Azure 匯入/匯出會保護用來透過加密金鑰鎖定磁片磁碟機的 BitLocker 金鑰。 BitLocker 金鑰預設會使用 Microsoft 管理的金鑰進行加密。 若要進一步控制加密金鑰，您也可以提供客戶管理的金鑰。

客戶管理的金鑰必須建立並儲存在 Azure Key Vault 中。 如需 Azure 金鑰保存庫的詳細資訊，請參閱 [什麼是 Azure 金鑰保存庫？](../../key-vault/general/overview.md)

本文說明如何將客戶管理的金鑰與 [Azure 入口網站](https://portal.azure.com/)中的匯入/匯出服務搭配使用。

## <a name="prerequisites"></a>Prerequisites

在您開始前，請確定：

1. 您已根據中的指示來建立匯入或匯出作業：

    - [建立 blob 的匯入作業](storage-import-export-data-to-blobs.md)。
    - 建立檔案的匯[入作業](storage-import-export-data-to-files.md)。
    - [建立 blob 的匯出作業](storage-import-export-data-from-blobs.md)

2. 您有一個現有的 Azure Key Vault，其中有一個金鑰可用來保護您的 BitLocker 金鑰。 若要瞭解如何使用 Azure 入口網站來建立金鑰保存庫，請參閱 [快速入門：使用 Azure 入口網站從 Azure Key Vault 設定和取出秘密](../../key-vault/secrets/quick-create-portal.md)。

    - [虛**刪除**] 和 [不要**清除**] 是在您現有的 Key Vault 上設定的。 這些屬性預設不會啟用。 若要啟用這些屬性，請參閱下列其中一篇文章中標題為 **啟用虛刪除** 和 **啟用清除保護** 的章節：

        - [如何搭配 PowerShell 使用虛刪除](../../key-vault/general/soft-delete-powershell.md)。
        - [如何搭配 CLI 使用虛刪除](../../key-vault/general/soft-delete-cli.md)。
    - 現有的金鑰保存庫應具有2048大小或更多的 RSA 金鑰。 如需有關金鑰的詳細資訊，請參閱[關於 Azure Key Vault 金鑰、秘密和憑證](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)的**Key Vault 金鑰**。
    - Key vault 必須位於與您的資料儲存體帳戶相同的區域中。  
    - 如果您沒有現有的 Azure Key Vault，您也可以建立內嵌，如下一節所述。

## <a name="enable-keys"></a>啟用金鑰

為您的匯入/匯出服務設定客戶管理的金鑰是選擇性的。 根據預設，匯入/匯出服務會使用 Microsoft 管理的金鑰來保護您的 BitLocker 金鑰。 若要在 Azure 入口網站中啟用客戶管理的金鑰，請遵循下列步驟：

1. 移至您匯入作業的 **總覽** 分頁。
2. 在右窗格中，選取 **[選擇加密 BitLocker 金鑰的方式**]。

    ![選擇加密選項](./media/storage-import-export-encryption-key-portal/encryption-key-1.png)

3. 在 [ **加密** ] 分頁中，您可以查看並複製裝置 BitLocker 金鑰。 在 [ **加密類型**] 下，您可以選擇要如何保護 BitLocker 金鑰。 預設會使用 Microsoft 管理的金鑰。

    ![查看 BitLocker 金鑰](./media/storage-import-export-encryption-key-portal/encryption-key-2.png)

4. 您可以選擇指定客戶管理的金鑰。 選取客戶管理的金鑰之後，請 **選取 [金鑰保存庫] 和 [金鑰**]。

    ![選取客戶管理的金鑰](./media/storage-import-export-encryption-key-portal/encryption-key-3.png)

5. 在 Azure Key Vault 分頁的 **Select 機碼** 中，系統會自動填入訂用帳戶。 針對 **金鑰保存庫**，您可以從下拉式清單中選取現有的金鑰保存庫。

    ![選取或建立 Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-4.png)

6. 您也可以選取 [ **建立新** 的] 來建立新的金鑰保存庫。 在 [ **建立金鑰保存庫**] 分頁中，輸入資源群組和金鑰保存庫名稱。 接受所有其他預設值。 選取 [檢閱 + 建立]  。

    ![建立新的 Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-5.png)

7. 檢查與金鑰保存庫相關聯的資訊，然後選取 [ **建立**]。 等候幾分鐘讓金鑰保存庫建立完成。

    ![建立 Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-6.png)

8. 在 Azure Key Vault 的 [ **選取金鑰**] 中，您可以選取現有金鑰保存庫中的金鑰。

9. 如果您已建立新的金鑰保存庫，請選取 [ **建立新** 的] 來建立金鑰。 RSA 金鑰大小可以是2048或更高的版本。

    ![在 Azure Key Vault 中建立新的金鑰](./media/storage-import-export-encryption-key-portal/encryption-key-7.png)

    如果您在建立金鑰保存庫時未啟用虛刪除和清除保護，將會更新金鑰保存庫，以啟用虛刪除和清除保護。

10. 提供金鑰的名稱、接受其他預設值，然後選取 [ **建立**]。

    ![建立新的金鑰](./media/storage-import-export-encryption-key-portal/encryption-key-8.png)

11. 選取 **版本** ，然後選擇 [ **選取**]。 系統會通知您金鑰保存庫中已建立金鑰。

    ![在 key vault 中建立的新金鑰](./media/storage-import-export-encryption-key-portal/encryption-key-9.png)

在 [ **加密** ] 分頁中，您可以看到金鑰保存庫，以及為客戶管理的金鑰選取的金鑰。

> [!IMPORTANT]
> 您只能在匯入/匯出作業的任何階段，停用 Microsoft 管理的金鑰並移至客戶管理的金鑰。 不過，在您建立客戶管理的金鑰後，即無法予以停用。

## <a name="troubleshoot-customer-managed-key-errors"></a>針對客戶管理的金鑰錯誤進行疑難排解

如果您收到與客戶管理金鑰相關的任何錯誤，請使用下表進行疑難排解：

| 錯誤碼     |詳細資料     | 追 討？    |
|----------------|------------|-----------------|
| CmkErrorAccessRevoked | 已撤銷客戶管理金鑰的存取權。                                                       | 是，請檢查： <ol><li>Key vault 仍具有存取原則中的 MSI。</li><li>存取原則已啟用「取得」、「包裝」和「解除包裝」許可權。</li><li>如果金鑰保存庫位於防火牆後方的 VNet 中，請檢查是否已啟用 [ **允許 Microsoft 信任的服務** ]。</li><li>檢查作業資源的 MSI 是否已重設為 `None` 使用 api。<br>如果是，則將值設回 `Identity = SystemAssigned` 。 這會重新建立作業資源的身分識別。<br>建立新的身分識別之後，請 `Get` `Wrap` `Unwrap` 在金鑰保存庫的存取原則中啟用、和許可權至新的身分識別</li></ol>                                                                                            |
| CmkErrorKeyDisabled      | 客戶管理的金鑰已停用。                                         | 是，啟用金鑰版本     |
| CmkErrorKeyNotFound      | 找不到客戶管理的金鑰。 | 是，如果已刪除金鑰，但仍在清除期間內，則會使用 [復原金鑰保存庫金鑰移除](https://docs.microsoft.com/powershell/module/az.keyvault/undo-azkeyvaultkeyremoval)。<br>還 <ol><li>是的，如果客戶已備份金鑰並加以還原。</li><li>否，否則為。</li></ol>
| CmkErrorVaultNotFound |找不到客戶管理金鑰的金鑰保存庫。 |   如果金鑰保存庫已刪除：<ol><li>是的，如果是在清除保護期間，請使用 [復原金鑰保存庫](https://docs.microsoft.com/azure/key-vault/general/soft-delete-powershell#recovering-a-key-vault)的步驟。</li><li>否，如果超出清除保護的持續時間。</li></ol><br>否則，如果金鑰保存庫已遷移至不同的租使用者，則可以使用下列其中一個步驟來復原：<ol><li>將金鑰保存庫還原回舊的租使用者。</li><li>設定 `Identity = None` ，然後將值設回 `Identity = SystemAssigned` 。 這會在新的身分識別建立之後，刪除並重新建立身分識別。 `Get` `Wrap` `Unwrap` 在金鑰保存庫的存取原則中啟用、和許可權至新的身分識別。</li></ol>|

## <a name="next-steps"></a>接下來的步驟

- [什麼是 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)？
