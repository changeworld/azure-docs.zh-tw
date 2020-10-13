---
title: Azure 安全性基準測試 V2-事件回應
description: Azure 安全性基準測試 V2 事件回應
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 172607a7f8f036bbfb68e8d15e77b2a3e3fb5377
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91326383"
---
# <a name="security-control-v2-incident-response"></a>安全性控制 V2：事件回應

事件回應涵蓋事件回應生命週期的控制：準備、偵測和分析、內含專案，以及事件後活動。 這包括使用 Azure 服務（例如 Azure 資訊安全中心和 Sentinel）將事件回應程式自動化。

## <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1：準備–更新 Azure 的事件回應程式

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| IR-1 | 19 | IR-4、IR-8 |

確定您的組織有處理常式來回應安全性事件、已更新 Azure 的這些處理常式，並會定期進行這些程式以確保就緒。

- [跨企業環境執行安全性](https://aka.ms/AzSec4)

- [事件回應參考指南](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全性作業](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [事件準備](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [威脅情報](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-2-preparation--setup-incident-notification"></a>IR-2：準備–安裝事件通知

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| IR-2 | 19.5 | IR-4、IR-5、IR-6、IR-8 |

在 Azure 資訊安全中心中設定安全性事件連絡人資訊。 如果 Microsoft 安全性回應中心 (MSRC) 發現您的資料已被非法或未經授權的物件存取，Microsoft 會使用此連絡人資訊來與您聯繫。 您也可以選擇根據您的事件回應需求，在不同的 Azure 服務中自訂事件警示和通知。 

- [如何設定 Azure 資訊安全中心安全性連絡人](../../security-center/security-center-provide-security-contact-details.md)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全性作業](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [事件準備](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3：偵測和分析–根據高品質警示建立事件

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| IR-3 | 19.6 | IR-4、IR-5 |

確定您有建立高品質警示的流程，並測量警示品質。 這可讓您學習過去事件的課程，並設定分析師的警示優先順序，讓他們不會浪費時間誤報。 

高品質的警示可以根據過去事件的體驗、驗證的社區來源，以及設計來藉由融合和關聯各種信號來源來產生和清除警示的工具來建立。 

Azure 資訊安全中心在許多 Azure 資產之間提供高品質的警示。 您可以使用 ASC 資料連線器，將警示串流至 Azure Sentinel。 Azure Sentinel 可讓您建立 advanced 警示規則，以自動產生調查的事件。 

使用「匯出」功能來匯出您的 Azure 資訊安全中心警示和建議，以協助找出 Azure 資源的風險。 以手動方式或持續持續的方式匯出警示和建議。

- [如何設定匯出](../../security-center/continuous-export.md)

- [如何將警示串流至 Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全性作業](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [事件準備](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [威脅情報](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4：偵測和分析–調查事件

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| IR-4 | 19 | IR-4 |

請確定分析師可以在調查潛在事件時查詢並使用各種不同的資料來源，以建立發生狀況的完整觀點。 您應收集不同的記錄檔，以追蹤潛在攻擊者在整個終止鏈上的活動，以避免點。  您也應該確定已針對其他分析師和未來的歷程記錄參考來捕捉見解和學習。  

用於調查的資料來源包括已從範圍內服務和執行中的系統收集的集中式記錄來源，但也可以包括：

- 網路資料–使用網路安全性群組的流量記錄、Azure 網路監看員和 Azure 監視器來捕捉網路流量記錄和其他分析資訊。 

- 執行中系統的快照集： 

    - 使用 Azure 虛擬機器的快照集功能來建立執行中系統磁片的快照集。 

    - 使用作業系統的原生記憶體傾印功能來建立執行中系統記憶體的快照。

    - 您可以使用 Azure 服務的快照集功能，或您軟體本身的功能來建立執行中系統的快照集。

Azure Sentinel 在幾乎任何記錄來源和案例管理入口網站中提供廣泛的資料分析，以管理事件的完整生命週期。 調查期間的智慧資訊可以與事件產生關聯，以供追蹤和報告之用。 

- [建立 Windows 機器磁片的快照集](../../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [建立 Linux 機器磁片的快照集](../../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure 支援診斷資訊和記憶體傾印集合](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [使用 Azure Sentinel 調查事件](../../sentinel/tutorial-investigate-cases.md)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全性作業](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [事件準備](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [威脅情報](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5：偵測和分析-設定事件優先順序

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| IR-5 | 19.8 | CA-2，IR-4 |

根據警示嚴重性和資產敏感性，提供內容給分析師，以根據警示的嚴重性和資產敏感性來優先處理事件。 

Azure 資訊安全中心會將嚴重性指派給每個警示，以協助您排列應先調查哪些警示的優先順序。 嚴重性取決於資訊安全中心在尋找或用來發出警示的分析中的可信度，以及導致警示的活動背後有惡意意圖的信賴等級。

此外，請使用標記來標示資源，並建立命名系統來識別和分類 Azure 資源，尤其是處理敏感性資料的資源。  您需負責根據發生事件的 Azure 資源和環境的重要性，設定警示的補救優先順序。

- [Azure 資訊安全中心的安全性警示](../../security-center/security-center-alerts-overview.md)

- [使用標記來組織 Azure 資源](/azure/azure-resource-manager/resource-group-using-tags)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全性作業](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [事件準備](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [威脅情報](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6：內含專案、根除和復原–將事件處理自動化

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| IR-6 | 19 | IR-4、IR-5、IR-6 |

將手動重複的工作自動化，以加快回應時間並降低分析師的負擔。 手動工作需要較長的時間來執行、讓每個事件變慢，並減少分析師可以處理的事件數目。 手動工作也會增加分析師疲勞，這樣會增加造成延遲的人為錯誤的風險，並降低分析師在複雜工作上有效地專注的能力。 使用 Azure 資訊安全中心和 Azure Sentinel 中的工作流程自動化功能，自動觸發動作或執行腳本，以回應傳入的安全性警示。 腳本會採取動作，例如傳送通知、停用帳戶，以及隔離有問題的網路。 

- [在安全中心設定工作流程自動化](../../security-center/workflow-automation.md)

- [在 Azure 資訊安全中心中設定自動化威脅回應](../../security-center/tutorial-security-incident.md#triage-security-alerts)

- [在 Azure Sentinel 中設定自動化威脅回應](../../sentinel/tutorial-respond-threats-playbook.md)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全性作業](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [事件準備](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [威脅情報](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

