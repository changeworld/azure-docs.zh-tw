---
title: Azure 安全性控制-資料復原
description: Azure 安全性控制資料復原
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 4f3e8540902809f951a441aa2fe8d00026c44d82
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81408573"
---
# <a name="security-control-data-recovery"></a>安全性控制：資料復原

確保所有系統資料、設定和秘密都會定期自動備份。

## <a name="91-ensure-regular-automated-back-ups"></a>9.1：確保會定期自動備份

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 9.1 | 10.1 | 客戶 |

啟用 Azure 備份並設定備份來源（Azure Vm、SQL Server 或檔案共用），以及所需的頻率和保留期限。

- [如何啟用 Azure 備份](https://docs.microsoft.com/azure/backup/)

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份，並備份任何客戶管理的金鑰

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 9.2 | 10.2 | 客戶 |

啟用 Azure 備份和目標 VM，以及所需的頻率和保留週期。 在 Azure Key Vault 內備份客戶管理的金鑰。

- [如何啟用 Azure 備份](https://docs.microsoft.com/azure/backup/)

- [如何在 Azure 中備份金鑰保存庫金鑰](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 9.3 | 10.3 | 客戶 |

確保能夠在 Azure 備份內定期執行內容的資料還原。 已備份客戶管理金鑰的測試還原。

- [如何從 Azure 虛擬機器備份復原檔案](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm)

- [如何在 Azure 中還原金鑰保存庫金鑰](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理的金鑰受到保護

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 9.4 | 10.4 | 客戶 |

針對內部部署備份，會使用您在備份至 Azure 時所提供的複雜密碼來提供靜態加密。 針對 Azure VM，會使用「儲存體服務加密」(SSE) 對資料進行靜態加密。 使用以角色為基礎的存取控制來保護備份和客戶管理的金鑰。  

啟用 Key Vault 中的虛刪除和清除保護，以防止金鑰遭到意外或惡意刪除。  如果 Azure 儲存體用來儲存備份，請啟用虛刪除，以在 blob 或 blob 快照集遭到刪除時儲存並復原您的資料。 

- [了解 Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

- [如何在 Key Vault 中啟用虛刪除和清除保護](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

- [Azure 儲存體 Blob 的虛刪除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)


## <a name="next-steps"></a>後續步驟

- 請參閱下一個安全性控制：[事件回應](security-control-incident-response.md)