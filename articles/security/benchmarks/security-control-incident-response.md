---
title: Azure 安全性控制-事件回應
description: Azure 安全性控制事件回應
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 993793d21e6253188dfc199d8701cbe117503517
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81408425"
---
# <a name="security-control-incident-response"></a>安全性控制：事件回應

藉由開發和實行事件回應基礎結構（例如計畫、定義的角色、訓練、通訊、管理監督）來快速探索攻擊，然後有效地包含損害、eradicating 攻擊者目前狀態，以及還原網路和系統的完整性，以保護組織的資訊以及其信譽。

## <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 10.1 | 19.1、19.2、19。3 | 客戶 |

建立您組織的事件回應指南。 請確定有書面的事件回應計畫，其中定義人員的所有角色，以及從偵測到事件後檢討的事件處理/管理階段。  

- [建立自有安全性事件回應程序的指引](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 安全性回應中心的事件剖析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [利用 NIST 的電腦安全性事件處理指南，協助建立自己的事件回應計畫](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程序

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 10.2 | 19.8 | 客戶 |

資訊安全中心會指派每個警示的嚴重性，協助您排定應先調查哪些警示。 嚴重性會依據資訊安全中心對用於發出警示的發現或分析其信心程度，以及信賴等級具有活動背後會導致警示的惡意意圖。 

此外，使用標記清楚地標示訂用帳戶 (例如， 生產、非生產) 並建立命名系統，以清楚地識別及分類 Azure 資源，尤其是處理敏感性資料的資源。  您需負責根據發生事件的 Azure 資源和環境的重要性，設定警示的補救優先順序。

- [Azure 資訊安全中心的安全性警示](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

- [使用標記來組織 Azure 資源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程序

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 10.3 | 19 | 客戶 |

進行練習以定期測試系統的事件回應功能，以協助保護您的 Azure 資源。 找出弱點和落差，並視需要修訂計畫。

- [NIST 的出版品 - Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://csrc.nist.gov/publications/detail/sp/800-84/final)

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 10.4 | 19.5 | 客戶 |

如果 Microsoft 安全性回應中心（MSRC）發現您的資料已由非法或未經授權的合作物件存取，Microsoft 將會使用安全性事件連絡人資訊來與您聯繫。 事後檢討事件，確保問題已解決。

- [如何設定 Azure 資訊安全中心的安全性連絡人](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

## <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 10.5 | 19.6 | 客戶 |

使用「連續匯出」功能來匯出您的 Azure 資訊安全中心警示和建議，以協助找出 Azure 資源的風險。 「連續匯出」可讓您以手動或持續不斷的方式來匯出警示和建議。 您可使用 Azure 資訊安全中心的資料連接器，將警示串流至 Azure Sentinel。

- [如何設定連續匯出](https://docs.microsoft.com/azure/security-center/continuous-export)

- [如何將警示串流至 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

## <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 10.6 | 19 | 客戶 |

使用 Azure 資訊安全中心中的工作流程自動化功能，透過「Logic Apps」來自動觸發安全性警示和建議的回應，以保護您的 Azure 資源。

- [如何設定工作流程自動化和 Logic Apps](https://docs.microsoft.com/azure/security-center/workflow-automation)


## <a name="next-steps"></a>後續步驟

- 請參閱下一個安全性控制：[滲透測試和 Red Team 練習](security-control-penetration-tests-red-team-exercises.md)