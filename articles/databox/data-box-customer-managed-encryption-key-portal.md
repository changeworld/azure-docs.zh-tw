---
title: 使用 Azure 入口網站管理客戶管理的金鑰 Azure 資料箱
description: 瞭解如何使用 Azure 入口網站，透過 Azure Key Vault Azure 資料箱來建立和管理客戶管理的金鑰。 客戶管理的金鑰可讓您建立、輪替、停用及撤銷存取控制。
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 11/19/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: f75907dc1fa079cebb3b80874090c658fd7b8174
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/27/2020
ms.locfileid: "96302829"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-azure-data-box"></a>在 Azure Key Vault 中使用客戶管理的金鑰 Azure 資料箱

Azure 資料箱保護裝置的解除鎖定金鑰 (也稱為裝置密碼) ，它會透過加密金鑰用來鎖定裝置。 根據預設，此加密金鑰是 Microsoft 管理的金鑰。 若要進行其他控制，您可以使用客戶管理的金鑰。

使用客戶管理的金鑰不會影響裝置上的資料加密的方式。 它只會影響裝置解除鎖定金鑰加密的方式。

若要在訂單程式中保持此控制層級，請在建立訂單時使用客戶管理的金鑰。 如需詳細資訊，請參閱 [教學課程：順序 Azure 資料箱](data-box-deploy-ordered.md)。

