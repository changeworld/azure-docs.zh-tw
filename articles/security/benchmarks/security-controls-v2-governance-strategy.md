---
title: Azure 安全性基準測試 V2-治理和策略
description: Azure 安全性基準測試 V2 治理和策略
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: af9a86613b3aa29cc01b5ef0daa1f3854940deaf
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96534058"
---
# <a name="security-control-v2-governance-and-strategy"></a>安全性控制 V2：治理和策略

治理和策略提供的指導方針可確保一致的安全性策略，以及記載的治理方法來引導並維持安全性保證，包括為不同的雲端安全性功能、統一的技術策略，以及支援的原則和標準建立角色和責任。

## <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1：定義資產管理和資料保護策略

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| GS-1 | 2，13 | SC、AC |

確定您記載並傳達可連續監視和保護系統和資料的清晰策略。 請優先探索、評估、保護及監視業務關鍵資料和系統。 

此策略應該包含下列項目的已記載指引、原則和標準： 

- 符合商業風險的資料分類標準

- 安全性組織對風險和資產清查的可見度 

- 安全性組織核准 Azure 服務以供使用的程序 

- 資產在其生命週期中的安全性

- 符合組織資料分類的必要存取控制策略

- Azure 原生和第三方資料保護功能的使用

- 傳輸中和待用使用案例的資料加密需求

- 適當的密碼編譯標準

如需詳細資訊，請參閱下列參考資料：
- [Azure 安全性架構建議 - 儲存體、資料和加密](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json) (機器翻譯)

- [Azure 安全性基礎觀念 - Azure 資料安全性、加密和儲存體](../fundamentals/encryption-overview.md) (機器翻譯)

- [雲端採用架構 - Azure 資料安全性和加密最佳做法](../fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json) (機器翻譯)

- [Azure 安全性效能評定 - 資產管理](security-controls-v2-asset-management.md)

