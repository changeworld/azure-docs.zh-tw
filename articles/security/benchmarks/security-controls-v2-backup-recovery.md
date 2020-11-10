---
title: Azure 安全性基準測試 V2-備份和復原
description: Azure 安全性基準測試 V2 備份和復原
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: c3812cef9b90a15870733441c9e830daf32e0b18
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94408935"
---
# <a name="security-control-v2-backup-and-recovery"></a>安全性控制 V2：備份和復原

備份和復原涵蓋的控制項，可確保在不同的服務層級執行、驗證和保護資料和設定備份。

## <a name="br-1-ensure-regular-automated-backups"></a>BR-1：確保定期自動備份

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| BR-1 | 10.1 | CP-2、CP4、CP-6、CP-9 |

確定您要備份系統和資料，以在發生未預期的事件之後維護商務持續性。 這應該由復原點目標的任何目標定義 (RPO) 和復原時間目標 (RTO) 。

啟用 Azure 備份並設定備份來源 (例如，Azure Vm、SQL Server、HANA 資料庫或檔案共用) ，以及所需的頻率和保留期限。  

針對較高的保護層級，您可以啟用地理區域冗余儲存選項，以將備份資料複寫到次要區域，並使用跨區域還原進行復原。

- [企業級商務持續性和災害復原](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery) \(部分機器翻譯\)

- [如何啟用 Azure 備份](../../backup/index.yml)

- [如何啟用跨區域還原](../../backup/backup-azure-arm-restore-vms.md#cross-region-restore)

**責任** ：客戶

**客戶安全性專案關係人** ( [深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [原則和標準](/azure/cloud-adoption-framework/organize/cloud-security-policy-standards)

- [安全性架構](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [基礎結構和端點安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [事件準備](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="br-2-encrypt-backup-data"></a>BR-2：加密備份資料

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| BR-2 | 10.2 | CP-9 |

確定您的備份受到保護，免于遭受攻擊。 這應該包括備份的加密，以防止機密性遺失。   

針對使用 Azure 備份的內部部署備份，會使用您提供的複雜密碼來提供靜態加密。 針對一般的 Azure 服務備份，系統會使用 Azure 平臺管理的金鑰自動加密備份資料。 您可以選擇使用客戶管理的金鑰來加密備份。 在此情況下，請確定金鑰保存庫中的客戶管理金鑰也在備份範圍中。 

在 Azure 備份、Azure Key Vault 或其他資源中使用角色型存取控制，以保護備份和客戶管理的金鑰。 此外，您可以啟用 advanced security 功能來要求 MFA，然後才能改變或刪除備份。

- [Azure 備份中的安全性功能概觀](../../backup/security-overview.md)

- [使用客戶管理的金鑰來加密備份資料](../../backup/encryption-at-rest-with-cmk.md) 

- [如何在 Azure 中備份 Key Vault 金鑰](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

- [協助保護混合式備份免于遭受攻擊的安全性功能](../../backup/backup-azure-security-feature.md#prevent-attacks)

**責任** ：客戶

**客戶安全性專案關係人** ( [深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全性架構](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [基礎結構和端點安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [事件準備](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3：驗證所有備份，包括客戶管理的金鑰

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| BR-3 | 10.3 | CP-4、CP-9 |

定期執行備份的資料還原。 確定您可以還原已備份的客戶管理金鑰。

- [如何從 Azure 虛擬機器備份復原檔案](../../backup/backup-azure-restore-files-from-vm.md)

- [如何在 Azure 中還原 Key Vault 金鑰](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**責任** ：客戶

**客戶安全性專案關係人** ( [深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [事件準備](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [安全性合規性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4：降低遺失金鑰的風險

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| BR-4 | 10.4 | CP-9 |

確定您已備妥量值，以防止和復原遺失的金鑰。 啟用 Azure Key Vault 中的虛刪除和清除保護，以防止金鑰遭到意外或惡意刪除。  

- [如何啟用 Key Vault 中的虛刪除和清除保護](../../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**責任** ：客戶

**客戶安全性專案關係人** ( [深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全性架構](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [事件準備](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [資料安全性](/azure/cloud-adoption-framework/organize/cloud-security-data-security)