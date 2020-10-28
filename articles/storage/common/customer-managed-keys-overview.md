---
title: 客戶管理的帳戶加密金鑰
titleSuffix: Azure Storage
description: 您可以使用自己的加密金鑰來保護儲存體帳戶中的資料。 當您指定客戶自控金鑰時，該金鑰會用來保護及控制加密資料所需金鑰的存取權。 客戶自控金鑰可提供更大的彈性來管理存取控制。
services: storage
author: tamram
ms.service: storage
ms.date: 09/15/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 2b474ae184374a2c91dcba15517048556686ec35
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92782224"
---
# <a name="customer-managed-keys-for-azure-storage-encryption"></a>Azure 儲存體加密的客戶管理金鑰

您可以使用自己的加密金鑰來保護儲存體帳戶中的資料。 當您指定客戶自控金鑰時，該金鑰會用來保護及控制加密資料所需金鑰的存取權。 客戶自控金鑰可提供更大的彈性來管理存取控制。

您必須使用 Azure Key Vault 或 Azure Key Vault 受控硬體安全性模型 (HSM)  (preview) 儲存客戶管理的金鑰。 您可以建立自己的金鑰，並將其儲存在金鑰保存庫或受控 HSM 中，也可以使用 Azure Key Vault Api 來產生金鑰。 儲存體帳戶和金鑰保存庫或受控 HSM 必須位於相同的區域中，且在相同的 Azure Active Directory (Azure AD) 租使用者，但它們可以在不同的訂用帳戶中。

如需 Azure Key Vault 的詳細資訊，請參閱 [什麼是 Azure Key Vault？](../../key-vault/general/overview.md)。

> [!NOTE]
> Azure Key Vault 和 Azure Key Vault 受控 HSM 支援相同的 Api 和管理介面進行設定。

## <a name="about-customer-managed-keys"></a>關於客戶管理的金鑰

下圖顯示 Azure 儲存體如何使用 Azure Active Directory 和金鑰保存庫或受控 HSM，以使用客戶管理的金鑰來提出要求：

![此圖顯示客戶管理的金鑰在 Azure 儲存體中的運作方式](media/customer-managed-keys-overview/encryption-customer-managed-keys-diagram.png)

下列清單說明圖表中編號的步驟：

1. Azure Key Vault 系統管理員會將加密金鑰的許可權授與與儲存體帳戶相關聯的受控識別。
2. Azure 儲存體系統管理員會使用客戶管理的金鑰來設定儲存體帳戶的加密。
3. Azure 儲存體使用與儲存體帳戶相關聯的受控識別，透過 Azure Active Directory 驗證對 Azure Key Vault 的存取。
4. Azure 儲存體使用 Azure Key Vault 的客戶金鑰來包裝帳戶加密金鑰。
5. 針對讀取/寫入作業，Azure 儲存體會將要求傳送至 Azure Key Vault 以解除包裝帳戶加密金鑰，以執行加密和解密作業。

## <a name="customer-managed-keys-for-queues-and-tables"></a>客戶管理的佇列和資料表金鑰

當針對儲存體帳戶啟用客戶管理的金鑰時，儲存在佇列和資料表儲存體中的資料不會自動受到客戶管理的金鑰保護。 您可以選擇性地將這些服務設定為在您建立儲存體帳戶時包含在此保護中。

如需有關如何建立支援客戶管理的佇列和資料表金鑰之儲存體帳戶的詳細資訊，請參閱 [建立支援資料表和佇列之客戶管理金鑰的帳戶](account-encryption-key-create.md)。

當針對儲存體帳戶設定客戶管理的金鑰時，Blob 儲存體和 Azure 檔案儲存體中的資料一律會受到客戶管理的金鑰保護。

## <a name="enable-customer-managed-keys-for-a-storage-account"></a>針對儲存體帳戶啟用客戶管理的金鑰

