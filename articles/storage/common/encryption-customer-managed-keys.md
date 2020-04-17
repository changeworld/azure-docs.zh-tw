---
title: 使用 Azure 金鑰保存庫的客戶管理金鑰來管理帳戶加密
titleSuffix: Azure Storage
description: 您可以使用自己的加密金鑰來保護儲存帳戶中的資料。 指定客戶管理的金鑰時,該金鑰用於保護和控制對加密數據的密鑰的訪問。 客戶管理的密鑰為管理存取控制提供了更大的靈活性。
services: storage
author: tamram
ms.service: storage
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: b2755d5aa5dbaa669fa2fdd8b84596e040b5dd6b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81456816"
---
# <a name="use-customer-managed-keys-with-azure-key-vault-to-manage-azure-storage-encryption"></a>使用具有 Azure 金鑰保存庫的客戶管理金鑰來管理 Azure 儲存加密

您可以使用自己的加密金鑰來保護儲存帳戶中的資料。 指定客戶管理的金鑰時,該金鑰用於保護和控制對加密數據的密鑰的訪問。 客戶管理的密鑰為管理存取控制提供了更大的靈活性。

您必須使用 Azure 金鑰保存庫來儲存客戶管理的金鑰。 您可以建立自己的密鑰並將其存儲在密鑰保管庫中,也可以使用 Azure 金鑰保管庫 API 產生金鑰。 存儲帳戶和密鑰保管庫必須位於同一區域和同一 Azure 活動目錄 (Azure AD) 租戶中,但它們可以位於不同的訂閱中。 有關 Azure 金鑰保管庫的詳細資訊,請參閱[什麼是 Azure 密鑰保管庫?](../../key-vault/general/overview.md)

## <a name="about-customer-managed-keys"></a>關於客戶管理的金鑰

下圖顯示 Azure 儲存如何使用 Azure 活動目錄和 Azure 金鑰保管庫使用客戶管理的金鑰發出請求:

![顯示客戶管理金鑰在 Azure 儲存中工作方式的圖表](media/encryption-customer-managed-keys/encryption-customer-managed-keys-diagram.png)

下面的清單解釋了關係圖中的編號步驟:

1. Azure 密鑰保管庫管理員向與存儲帳戶關聯的託管標識授予加密密鑰的許可權。
2. Azure 儲存管理員使用儲存帳戶的客戶管理金鑰配置加密。
3. Azure 儲存使用與儲存帳戶關聯的託管標識通過 Azure 活動目錄對 Azure 密鑰保管庫的訪問進行身份驗證。
4. Azure 儲存使用 Azure 密鑰保管庫中的客戶密鑰包裝帳戶加密密鑰。
5. 對於讀/寫操作,Azure 存儲向 Azure 密鑰保管庫發送請求以解包帳戶加密密鑰以執行加密和解密操作。

## <a name="create-an-account-that-supports-customer-managed-keys-for-queues-and-tables"></a>建立支援佇列和表的客戶管理金鑰的帳戶

在為存儲帳戶啟用客戶管理的密鑰時,存儲在佇列和表服務中的數據不會由客戶管理的密鑰自動保護。 您可以選擇在創建要包含在此保護中的儲存帳戶時配置這些服務。

有關如何建立支援佇列和表的客戶管理的金鑰的儲存帳戶的詳細資訊,請參閱[建立帳戶,該帳戶支援表和佇列的客戶管理金鑰](account-encryption-key-create.md)。

在為儲存帳戶配置客戶管理的密鑰時,Blob 和 File 服務中的數據始終受客戶管理的密鑰保護。

## <a name="enable-customer-managed-keys-for-a-storage-account"></a>為儲存帳戶啟用客戶管理的金鑰

客戶管理的金鑰只能在現有存儲帳戶上啟用。 密鑰保管庫必須預配存取策略,這些策略向與存儲帳戶關聯的託管標識授予金鑰許可權。 託管標識僅在創建存儲帳戶後可用。

設定客戶管理的金鑰時,Azure 儲存使用關聯的密鑰保管庫中的客戶託管金鑰包裝帳戶的根數據加密密鑰。 啟用客戶管理的密鑰不會影響性能,並立即生效。

通過啟用或禁用客戶管理的密鑰、更新密鑰版本或指定其他密鑰來修改用於 Azure 儲存加密的金鑰時,根金鑰的加密將更改,但 Azure 儲存帳戶中的數據不需要重新加密。

啟用或禁用客戶託管金鑰或修改金鑰或密鑰版本時,根加密密鑰的保護將更改,但 Azure 儲存帳戶中的數據不需要重新加密。

要瞭解如何將客戶管理的金鑰與 Azure 密鑰保管庫一起使用以進行 Azure 儲存加密,請參閱以下文章之一:

