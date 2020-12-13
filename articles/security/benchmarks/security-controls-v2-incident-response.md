---
title: Azure 安全性基準測試 V2-事件回應
description: Azure 安全性基準測試 V2 事件回應
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 043e365bfe27db516a42386a91bc0433e27e2068
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368829"
---
# <a name="security-control-v2-incident-response"></a>安全性控制 V2：事件回應

事件回應涵蓋事件回應生命週期的控制：準備、偵測和分析、內含專案，以及事件後活動。 這包括使用 Azure 服務（例如 Azure 資訊安全中心和 Sentinel）將事件回應程式自動化。

## <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1：準備 – 更新 Azure 的事件回應流程

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP 800-53 r4 識別碼 (s)  |
|--|--|--|--|
| IR-1 | 19 | IR-4、IR-8 |

確定您的組織有處理常式來回應安全性事件、已更新 Azure 的這些處理常式，並會定期進行這些程式以確保就緒。

- [在企業環境中實作安全性](/azure/cloud-adoption-framework/security/security-top-10#3-process-assign-accountability-for-cloud-security-decisions) (機器翻譯)

- [事件回應參考指南](/microsoft-365/downloads/IR-Reference-Guide.pdf) (英文)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全性作業](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [事件準備](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [威脅情報](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-2-preparation--setup-incident-notification"></a>IR-2：準備 – 設定事件通知

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP 800-53 r4 識別碼 (s)  |
|--|--|--|--|
| IR-2 | 19.5 | IR-4、IR-5、IR-6、IR-8 |

在 Azure 資訊安全中心中設定安全性事件連絡人資訊。 當 Microsoft 安全回應中心 (MSRC) 發現您的資料已遭非法或未經授權的合作對象存取時，Microsoft 會使用此連絡人資訊來與您連絡。 您也可以選擇根據事件回應需求，在不同的 Azure 服務中自訂事件警示和通知。 

- [如何設定 Azure 資訊安全中心安全性連絡人](../../security-center/security-center-provide-security-contact-details.md)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全性作業](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [事件準備](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3：偵測與分析 – 根據高品質警示建立事件

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP 800-53 r4 識別碼 (s)  |
|--|--|--|--|
| IR-3 | 19.6 | IR-4、IR-5 |

確定您有建立高品質警示的流程，並測量警示品質。 這可讓您從過去的事件中汲取經驗，並且為分析師設定警示的優先順序，以避免浪費時間處理誤報。 

您可以根據從過去的事件獲取的經驗、已驗證的社群來源，以及可藉由融合和相互關聯不同訊號來源而產生和清除警示的工具，來建立高品質的警示。 

Azure 資訊安全中心可在許多 Azure 資產間提供高品質的警示。 您可以使用 ASC 資料連接器將警示串流至 Azure Sentinel。 Azure Sentinel 可讓您建立進階警示規則，以自動產生事件供調查之用。 

使用匯出功能匯出 Azure 資訊安全中心警示和建議，以利找出 Azure 資源的風險。 以手動或持續不斷的方式匯出警示和建議。

- [如何設定匯出](../../security-center/continuous-export.md)

- [如何將警示串流至 Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全性作業](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [事件準備](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [威脅情報](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4：偵測與分析 – 調查事件

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP 800-53 r4 識別碼 (s)  |
|--|--|--|--|
| IR-4 | 19 | IR-4 |

請確定分析師可以在調查潛在事件時查詢並使用各種不同的資料來源，以建立發生狀況的完整觀點。 您應收集各種記錄，以追蹤整個攻擊鏈中潛在攻擊者的活動，而避免產生盲點。  您也應確實獲取深入解析和知識，以供其他分析師和未來的歷程記錄參考使用。  

調查的資料來源包括從範圍內的服務和執行中的系統收集到的集中式記錄來源，但也可包括：

- 網路資料 – 使用網路安全性群組的流量記錄、Azure 網路監看員和 Azure 監視器，擷取網路流量記錄和其他分析資訊。 

- 執行中系統的快照集： 

    - 使用 Azure 虛擬機器的快照集功能，建立執行中系統磁碟的快照集。 

    - 使用作業系統的原生記憶體傾印功能，建立執行中系統記憶體的快照集。

    - 使用 Azure 服務的快照集功能或軟體本身的功能，建立執行中系統的快照集。

Azure Sentinel 可讓您對絕大多數的記錄來源進行廣泛的資料分析，並提供案例管理入口網站來管理事件的完整生命週期。 調查期間的情報資訊可以與事件相關聯，以供追蹤和報告之用。 

- [為 Windows 機器的磁碟建立快照集](../../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [為 Linux 機器的磁碟建立快照集](../../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure 支援診斷資訊和記憶體傾印收集](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [使用 Azure Sentinel 調查事件](../../sentinel/tutorial-investigate-cases.md)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全性作業](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [事件準備](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [威脅情報](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5：偵測與分析 – 設定事件的優先順序

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP 800-53 r4 識別碼 (s)  |
|--|--|--|--|
| IR-5 | 19.8 | CA-2，IR-4 |

根據警示嚴重性和資產敏感性，提供內容給分析師，以根據警示的嚴重性和資產敏感性來優先處理事件。 

Azure 資訊安全中心會指派每個警示的嚴重性，以協助您設定應優先調查哪些警示。 嚴重性的依據，在於資訊安全中心對於據以發出警示的發現結果或分析結果有多少信心，以及認定導致警示的活動背後存在惡意意圖的把握程度。

此外，請使用標籤來標示資源，並建立命名系統以識別及分類 Azure 資源，尤其是處理敏感資料的資源。  您需負責根據發生事件的 Azure 資源和環境的重要性，設定警示的補救優先順序。

- [Azure 資訊安全中心的安全性警示](../../security-center/security-center-alerts-overview.md)

- [使用標記來組織 Azure 資源](../../azure-resource-manager/management/tag-resources.md)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全性作業](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [事件準備](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [威脅情報](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6：圍堵、根除及復原 – 將事件處理自動化

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP 800-53 r4 識別碼 (s)  |
|--|--|--|--|
| IR-6 | 19 | IR-4、IR-5、IR-6 |

將手動重複的工作自動化，以加快回應時間並降低分析師的負擔。 手動工作需要較長的時間來執行，進而降低每個事件的速度，以及減少分析師可以處理的事件數目。 手動工作也會使分析師更加疲勞，而增加人為錯誤導致延遲的風險，並降低分析師有效專注處理複雜工作的能力。 請使用 Azure 資訊安全中心和 Azure Sentinel 中的工作流程自動化功能來自動觸發動作，或執行劇本以回應傳入的安全性警示。 劇本會採取動作，例如傳送通知、停用帳戶，以及隔離有問題的網路。 

- [在資訊安全中心設定工作流程自動化](../../security-center/workflow-automation.md)

- [在 Azure 資訊安全中心設定自動化威脅回應](../../security-center/tutorial-security-incident.md#triage-security-alerts)

- [在 Azure Sentinel 中設定自動化威脅回應](../../sentinel/tutorial-respond-threats-playbook.md)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全性作業](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [事件準備](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [威脅情報](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)