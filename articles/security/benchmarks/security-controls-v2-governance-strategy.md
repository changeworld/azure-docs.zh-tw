---
title: Azure 安全性基準測試 V2-治理和策略
description: Azure 安全性基準測試 V2 治理和策略
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: e8a5196bf71712caae1218933ed13345f4cecd99
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059160"
---
# <a name="security-control-governance-and-strategy"></a>安全性控制：治理和策略

備份和復原涵蓋的控制項，可確保在不同的服務層級執行、驗證和保護資料和設定備份。

## <a name="gs-1-define-asset-management-and-protection-strategy"></a>GS-1：定義資產管理和保護原則

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| GS-1 | 2，13 | SC、AC |

確定您記載並傳達可連續監視和保護系統和資料的清晰策略。 優先探索、評估、保護及監視商務關鍵資料和系統。 

此策略應該包含已記載的指導方針、原則，以及下列元素的標準： 

-   以商務風險為依據的資料分類標準

-   安全性組織風險和資產清查的可見度 

-   安全性組織核准 Azure 服務以供使用 

-   資產在其生命週期內的安全性

-   根據組織資料分類所需的存取控制策略

-   使用 Azure 原生和協力廠商資料保護功能

-   傳輸中和待用使用案例的資料加密需求

-   適當的密碼編譯標準

注意：您的雲端和內部部署資產管理和保護方法可能會因多種因素而有所不同，例如應用程式服務/裝載模型、商務風險和合規性需求。 

- [Azure 安全性架構建議-儲存體、資料和加密](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Azure 安全性基礎-Azure 資料安全性、加密和儲存體](../fundamentals/encryption-overview.md)

- [雲端採用架構-Azure 資料安全性和加密最佳作法](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure 安全性基準測試-資產管理](/azure/security/benchmarks/security-controls-v2-asset-management)

- [Azure 安全性基準測試-資料保護](/azure/security/benchmarks/security-controls-v2-data-protection)

**責任**：客戶

**客戶安全性專案關係人**：