- [使用金鑰保存庫配置客戶管理的金鑰,以便從 Azure 閘戶進行 Azure 儲存加密](storage-encryption-keys-portal.md)
- [使用金鑰保存式庫設定客戶管理的金鑰,以便從 PowerShell 進行 Azure 儲存加密](storage-encryption-keys-powershell.md)
- [使用金鑰保存庫配置客戶管理的金鑰,以便從 Azure CLI 進行 Azure 儲存加密](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> 客戶託管金鑰依賴於 Azure 資源的託管標識,這是 Azure AD 的一項功能。 受控識別目前不支援跨目錄案例。 在 Azure 門戶中配置客戶管理密鑰時,託管標識將自動分配給封面下的存儲帳戶。 如果隨後將訂閱、資源組或存儲帳戶從一個 Azure AD 目錄移動到另一個 Azure AD 目錄,則與存儲帳戶關聯的託管標識不會傳輸到新租戶,因此客戶託管密鑰可能不再工作。 有關詳細資訊,請參閱在 FAQ 中的 Azure AD 目錄[與 Azure 資源的託管識別的已知問題](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)**之間傳輸訂閱**。  

## <a name="store-customer-managed-keys-in-azure-key-vault"></a>在 Azure 金鑰保存者儲存客戶管理的金鑰

要在儲存帳戶上啟用客戶管理的密鑰,必須使用 Azure 密鑰保管庫來儲存金鑰。 您必須在金鑰保管庫中同時啟用 **「軟刪除****」和「不清除」** 屬性。

Azure 儲存加密僅支援 2048 位 RSA 和 RSA-HSM 密鑰。 關於金鑰的詳細資訊,請參考[「關於 Azure 金鑰保管庫金鑰、機密和憑證」](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)的**金鑰保管庫金鑰**。

## <a name="rotate-customer-managed-keys"></a>旋轉客戶管理的金鑰

您可以根據合規性策略在 Azure 密鑰保管庫中輪換客戶管理的密鑰。 旋轉金鑰時,必須更新儲存帳戶才能使用新的密鑰版本 URI。 要瞭解如何更新儲存帳戶以在 Azure 門戶中使用密鑰的新版本,請參閱使用 Azure 門戶 在[「設定 Azure 儲存的客戶管理密鑰」](storage-encryption-keys-portal.md)中**更新金鑰版本的**部分。

旋轉金鑰不會觸發儲存帳戶中數據的重新加密。 使用者無需執行進一步操作。

## <a name="revoke-access-to-customer-managed-keys"></a>復原對客戶管理金鑰的存取

您可以隨時撤銷儲存帳戶對客戶管理金鑰的訪問許可權。 撤銷對客戶管理金鑰的訪問後,或在禁用或刪除密鑰後,客戶端無法呼叫從 Blob 或其中繼資料讀取或寫入的操作。 嘗試呼叫以下任一動作將失敗,對於所有使用者來說,錯誤代碼 403(禁止):

- [清單檢視](/rest/api/storageservices/list-blobs)器,`include=metadata`使用請求 URI 上的參數呼叫
- [取得 Blob](/rest/api/storageservices/get-blob)
- [取得 Blob 屬性](/rest/api/storageservices/get-blob-properties)
- [取得 Blob 中繼資料](/rest/api/storageservices/get-blob-metadata)
- [設定 Blob 中繼資料](/rest/api/storageservices/set-blob-metadata)
- [快照 Blob](/rest/api/storageservices/snapshot-blob),`x-ms-meta-name`當使用 要求標頭呼叫時
- [複製 Blob](/rest/api/storageservices/copy-blob)
- [從網址複製 Blob](/rest/api/storageservices/copy-blob-from-url)
- [設定 Blob 層](/rest/api/storageservices/set-blob-tier)
- [放置區塊](/rest/api/storageservices/put-block)
- [從網址放置區塊](/rest/api/storageservices/put-block-from-url)
- [附加區塊](/rest/api/storageservices/append-block)
- [從 URL 新增區](/rest/api/storageservices/append-block-from-url)
- [Put Blob](/rest/api/storageservices/put-blob) \(英文\)
- [放置頁面](/rest/api/storageservices/put-page)
- [從網址放置頁面](/rest/api/storageservices/put-page-from-url)
- [漸寫 Blob](/rest/api/storageservices/incremental-copy-blob)

要再次呼叫這些操作,請恢復對客戶管理的密鑰的訪問。

在撤銷客戶管理的金鑰或禁用或刪除金鑰后,本節中未列出的所有數據操作都可能繼續進行。

要復原對客戶管理金鑰的存取,請使用[PowerShell](storage-encryption-keys-powershell.md#revoke-customer-managed-keys)或[Azure CLI](storage-encryption-keys-cli.md#revoke-customer-managed-keys)。

## <a name="customer-managed-keys-for-azure-managed-disks"></a>Azure 託管磁碟的客戶託管金鑰

客戶管理的密鑰也可用於管理 Azure 託管磁碟的加密。 客戶託管金鑰在託管磁碟上的行為與 Azure 儲存資源的行為不同。 有關詳細資訊,請參閱適用於 Linux 的 Windows 託管磁碟的 Azure[託管磁碟](../../virtual-machines/windows/disk-encryption.md)[的伺服器端加密或伺服器端加密](../../virtual-machines/linux/disk-encryption.md)。

## <a name="next-steps"></a>後續步驟

- [使用金鑰保存庫配置客戶管理的金鑰,以便從 Azure 閘戶進行 Azure 儲存加密](storage-encryption-keys-portal.md)
- [使用金鑰保存式庫設定客戶管理的金鑰,以便從 PowerShell 進行 Azure 儲存加密](storage-encryption-keys-powershell.md)
- [使用金鑰保存庫配置客戶管理的金鑰,以便從 Azure CLI 進行 Azure 儲存加密](storage-encryption-keys-cli.md)
- [靜態資料的 Azure 儲存加密](storage-service-encryption.md)
