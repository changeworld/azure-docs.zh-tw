---
title: Azure 安全控制 ─ 事件回應
description: Azure 安全控制事件回應
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 993793d21e6253188dfc199d8701cbe117503517
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408425"
---
# <a name="security-control-incident-response"></a>安全性控制:事件回應

通過開發和實施事件回應基礎結構(例如計劃、定義的角色、培訓、通信、管理監督),以快速發現攻擊,然後有效地遏制損害,消除攻擊者的存在,恢復網路和系統的完整性,保護組織的資訊及其聲譽。

## <a name="101-create-an-incident-response-guide"></a>10.1:建立事件回應指南

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 10.1 | 19.1, 19.2, 19.3 | 客戶 |

為您的組織構建事件回應指南。 確保有書面事件回應計劃,定義人員的所有角色以及事件處理/管理階段,從檢測到事件後審查。  

- [有關構建您自己的安全事件回應流程的指導](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [微軟安全回應中心事件剖析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [利用 NIST 的電腦安全事件處理指南,幫助建立您自己的事件回應計劃](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 建立事件評分與優先順序處理程序

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 10.2 | 19.8 | 客戶 |

安全中心為每個警報分配嚴重性,以説明您確定應首先調查哪些警報的優先順序。 嚴重性基於安全中心在查找中的信心程度或用於發出警報的分析,以及導致警報的活動背後存在惡意的置信度。 

此外,清楚地標記訂閱(對於前 使用標記創建命名系統以明確識別和分類 Azure 資源,尤其是處理敏感數據的資源,生產非 prod)。  您有責任根據事件發生的 Azure 資源和環境的重要性確定警報的修復優先順序。

- [Azure 資訊安全中心的安全性警示](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

- [使用標記來組織 Azure 資源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="103-test-security-response-procedures"></a>10.3: 測試安全回應程式

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 10.3 | 19 | 客戶 |

執行練習,以常規節奏測試系統的事件回應功能,以幫助保護 Azure 資源。 找出薄弱環節和差距,並根據需要修訂計劃。

- [NIST 出版物 - IT 計劃和功能測試、培訓和鍛煉計劃指南](https://csrc.nist.gov/publications/detail/sp/800-84/final)

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:提供安全事件聯繫人詳細資訊,並配置安全事件的警報通知

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 10.4 | 19.5 | 客戶 |

如果 Microsoft 安全回應中心 (MSRC) 發現您的數據已被非法或未經授權的方訪問,Microsoft 將使用安全事件聯繫資訊與您聯繫。 事後查看事件,以確保問題得到解決。

- [如何設定 Azure 安全中心安全連絡人](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

## <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 將安全警報納入事件回應系統

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 10.5 | 19.6 | 客戶 |

使用「連續匯出」功能匯出 Azure 安全中心警報和建議,以幫助識別 Azure 資源的風險。 "連續匯出"允許您手動或持續、持續的方式匯出警報和建議。 您可以使用 Azure 安全中心數據連接器將警報流式傳輸到 Azure 哨兵。

- [如何設定連續匯出](https://docs.microsoft.com/azure/security-center/continuous-export)

- [如何將警示流式傳輸到 Azure 哨兵](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

## <a name="106-automate-the-response-to-security-alerts"></a>10.6: 自動回應安全警報

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 10.6 | 19 | 客戶 |

使用 Azure 安全中心的工作流自動化功能通過安全警報和建議上的「邏輯應用」自動觸發回應,以保護 Azure 資源。

- [如何設定工作流程自動化和邏輯應用](https://docs.microsoft.com/azure/security-center/workflow-automation)


## <a name="next-steps"></a>後續步驟

- 請參閱下一個安全控制:[滲透測試和紅隊練習](security-control-penetration-tests-red-team-exercises.md)