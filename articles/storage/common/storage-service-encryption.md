---
title: 靜態資料的 Azure 存儲加密
description: Azure 存儲通過自動加密資料，然後再將其保存到雲中來保護資料。 您可以依賴 Microsoft 管理的金鑰來加密存儲帳戶中的資料，也可以使用自己的金鑰管理加密。
services: storage
author: tamram
ms.service: storage
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f8f6f40f8ce8297b3cbfe6b3afcbf10df4db6572
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409825"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>靜態資料的 Azure 存儲加密

Azure 存儲在將資料保存到雲時自動加密資料。 Azure 存儲加密可保護資料並説明您履行組織安全和合規性承諾。

## <a name="about-azure-storage-encryption"></a>關於 Azure 存儲加密

Azure 存儲中的資料使用 256 位[AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)進行加密和透明解密，這是可用的最強的塊密碼之一，並且符合 FIPS 140-2 標準。 Azure 存儲加密類似于 Windows 上的 BitLocker 加密。

為所有存儲帳戶（包括資源管理器和經典存儲帳戶）啟用 Azure 存儲加密。 無法禁用 Azure 存儲加密。 由於資料在預設情況下是安全的，因此無需修改代碼或應用程式就可以利用 Azure 存儲加密。

無論性能層（標準層或高級級）、訪問層（熱或酷）或部署模型（Azure 資源管理器或經典）如何，存儲帳戶中的資料都會加密。 存檔層中的所有 Blob 也進行加密。 所有 Azure 存儲冗余選項都支援加密，啟用異地複製後，主區域和次要區域中的所有資料都會加密。 所有 Azure 存儲資源都已加密，包括 Blob、磁片、檔、佇列和表。 所有物件中繼資料也已加密。 Azure 存儲加密沒有額外費用。

2017 年 10 月 20 日之後寫入 Azure 存儲的每個塊 Blob、追加 Blob 或頁面 Blob 都會加密。 在此日期之前創建的 Blob 將繼續由後臺進程加密。 要強制加密在 2017 年 10 月 20 日之前創建的 Blob，可以重寫 Blob。 要瞭解如何檢查 Blob 的加密狀態，[請參閱檢查 blob 的加密狀態](../blobs/storage-blob-encryption-status.md)。

有關 Azure 存儲加密基礎的加密模組的詳細資訊，請參閱[加密 API：下一代](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)。

## <a name="about-encryption-key-management"></a>關於加密金鑰管理

預設情況下，存儲帳戶中的資料使用 Microsoft 管理的金鑰進行加密。 您可以依賴 Microsoft 管理的金鑰來加密資料，也可以使用自己的金鑰管理加密。 如果您選擇使用自己的金鑰管理加密，則有兩個選項：

- 您可以使用 Azure 金鑰保存庫指定*客戶管理的金鑰*，用於加密和解密 Blob 存儲和 Azure 檔中的資料。<sup>1，2</sup>有關客戶管理的金鑰的詳細資訊，請參閱使用[Azure 金鑰保存庫使用客戶管理的金鑰來管理 Azure 存儲加密](encryption-customer-managed-keys.md)。
- 您可以在 Blob 存儲操作上指定*客戶提供的金鑰*。 針對 Blob 存儲發出讀取或寫入請求的用戶端可以包含對 Blob 資料加密和解密方式進行精細控制請求的加密金鑰。 有關客戶提供金鑰的詳細資訊，請參閱[在請求 Blob 存儲時提供加密金鑰（預覽）。](encryption-customer-provided-keys.md)

下表比較了 Azure 存儲加密的關鍵管理選項。

|                                        |    微軟管理的金鑰                             |    客戶管理的金鑰                                                                                                                        |    客戶提供的金鑰                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    加密/解密操作    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    支援 Azure 存儲服務    |    全部                                                |    Blob 存儲，Azure 檔<sup>1，2</sup>                                                                                                               |    Blob 儲存體                                                                  |
|    金鑰存儲                         |    微軟金鑰存儲    |    Azure 金鑰保存庫                                                                                                                              |    Azure 金鑰保存庫或任何其他金鑰存儲                                                                 |
|    關鍵輪換責任         |    Microsoft                                          |    客戶                                                                                                                                     |    客戶                                                                      |
|    金鑰使用量                           |    Microsoft                                          |    Azure 門戶、存儲資來源提供者 REST API、Azure 存儲管理庫、PowerShell、CLI        |    Azure 存儲 REST API（Blob 存儲）、Azure 存儲用戶端庫    |
|    金鑰存取                          |    僅限微軟                                     |    微軟， 客戶                                                                                                                    |    僅限客戶                                                                 |

<sup>1</sup>有關創建支援使用具有佇列存儲的客戶託管金鑰的帳戶的資訊，請參閱[創建一個帳戶，該帳戶支援佇列的客戶託管金鑰](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)。<br />
<sup>2</sup>有關創建支援將客戶管理的金鑰與表存儲一起使用的帳戶的資訊，請參閱[創建一個帳戶，該帳戶支援表的客戶託管金鑰](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)。

## <a name="next-steps"></a>後續步驟

- [何謂 Azure Key Vault？](../../key-vault/key-vault-overview.md)
- [從 Azure 入口網站設定客戶管理金鑰以進行 Azure 儲存體加密](storage-encryption-keys-portal.md)
- [從 PowerShell 設定客戶管理金鑰以進行 Azure 儲存體加密](storage-encryption-keys-powershell.md)
- [從 Azure CLI 設定客戶管理金鑰以進行 Azure 儲存體加密](storage-encryption-keys-cli.md)
