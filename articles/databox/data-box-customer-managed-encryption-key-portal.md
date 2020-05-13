---
title: 使用 Azure 入口網站為 Azure 資料箱設定客戶管理的金鑰
description: 瞭解如何使用 Azure 入口網站，以 Azure 資料箱的 Azure Key Vault 來設定客戶管理的金鑰。 客戶管理的金鑰可讓您建立、輪替、停用及撤銷存取控制。
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 05/07/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: 6f543b3f5c2bb7d4949c431580771c4b0d965e4d
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125298"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-azure-data-box"></a>在 Azure Key Vault 中使用客戶管理的金鑰 Azure 資料箱

Azure 資料箱保護用來透過加密金鑰鎖定裝置的裝置解除鎖定金鑰（也稱為裝置密碼）。 根據預設，會使用 Microsoft 管理的金鑰來加密資料箱訂單的裝置解除鎖定金鑰。 若要對裝置解除鎖定金鑰進行其他控制，您也可以提供客戶管理的金鑰。 

客戶管理的金鑰必須建立並儲存在 Azure Key Vault 中。 如需 Azure Key Vault 的詳細資訊，請參閱[什麼是 Azure Key Vault？](../key-vault/general/overview.md)。

本文說明如何在[Azure 入口網站](https://portal.azure.com/)中使用客戶管理的金鑰搭配 Azure 資料箱。 本文適用于 Azure 資料箱裝置和 Azure Data Box Heavy 裝置。

## <a name="prerequisites"></a>先決條件

在您開始前，請確定：

1. 您已依據[教學課程：訂單 Azure 資料箱](data-box-deploy-ordered.md)中的指示建立 Azure 資料箱順序。

2. 您有現有的 Azure Key Vault，其中含有金鑰，可讓您用來保護裝置的解除鎖定金鑰。 若要瞭解如何使用 Azure 入口網站建立金鑰保存庫，請參閱[快速入門：使用 Azure 入口網站從 Azure Key Vault 設定和取出秘密](../key-vault/secrets/quick-create-portal.md)。

    - 「虛**刪除**」和「不要**清除**」會在您現有的金鑰保存庫上設定。 預設不會啟用這些屬性。 若要啟用這些屬性，請參閱下列其中一篇文章中標題為**啟用虛刪除**和**啟用清除保護**的章節：

        - [如何使用 PowerShell](../key-vault/general/soft-delete-powershell.md)進行虛刪除。
        - [如何使用 CLI](../key-vault/general/soft-delete-cli.md)進行虛刪除。
    - 現有的金鑰保存庫應具有2048大小或更多的 RSA 金鑰。 如需金鑰的詳細資訊，請參閱[關於 Azure Key Vault 金鑰](../key-vault/keys/about-keys.md)。
    - 金鑰保存庫必須位於與用於資料的儲存體帳戶相同的區域中。 您的 Azure 資料箱資源可以連結多個儲存體帳戶。
    - 如果您沒有現有的金鑰保存庫，您也可以依照下一節中的說明，以內嵌方式建立。

## <a name="enable-keys"></a>啟用金鑰

為您的 Azure 資料箱設定客戶管理的金鑰是選擇性的。 根據預設，資料箱會使用 Microsoft 管理的金鑰來保護您的 BitLocker 金鑰。 若要在 Azure 入口網站中啟用客戶管理的金鑰，請遵循下列步驟：

1. 移至 [**總覽**] 分頁，以取得您的資料箱順序。

    ![資料箱順序的總覽分頁](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-1.png)

2. 移至 [設定] [ **> 加密**]。 在 [**加密類型**] 底下，您可以選擇要如何保護裝置的解除鎖定金鑰。 根據預設，系統會使用 Microsoft 管理的金鑰來保護您的裝置解除鎖定密碼。 

    ![選擇加密選項](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-2.png)

3. 選取 [加密類型] 作為 [**客戶管理的金鑰**]。 選取客戶管理的金鑰之後，請**選取金鑰保存庫和金鑰**。

    ![選取客戶管理的金鑰](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3.png)

5. 在 [**從 Azure Key Vault** ] 分頁的 [選取金鑰] 中，會自動填入訂用帳戶。 針對**金鑰保存庫**，您可以從下拉式清單中選取現有的金鑰保存庫。

    ![建立新的 Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-31.png)

    您也可以選取 [**建立新**的] 來建立新的金鑰保存庫。 在 [**建立金鑰保存庫**] 分頁中，輸入資源群組和金鑰保存庫名稱。 請確定已啟用虛**刪除**和**清除保護**。 接受所有其他預設值。 選取 [檢閱 + 建立]  。

    ![建立新的 Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-4.png)

7. 檢查與您的金鑰保存庫相關聯的資訊，然後選取 [**建立**]。 等候幾分鐘的時間，讓金鑰保存庫建立完成。

    ![建立 Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-5.png)

8. 在 [**從 Azure Key Vault 選取金鑰**] 中，您可以選取現有金鑰保存庫中的金鑰。

    ![在 Azure Key Vault 中建立新的金鑰](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6.png)

9. 如果您想要建立新的金鑰，請選取 [**建立新**的] 以建立金鑰。 RSA 金鑰大小可以是2048或更高。

    ![在 Azure Key Vault 中建立新的金鑰](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-61.png)

10. 提供您的金鑰名稱、接受其他預設值，然後選取 [**建立**]。 

    ![建立新的金鑰](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-7.png)


11. 系統會通知您在金鑰保存庫中建立金鑰。 選取**版本**，然後選擇 [**選取**]。

    ![金鑰保存庫中建立的新金鑰](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8.png)

12. 在 [**加密類型**] 窗格中，您可以看到金鑰保存庫，以及為客戶管理的金鑰選取的金鑰。

    ![客戶管理的金鑰的金鑰和金鑰保存庫](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-9.png)

13. 儲存金鑰。 

    ![儲存客戶管理的金鑰](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-10.png)

    金鑰 URL 會顯示在 [**加密類型**] 之下。

    ![客戶管理的金鑰 URL](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-11.png)

> [!IMPORTANT]
> 您可以在資料箱訂單的任何階段停用 Microsoft 管理的金鑰，並移至客戶管理的金鑰。 不過，一旦您建立了客戶管理的金鑰，就無法切換回 Microsoft 管理的金鑰。

## <a name="troubleshoot-errors"></a>針對錯誤進行疑難排解

如果您收到與客戶管理的金鑰相關的任何錯誤，請使用下表進行疑難排解。

| 錯誤碼| 錯誤訊息| 詳細資料|
|-------------|--------------|---------|
| SsemUserErrorEncryptionKeyDisabled| 因為客戶管理的金鑰已停用，所以無法提取金鑰。| 是，藉由啟用金鑰版本。|
| SsemUserErrorEncryptionKeyExpired| 因為客戶管理的金鑰已過期，所以無法提取金鑰。| 是，藉由啟用金鑰版本。|
| SsemUserErrorKeyDetailsNotFound| 因為找不到客戶管理的金鑰，所以無法提取金鑰。| 如果您已刪除金鑰保存庫，則無法復原客戶管理的金鑰。  如果您已將金鑰保存庫遷移至不同的租使用者，請參閱在[訂用帳戶移動之後變更金鑰保存庫租使用者識別碼](https://docs.microsoft.com/azure/key-vault/key-vault-subscription-move-fix)。 如果您已刪除金鑰保存庫：<ol><li>是，如果是在清除保護期間，請使用[復原金鑰保存庫](https://docs.microsoft.com/azure/key-vault/general/soft-delete-powershell#recovering-a-key-vault)中的步驟。</li><li>否（如果超過清除保護期間）。</li></ol><br>否則，如果金鑰保存庫進行租使用者遷移，可以使用下列其中一個步驟來復原： <ol><li>將金鑰保存庫還原回舊的租使用者。</li><li>設定 `Identity = None` ，然後將值設定回 `Identity = SystemAssigned` 。 這會在建立新的身分識別之後，刪除並重新建立身分識別。 針對 `Get` `Wrap` `Unwrap` 金鑰保存庫的存取原則中的新身分識別啟用、和許可權。</li></ol> |
| SsemUserErrorKeyVaultBadRequestException| 因為已撤銷客戶管理的金鑰存取權，所以無法提取金鑰。| 是，請檢查： <ol><li>金鑰保存庫在存取原則中仍有 MSI。</li><li>存取原則提供了取得、包裝、解除封裝的許可權。</li><li>如果 key vault 位於防火牆後方的 vNet 中，請檢查是否已啟用 [**允許 Microsoft 信任的服務**]。</li></ol>|
| SsemUserErrorKeyVaultDetailsNotFound| 因為找不到客戶管理的金鑰相關聯的金鑰保存庫，所以無法提取金鑰。 | 如果您已刪除金鑰保存庫，則無法復原客戶管理的金鑰。  如果您已將金鑰保存庫遷移至不同的租使用者，請參閱在[訂用帳戶移動之後變更金鑰保存庫租使用者識別碼](https://docs.microsoft.com/azure/key-vault/key-vault-subscription-move-fix)。 如果您已刪除金鑰保存庫：<ol><li>是，如果是在清除保護期間，請使用[復原金鑰保存庫](https://docs.microsoft.com/azure/key-vault/general/soft-delete-powershell#recovering-a-key-vault)中的步驟。</li><li>否（如果超過清除保護期間）。</li></ol><br>否則，如果金鑰保存庫進行租使用者遷移，可以使用下列其中一個步驟來復原： <ol><li>將金鑰保存庫還原回舊的租使用者。</li><li>設定 `Identity = None` ，然後將值設定回 `Identity = SystemAssigned` 。 這會在建立新的身分識別之後，刪除並重新建立身分識別。 針對 `Get` `Wrap` `Unwrap` 金鑰保存庫的存取原則中的新身分識別啟用、和許可權。</li></ol> |
| SsemUserErrorSystemAssignedIdentityAbsent  | 因為找不到客戶管理的金鑰，所以無法提取金鑰。| 是，請檢查： <ol><li>金鑰保存庫在存取原則中仍有 MSI。</li><li>身分識別是系統指派的類型。</li><li>針對金鑰保存庫的存取原則中的身分識別啟用 Get、Wrap 和解除包裝許可權。</li></ol>|
| 一般錯誤  | 無法提取金鑰。| 這是一般錯誤。 請聯絡 Microsoft 支援服務以疑難排解錯誤，並決定後續步驟。|


## <a name="next-steps"></a>後續步驟

- [什麼是 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)？