- [Azure 安全性效能評定 - 資料保護](security-controls-v2-data-protection.md)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [所有專案關係人](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2：定義企業分割策略

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| GS-2 | 4、9、16 | AC、CA、SC |

使用身分識別、網路、應用程式、訂用帳戶、管理群組和其他控制項的組合，建立企業級的策略來分割資產的存取權。

審慎權衡安全性區隔的需求，以及需要與彼此通訊並存取資料的啟用每日作業需求。

確保在各種控制項類型上一致地實作分割策略，這些控制項類型包括網路安全性、身分識別和存取模型，以及應用程式權限/存取模型與人力流程控制。

- [Azure 中的分割策略指引 (影片)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) (機器翻譯)

- [Azure 中的分割策略指引 (文件)](/security/compass/governance#enterprise-segmentation-strategy) (機器翻譯)

- [使用企業分割策略來調整網路分割](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy) (機器翻譯)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [所有專案關係人](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-3-define-security-posture-management-strategy"></a>GS-3：定義安全性狀態管理策略

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| GS-3 | 20、3、5 | RA、CM、SC |

持續測量並減輕個別資產及其託管環境的風險。 優先處理高價值資產和高度公開的攻擊面，例如已發佈的應用程式、網路輸入和輸出點、使用者和系統管理員端點等等。

- [Azure 安全性效能評定 - 狀態和弱點管理](security-controls-v2-posture-vulnerability-management.md)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [所有專案關係人](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4：協調組織角色、責任及權責

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| GS-4 | N/A | PL，PM |

確定您記載並傳達安全性組織中角色和責任的明確策略。 優先為安全性決策提供清楚的權責、讓每個人熟知共同責任模型，並讓技術團隊熟知保護雲端的技術。

- [Azure 安全性最佳做法 1 – 人員：讓小組熟知雲端安全性旅程](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey) (機器翻譯)

- [Azure 安全性最佳做法 2 - 人員：讓小組熟知雲端安全性技術](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology) (機器翻譯)

- [Azure 安全性最佳做法 3 - 流程：指派雲端安全性決策的權責](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud) (機器翻譯)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [所有專案關係人](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-5-define-network-security-strategy"></a>GS-5：定義網路安全性策略

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| GS-5 | 9 | CA，SC |

建立 Azure 網路安全性方法，作為組織整體安全性存取控制策略的一部分。  

此策略應該包含下列項目的已記載指引、原則和標準： 

- 集中式網路管理和安全性責任

- 與企業分割策略一致的虛擬網路分割模型

- 不同威脅和攻擊案例的補救策略

- 網際網路邊緣和輸入與輸出策略

- 混合式雲端和內部部署互連能力策略

- 最新的網路安全性成品 (例如網路圖、參考網路架構)

如需詳細資訊，請參閱下列參考資料：

- [Azure 安全性最佳做法 11 - 架構。單一整合的安全性策略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy) (機器翻譯)

- [Azure 安全性效能評定 - 網路安全性](security-controls-v2-network-security.md)

- [Azure 網路安全性概觀](../fundamentals/network-overview.md)

- [企業網路架構策略](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture) (機器翻譯)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [所有專案關係人](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6：定義身分識別和特殊權限存取策略

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| GS-6 | 16、4 | AC、AU、SC |

建立 Azure 身分識別和特殊許可權的存取方法，作為組織整體安全性存取控制策略的一部分。  

此策略應該包含下列項目的已記載指引、原則和標準： 

- 集中式身分識別和驗證系統，以及其與其他內部和外部身分識別系統的互連能力

- 不同使用案例和條件中的增強式驗證方法

- 高權限使用者的保護

- 異常使用者活動監視和處理  

- 使用者身分識別、存取權檢閱與核對流程

如需詳細資訊，請參閱下列參考資料：

- [Azure 安全性效能評定 - 身分識別管理](security-controls-v2-identity-management.md)

- [Azure 安全性效能評定 - 特殊權限存取](security-controls-v2-privileged-access.md)

- [Azure 安全性最佳做法 11 - 架構。單一整合的安全性策略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy) (機器翻譯)

- [Azure 身分識別管理安全性概觀](../fundamentals/identity-management-overview.md)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [所有專案關係人](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7：定義記錄和威脅回應策略

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| GS-7 | 19 | IR、AU、RA、SC |

建立記錄和威脅回應策略，以快速偵測和補救威脅，同時符合合規性需求。 優先為分析師提供高品質的警示和順暢的體驗，使其能夠全心處理威脅，而非整合和手動步驟。 

此策略應包含針對下列元素而記載的指引、原則和標準： 

- 安全性作業 (SecOps) 組織的角色和責任 

- 妥善定義且與 NIST 或其他產業架構一致的事件回應流程 

- 記錄擷取和保留，以支援威脅偵測、事件回應及合規性需求

- 使用 SIEM、原生 Azure 功能及其他來源，集中顯示及相互關聯威脅的相關資訊 

- 與客戶、供應商和相關公開合作對象之間的溝通和通知計畫

- 使用 Azure 原生和協力廠商平台進行事件處理，例如記錄和威脅偵測、鑑定，以及攻擊補救和根除

- 處理事件和事件後活動的流程，例如吸取的經驗和證據保留

如需詳細資訊，請參閱下列參考資料：
- [Azure 安全性效能評定 - 記錄與威脅偵測](security-controls-v2-logging-threat-detection.md)

- [Azure 安全性效能評定 - 事件回應](security-controls-v2-incident-response.md)

- [Azure 安全性最佳做法 4 - 流程。更新雲端的事件回應流程](/azure/cloud-adoption-framework/security/security-top-10#3-process-assign-accountability-for-cloud-security-decisions) (機器翻譯)

- [Azure 採用架構、記錄與報告決策指南](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure 企業規模、管理與監視](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring) (機器翻譯)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [所有專案關係人](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-8-define-backup-and-recovery-strategy"></a>GS-8：定義備份和復原策略

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| GS-8 | 10 | CP |

為您的組織建立 Azure 備份和復原策略。 

此策略應該包含下列項目的已記載指引、原則和標準： 

- 復原時間目標 (RTO) 和復原點目標 (RPO) 定義符合您的企業復原目標

- 應用程式和基礎結構設定中的冗余設計

- 使用存取控制和資料加密來保護備份

如需詳細資訊，請參閱下列參考資料：
- [Azure 安全性基準測試-備份和復原](security-controls-v2-backup-recovery.md)

- [Azure Well-Architecture Framework-Azure 應用程式的備份和災難復原](/azure/architecture/framework/resiliency/backup-and-recovery)

- [Azure 採用架構-商務持續性和嚴重損壞修復](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [所有專案關係人](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)