---
title: 待用資料的 Azure 儲存體加密
description: Azure 儲存體保護您的資料，方法是在將它保存到雲端之前自動將其加密。 您可以依賴 Microsoft 管理的金鑰來加密儲存體帳戶中的資料，也可以使用您自己的金鑰來管理加密。
services: storage
author: tamram
ms.service: storage
ms.date: 06/17/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 8b4236e40e8dfbe6ce67bca007be0b6737a6e0c8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84945574"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>待用資料的 Azure 儲存體加密

當您的資料保存到雲端時，Azure 儲存體會自動將其加密。 Azure 儲存體加密可保護您的資料，並協助您符合組織的安全性和合規性承諾。

## <a name="about-azure-storage-encryption"></a>關於 Azure 儲存體加密

Azure 儲存體中的資料會使用256位[AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)（可用的最強區塊密碼之一），以透明的方式進行加密和解密，且符合 FIPS 140-2 標準。 Azure 儲存體加密類似于 Windows 上的 BitLocker 加密。

所有儲存體帳戶都會啟用 Azure 儲存體加密，包括 Resource Manager 和傳統儲存體帳戶。 無法停用 Azure 儲存體加密。 因為預設會保護您的資料，所以您不需要修改程式碼或應用程式，就能利用 Azure 儲存體加密。

不論效能層級（標準或高階）、存取層（經常性或非經常性）或部署模型（Azure Resource Manager 或傳統），儲存體帳戶中的資料都會進行加密。 封存層中的所有 blob 也會進行加密。 所有 Azure 儲存體的冗余選項都支援加密，而且主要和次要區域中的所有資料都會在啟用異地複寫時加密。 所有 Azure 儲存體資源都會加密，包括 blob、磁片、檔案、佇列和資料表。 所有物件中繼資料也會加密。 Azure 儲存體加密不會產生額外的費用。

在2017年10月20日之後寫入 Azure 儲存體的每個區塊 blob、附加 blob 或分頁 blob 都會進行加密。 在此日期之前建立的 blob 會繼續由背景處理常式加密。 若要強制加密在2017年10月20日之前建立的 blob，您可以重寫 blob。 若要瞭解如何檢查 blob 的加密狀態，請參閱[檢查 blob 的加密狀態](../blobs/storage-blob-encryption-status.md)。

如需 Azure 儲存體加密之基礎密碼編譯模組的詳細資訊，請參閱[密碼編譯 API：新一代](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)。

## <a name="about-encryption-key-management"></a>關於加密金鑰管理

新儲存體帳戶中的資料會使用 Microsoft 管理的金鑰進行加密。 您可以依賴 Microsoft 管理的金鑰來加密您的資料，也可以使用您自己的金鑰來管理加密。 如果您選擇使用自己的金鑰來管理加密，您有兩個選項：

- 您可以使用 Azure Key Vault 來指定*客戶管理的金鑰*，以用來加密和解密 Blob 儲存體和 Azure 檔案儲存體中的資料。<sup>1，2</sup>如需有關客戶管理金鑰的詳細資訊，請參閱搭配[使用客戶管理的金鑰與 Azure Key Vault 來管理 Azure 儲存體加密](encryption-customer-managed-keys.md)。
- 您可以在 Blob 儲存體作業上指定*客戶提供的金鑰*。 對 Blob 儲存體發出讀取或寫入要求的用戶端可以在要求中包含加密金鑰，以便更精確地控制 blob 資料的加密和解密方式。 如需有關客戶提供的金鑰的詳細資訊，請參閱[在對 Blob 儲存體的要求上提供加密金鑰（預覽）](encryption-customer-provided-keys.md)。

下表比較 Azure 儲存體加密的金鑰管理選項。

|                                        |    Microsoft 管理的金鑰                             |    客戶管理的金鑰                                                                                                                        |    客戶提供的金鑰                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    加密/解密作業    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    支援的 Azure 儲存體服務    |    全部                                                |    Blob 儲存體，Azure 檔案儲存體<sup>1，2</sup>                                                                                                               |    Blob 儲存體                                                                  |
|    金鑰儲存體                         |    Microsoft 金鑰存放區    |    Azure 金鑰保存庫                                                                                                                              |    客戶自己的金鑰存放區                                                                 |
|    金鑰輪替責任         |    Microsoft                                          |    客戶                                                                                                                                     |    客戶                                                                      |
|    金鑰控制項                          |    Microsoft                                     |    客戶                                                                                                                    |    客戶                                                                 |

<sup>1</sup>如需有關建立支援使用客戶管理的金鑰搭配佇列儲存體的帳戶的詳細資訊，請參閱[建立支援佇列客戶管理金鑰的帳戶](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)。<br />
<sup>2</sup>如需建立支援使用客戶管理的金鑰搭配資料表儲存體之帳戶的相關資訊，請參閱[建立支援資料表之客戶管理金鑰的帳戶](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)。

如需 Azure 受控磁片的加密和金鑰管理的相關資訊，請參閱適用于 Windows Vm 的[azure 受控磁片的伺服器端加密](../../virtual-machines/windows/disk-encryption.md)或適用于 Linux Vm 的[azure 受控磁片的伺服器端加密](../../virtual-machines/linux/disk-encryption.md)。

## <a name="next-steps"></a>後續步驟

- [什麼是 Azure 金鑰保存庫？](../../key-vault/general/overview.md)
- [從 Azure 入口網站設定客戶管理金鑰以進行 Azure 儲存體加密](storage-encryption-keys-portal.md)
- [從 PowerShell 設定客戶管理金鑰以進行 Azure 儲存體加密](storage-encryption-keys-powershell.md)
- [從 Azure CLI 設定客戶管理金鑰以進行 Azure 儲存體加密](storage-encryption-keys-cli.md)