當您設定客戶管理的金鑰時，Azure 儲存體會使用相關聯的金鑰保存庫或受控 HSM 中客戶管理的金鑰，來包裝帳戶的根資料加密金鑰。 啟用客戶管理的金鑰不會影響效能，而且會立即生效。

當您啟用或停用客戶管理的金鑰，或修改金鑰或金鑰版本時，會變更根加密金鑰的保護，但 Azure 儲存體帳戶中的資料不需要重新加密。

客戶管理的金鑰只能在現有的儲存體帳戶上啟用。 金鑰保存庫或受控 HSM 必須設定為將許可權授與與儲存體帳戶相關聯的受控識別。 只有在建立儲存體帳戶之後，才可使用受控識別。

您可以隨時在客戶管理的金鑰和 Microsoft 管理的金鑰之間切換。 如需有關 Microsoft 管理之金鑰的詳細資訊，請參閱 [關於加密金鑰管理](storage-service-encryption.md#about-encryption-key-management)。

若要瞭解如何使用金鑰保存庫中客戶管理的金鑰來設定 Azure 儲存體加密，請參閱 [使用儲存在 Azure Key Vault 中之客戶管理的金鑰來設定加密](customer-managed-keys-configure-key-vault.md)。 若要在受管理的 HSM 中設定客戶管理的金鑰，請參閱 [使用儲存在 Azure Key Vault 受控 hsm (preview) 中的客戶管理金鑰來設定加密 ](customer-managed-keys-configure-key-vault-hsm.md)。

> [!IMPORTANT]
> 客戶管理的金鑰依賴適用于 Azure 資源的受控識別，這是 Azure AD 的功能。 受控識別目前不支援跨目錄案例。 當您在 Azure 入口網站中設定客戶管理的金鑰時，系統會自動將受控識別指派給您的儲存體帳戶。 如果您之後將訂用帳戶、資源群組或儲存體帳戶從一個 Azure AD 目錄移至另一個目錄，與儲存體帳戶相關聯的受控身分識別不會傳送至新的租使用者，因此客戶管理的金鑰可能無法再運作。 如需詳細資訊，請參閱在 [Azure 資源受控識別的常見問題和已知問題](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)中， **傳輸 Azure AD 目錄之間的訂** 用帳戶。  

Azure 儲存體加密支援2048、3072和4096大小的 RSA 和 RSA HSM 金鑰。 如需有關金鑰的詳細資訊，請參閱 [關於金鑰](../../key-vault/keys/about-keys.md)。

使用金鑰保存庫或受控 HSM 有相關聯的成本。 如需詳細資訊，請參閱 [Key Vault 定價](https://azure.microsoft.com/pricing/details/key-vault/)。

## <a name="update-the-key-version"></a>更新金鑰版本

當您使用客戶管理的金鑰來設定加密時，有兩個選項可供您更新金鑰版本：

- **自動更新金鑰版本：** 若要在有可用的新版本時自動更新客戶管理的金鑰，請在您針對儲存體帳戶使用客戶管理的金鑰啟用加密時，省略金鑰版本。 如果省略金鑰版本，則 Azure 儲存體會每天檢查 key vault 或受控 HSM 是否有新版本的客戶管理金鑰。 Azure 儲存體會自動使用最新版本的金鑰。
- **手動更新金鑰版本：** 若要使用特定版本的金鑰進行 Azure 儲存體加密，請在針對儲存體帳戶使用客戶管理的金鑰啟用加密時，指定該金鑰版本。 如果您指定金鑰版本，Azure 儲存體會使用該版本進行加密，直到您手動更新金鑰版本為止。

    明確指定金鑰版本時，您必須在建立新版本時，手動更新儲存體帳戶，以使用新的金鑰版本 URI。 若要瞭解如何將儲存體帳戶更新為使用新版本的金鑰，請參閱使用儲存 [在 Azure Key Vault 中的客戶管理金鑰來設定加密](customer-managed-keys-configure-key-vault.md) ，或 [使用儲存在 Azure Key Vault 受控 HSM (preview) 中客戶管理的金鑰 ](customer-managed-keys-configure-key-vault-hsm.md)來設定加密。

更新客戶管理金鑰的金鑰版本並不會在儲存體帳戶中觸發資料的重新加密。 使用者不需要採取任何進一步的動作。

> [!NOTE]
> 若要輪替金鑰，請根據您的合規性政策，在金鑰保存庫或受控 HSM 中建立新版本的金鑰。 您可以手動輪替金鑰，或建立可依排程旋轉的函式。

## <a name="revoke-access-to-customer-managed-keys"></a>撤銷客戶管理金鑰的存取權

您可以隨時撤銷儲存體帳戶對客戶管理金鑰的存取權。 撤銷客戶管理的金鑰存取權之後，或在停用或刪除金鑰之後，用戶端就無法呼叫讀取或寫入 blob 或其中繼資料的作業。 嘗試呼叫下列任一項作業將會失敗，並出現錯誤碼 403 (禁止所有使用者) ：

- 當使用要求 URI 上的參數呼叫時，[列出 blob](/rest/api/storageservices/list-blobs) `include=metadata`
- [取得 Blob](/rest/api/storageservices/get-blob)
- [Get Blob Properties](/rest/api/storageservices/get-blob-properties)
- [取得 Blob 中繼資料](/rest/api/storageservices/get-blob-metadata)
- [設定 Blob 中繼資料](/rest/api/storageservices/set-blob-metadata)
- [Snapshot Blob](/rest/api/storageservices/snapshot-blob)使用 `x-ms-meta-name` 要求標頭呼叫時的快照集 Blob
- [複製 Blob](/rest/api/storageservices/copy-blob)
- [從 URL 複製 Blob](/rest/api/storageservices/copy-blob-from-url)
- [Set Blob Tier](/rest/api/storageservices/set-blob-tier)
- [放置區塊](/rest/api/storageservices/put-block)
- [從 URL 放置區塊](/rest/api/storageservices/put-block-from-url)
- [附加區塊](/rest/api/storageservices/append-block)
- [從 URL 附加區塊](/rest/api/storageservices/append-block-from-url)
- [放置 Blob](/rest/api/storageservices/put-blob)
- [放置頁面](/rest/api/storageservices/put-page)
- [從 URL 放置頁面](/rest/api/storageservices/put-page-from-url)
- [累加複製 Blob](/rest/api/storageservices/incremental-copy-blob) (機器翻譯)

若要再次呼叫這些作業，請還原客戶管理金鑰的存取權。

撤銷客戶管理的金鑰或停用或刪除金鑰之後，本節中未列出的所有資料作業可能會繼續進行。

若要撤銷客戶管理金鑰的存取權，請使用 [PowerShell](./customer-managed-keys-configure-key-vault.md#revoke-customer-managed-keys) 或 [Azure CLI](./customer-managed-keys-configure-key-vault.md#revoke-customer-managed-keys)。

## <a name="customer-managed-keys-for-azure-managed-disks"></a>適用于 Azure 受控磁片的客戶管理金鑰

客戶管理的金鑰也可用於管理 Azure 受控磁片的加密。 客戶管理的金鑰在受控磁片上的行為會與 Azure 儲存體資源的行為不同。 如需詳細資訊，請參閱適用于 Windows 的 [azure 受控磁片的伺服器端加密](../../virtual-machines/windows/disk-encryption.md) ，或適用于 Linux [的 azure 受控磁片的伺服器端加密](../../virtual-machines/linux/disk-encryption.md) 。

## <a name="next-steps"></a>後續步驟

- [待用資料的 Azure 儲存體加密](storage-service-encryption.md)
- [使用儲存在 Azure Key Vault 中客戶管理的金鑰來設定加密](customer-managed-keys-configure-key-vault.md)
- [使用儲存在 Azure Key Vault 受控 HSM (preview) 中的客戶管理金鑰來設定加密 ](customer-managed-keys-configure-key-vault-hsm.md)