---
title: Azure 安全性基準測試 V2 的總覽
description: Azure 安全性基準測試 V2 總覽
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 2471aaad4ef10b611ab4ad812c5b1566cc7c4a07
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94412675"
---
# <a name="overview-of-the-azure-security-benchmark-v2"></a>Azure 安全性基準測試 (V2) 總覽

Azure 安全性基準測試 (ASB) 提供規定的最佳作法和建議，以協助改善 Azure 上的工作負載、資料和服務的安全性。

此基準測試是一組整體安全性指導方針的一部分，其中也包含：

- **雲端採用架構** –有關安全性的指引，包括 [策略](/azure/cloud-adoption-framework/strategy/define-security-strategy)、 [角色和責任](/azure/cloud-adoption-framework/organize/cloud-security)、 [Azure 前10個安全性最佳作法](/azure/cloud-adoption-framework/get-started/security#step-1-establish-essential-security-practices)，以及 [參考實行](/azure/cloud-adoption-framework/ready/enterprise-scale/)。
- **Azure Well-Architected Framework** -在 azure 上 [保護工作負載](/assessments/?mode=pre-assessment&session=local) 的指引。
- **Microsoft 安全性最佳作法** -使用 Azure 的 [建議](/security/compass/microsoft-security-compass-introduction) 。

 Azure 安全性基準測試著重于以雲端為中心的控制區。 這些控制項與知名的安全性效能評定一致，例如，透過網際網路安全性的中心所述的 (CIS) 控制7.1 版和美國國家標準規範的標準與技術 (NIST) SP800-53。
下列控制項包含在 Azure 安全性基準測試中：

| ASB 控制網域 | 描述 
|--|--|
| [&nbsp; &nbsp; (NS) 的網路安全性](security-controls-v2-network-security.md) | 網路安全性涵蓋的控制項可保護和保護 Azure 網路，包括保護虛擬網路、建立私人連線、預防和緩和外部攻擊，以及保護 DNS 的安全。 |
| [&nbsp; &nbsp; (IM) 的身分識別管理](security-controls-v2-identity-management.md) | 身分識別管理涵蓋的控制項可讓您使用 Azure Active Directory 建立安全的身分識別與存取控制，包括使用單一登入、增強式驗證、受控識別 (和服務主體) 用於應用程式、條件式存取，以及帳戶異常監視。 |
| [特殊許可權 &nbsp; 存取 &nbsp; (PA) ](security-controls-v2-privileged-access.md) | 特殊許可權存取涵蓋的控制項可保護您的 Azure 租使用者和資源的特殊許可權存取權，包括各種控制措施來保護您的系統管理模型、系統管理帳戶和特殊許可權的存取工作站，以防止刻意和不慎的風險。 |
| [資料 &nbsp; 保護 &nbsp; (DP) ](security-controls-v2-data-protection.md) | 資料保護涵蓋了待用資料保護、傳輸中以及透過授權的存取機制的控制，包括使用 Azure 中的存取控制、加密和記錄來探索、分類、保護及監視機密資料資產。 |
| [資產 &nbsp; 管理 &nbsp; (AM) ](security-controls-v2-asset-management.md) | 資產管理涵蓋的控制項可確保 Azure 資源的安全性可見度和治理，包括安全性人員的許可權建議、資產清查的安全性存取，以及管理服務和資源的核准， (清查、追蹤和正確的) 。 |
| [記錄 &nbsp; 和 &nbsp; 威脅 &nbsp; 偵測 (LT) ](security-controls-v2-logging-threat-detection.md) | 記錄和威脅偵測涵蓋的控制項可偵測 Azure 上的威脅，以及啟用、收集和儲存 Azure 服務的審核記錄，包括使用控制項啟用偵測、調查和補救程式，以在 Azure 服務中使用原生威脅偵測來產生高品質的警示;它也包括使用 Azure 監視器收集記錄，並使用 Azure Sentinel、時間同步處理和記錄保留來集中進行安全性分析。 |
| [&nbsp; &nbsp; (IR) 的事件回應](security-controls-v2-incident-response.md) | 事件回應涵蓋事件回應生命週期的控制（準備、偵測和分析、內含專案和事件後活動），包括使用 Azure 服務（例如 Azure 資訊安全中心和 Sentinel）來將事件回應程式自動化。 |
| [狀況 &nbsp; 和 &nbsp; 弱點 &nbsp; 管理 &nbsp; (PV) ](security-controls-v2-posture-vulnerability-management.md) | 狀態和弱點管理著重于評定和改進 Azure 安全性狀態的控制項，包括弱點掃描、滲透測試和補救，以及 Azure 資源中的安全性設定追蹤、報告和修正。 |
| [端點 &nbsp; 安全性 &nbsp; (ES) ](security-controls-v2-endpoint-security.md) | 端點安全性涵蓋端點偵測和回應中的控制項，包括在 Azure 環境中使用端點偵測和回應 (EDR) 和反惡意程式碼服務。 |
| [備份 &nbsp; 和 &nbsp; 修復 &nbsp; (BR) ](security-controls-v2-backup-recovery.md) | 備份和復原涵蓋的控制項，可確保在不同的服務層級執行、驗證和保護資料和設定備份。 |
| [&nbsp; &nbsp; &nbsp; (GS) 治理和策略](security-controls-v2-governance-strategy.md) | 治理和策略提供的指導方針可確保一致的安全性策略，以及記載的治理方法來引導並維持安全性保證，包括為不同的雲端安全性功能、統一的技術策略，以及支援的原則和標準建立角色和責任。 |

## <a name="azure-security-benchmark-recommendations"></a>Azure 安全性基準測試建議

每個建議都包含下列資訊：

- **AZURE 識別碼** ：對應至建議的 Azure 安全性基準測試識別碼。
- **Cis 控制7.1 識別碼 (s)** ： cis 控制與此建議相對應的) v 7.1 控制項 (s。
- **NIST SP800-53 R4 ID (s)** ： NIST SP800-53 r4 (中等) 控制項 () 對應到此建議。
- **詳細資料** ：建議的原理，以及如何執行的指引連結。 如果 Azure 資訊安全中心支援此建議，則也會列出該資訊。
- **責任** ：客戶、服務提供者或兩者是否負責實施這項建議。 在公用雲端中共用安全性責任。 某些安全性控制僅適用于雲端服務提供者，因此提供者負責定址這些控制項。 這些是一般觀察-對於某些個別的服務，責任會與 Azure 安全性基準測試中列出的不同。 個別服務的基準建議中會說明這些差異。
- **客戶安全性專案關係人** ：客戶組織的 [安全性功能](/azure/cloud-adoption-framework/organize/cloud-security#security-functions) ，這些功能可能會對個別的控制項負責任、負責任或諮詢。 視貴公司的安全性組織結構，以及您設定與 Azure 安全性相關的角色和責任而定，它可能會與組織不同。

> [!NOTE]
> ASB 與產業基準測試 (（例如 NIST 和 CIS）之間的控制項對應) 只表示特定的 Azure 功能可以用來完全或部分解決 NIST 或 CIS 中定義的控制需求。 請注意，這種執行不一定會轉譯為 CIS 或 NIST 中對應控制項的完整合規性。

我們歡迎您提供 Azure 安全性基準測試工作的詳細意見反應和積極參與。 如果您想要提供 Azure 安全性基準測試小組直接輸入，請填寫表單，網址為 https://aka.ms/AzSecBenchmark

## <a name="download"></a>下載

您可以下載 [試算表格式](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Security%20Benchmark)的 Azure 安全性基準測試。

## <a name="next-steps"></a>後續步驟 
- 請參閱第一種安全性控制： [網路安全性](security-control-network-security.md)
- 閱讀 [Azure 安全性基準測試簡介](introduction.md)
- 瞭解 [Azure 安全性基礎](../fundamentals/index.yml)