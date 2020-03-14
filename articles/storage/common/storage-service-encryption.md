---
title: 待用資料的 Azure 儲存體加密
description: Azure 儲存體保護您的資料，方法是在將它保存到雲端之前自動將其加密。 您可以依賴 Microsoft 管理的金鑰來加密您的儲存體帳戶，也可以使用您自己的金鑰來管理加密。
services: storage
author: tamram
ms.service: storage
ms.date: 03/09/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 028b186964643a08a4370741a3f1ff2ba33a4e85
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370299"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>待用資料的 Azure 儲存體加密

當您的資料保存到雲端時，Azure 儲存體會自動將其加密。 Azure 儲存體加密可保護您的資料，並協助您符合組織的安全性和合規性承諾。

## <a name="about-azure-storage-encryption"></a>關於 Azure 儲存體加密

Azure 儲存體中的資料會使用256位[AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)（可用的最強區塊密碼之一），以透明的方式進行加密和解密，且符合 FIPS 140-2 標準。 Azure 儲存體加密類似于 Windows 上的 BitLocker 加密。

所有新的儲存體帳戶（包括 Resource Manager 和傳統儲存體帳戶）都會啟用 Azure 儲存體加密。 無法停用 Azure 儲存體加密。 因為預設會保護您的資料，所以您不需要修改程式碼或應用程式，就能利用 Azure 儲存體加密。

無論儲存體帳戶的效能層級（標準或高階）或部署模型（Azure Resource Manager 或傳統），都會進行加密。 所有 Azure 儲存體的冗余選項都支援加密，而且儲存體帳戶的所有複本都會加密。 所有 Azure 儲存體資源都會加密，包括 blob、磁片、檔案、佇列和資料表。 所有物件中繼資料也會加密。

加密不會影響 Azure 儲存體效能。 Azure 儲存體加密不會產生額外的費用。

在2017年10月20日之後寫入 Azure 儲存體的每個區塊 blob、附加 blob 或分頁 blob 都會進行加密。 在此日期之前建立的 blob 會繼續由背景處理常式加密。 若要強制加密在2017年10月20日之前建立的 blob，您可以重寫 blob。 若要瞭解如何檢查 blob 的加密狀態，請參閱[檢查 blob 的加密狀態](../blobs/storage-blob-encryption-status.md)。

如需 Azure 儲存體加密之基礎密碼編譯模組的詳細資訊，請參閱[密碼編譯 API：新一代](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)。

## <a name="about-encryption-key-management"></a>關於加密金鑰管理

您可以依賴 Microsoft 管理的金鑰來加密您的儲存體帳戶，也可以使用您自己的金鑰來管理加密。 如果您選擇使用自己的金鑰來管理加密，您有兩個選項：

- 您可以使用 Azure Key Vault 來指定*客戶管理的金鑰*，以用來加密和解密 Blob 儲存體和 Azure 檔案儲存體中的資料。<sup>1、2</sup>
- 您可以在 Blob 儲存體作業上指定*客戶提供的金鑰*。 對 Blob 儲存體發出讀取或寫入要求的用戶端可以在要求中包含加密金鑰，以便更精確地控制 blob 資料的加密和解密方式。

下表比較 Azure 儲存體加密的金鑰管理選項。

|                                        |    Microsoft 管理的金鑰                             |    客戶管理的金鑰                                                                                                                        |    客戶提供的金鑰                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    加密/解密作業    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    支援的 Azure 儲存體服務    |    全部                                                |    Blob 儲存體，Azure 檔案儲存體<sup>1，2</sup>                                                                                                               |    Blob 儲存體                                                                  |
|    金鑰儲存                         |    Microsoft 金鑰存放區    |    Azure 金鑰保存庫                                                                                                                              |    Azure Key Vault 或任何其他金鑰存放區                                                                 |
|    金鑰輪替責任         |    Microsoft                                          |    客戶                                                                                                                                     |    客戶                                                                      |
|    金鑰使用量                           |    Microsoft                                          |    Azure 入口網站，儲存體資源提供者 REST API，Azure 儲存體管理程式庫，PowerShell，CLI        |    Azure 儲存體 REST API （Blob 儲存體），Azure 儲存體用戶端程式庫    |
|    金鑰存取                          |    僅限 Microsoft                                     |    Microsoft、客戶                                                                                                                    |    僅限客戶                                                                 |

<sup>1</sup>如需有關建立支援使用客戶管理的金鑰搭配佇列儲存體的帳戶的詳細資訊，請參閱[建立支援佇列客戶管理金鑰的帳戶](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)。<br />
<sup>2</sup>如需建立支援使用客戶管理的金鑰搭配資料表儲存體之帳戶的相關資訊，請參閱[建立支援資料表之客戶管理金鑰的帳戶](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)。