本文說明如何在 [Azure 入口網站](https://portal.azure.com/)中為您現有的資料箱訂單啟用客戶管理的金鑰。 您將瞭解如何變更目前客戶管理金鑰的金鑰保存庫、金鑰、版本或身分識別，或切換回使用 Microsoft 管理的金鑰。

本文適用于 Azure 資料箱和 Azure Data Box Heavy 裝置。

## <a name="requirements"></a>規格需求

適用于資料箱訂單的客戶管理金鑰必須符合下列需求：

- 您必須建立金鑰，並將其儲存在具有虛 **刪除** 且未啟用 **清除** 的 Azure Key Vault 中。 如需詳細資訊，請參閱 [什麼是 Azure 金鑰保存庫？](../key-vault/general/overview.md)。 您可以在建立或更新訂單時建立金鑰保存庫和金鑰。

- 金鑰必須是2048大小或更大的 RSA 金鑰。

## <a name="enable-key"></a>啟用金鑰

若要在 Azure 入口網站中為您現有的資料箱訂單啟用客戶管理的金鑰，請遵循下列步驟：

1. 移至您的資料箱訂單的 **總覽** 畫面。

    ![資料箱訂單的總覽畫面-1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-1.png)

2. 移至 [ **設定 > 加密**]，然後選取 [ **客戶管理的金鑰**]。 然後選取 [ **選取金鑰和金鑰保存庫**]。

    ![選取客戶管理的金鑰加密選項](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3.png)

   在 [ **從 Azure Key Vault 選取金鑰** ] 畫面上，會自動填入您的訂用帳戶。

 3. 針對 **金鑰保存庫**，您可以從下拉式清單中選取現有的金鑰保存庫，或選取 [ **建立新** 的金鑰保存庫] 並建立新的金鑰保存庫。

     ![選取客戶管理的金鑰時的金鑰保存庫選項](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3-a.png)

     若要建立新的金鑰保存庫，請在 [ **建立新的金鑰保存庫** ] 畫面上輸入訂用帳戶、資源群組、金鑰保存庫名稱和其他資訊。 在 [復原 **選項**] 中，確定已啟用虛 **刪除** 和 **清除保護** 。 然後選取 [檢閱 + 建立]。

      ![複習和建立 Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-4.png)

      檢查金鑰保存庫的資訊，然後選取 [ **建立**]。 等候幾分鐘的時間來完成金鑰保存庫的建立。

       ![使用您的設定建立 Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-5.png)

4. 在 [ **從 Azure Key Vault 選取金鑰** ] 畫面上，您可以從金鑰保存庫選取現有的金鑰，或建立一個新的金鑰。

    ![從 Azure Key Vault 選取金鑰](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6.png)

   如果您想要建立新的金鑰，請選取 [ **建立新** 的]。 您必須使用 RSA 金鑰。 大小可以是2048或更大。

    ![在 Azure Key Vault 中建立新的金鑰](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6-a.png)

    輸入新金鑰的名稱、接受其他預設值，然後選取 [ **建立**]。 系統會通知您金鑰保存庫中已建立金鑰。

    ![命名新的金鑰](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-7.png)

5. 在 [ **版本**] 中，您可以從下拉式清單中選取現有的金鑰版本。

    ![選取新金鑰的版本](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8.png)

    如果您想要產生新的金鑰版本，請選取 [ **建立新** 的]。

    ![開啟對話方塊來建立新的金鑰版本](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-a.png)

    選擇新金鑰版本的設定，然後選取 [ **建立**]。

    ![建立新的金鑰版本](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-b.png)

6. 當您選取金鑰保存庫、金鑰和金鑰版本時，請選擇 [ **選取**]。

    ![Azure Key Vault 中的索引鍵](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-c.png)

    **加密類型** 設定會顯示您所選擇的金鑰保存庫和金鑰。

    ![客戶管理金鑰的金鑰和金鑰保存庫](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-9.png)

7. 選取用來管理此資源之客戶管理金鑰的身分識別類型。 您可以使用在訂單建立期間產生的 **系統指派** 身分識別，或選擇使用者指派的身分識別。

    使用者指派的身分識別是一種獨立的資源，可讓您用來管理資源的存取權。 如需詳細資訊，請參閱 [受控識別類型](/azure/active-directory/managed-identities-azure-resources/overview)。

    ![選取身分識別類型](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-13.png)

    若要指派使用者識別，請選取 [ **使用者指派**]。 然後選取 [ **選取使用者識別**]，然後選取您想要使用的受控識別。

    ![選取要使用的身分識別](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-14.png)

    您無法在這裡建立新的使用者身分識別。 若要瞭解如何建立一個，請參閱 [使用 Azure 入口網站建立、列出、刪除或指派角色給使用者指派的受控識別](/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal)。

    選取的使用者身分識別會顯示在 [ **加密類型** ] 設定中。

    ![在 [加密類型設定] 中顯示所選的使用者身分識別](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-15.png)

 9. 選取 [ **儲存** ] 以儲存已更新的 **加密類型** 設定。

     ![儲存客戶管理的金鑰](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-10.png)

    金鑰 URL 會顯示在 [ **加密類型**] 之下。

    ![客戶管理的金鑰 URL](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-11.png)<!--Probably need new screen from recent order. Can you provide one? I can't create an order using CMK with the subscription I'm using.-->

## <a name="change-key"></a>變更金鑰

若要變更您目前所使用之客戶管理金鑰的金鑰保存庫、金鑰和/或金鑰版本，請遵循下列步驟：

1. 在您的資料箱訂單的總覽畫面上，移至 **[** **設定**  >  **加密**]，然後按一下 [**變更金鑰**]。

    ![使用客戶管理的金鑰的資料箱訂單的總覽畫面-1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-16.png)

2. 選擇 [ **選取不同的金鑰保存庫和金鑰**]。

    ![資料箱訂單的總覽畫面，選取不同的金鑰和金鑰保存庫選項](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-16-a.png)

3. **Key vault 中的 [選取金鑰**] 畫面會顯示訂用帳戶，但不會顯示金鑰保存庫、金鑰或金鑰版本。 您可以進行下列任何變更：

   - 從相同的金鑰保存庫選取不同的金鑰。 選取金鑰和版本之前，您必須先選取金鑰保存庫。

   - 選取不同的金鑰保存庫，並指派新的金鑰。

   - 變更目前金鑰的版本。
   
    當您完成變更時，請選擇 [ **選取**]。

    ![選擇加密選項-2](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17.png)

4. 選取 [儲存]。

    ![儲存更新的加密設定-1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17-a.png)

## <a name="change-identity"></a>變更身分識別

若要變更用來管理此訂單之客戶管理金鑰存取權的身分識別，請遵循下列步驟：

1. 在您已完成的資料箱訂單的總覽畫面上，移至 **[** **設定**  >  **加密**]。

2. 進行下列其中一項變更：

     - 若要變更為不同的使用者身分識別，請按一下 [ **選取不同的使用者身分識別**]。 然後，在畫面右邊的面板中選取不同的身分識別，然後選擇 [ **選取**]。

       ![變更客戶管理之金鑰的使用者指派身分識別的選項](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-18.png)

   - 若要切換至在建立訂單期間產生的系統指派身分識別，請選取 [由 **選取識別類型****指派的系統**]。

     ![針對客戶管理的金鑰變更為系統指派的選項](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-19.png)

3. 選取 [儲存]。

    ![儲存已更新的加密設定-2](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17-a.png)

## <a name="use-microsoft-managed-key"></a>使用 Microsoft 管理的金鑰

若要使用客戶管理的金鑰變更為您訂單的 Microsoft 受控金鑰，請遵循下列步驟：

1. 在您已完成的資料箱訂單的總覽畫面上，移至 **[** **設定**  >  **加密**]。

2. 選取 [ **類型**]，選取 [ **Microsoft 管理的金鑰**]。

    ![資料箱訂單-5 的總覽畫面](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-20.png)

3. 選取 [儲存]。

    ![儲存 Microsoft 管理金鑰的已更新加密設定](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-21.png)

## <a name="troubleshoot-errors"></a>針對錯誤進行疑難排解

如果您收到與客戶管理金鑰相關的任何錯誤，請使用下表來進行疑難排解。

| 錯誤碼| 錯誤詳細資料| 追 討？|
|-------------|--------------|---------|
| SsemUserErrorEncryptionKeyDisabled| 因為已停用客戶管理的金鑰，所以無法提取金鑰。| 是，啟用金鑰版本。|
| SsemUserErrorEncryptionKeyExpired| 因為客戶管理的金鑰已過期，所以無法提取金鑰。| 是，啟用金鑰版本。|
| SsemUserErrorKeyDetailsNotFound| 因為找不到客戶管理的金鑰，所以無法提取金鑰。| 如果您刪除了金鑰保存庫，則無法復原客戶管理的金鑰。  如果您將金鑰保存庫遷移至不同的租使用者，請參閱在 [訂用帳戶移動之後變更金鑰保存庫租使用者識別碼](../key-vault/general/move-subscription.md)。 如果您刪除了金鑰保存庫：<ol><li>是的，如果是在清除保護期間，請使用 [復原金鑰保存庫](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault)的步驟。</li><li>否，如果超出清除保護的持續時間。</li></ol><br>否則，如果金鑰保存庫進行了租使用者遷移，則可以使用下列其中一個步驟來復原： <ol><li>將金鑰保存庫還原回舊的租使用者。</li><li>設定 `Identity = None` ，然後將值設回 `Identity = SystemAssigned` 。 這會在新的身分識別建立之後，刪除並重新建立身分識別。 `Get` `Wrap` `Unwrap` 在金鑰保存庫的存取原則中啟用、和許可權至新的身分識別。</li></ol> |
| SsemUserErrorKeyVaultBadRequestException | 已套用客戶管理的金鑰，但未授與或撤銷金鑰存取權，或因為啟用防火牆而無法存取金鑰保存庫。 | 將選取的身分識別新增至金鑰保存庫，以允許存取客戶管理的金鑰。 如果金鑰保存庫已啟用防火牆，請切換至系統指派的身分識別，然後新增客戶管理的金鑰。 如需詳細資訊，請參閱如何 [啟用金鑰](#enable-key)。 |
| SsemUserErrorKeyVaultDetailsNotFound| 因為找不到客戶管理的金鑰相關聯的金鑰保存庫，所以無法提取金鑰。 | 如果您刪除了金鑰保存庫，則無法復原客戶管理的金鑰。  如果您將金鑰保存庫遷移至不同的租使用者，請參閱在 [訂用帳戶移動之後變更金鑰保存庫租使用者識別碼](../key-vault/general/move-subscription.md)。 如果您刪除了金鑰保存庫：<ol><li>是的，如果是在清除保護期間，請使用 [復原金鑰保存庫](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault)的步驟。</li><li>否，如果超出清除保護的持續時間。</li></ol><br>否則，如果金鑰保存庫進行了租使用者遷移，則可以使用下列其中一個步驟來復原： <ol><li>將金鑰保存庫還原回舊的租使用者。</li><li>設定 `Identity = None` ，然後將值設回 `Identity = SystemAssigned` 。 這會在新的身分識別建立之後，刪除並重新建立身分識別。 `Get` `Wrap` `Unwrap` 在金鑰保存庫的存取原則中啟用、和許可權至新的身分識別。</li></ol> |
| SsemUserErrorSystemAssignedIdentityAbsent  | 因為找不到客戶管理的金鑰，所以無法提取金鑰。| 是，請檢查： <ol><li>Key vault 仍具有存取原則中的 MSI。</li><li>身分識別是系統指派的類型。</li><li>在金鑰保存庫的存取原則中啟用身分識別的取得、包裝和解除包裝許可權。</li></ol>|
| SsemUserErrorUserAssignedLimitReached | 新增使用者指派的身分識別失敗，因為您已達到可新增的使用者指派身分識別總數上限。 | 請在重試之前，以較少的使用者身分識別來重試作業，或從資源移除一些使用者指派的身分識別。 |
| SsemUserErrorCrossTenantIdentityAccessForbidden | 受控識別存取作業失敗。 <br> 注意：這適用于將訂用帳戶移到不同租使用者的案例。 客戶必須手動將身分識別移至新的租使用者。 PFA mail 以取得詳細資料。 | 請將選取的身分識別移至出現訂用帳戶的新租使用者。 如需詳細資訊，請參閱如何 [啟用金鑰](#enable-key)。 |
| SsemUserErrorKekUserIdentityNotFound | 已套用客戶管理的金鑰，但在 active directory 中找不到具有金鑰存取權的使用者指派身分識別。 <br> 注意：這適用于從 Azure 刪除使用者身分識別的情況。| 請嘗試將其他使用者指派的身分識別新增至您的金鑰保存庫，以啟用客戶管理金鑰的存取權。 如需詳細資訊，請參閱如何 [啟用金鑰](#enable-key)。 |
| SsemUserErrorUserAssignedIdentityAbsent | 因為找不到客戶管理的金鑰，所以無法提取金鑰。 | 無法存取客戶管理的金鑰。 使用者指派的身分識別 (與該金鑰相關聯的 UAI) 已刪除，或 UAI 類型已變更。 |
| SsemUserErrorCrossTenantIdentityAccessForbidden | 受控識別存取作業失敗。 <br> 注意：這適用于將訂用帳戶移到不同租使用者的案例。 客戶必須手動將身分識別移至新的租使用者。 PFA mail 以取得詳細資料。 | 請嘗試將其他使用者指派的身分識別新增至您的金鑰保存庫，以啟用客戶管理金鑰的存取權。 如需詳細資訊，請參閱如何 [啟用金鑰](#enable-key)。|
| SsemUserErrorKeyVaultBadRequestException | 已套用客戶管理的金鑰，但未授與或撤銷金鑰存取權，或因為啟用防火牆而無法存取金鑰保存庫。 | 將選取的身分識別新增至金鑰保存庫，以允許存取客戶管理的金鑰。 如果金鑰保存庫已啟用防火牆，請切換至系統指派的身分識別，然後新增客戶管理的金鑰。 如需詳細資訊，請參閱如何 [啟用金鑰](#enable-key)。 |
| 一般錯誤  | 無法提取金鑰。| 這是一般錯誤。 請聯絡 Microsoft 支援服務以針對錯誤進行疑難排解，並決定後續步驟。|

## <a name="next-steps"></a>後續步驟

- [什麼是 Azure 金鑰保存庫？](../key-vault/general/overview.md)
- [快速入門：使用 Azure 入口網站從 Azure Key Vault 設定及擷取祕密](../key-vault/secrets/quick-create-portal.md)
