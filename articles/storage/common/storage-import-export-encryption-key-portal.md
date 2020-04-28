---
title: 使用 Azure 入口網站來設定匯入/匯出服務的客戶管理金鑰
description: 瞭解如何透過 Azure 匯入/匯出服務的 Azure Key Vault，使用 Azure 入口網站來設定客戶管理的金鑰。 客戶管理的金鑰可讓您建立、輪替、停用及撤銷存取控制。
services: storage
author: alkohli
ms.service: storage
ms.topic: how-to
ms.date: 03/12/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: d3e4535c05ef077d14ef74310459a84af0f02fd5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "82176323"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-importexport-service"></a>在匯入/匯出服務的 Azure Key Vault 中使用客戶管理的金鑰

Azure 匯入/匯出會保護用來透過加密金鑰鎖定磁片磁碟機的 BitLocker 金鑰。 根據預設，BitLocker 金鑰會使用 Microsoft 管理的金鑰進行加密。 若要進一步控制加密金鑰，您也可以提供客戶管理的金鑰。

客戶管理的金鑰必須建立並儲存在 Azure Key Vault 中。 如需 Azure Key Vault 的詳細資訊，請參閱[什麼是 Azure Key Vault？](../../key-vault/general/overview.md)

本文說明如何在[Azure 入口網站](https://portal.azure.com/)中使用客戶管理的金鑰搭配匯入/匯出服務。

## <a name="prerequisites"></a>Prerequisites

在您開始前，請確定：

1. 您已依據中的指示建立匯入或匯出工作：

    - [建立 blob 的匯入作業](storage-import-export-data-to-blobs.md)。
    - 建立檔案的匯[入作業](storage-import-export-data-to-files.md)。
    - [建立 blob 的匯出作業](storage-import-export-data-from-blobs.md)

2. 您有現有的 Azure Key Vault，其中含有金鑰，可用來保護您的 BitLocker 金鑰。 若要瞭解如何使用 Azure 入口網站建立金鑰保存庫，請參閱[快速入門：使用 Azure 入口網站從 Azure Key Vault 設定和取出秘密](../../key-vault/secrets/quick-create-portal.md)。

    - 「虛**刪除**」和「不要**清除**」會在您現有的 Key Vault 上設定。 預設不會啟用這些屬性。 若要啟用這些屬性，請參閱下列其中一篇文章中標題為**啟用虛刪除**和**啟用清除保護**的章節：

        - [如何使用 PowerShell](../../key-vault/general/soft-delete-powershell.md)進行虛刪除。
        - [如何使用 CLI](../../key-vault/general/soft-delete-cli.md)進行虛刪除。
    - 現有的金鑰保存庫應具有2048大小或更多的 RSA 金鑰。 如需金鑰的詳細資訊，請參閱[關於 Azure Key Vault 金鑰、秘密和憑證](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)中的**Key Vault 金鑰**。
    - 金鑰保存庫必須位於與您的資料儲存體帳戶相同的區域中。  
    - 如果您沒有現有的 Azure Key Vault，您也可以依照下一節中的說明，以內嵌方式建立。

## <a name="enable-keys"></a>啟用金鑰

為您的匯入/匯出服務設定客戶管理的金鑰是選擇性的。 根據預設，匯入/匯出服務會使用 Microsoft 管理的金鑰來保護您的 BitLocker 金鑰。 若要在 Azure 入口網站中啟用客戶管理的金鑰，請遵循下列步驟：

1. 移至您匯入作業的 [**總覽**] 分頁。
2. 在右窗格中，選取 **[選擇您的 BitLocker 金鑰如何加密**]。

    ![選擇加密選項](./media/storage-import-export-encryption-key-portal/encryption-key-1.png)

3. 在 [**加密**] 分頁中，您可以查看並複製裝置 BitLocker 金鑰。 在 [**加密類型**] 底下，您可以選擇要如何保護 BitLocker 金鑰。 根據預設，會使用 Microsoft 管理的金鑰。

    ![查看 BitLocker 金鑰](./media/storage-import-export-encryption-key-portal/encryption-key-2.png)

4. 您可以選擇指定客戶管理的金鑰。 選取客戶管理的金鑰之後，請**選取 [金鑰保存庫] 和 [金鑰**]。

    ![選取客戶管理的金鑰](./media/storage-import-export-encryption-key-portal/encryption-key-3.png)

5. 在 [**從 Azure Key Vault** ] 分頁的 [選取金鑰] 中，會自動填入訂用帳戶。 針對**金鑰保存庫**，您可以從下拉式清單中選取現有的金鑰保存庫。

    ![選取或建立 Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-4.png)

6. 您也可以選取 [**建立新**的] 來建立新的金鑰保存庫。 在 [**建立金鑰保存庫**] 分頁中，輸入資源群組和金鑰保存庫名稱。 接受所有其他預設值。 選取 [檢閱 + 建立]  。

    ![建立新的 Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-5.png)

7. 檢查與您的金鑰保存庫相關聯的資訊，然後選取 [**建立**]。 等候幾分鐘的時間，讓金鑰保存庫建立完成。

    ![建立 Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-6.png)

8. 在 [**從 Azure Key Vault 選取金鑰**] 中，您可以選取現有金鑰保存庫中的金鑰。

9. 如果您已建立新的金鑰保存庫，請選取 [**建立新**的] 來建立金鑰。 RSA 金鑰大小可以是2048或更高。

    ![在 Azure Key Vault 中建立新的金鑰](./media/storage-import-export-encryption-key-portal/encryption-key-7.png)

    當您建立金鑰保存庫時，如果未啟用「虛刪除」和「清除」保護，將會更新金鑰保存庫，以啟用虛刪除和清除保護。

10. 提供您的金鑰名稱、接受其他預設值，然後選取 [**建立**]。

    ![建立新的金鑰](./media/storage-import-export-encryption-key-portal/encryption-key-8.png)

11. 選取**版本**，然後選擇 [**選取**]。 系統會通知您在金鑰保存庫中建立金鑰。

    ![金鑰保存庫中建立的新金鑰](./media/storage-import-export-encryption-key-portal/encryption-key-9.png)

在 [**加密**] 分頁中，您可以看到金鑰保存庫，以及為您的客戶管理的金鑰選取的金鑰。

## <a name="disable-keys"></a>停用金鑰

您只能在匯入/匯出作業的任何階段停用 Microsoft 管理的金鑰，並移至客戶管理的金鑰。 不過，一旦您建立了客戶管理的金鑰，就無法加以停用。

## <a name="troubleshoot-customer-managed-key-errors"></a>針對客戶受控金鑰錯誤進行疑難排解

如果您收到與客戶管理的金鑰相關的任何錯誤，請使用下表來進行疑難排解：

| 錯誤碼     |詳細資料     | 升高?    |
|----------------|------------|-----------------|
| CmkErrorAccessRevoked | 已套用客戶管理的金鑰，但目前已撤銷金鑰存取權。 如需詳細資訊，請參閱如何[啟用金鑰存取](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy)。                                                      | 是，請檢查： <ol><li>金鑰保存庫在存取原則中仍有 MSI。</li><li>存取原則提供了取得、包裝、解除封裝的許可權。</li><li>如果 key vault 位於防火牆後方的 vNet 中，請檢查是否已啟用 [**允許 Microsoft 信任的服務**]。</li></ol>                                                                                            |
| CmkErrorKeyDisabled      | 已套用客戶管理的金鑰，但金鑰已停用。 如需詳細資訊，請參閱如何[啟用金鑰](https://docs.microsoft.com/rest/api/keyvault/vaults/createorupdate)。                                                                             | 是，啟用金鑰版本     |
| CmkErrorKeyNotFound      | 已套用客戶管理的金鑰，但找不到與金鑰相關聯的金鑰保存庫。<br>如果您已刪除金鑰保存庫，則無法復原客戶管理的金鑰。  如果您已將金鑰保存庫遷移至不同的租使用者，請參閱在[訂用帳戶移動之後變更金鑰保存庫租使用者識別碼](https://docs.microsoft.com/azure/key-vault/key-vault-subscription-move-fix)。 |   如果您已刪除金鑰保存庫：<ol><li>是，如果是在清除保護期間，請使用[復原金鑰保存庫](https://docs.microsoft.com/azure/key-vault/general/soft-delete-powershell#recovering-a-key-vault)中的步驟。</li><li>否（如果超過清除保護期間）。</li></ol><br>否則，如果金鑰保存庫進行租使用者遷移，可以使用下列其中一個步驟來復原： <ol><li>將金鑰保存庫還原回舊的租使用者。</li><li>設定`Identity = None` ，然後將值設定回`Identity = SystemAssigned`。 這會在建立新的身分識別之後，刪除並重新建立身分識別。 針對`Get`金鑰`Wrap`保存庫`Unwrap`的存取原則中的新身分識別啟用、和許可權。</li></ol>|

## <a name="next-steps"></a>後續步驟

- [什麼是 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)？