- [所有專案關係人](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-2-define-security-posture-management-strategy"></a>GS-2：定義安全性狀態管理原則

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| GS-2 | 20、3、5 | RA、CM、SC |

持續測量並減輕個別資產及其託管環境的風險。 設定高價值資產的優先順序以及高度公開的攻擊面，例如已發佈的應用程式、網路輸入和輸出點、使用者和系統管理員端點等等。

- [Azure 安全性基準測試-狀態與弱點管理](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

**責任**：客戶

**客戶安全性專案關係人**：

- [所有專案關係人](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-3-align-organization-roles-responsibilities-and-accountabilities"></a>GS-3：讓組織角色、職責和責任保持一致

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| GS-3 | N/A | PL，PM |

確定您記載並傳達安全性組織中角色和責任的明確策略。 優先提供安全性決策的明確責任、對共同責任模型的教育，以及雲端安全性的技術教育。 

- [Azure 安全性最佳作法 1-人員：教育小組雲端安全性旅程](https://aka.ms/AzSec1)

- [Azure 安全性最佳作法 2-人員：教育小組雲端安全性技術](https://aka.ms/AzSec2)

- [Azure 安全性最佳作法 3-處理：指派雲端安全性決策的責任](https://aka.ms/AzSec3)

**責任**：客戶

**客戶安全性專案關係人**：

- [所有專案關係人](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-4-define-network-security-strategy"></a>GS-4：定義網路安全性策略

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| GS-4 | 9 | CA，SC |

建立 Azure 網路安全性方法，作為組織整體安全性存取控制策略的一部分。  

此策略應該包含已記載的指導方針、原則，以及下列元素的標準： 

-   集中式網路管理和安全性責任

-   虛擬網路分割模型與企業分割策略一致

-   不同威脅和攻擊案例中的補救策略

-   網際網路邊緣與輸入和輸出策略

-   混合式雲端和內部部署互連能力策略

-   最新的網路安全性構件 (例如網狀圖、參考網路架構) 

注意：您的雲端和內部部署的網路安全性方法可能會因多種因素而有所不同，例如應用程式服務模型、威脅暴露和混合式網路設定。

- [Azure 安全性最佳做法 11-架構。單一整合的安全性策略](https://aka.ms/AzSec11)

- [Azure 安全性基準測試-網路安全性](/azure/security/benchmarks/security-controls-v2-network-security)

- [Azure 網路安全性概觀](../fundamentals/network-overview.md)

- [商業網路架構策略](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**責任**：客戶

**客戶安全性專案關係人**：

- [所有專案關係人](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-5-define-identity-and-privileged-access-strategy"></a>GS-5：定義身分識別和特殊許可權存取策略

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| GS-5 | 16、4 | AC、AU、SC |

建立 Azure 身分識別和特殊許可權的存取方法，作為組織整體安全性存取控制策略的一部分。  

此策略應該包含已記載的指導方針、原則，以及下列元素的標準： 

-   集中式身分識別和驗證系統，以及其與其他內部和外部身分識別系統的互連能力

-   不同使用案例和條件中的強式驗證方法

-   保護高許可權的使用者

-   異常使用者活動監視和處理  

-   使用者身分識別和存取權檢查和協調流程

注意：您的雲端和內部部署的身分識別和特殊許可權存取方法，可能會因多種因素而有所不同，例如資料/應用程式存取路徑、服務模型和客戶/合作夥伴存取策略。

- [Azure 安全性基準測試-身分識別管理](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Azure 安全性基準測試-特殊許可權存取](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Azure 安全性最佳做法 11-架構。單一整合的安全性策略](https://aka.ms/AzSec11)

- [Azure 身分識別管理安全性概觀](../fundamentals/identity-management-overview.md) 

**責任**：客戶

**客戶安全性專案關係人**：

- [所有專案關係人](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-6-define-logging-and-threat-response-strategy"></a>GS-6：定義記錄和威脅回應策略

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| GS-6 | 19 | IR、AU、RA、SC |

建立記錄和威脅回應策略，以快速偵測和補救威脅，同時符合合規性需求。 排定為分析師提供高品質的警示和順暢的體驗，讓他們可以專注于威脅，而不是整合和手動步驟。 

此策略應該包含已記載的指導方針、原則，以及下列元素的標準： 

-   安全性作業 (SecOps) 組織的角色和責任 

-   妥善定義的事件回應程式，與 NIST 或其他產業架構一致 

-   記錄檔捕捉和保留，以支援威脅偵測、事件回應和合規性需求

-   使用 SIEM、原生 Azure 功能和其他來源，集中查看威脅的相關資訊以及相關資訊 

-   與您的客戶、供應商和感興趣的公開方相關的通訊和通知計畫

-   使用 Azure 原生和協力廠商平臺進行事件處理，例如記錄和威脅偵測、取證和攻擊補救和根除

-   處理事件和事件後活動的流程，例如經驗教訓和辨識項保留

注意：您的雲端和內部部署的記錄和威脅偵測方法可能會因多種因素而有所不同，例如合規性需求、威脅環境，以及偵測和補救功能。 

- [Azure 安全性基準測試-記錄和威脅偵測](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Azure 安全性基準測試-事件回應](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Azure 安全性最佳作法 4-處理。更新雲端的事件回應程式](https://aka.ms/AzSec11)

- [Azure 採用架構、記錄和報告決策指南](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure 企業規模、管理及監視](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**責任**：客戶

**客戶安全性專案關係人**：

- [所有專案關係人](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-7-define-backup-and-recovery-strategy"></a>GS-7：定義備份和復原策略

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| GS-7 | 10 | CP |

為您的組織建立 Azure 備份和復原策略。 

此策略應該包含已記載的指導方針、原則，以及下列元素的標準： 

-   復原時間目標 (RTO) 和復原點目標 (RPO) 定義符合您的企業復原目標

-   應用程式和基礎結構設定中的冗余設計

-   使用存取控制和資料加密來保護備份

注意：雲端和內部部署的備份和復原方法，可能會因為多個因素而異，例如基礎結構冗余、應用程式服務/裝載模型，以及合規性需求。

- [Azure 安全性基準測試-備份和復原](/azure/security/benchmarks/security-controls-v2-backup-recovery)

- [Azure 架構完善的架構-Azure 應用程式的備份和災難復原](/azure/architecture/framework/resiliency/backup-and-recovery)

- [Azure 採用架構-商務持續性和嚴重損壞修復](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

**責任**：客戶

**客戶安全性專案關係人**：

- [所有專案關係人](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