下列各節會更詳細地說明金鑰管理的每個選項。

## <a name="microsoft-managed-keys"></a>Microsoft 管理的金鑰

根據預設，您的儲存體帳戶會使用 Microsoft 管理的加密金鑰。 您可以在[Azure 入口網站](https://portal.azure.com)的 [**加密**] 區段中看到儲存體帳戶的加密設定，如下圖所示。

![查看使用 Microsoft 管理的金鑰加密的帳戶](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

## <a name="customer-managed-keys-with-azure-key-vault"></a>客戶管理的金鑰與 Azure Key Vault

您可以使用自己的金鑰來管理儲存體帳戶層級的 Azure 儲存體加密。 當您在儲存體帳戶層級指定客戶管理的金鑰時，該金鑰會用來保護並控制儲存體帳戶根加密金鑰的存取權，而這會用來加密和解密所有的 blob 和檔案資料。 客戶管理的金鑰提供更大的彈性來管理存取控制。 您也可以審核用來保護資料的加密金鑰。

您必須使用 Azure Key Vault 來儲存客戶管理的金鑰。 您可以建立自己的金鑰，並將其儲存在金鑰保存庫中，或者您可以使用 Azure Key Vault Api 來產生金鑰。 儲存體帳戶和金鑰保存庫必須位於相同的區域，而且位於相同的 Azure Active Directory （Azure AD）租使用者中，但它們可以位於不同的訂用帳戶中。 如需 Azure Key Vault 的詳細資訊，請參閱[什麼是 Azure Key Vault？](../../key-vault/key-vault-overview.md)。

下圖顯示 Azure 儲存體如何使用 Azure Active Directory 和 Azure Key Vault，以使用客戶管理的金鑰來提出要求：

![此圖顯示客戶管理的金鑰在 Azure 儲存體中的使用方式](media/storage-service-encryption/encryption-customer-managed-keys-diagram.png)

下列清單說明圖表中編號的步驟：

1. Azure Key Vault 系統管理員會將加密金鑰的許可權授與儲存體帳戶相關聯的受控識別。
2. Azure 儲存體管理員會使用儲存體帳戶的客戶管理金鑰來設定加密。
3. Azure 儲存體會使用與儲存體帳戶相關聯的受控識別，透過 Azure Active Directory 來驗證 Azure Key Vault 的存取權。
4. Azure 儲存體會將帳戶加密金鑰包裝在 Azure Key Vault 中的客戶金鑰。
5. 對於讀取/寫入作業，Azure 儲存體會將要求傳送給 Azure Key Vault，以包裝和解除封裝帳戶加密金鑰，以執行加密和解密作業。

### <a name="enable-customer-managed-keys-for-a-storage-account"></a>為儲存體帳戶啟用客戶管理的金鑰

當您針對儲存體帳戶使用客戶管理的金鑰來啟用加密時，Azure 儲存體會在相關聯的金鑰保存庫中，將帳戶加密金鑰與客戶管理的金鑰包裝在一起。 啟用客戶管理的金鑰並不會影響效能，而且帳戶會立即以新的金鑰加密，而不會有任何時間延遲。

新的儲存體帳戶一律使用 Microsoft 管理的金鑰進行加密。 建立帳戶時，不可能啟用客戶管理的金鑰。 客戶管理的金鑰會儲存在 Azure Key Vault 中，而金鑰保存庫必須布建存取原則，以將金鑰許可權授與儲存體帳戶相關聯的受控識別。 只有在建立儲存體帳戶之後，才可以使用受控識別。

當您透過啟用或停用客戶管理的金鑰、更新金鑰版本或指定不同的金鑰來修改用於 Azure 儲存體加密的金鑰時，根金鑰的加密會變更，但您 Azure 儲存體帳戶中的資料不會需要重新加密。

若要瞭解如何使用客戶管理的金鑰搭配 Azure Key Vault 來 Azure 儲存體加密，請參閱下列其中一篇文章：

- [使用 Key Vault 從 Azure 入口網站設定 Azure 儲存體加密的客戶管理金鑰](storage-encryption-keys-portal.md)
- [使用從 PowerShell Azure 儲存體加密的 Key Vault 來設定客戶管理的金鑰](storage-encryption-keys-powershell.md)
- [使用 Key Vault 從 Azure CLI 設定 Azure 儲存體加密的客戶管理金鑰](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> 客戶管理的金鑰依賴 Azure 資源的受控識別，這是一項 Azure AD 的功能。 受控識別目前不支援跨目錄案例。 當您在 Azure 入口網站中設定客戶管理的金鑰時，受管理的身分識別會自動指派給您的儲存體帳戶。 如果您接著將訂用帳戶、資源群組或儲存體帳戶從一個 Azure AD 目錄移到另一個，則與儲存體帳戶相關聯的受控識別不會傳輸至新的租使用者，因此客戶管理的金鑰可能無法再使用。 如需詳細資訊，請參閱常見問題中的 Azure AD 目錄[和 Azure 資源的受控識別的已知問題](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)中**的訂**用帳戶。  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>將客戶管理的金鑰儲存在 Azure Key Vault

若要在儲存體帳戶上啟用客戶管理的金鑰，您必須使用 Azure Key Vault 來儲存您的金鑰。 您必須同時啟用「虛**刪除**」和「不要**清除**」金鑰保存庫的屬性。

Azure 儲存體加密僅支援大小為2048的 RSA 金鑰。 如需金鑰的詳細資訊，請參閱[關於 Azure Key Vault 金鑰、秘密和憑證](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)中的**Key Vault 金鑰**。

### <a name="rotate-customer-managed-keys"></a>輪替客戶管理的金鑰

您可以根據您的相容性原則，在 Azure Key Vault 中旋轉客戶管理的金鑰。 輪替金鑰時，您必須更新儲存體帳戶以使用新的金鑰 URI。 若要瞭解如何更新儲存體帳戶以在 Azure 入口網站中使用新版本的金鑰，請參閱[使用 Azure 入口網站來設定 Azure 儲存體的客戶管理金鑰](storage-encryption-keys-portal.md)中的「**更新金鑰版本**」一節。

輪替金鑰並不會觸發儲存體帳戶中的資料重新加密。 使用者不需要採取進一步的動作。

### <a name="revoke-access-to-customer-managed-keys"></a>撤銷對客戶管理的金鑰的存取權

您隨時都可以撤銷儲存體帳戶對客戶管理的金鑰的存取權。 撤銷客戶管理金鑰的存取權，或在停用或刪除金鑰之後，用戶端就無法呼叫讀取或寫入 blob 或其中繼資料的作業。 嘗試呼叫下列任何作業將會失敗，並出現錯誤碼403（禁止）給所有使用者：

- 使用要求 URI 上的 `include=metadata` 參數呼叫時，[列出 blob](/rest/api/storageservices/list-blobs)
- [取得 Blob](/rest/api/storageservices/get-blob)
- [取得 Blob 屬性](/rest/api/storageservices/get-blob-properties)
- [取得 Blob 中繼資料](/rest/api/storageservices/get-blob-metadata)
- [設定 Blob 中繼資料](/rest/api/storageservices/set-blob-metadata)
- 使用 `x-ms-meta-name` 要求標頭呼叫[快照集 Blob](/rest/api/storageservices/snapshot-blob)
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

### <a name="customer-managed-keys-for-azure-managed-disks-preview"></a>適用于 Azure 受控磁片的客戶管理金鑰（預覽）

客戶管理的金鑰也可用於管理 Azure 受控磁片的加密（預覽）。 客戶管理的金鑰在受控磁片上的行為不同于 Azure 儲存體資源。 如需詳細資訊，請參閱適用于 Windows 的[azure 受控磁片的伺服器端加密](../../virtual-machines/windows/disk-encryption.md)或適用于 Linux 的[azure 受控磁片的伺服器端加密](../../virtual-machines/linux/disk-encryption.md)。

## <a name="customer-provided-keys-preview"></a>客戶提供的金鑰（預覽）

對 Azure Blob 儲存體提出要求的用戶端，可以選擇在個別要求上提供加密金鑰。 在要求中包含加密金鑰，可讓您更精確地控制 Blob 儲存體作業的加密設定。 客戶提供的金鑰（預覽）可以儲存在 Azure Key Vault 或另一個金鑰存放區中。

如需示範如何在對 Blob 儲存體的要求上指定客戶提供的金鑰的範例，請參閱[使用 .net 對 blob 儲存體的要求指定客戶提供的金鑰](../blobs/storage-blob-customer-provided-key.md)。

### <a name="encrypting-read-and-write-operations"></a>加密讀取和寫入作業

當用戶端應用程式在要求上提供加密金鑰時，Azure 儲存體會在讀取和寫入 blob 資料時，以透明的方式執行加密和解密。 Azure 儲存體會將加密金鑰的 SHA-256 雜湊連同 blob 的內容一起寫入。 雜湊是用來確認所有對 blob 的後續作業都使用相同的加密金鑰。

Azure 儲存體不會儲存或管理用戶端與要求一起傳送的加密金鑰。 加密或解密程式完成後，就會安全地捨棄金鑰。

當用戶端使用客戶提供的金鑰來建立或更新 blob 時，該 blob 的後續讀取和寫入要求也必須提供金鑰。 如果未在已使用客戶提供的金鑰加密之 blob 的要求上提供金鑰，則要求會失敗，錯誤碼為409（衝突）。

如果用戶端應用程式在要求上傳送加密金鑰，而且也使用 Microsoft 管理的金鑰或客戶管理的金鑰來加密儲存體帳戶，則 Azure 儲存體會使用要求中提供的金鑰進行加密和解密。

若要傳送加密金鑰做為要求的一部分，用戶端必須使用 HTTPS 建立與 Azure 儲存體的安全連線。

每個 blob 快照集都可以有自己的加密金鑰。

### <a name="request-headers-for-specifying-customer-provided-keys"></a>要求標頭，用於指定客戶提供的金鑰

針對 REST 呼叫，用戶端可以使用下列標頭，安全地將要求的加密金鑰資訊傳遞至 Blob 儲存體：

|要求標頭 | 描述 |
|---------------|-------------|
|`x-ms-encryption-key` |寫入和讀取要求都需要。 Base64 編碼的 AES-256 加密金鑰值。 |
|`x-ms-encryption-key-sha256`| 寫入和讀取要求都需要。 加密金鑰的 Base64 編碼 SHA256。 |
|`x-ms-encryption-algorithm` | 寫入要求的必要，對於讀取要求則為選擇性。 指定使用指定的索引鍵來加密資料時，所要使用的演算法。 必須是 AES256。 |

在要求上指定加密金鑰是選擇性的。 不過，如果您指定上面列出的其中一個標頭來進行寫入作業，則必須指定它們的全部。

### <a name="blob-storage-operations-supporting-customer-provided-keys"></a>支援客戶提供之金鑰的 Blob 儲存體作業

下列 Blob 儲存體作業支援在要求上傳送客戶提供的加密金鑰：

- [Put Blob](/rest/api/storageservices/put-blob) \(英文\)
- [Put Block List](/rest/api/storageservices/put-block-list) \(英文\)
- [放置區塊](/rest/api/storageservices/put-block)
- [將區塊從 URL 放入](/rest/api/storageservices/put-block-from-url)
- [放置頁面](/rest/api/storageservices/put-page)
- [從 URL 放置頁面](/rest/api/storageservices/put-page-from-url)
- [附加區塊](/rest/api/storageservices/append-block)
- [Set Blob Properties](/rest/api/storageservices/set-blob-properties) \(英文\)
- [設定 Blob 中繼資料](/rest/api/storageservices/set-blob-metadata)
- [取得 Blob](/rest/api/storageservices/get-blob)
- [取得 Blob 屬性](/rest/api/storageservices/get-blob-properties)
- [取得 Blob 中繼資料](/rest/api/storageservices/get-blob-metadata)
- [快照集 Blob](/rest/api/storageservices/snapshot-blob)

### <a name="rotate-customer-provided-keys"></a>輪替客戶提供的金鑰

若要輪替要求所傳遞的加密金鑰，請下載該 blob，然後使用新的加密金鑰重新上傳它。

> [!IMPORTANT]
> Azure 入口網站不能用來讀取或寫入使用要求所提供之金鑰加密的容器或 blob。
>
> 請務必保護您在 Azure Key Vault 的安全金鑰存放區中，對 Blob 儲存體的要求所提供的加密金鑰。 如果您嘗試在沒有加密金鑰的容器或 blob 上進行寫入作業，此操作將會失敗，而且您將無法存取物件。

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Azure 儲存體加密與磁片加密

Azure 儲存體加密會加密備份 Azure 虛擬機器磁片的分頁 blob。 此外，所有的 Azure 虛擬機器磁片（包括本機暫存磁片）都可以選擇性地使用[Azure 磁碟加密](../../security/azure-security-disk-encryption-overview.md)進行加密。 Azure 磁碟加密在 Windows 上使用業界標準的[BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) ，而在 Linux 上則為[DM Crypt](https://en.wikipedia.org/wiki/Dm-crypt) ，以提供與 Azure Key Vault 整合之以作業系統為基礎的加密解決方案。

## <a name="next-steps"></a>後續步驟

- [什麼是 Azure 金鑰保存庫？](../../key-vault/key-vault-overview.md)
- [從 Azure 入口網站設定客戶管理金鑰以進行 Azure 儲存體加密](storage-encryption-keys-portal.md)
- [從 PowerShell 設定客戶管理金鑰以進行 Azure 儲存體加密](storage-encryption-keys-powershell.md)
- [從 Azure CLI 設定客戶管理金鑰以進行 Azure 儲存體加密](storage-encryption-keys-cli.md)
