---
title: 搭配 Azure Key Vault 使用客戶管理的金鑰來管理帳戶加密
titleSuffix: Azure Storage
description: 您可以使用自己的加密金鑰來保護儲存體帳戶中的資料。 當您指定客戶管理的金鑰時，該金鑰會用來保護及控制加密資料之金鑰的存取權。 客戶管理的金鑰提供更大的彈性來管理存取控制。
services: storage
author: tamram
ms.service: storage
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: b2755d5aa5dbaa669fa2fdd8b84596e040b5dd6b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81456816"
---
# <a name="use-customer-managed-keys-with-azure-key-vault-to-manage-azure-storage-encryption"></a>搭配 Azure Key Vault 使用客戶管理的金鑰來管理 Azure 儲存體加密

您可以使用自己的加密金鑰來保護儲存體帳戶中的資料。 當您指定客戶管理的金鑰時，該金鑰會用來保護及控制加密資料之金鑰的存取權。 客戶管理的金鑰提供更大的彈性來管理存取控制。

您必須使用 Azure Key Vault 來儲存客戶管理的金鑰。 您可以建立自己的金鑰，並將其儲存在金鑰保存庫中，或者您可以使用 Azure Key Vault Api 來產生金鑰。 儲存體帳戶和金鑰保存庫必須位於相同的區域，而且位於相同的 Azure Active Directory （Azure AD）租使用者中，但它們可以位於不同的訂用帳戶中。 如需 Azure Key Vault 的詳細資訊，請參閱[什麼是 Azure Key Vault？](../../key-vault/general/overview.md)。

## <a name="about-customer-managed-keys"></a>關於客戶管理的金鑰

下圖顯示 Azure 儲存體如何使用 Azure Active Directory 和 Azure Key Vault，以使用客戶管理的金鑰來提出要求：

![此圖顯示客戶管理的金鑰在 Azure 儲存體中的使用方式](media/encryption-customer-managed-keys/encryption-customer-managed-keys-diagram.png)

下列清單說明圖表中編號的步驟：

1. Azure Key Vault 系統管理員會將加密金鑰的許可權授與儲存體帳戶相關聯的受控識別。
2. Azure 儲存體管理員會使用儲存體帳戶的客戶管理金鑰來設定加密。
3. Azure 儲存體會使用與儲存體帳戶相關聯的受控識別，透過 Azure Active Directory 來驗證 Azure Key Vault 的存取權。
4. Azure 儲存體會將帳戶加密金鑰包裝在 Azure Key Vault 中的客戶金鑰。
5. 對於讀取/寫入作業，Azure 儲存體會將要求傳送給 Azure Key Vault，以解除包裝帳戶加密金鑰，以執行加密和解密作業。

## <a name="create-an-account-that-supports-customer-managed-keys-for-queues-and-tables"></a>建立支援佇列和資料表之客戶管理金鑰的帳戶

當客戶管理的金鑰已針對儲存體帳戶啟用時，不會自動由客戶管理的金鑰保護儲存在佇列和表格服務中的資料。 您可以在建立要包含在此保護中的儲存體帳戶時，選擇性地設定這些服務。

如需有關如何建立儲存體帳戶以支援佇列和資料表之客戶管理金鑰的詳細資訊，請參閱[建立支援資料表和佇列之客戶管理金鑰的帳戶](account-encryption-key-create.md)。

當客戶管理的金鑰已針對儲存體帳戶設定時，Blob 和檔案服務中的資料一律會受到客戶管理金鑰的保護。

## <a name="enable-customer-managed-keys-for-a-storage-account"></a>為儲存體帳戶啟用客戶管理的金鑰

客戶管理的金鑰只能在現有的儲存體帳戶上啟用。 金鑰保存庫必須布建存取原則，以將金鑰許可權授與儲存體帳戶相關聯的受控識別。 只有在建立儲存體帳戶之後，才可以使用受控識別。

當您設定客戶管理的金鑰時，Azure 儲存體會將帳戶的根資料加密金鑰包裝在相關聯的金鑰保存庫中，並使用客戶管理的金鑰。 啟用客戶管理的金鑰並不會影響效能，而且會立即生效。

當您透過啟用或停用客戶管理的金鑰、更新金鑰版本或指定不同的金鑰來修改用於 Azure 儲存體加密的金鑰時，根金鑰的加密會變更，但您的 Azure 儲存體帳戶中的資料不需要重新加密。

當您啟用或停用客戶管理的金鑰，或修改金鑰或金鑰版本時，根加密金鑰的保護會變更，但您的 Azure 儲存體帳戶中的資料不需要重新加密。

若要瞭解如何使用客戶管理的金鑰搭配 Azure Key Vault 來 Azure 儲存體加密，請參閱下列其中一篇文章：

