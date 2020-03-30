---
title: Azure 安全控制 - 資料恢復
description: 安全控制資料恢復
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: c585ebd903d4070f6247456e06efffbc6ec45270
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934499"
---
# <a name="security-control-data-recovery"></a>安全控制：資料恢復

確保定期自動備份所有系統資料、配置和機密。

## <a name="91-ensure-regular-automated-back-ups"></a>9.1： 確保定期自動備份

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 9.1 | 10.1 | 客戶 |

啟用 Azure 備份並配置備份源（Azure VM、SQL Server 或檔共用）以及所需的頻率和保留期。

如何啟用 Azure 備份：

https://docs.microsoft.com/azure/backup/

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2： 執行完整的系統備份並備份任何客戶託管金鑰

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 9.2 | 10.2 | 客戶 |

啟用 Azure 備份和目標 VM，以及所需的頻率和保留期。 在 Azure 金鑰保存庫中備份客戶託管金鑰。

如何啟用 Azure 備份：

https://docs.microsoft.com/azure/backup/

如何在 Azure 中備份金鑰保存庫金鑰：

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶託管金鑰

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 9.3 | 10.3 | 客戶 |

確保能夠在 Azure 備份中定期執行內容的資料還原。 如有必要，測試還原到隔離的 VLAN。 測試備份的客戶託管金鑰的恢復。

如何從 Azure 虛擬機器備份中恢復檔：

https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm

如何在 Azure 中還原金鑰保存庫金鑰：

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保保護備份和客戶管理金鑰

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 9.4 | 10.4 | 客戶 |

針對內部部署備份，會使用您在備份至 Azure 時所提供的複雜密碼來提供靜態加密。 針對 Azure VM，會使用「儲存體服務加密」(SSE) 對資料進行靜態加密。 您可以在金鑰保存庫中啟用虛刪除，以保護金鑰免受意外或惡意刪除。

如何在金鑰保存庫中啟用虛刪除：

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

## <a name="next-steps"></a>後續步驟

請參閱下一個安全控制：[事件回應](security-control-incident-response.md)
