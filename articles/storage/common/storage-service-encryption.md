---
title: 待用資料的 Azure 儲存體加密
description: Azure 儲存體藉由在將資料保存到雲端之前自動加密，來保護您的資料。 您可以依賴 Microsoft 管理的金鑰來加密儲存體帳戶中的資料，也可以使用您自己的金鑰管理加密。
services: storage
author: tamram
ms.service: storage
ms.date: 08/24/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: e949c3db6d8c0cafab8556dbfde367e6e49273e9
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078192"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>待用資料的 Azure 儲存體加密

Azure 儲存體將資料保存到雲端時，會自動將您的資料加密。 Azure 儲存體加密可保護您的資料，並協助您符合組織的安全性和合規性承諾。

## <a name="about-azure-storage-encryption"></a>關於 Azure 儲存體加密

Azure 儲存體中的資料會以透明的方式使用256位 [AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)（可用的最強區塊編碼器之一）進行加密和解密，並符合 FIPS 140-2 規範。 Azure 儲存體加密類似于 Windows 上的 BitLocker 加密。

已針對所有儲存體帳戶啟用 Azure 儲存體加密，包括 Resource Manager 和傳統儲存體帳戶。 無法停用 Azure 儲存體加密。 由於預設會保護您的資料，因此您不需要修改您的程式碼或應用程式，即可利用 Azure 儲存體加密。

無論效能層級 (standard 或 premium) 、存取層 (經常性存取) 或非經常性存取層，或部署模型 (Azure Resource Manager 或傳統) ，儲存體帳戶中的資料都會進行加密。 封存層中的所有 blob 也會加密。 所有 Azure 儲存體的冗余選項都支援加密，且在啟用異地複寫時，主要區域和次要區域中的所有資料都會加密。 所有 Azure 儲存體資源都會加密，包括 blob、磁片、檔案、佇列和資料表。 所有物件中繼資料也會加密。 Azure 儲存體加密沒有額外的成本。

2017年10月20日之後，寫入至 Azure 儲存體的每個區塊 blob、附加 blob 或分頁 blob 都會加密。 在此日期之前建立的 blob 將繼續由背景進程加密。 若要強制加密于2017年10月20日之前建立的 blob，您可以重寫 blob。 若要瞭解如何檢查 blob 的加密狀態，請參閱 [檢查 blob 的加密狀態](../blobs/storage-blob-encryption-status.md)。

如需基礎 Azure 儲存體加密的密碼編譯模組的詳細資訊，請參閱 [密碼編譯 API：新一代](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)。

如需 Azure 受控磁片的加密和金鑰管理的相關資訊，請參閱適用于 Windows Vm 的 [azure 受控磁片的伺服器端加密](../../virtual-machines/windows/disk-encryption.md) ，或適用于 Linux Vm [之 azure 受控磁片的伺服器端加密](../../virtual-machines/linux/disk-encryption.md) 。

## <a name="about-encryption-key-management"></a>關於加密金鑰管理

根據預設，新儲存體帳戶中的資料會使用 Microsoft 管理的金鑰進行加密。 您可以繼續依賴 Microsoft 管理的金鑰來加密您的資料，也可以使用您自己的金鑰管理加密。 如果您選擇使用自己的金鑰管理加密，您有兩個選項。 您可以使用任一種類型的金鑰管理或兩者：

- 您可以使用 Azure Key Vault 來指定 *客戶管理的金鑰* ，以用於加密和解密 Blob 儲存體和 Azure 檔案儲存體中的資料。<sup>1，2</sup> 如需客戶管理金鑰的詳細資訊，請參閱 [使用客戶管理的金鑰搭配 Azure Key Vault 管理 Azure 儲存體加密](encryption-customer-managed-keys.md)。
- 您可以在 Blob 儲存體作業上指定 *客戶提供的金鑰* 。 對 Blob 儲存體進行讀取或寫入要求的用戶端可以在要求上包含加密金鑰，以對 blob 資料的加密和解密方式進行細微的控制。 如需客戶提供之金鑰的詳細資訊，請參閱針對 [Blob 儲存體的要求提供加密金鑰](encryption-customer-provided-keys.md)。

下表比較 Azure 儲存體加密的金鑰管理選項。

| 金鑰管理參數 | Microsoft 管理的金鑰 | 客戶管理的金鑰 | 客戶提供的金鑰 |
|--|--|--|--|
| 加密/解密作業 | Azure | Azure | Azure |
| 支援 Azure 儲存體服務 | 全部 | Blob 儲存體，Azure 檔案儲存體<sup>1，2</sup> | Blob 儲存體 |
| 金鑰儲存體 | Microsoft 金鑰存放區 | Azure 金鑰保存庫 | 客戶自己的金鑰存放區 |
| 金鑰輪替責任 | Microsoft | 客戶 | 客戶 |
| 按鍵控制 | Microsoft | 客戶 | 客戶 |

