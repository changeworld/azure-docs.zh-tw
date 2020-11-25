---
title: 使用 Azure 入口網站設定客戶管理的金鑰
titleSuffix: Cognitive Services
description: 瞭解如何使用 Azure 入口網站，透過 Azure Key Vault 來設定客戶管理的金鑰。 客戶管理的金鑰可讓您建立、輪替、停用及撤銷存取控制。
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 05/28/2020
ms.author: egeaney
ms.openlocfilehash: 17dff6056564b421f0741f55ce8171f3251c94c9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028534"
---
## <a name="customer-managed-keys-with-azure-key-vault"></a>客戶管理的金鑰與 Azure Key Vault

您必須使用 Azure Key Vault 儲存客戶管理的金鑰。 您可以建立自己的金鑰並將其儲存在金鑰保存庫中，或是使用 Azure Key Vault API 來產生金鑰。 認知服務資源和金鑰保存庫必須位於相同的區域中，且在相同的 Azure Active Directory (Azure AD) 租使用者中，但它們可以在不同的訂用帳戶中。 如需 Azure Key Vault 的詳細資訊，請參閱 [什麼是 Azure Key Vault？](../../key-vault/general/overview.md)。

建立新的認知服務資源時，一律會使用 Microsoft 管理的金鑰來加密。 建立資源時，無法啟用客戶管理的金鑰。 客戶管理的金鑰會儲存在 Azure Key Vault 中，而金鑰保存庫必須布建存取原則，以將金鑰許可權授與與認知服務資源相關聯的受控識別。 只有在使用 CMK 所需的定價層建立資源之後，才可以使用受控識別。

啟用客戶管理的金鑰也會啟用系統指派的 [受控識別](../../active-directory/managed-identities-azure-resources/overview.md)，這是 Azure AD 的功能。 一旦啟用系統指派的受控識別，此資源就會向 Azure Active Directory 註冊。 註冊之後，受控識別將會獲得在客戶管理的金鑰設定期間選取的 Key Vault 存取權。 

> [!IMPORTANT]
> 如果您停用系統指派的受控識別，將會移除對金鑰保存庫的存取權，而且將無法再存取使用客戶金鑰加密的任何資料。 相依于此資料的任何功能將會停止運作。

> [!IMPORTANT]
> 受控識別目前不支援跨目錄案例。 當您在 Azure 入口網站中設定客戶管理的金鑰時，系統會自動在幕後指派受控識別。 如果您之後將訂用帳戶、資源群組或資源從某個 Azure AD 目錄移至另一個目錄，與該資源相關聯的受控識別不會傳送至新的租使用者，因此客戶管理的金鑰可能無法再運作。 如需詳細資訊，請參閱在 [Azure 資源受控識別的常見問題和已知問題](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)中，**傳輸 Azure AD 目錄之間的訂** 用帳戶。  

## <a name="configure-azure-key-vault"></a>設定 Azure Key Vault

使用客戶管理的金鑰時，需要在金鑰保存庫、虛 **刪除** 和 **不清除** 中設定兩個屬性。 這些屬性預設不會啟用，但可使用 PowerShell 或 Azure CLI 在新的或現有的金鑰保存庫上啟用。

> [!IMPORTANT]
> 如果您沒有虛 **刪除** ，且未啟用 [ **清除** 屬性]，而您刪除了金鑰，將無法復原認知服務資源中的資料。

若要了解如何在現有的金鑰保存庫上啟用這些屬性，請參閱下列其中一篇文章中的 **啟用虛刪除** 和 **啟用清除保護** 小節：

- [如何搭配 PowerShell 使用虛刪除](../../key-vault/general/soft-delete-powershell.md)。
- [如何搭配 CLI 使用虛刪除](../../key-vault/general/soft-delete-cli.md)。

Azure 儲存體加密只支援大小為2048的 RSA 金鑰。 如需有關金鑰的詳細資訊，請參閱 [關於 Azure Key Vault 金鑰、秘密和憑證](../../key-vault/general/about-keys-secrets-certificates.md)的 **Key Vault 金鑰**。

## <a name="enable-customer-managed-keys-for-your-resource"></a>為您的資源啟用客戶管理的金鑰

若要在 Azure 入口網站中啟用客戶管理的金鑰，請遵循下列步驟：

