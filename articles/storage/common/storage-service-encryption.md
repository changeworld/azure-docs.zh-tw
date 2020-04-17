---
title: 靜態資料的 Azure 儲存加密
description: Azure 儲存通過自動加密數據,然後再將其保存到雲中來保護數據。 您可以依賴 Microsoft 管理的密鑰來加密儲存帳戶中的數據,也可以使用自己的密鑰管理加密。
services: storage
author: tamram
ms.service: storage
ms.date: 04/10/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c737ccf83dae0cc4b198b9cd708a55b988e6593b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457938"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>靜態資料的 Azure 儲存加密

Azure 儲存在將數據保存到雲時自動加密數據。 Azure 儲存加密可保護數據並説明您履行組織安全和合規性承諾。

## <a name="about-azure-storage-encryption"></a>關於 Azure 儲存加密

Azure 儲存中的數據使用 256 位[元 AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)進行加密和透明解密,這是可用的最強的塊密碼之一,並且符合 FIPS 140-2 標準。 Azure 儲存加密類似於 Windows 上的 BitLocker 加密。

為所有儲存帳戶(包括資源管理員和經典儲存帳戶)啟用Azure儲存加密。 無法禁用 Azure 儲存加密。 由於數據在默認情況下是安全的,因此無需修改代碼或應用程式就可以利用 Azure 儲存加密。

無論性能層(標準層或高級級)、訪問層(熱或酷)或部署模型(Azure 資源管理器或經典)如何,存儲帳戶中的數據都會加密。 存檔層中的所有 Blob 也進行加密。 所有 Azure 儲存冗餘選項都支援加密,啟用異地複製後,主區域和輔助區域中的所有數據都會加密。 所有 Azure 儲存資源都已加密,包括 Blob、磁碟、檔、佇列和表。 所有物件元數據也已加密。 Azure 儲存加密沒有額外費用。

2017 年 10 月 20 日之後寫入 Azure 儲存的每個塊 Blob、追加 Blob 或頁面 Blob 都會加密。 在此日期之前創建的 Blob 將繼續由後台進程加密。 要強制加密在 2017 年 10 月 20 日之前創建的 Blob,可以重寫 Blob。 要瞭解如何檢查 Blob 的加密狀態,[請參考 blob 的加密狀態](../blobs/storage-blob-encryption-status.md)。

有關 Azure 儲存加密基礎的加密模組的詳細資訊,請參閱[加密 API:下一代](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)。

## <a name="about-encryption-key-management"></a>關於加密金鑰管理

新儲存帳戶中的數據使用Microsoft管理的密鑰進行加密。 您可以依賴 Microsoft 管理的密鑰來加密數據,也可以使用自己的密鑰管理加密。 如果您選擇使用自己的金鑰管理加密,則有兩個選項:

- 您可以使用 Azure 金鑰保管庫指定*客戶管理的密鑰*,用於加密和解密 Blob 儲存和 Azure 檔案中的數據。<sup>1,2</sup>有關客戶管理的金鑰的詳細資訊,請參閱使用[Azure 金鑰保存式庫使用客戶管理的金鑰來管理 Azure 儲存加密](encryption-customer-managed-keys.md)。
- 您可以在 Blob 儲存的操作上指定*客戶提供的金鑰*。 針對 Blob 儲存發出讀取或寫入請求的用戶端可以包含對 Blob 資料加密和解密方式進行精細控制請求的加密金鑰。 有關客戶提供密鑰的詳細資訊,請參閱[在請求 Blob 儲存時提供加密密鑰(預覽)。](encryption-customer-provided-keys.md)

下表比較了 Azure 儲存加密的關鍵管理選項。

|                                        |    Microsoft 管理的金鑰                             |    客戶管理的金鑰                                                                                                                        |    客戶提供的金鑰                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    加密/解密作業    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    支援 Azure 儲存服務    |    全部                                                |    Blob 儲存,Azure 檔案<sup>1,2</sup>                                                                                                               |    Blob 儲存體                                                                  |
|    金鑰儲存                         |    Microsoft S-Microt    |    Azure 金鑰保存庫                                                                                                                              |    客戶自己的金鑰儲存                                                                 |
|    關鍵輪換責任         |    Microsoft                                          |    客戶                                                                                                                                     |    客戶                                                                      |
|    金鑰控制                          |    Microsoft                                     |    客戶                                                                                                                    |    客戶                                                                 |

<sup>1</sup>有關建立支援使用具有佇列儲存的客戶託管金鑰的帳戶的資訊,請參考[建立帳戶,該帳戶支援佇列的客戶託管金鑰](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)。<br />
<sup>2</sup>有關建立支援將客戶管理的金鑰與表儲存一起使用的帳戶的資訊,請參考[建立帳戶,該帳戶支援表的客戶託管金鑰](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)。

## <a name="next-steps"></a>後續步驟

- [何謂 Azure Key Vault？](../../key-vault/general/overview.md)
- [從 Azure 入口網站設定客戶管理金鑰以進行 Azure 儲存體加密](storage-encryption-keys-portal.md)
- [從 PowerShell 設定客戶管理金鑰以進行 Azure 儲存體加密](storage-encryption-keys-powershell.md)
- [從 Azure CLI 設定客戶管理金鑰以進行 Azure 儲存體加密](storage-encryption-keys-cli.md)