<sup>1</sup> 如需有關建立支援將客戶管理的金鑰與佇列儲存體搭配使用之帳戶的詳細資訊，請參閱 [建立支援客戶管理的佇列金鑰的帳戶](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)。<br />
<sup>2</sup> 如需有關建立支援將客戶管理的金鑰與資料表儲存體搭配使用之帳戶的詳細資訊，請參閱 [建立支援資料表之客戶管理金鑰的帳戶](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)。

> [!NOTE]
> Microsoft 管理的金鑰會根據合規性需求適當地輪替。 如果您有特定的金鑰輪替需求，Microsoft 建議您移至客戶管理的金鑰，讓您可以自行管理和審核輪替。

## <a name="encryption-scopes-for-blob-storage-preview"></a>Blob 儲存體 (預覽) 的加密範圍

根據預設，儲存體帳戶會使用限定于儲存體帳戶的金鑰進行加密。 您可以選擇使用儲存在 Azure Key Vault 中的 Microsoft 管理金鑰或由客戶管理的金鑰，來保護及控制加密資料的金鑰存取權。

加密範圍可讓您選擇性地管理容器層級或個別 blob 的加密。 您可以使用加密範圍來建立位於相同儲存體帳戶但屬於不同客戶的資料之間的安全界限。

您可以使用 Azure 儲存體資源提供者，為儲存體帳戶建立一或多個加密範圍。 當您建立加密範圍時，可指定範圍是否以受 Microsoft 管理的金鑰或 Azure Key Vault 中所儲存的客戶管理金鑰來保護。 相同儲存體帳戶上的不同加密範圍可以使用 Microsoft 管理或客戶管理的金鑰。

建立加密範圍之後，您可以在要求上指定該加密範圍來建立容器或 blob。 如需如何建立加密範圍的詳細資訊，請參閱 [建立及管理加密範圍 (預覽) ](../blobs/encryption-scope-manage.md)。

> [!NOTE]
> 在預覽期間，讀取權限異地冗余儲存體 (GRS) 和讀取權限異地區域冗余儲存體 (RA-GZRS) 帳戶不支援加密範圍。

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

> [!IMPORTANT]
> 加密範圍預覽僅供非生產環境使用。 生產環境的服務等級協定 (SLA) 目前無法使用。
>
> 為了避免非預期的成本，請務必停用您目前不需要的任何加密範圍。

### <a name="create-a-container-or-blob-with-an-encryption-scope"></a>使用加密範圍建立容器或 blob

在加密範圍下建立的 blob 會以該範圍指定的金鑰進行加密。 您可以在建立 blob 時指定個別 blob 的加密範圍，也可以在建立容器時指定預設的加密範圍。 在容器層級指定預設加密範圍時，該容器中的所有 blob 都會使用與預設範圍相關聯的金鑰進行加密。

當您在具有預設加密範圍的容器中建立 blob 時，如果容器設定為允許覆寫預設加密範圍，您就可以指定覆寫預設加密範圍的加密範圍。 若要避免覆寫預設加密範圍，請將容器設定為拒絕個別 blob 的覆寫。

只要加密範圍未停用，就會以透明的方式執行屬於加密範圍之 blob 的讀取作業。

### <a name="disable-an-encryption-scope"></a>停用加密範圍

當您停用加密範圍時，任何使用加密範圍進行的後續讀取或寫入作業都會失敗，並出現 HTTP 錯誤碼 403 (禁止) 。 如果您重新啟用加密範圍，讀取和寫入作業將會再次正常執行。

當加密範圍停用時，就不會再向您收費。 停用任何不需要的加密範圍，以避免不必要的費用。

如果您的加密範圍使用客戶管理的金鑰進行 Azure Key Vault 的保護，則您也可以在金鑰保存庫中刪除相關聯的金鑰，以便停用加密範圍。 請記住，Azure Key Vault 中客戶管理的金鑰受到虛刪除和清除保護的保護，且已刪除的金鑰受限於這些屬性所定義的行為。 如需詳細資訊，請參閱 Azure Key Vault 檔中的下列其中一個主題：

- [透過 PowerShell 使用虛刪除](../../key-vault/general/soft-delete-powershell.md)
- [如何透過 CLI 使用虛刪除](../../key-vault/general/soft-delete-cli.md)

> [!NOTE]
> 不可能刪除加密範圍。

## <a name="next-steps"></a>後續步驟

- [什麼是 Azure 金鑰保存庫？](../../key-vault/general/overview.md)
- [從 Azure 入口網站設定客戶管理金鑰以進行 Azure 儲存體加密](storage-encryption-keys-portal.md)
- [從 PowerShell 設定客戶管理金鑰以進行 Azure 儲存體加密](storage-encryption-keys-powershell.md)
- [從 Azure CLI 設定客戶管理金鑰以進行 Azure 儲存體加密](storage-encryption-keys-cli.md)
