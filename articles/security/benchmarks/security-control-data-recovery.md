---
title: Azure 安全控制 - 資料回復
description: Azure 安全控制資料回復
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 4f3e8540902809f951a441aa2fe8d00026c44d82
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408573"
---
# <a name="security-control-data-recovery"></a>安全控制:資料恢復

確保定期自動備份所有系統數據、配置和機密。

## <a name="91-ensure-regular-automated-back-ups"></a>9.1: 確保定期自動備份

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 9.1 | 10.1 | 客戶 |

啟用 Azure 備份並配置備份來源(Azure VM、SQL Server 或檔案共享)以及所需的頻率和保留期。

- [如何開啟 Azure 備份](https://docs.microsoft.com/azure/backup/)

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 執行完整的系統備份並備份任何客戶託管金鑰

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 9.2 | 10.2 | 客戶 |

啟用 Azure 備份和目標 VM,以及所需的頻率和保留期。 在 Azure 金鑰保管庫中備份客戶託管密鑰。

- [如何開啟 Azure 備份](https://docs.microsoft.com/azure/backup/)

- [如何在 Azure 中備份金鑰保管庫金鑰](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:驗證所有備份,包括客戶託管金鑰

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 9.3 | 10.3 | 客戶 |

確保能夠在 Azure 備份中定期執行內容的數據還原。 測試備份的客戶託管密鑰的恢復。

- [如何從 Azure 虛擬機器備份中復原檔案](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm)

- [如何在 Azure 中還原金鑰保管庫金鑰](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4:確保保護備份和客戶管理金鑰

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 9.4 | 10.4 | 客戶 |

針對內部部署備份，會使用您在備份至 Azure 時所提供的複雜密碼來提供靜態加密。 針對 Azure VM，會使用「儲存體服務加密」(SSE) 對資料進行靜態加密。 使用基於角色的存取控制來保護備份和客戶託管金鑰。  

在金鑰保管庫中啟用軟刪除和清除保護,以保護密鑰免受意外或惡意刪除。  如果 Azure 儲存用於存儲備份,請啟用軟刪除以在刪除 Blob 或 Blob 快照時保存和恢復資料。 

- [瞭解 Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

- [如何在金鑰保存庫中開啟軟刪除與清除保護](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

- [Azure 儲存體 Blob 的虛刪除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)


## <a name="next-steps"></a>後續步驟

- 請參考下一個安全控制:[事件回應](security-control-incident-response.md)