- [使用 Key Vault 從 Azure 入口網站設定 Azure 儲存體加密的客戶管理金鑰](storage-encryption-keys-portal.md)
- [使用從 PowerShell Azure 儲存體加密的 Key Vault 來設定客戶管理的金鑰](storage-encryption-keys-powershell.md)
- [使用 Key Vault 從 Azure CLI 設定 Azure 儲存體加密的客戶管理金鑰](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> 客戶管理的金鑰依賴 Azure 資源的受控識別，這是一項 Azure AD 的功能。 受控識別目前不支援跨目錄案例。 當您在 Azure 入口網站中設定客戶管理的金鑰時，受管理的身分識別會自動指派給您的儲存體帳戶。 如果您接著將訂用帳戶、資源群組或儲存體帳戶從一個 Azure AD 目錄移到另一個，則與儲存體帳戶相關聯的受控識別不會傳輸至新的租使用者，因此客戶管理的金鑰可能無法再使用。 如需詳細資訊，請參閱常見問題中的 Azure AD 目錄[和 Azure 資源的受控識別的已知問題](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)中**的訂**用帳戶。  

## <a name="store-customer-managed-keys-in-azure-key-vault"></a>將客戶管理的金鑰儲存在 Azure Key Vault

若要在儲存體帳戶上啟用客戶管理的金鑰，您必須使用 Azure Key Vault 來儲存您的金鑰。 您必須同時啟用「虛**刪除**」和「不要**清除**」金鑰保存庫的屬性。

Azure 儲存體加密僅支援2048位 RSA 和 RSA-HSM 金鑰。 如需金鑰的詳細資訊，請參閱[關於 Azure Key Vault 金鑰、秘密和憑證](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)中的**Key Vault 金鑰**。

## <a name="rotate-customer-managed-keys"></a>輪替客戶管理的金鑰

您可以根據您的相容性原則，在 Azure Key Vault 中旋轉客戶管理的金鑰。 輪替金鑰時，您必須更新儲存體帳戶以使用新的金鑰版本 URI。 若要瞭解如何更新儲存體帳戶以在 Azure 入口網站中使用新版本的金鑰，請參閱[使用 Azure 入口網站來設定 Azure 儲存體的客戶管理金鑰](storage-encryption-keys-portal.md)中的「**更新金鑰版本**」一節。

輪替金鑰並不會觸發儲存體帳戶中的資料重新加密。 使用者不需要採取進一步的動作。

## <a name="revoke-access-to-customer-managed-keys"></a>撤銷對客戶管理的金鑰的存取權

您隨時都可以撤銷儲存體帳戶對客戶管理的金鑰的存取權。 撤銷客戶管理金鑰的存取權，或在停用或刪除金鑰之後，用戶端就無法呼叫讀取或寫入 blob 或其中繼資料的作業。 嘗試呼叫下列任何作業將會失敗，並出現錯誤碼403（禁止）給所有使用者：

- 在要求 URI 上以`include=metadata`參數呼叫時，[列出 blob](/rest/api/storageservices/list-blobs)
- [取得 Blob](/rest/api/storageservices/get-blob)
- [取得 Blob 屬性](/rest/api/storageservices/get-blob-properties)
- [取得 Blob 中繼資料](/rest/api/storageservices/get-blob-metadata)
- [設定 Blob 中繼資料](/rest/api/storageservices/set-blob-metadata)
- [Snapshot Blob](/rest/api/storageservices/snapshot-blob)使用`x-ms-meta-name`要求標頭呼叫時的快照集 Blob
- [複製 Blob](/rest/api/storageservices/copy-blob)
- [從 URL 複製 Blob](/rest/api/storageservices/copy-blob-from-url)
- [設定 Blob 層](/rest/api/storageservices/set-blob-tier)
- [放置區塊](/rest/api/storageservices/put-block)
- [將區塊從 URL 放入](/rest/api/storageservices/put-block-from-url)
- [附加區塊](/rest/api/storageservices/append-block)
- [從 URL 附加區塊](/rest/api/storageservices/append-block-from-url)
- [Put Blob](/rest/api/storageservices/put-blob) \(英文\)
- [放置頁面](/rest/api/storageservices/put-page)
- [從 URL 放置頁面](/rest/api/storageservices/put-page-from-url)
- [增量複製 Blob](/rest/api/storageservices/incremental-copy-blob)

若要再次呼叫這些作業，請還原對客戶管理的金鑰的存取權。

這一節中未列出的所有資料作業，可能會在客戶管理的金鑰被撤銷或金鑰已停用或刪除時繼續進行。

若要撤銷對客戶管理的金鑰的存取權，請使用[PowerShell](storage-encryption-keys-powershell.md#revoke-customer-managed-keys)或[Azure CLI](storage-encryption-keys-cli.md#revoke-customer-managed-keys)。

## <a name="customer-managed-keys-for-azure-managed-disks"></a>適用于 Azure 受控磁片的客戶管理金鑰

客戶管理的金鑰也可用於管理 Azure 受控磁片的加密。 客戶管理的金鑰在受控磁片上的行為不同于 Azure 儲存體資源。 如需詳細資訊，請參閱適用于 Windows 的[azure 受控磁片的伺服器端加密](../../virtual-machines/windows/disk-encryption.md)或適用于 Linux 的[azure 受控磁片的伺服器端加密](../../virtual-machines/linux/disk-encryption.md)。

## <a name="next-steps"></a>後續步驟

- [使用 Key Vault 從 Azure 入口網站設定 Azure 儲存體加密的客戶管理金鑰](storage-encryption-keys-portal.md)
- [使用從 PowerShell Azure 儲存體加密的 Key Vault 來設定客戶管理的金鑰](storage-encryption-keys-powershell.md)
- [使用 Key Vault 從 Azure CLI 設定 Azure 儲存體加密的客戶管理金鑰](storage-encryption-keys-cli.md)
- [待用資料的 Azure 儲存體加密](storage-service-encryption.md)