1. 流覽至您的認知服務資源。
1. 在認知服務資源的 [ **設定** ] 分頁上，按一下 [ **加密**]。 選取 [ **客戶管理的金鑰** ] 選項，如下圖所示。

    ![顯示如何選取客戶管理的金鑰的螢幕擷取畫面](../media/cognitive-services-encryption/selectcmk.png)

## <a name="specify-a-key"></a>指定金鑰

啟用客戶管理的金鑰之後，您將有機會指定與認知服務資源相關聯的金鑰。

### <a name="specify-a-key-as-a-uri"></a>以 URI 形式指定金鑰

若要將金鑰指定為 URI，請遵循下列步驟：

1. 若要在 Azure 入口網站中找出金鑰 URI，請流覽至您的金鑰保存庫，然後選取 [ **金鑰** ] 設定。 選取所需的金鑰，然後按一下金鑰以查看其版本。 選取金鑰版本以查看該版本的設定。
1. 複製 [ **金鑰識別碼** ] 欄位的值，以提供 URI。

    ![顯示 key vault 金鑰 URI 的螢幕擷取畫面](../media/cognitive-services-encryption/key-uri-portal.png)

1. 在儲存體帳戶的 **加密** 設定中，選擇 [ **輸入金鑰 URI** ] 選項。
1. 將您複製的 URI 貼到 [ **金鑰 URI** ] 欄位中。

   ![顯示如何輸入金鑰 URI 的螢幕擷取畫面](../media/cognitive-services-encryption/ssecmk2.png)

1. 指定包含金鑰保存庫的訂用帳戶。
1. 儲存您的變更。

### <a name="specify-a-key-from-a-key-vault"></a>從金鑰保存庫指定金鑰

若要指定金鑰保存庫中的金鑰，請先確定您有包含金鑰的金鑰保存庫。 若要指定金鑰保存庫中的金鑰，請遵循下列步驟：

1. 選擇 [從 Key Vault 選取] 選項。
1. 選取包含您要使用之金鑰的金鑰保存庫。
1. 選取金鑰保存庫中的金鑰。

   ![顯示客戶管理的金鑰選項的螢幕擷取畫面](../media/cognitive-services-encryption/ssecmk3.png)

1. 儲存您的變更。

## <a name="update-the-key-version"></a>更新金鑰版本

當您建立新版本的金鑰時，請更新認知服務資源，以使用新的版本。 請遵循下列步驟：

1. 流覽至您的認知服務資源，並顯示 **加密** 設定。
1. 輸入新金鑰版本的 URI。 或者，您可以再次選取金鑰保存庫和金鑰，以更新版本。
1. 儲存您的變更。

## <a name="use-a-different-key"></a>使用不同的金鑰

若要變更加密所使用的金鑰，請遵循下列步驟：

1. 流覽至您的認知服務資源，並顯示 **加密** 設定。
1. 輸入新金鑰的 URI。 或者，您也可以選取金鑰保存庫，然後選擇新的金鑰。
1. 儲存您的變更。

## <a name="rotate-customer-managed-keys"></a>輪替客戶管理的金鑰

您可以根據您的合規性原則，在 Azure Key Vault 中輪替客戶管理的金鑰。 輪替金鑰時，您必須更新認知服務資源，以使用新的金鑰 URI。 若要瞭解如何更新資源以在 Azure 入口網站中使用新版本的金鑰，請參閱 [更新金鑰版本](#update-the-key-version)。

輪替金鑰並不會觸發資源中資料的重新加密。 使用者不需要採取任何進一步的動作。

## <a name="revoke-access-to-customer-managed-keys"></a>撤銷客戶管理金鑰的存取權

若要撤銷客戶管理金鑰的存取權，請使用 PowerShell 或 Azure CLI。 如需詳細資訊，請參閱 [Azure Key Vault PowerShell](/powershell/module/az.keyvault//) 或 [Azure Key Vault CLI](/cli/azure/keyvault)。 撤銷存取權實際上會封鎖對認知服務資源中所有資料的存取，因為認知服務無法存取加密金鑰。

## <a name="disable-customer-managed-keys"></a>停用客戶管理的金鑰

當您停用客戶管理的金鑰時，您的認知服務資源會使用 Microsoft 管理的金鑰進行加密。 若要停用客戶管理的金鑰，請遵循下列步驟：

1. 流覽至您的認知服務資源，並顯示 **加密** 設定。
1. 取消選取 [ **使用您自己的金鑰** ] 設定旁的核取方塊。
