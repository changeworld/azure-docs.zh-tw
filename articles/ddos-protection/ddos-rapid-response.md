---
title: Azure DDoS 快速回應
description: 瞭解如何在主動攻擊期間，與 DDoS 專家聯繫以取得特殊支援。
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: yitoh
ms.openlocfilehash: 8de95a56f3da928997a97ff216b13d522c82afca
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814188"
---
# <a name="azure-ddos-rapid-response"></a>Azure DDoS 快速回應

在主動存取期間，Azure DDoS 保護標準客戶可以存取 DDoS 快速回應 (DRR) 小組，以協助在攻擊和攻擊後分析期間進行攻擊調查。

## <a name="prerequisites"></a>必要條件

- 在您可以完成本教學課程中的步驟之前，您必須先建立 [Azure DDoS 標準保護計劃](manage-ddos-protection.md)。

## <a name="when-to-engage-drr"></a>參與 DRR 的時機

如果有下列情況，您應該只參與 DRR： 

- 在 DDoS 攻擊期間，如果您發現受保護資源的效能嚴重降低或無法使用資源。 複習上述的步驟2，設定監視以偵測資源可用性和效能問題。
- 您認為您的資源正遭受 DDoS 攻擊，但 DDoS 保護服務不會有效地緩和攻擊。
- 您正在計劃一個會大幅增加網路流量的病毒式事件。
- 適用于對業務造成重大影響的攻擊。

## <a name="engage-drr-during-an-active-attack"></a>在主動攻擊期間參與 DRR

1. 從 Azure 入口網站建立新的支援要求時，選擇 [ **問題類型** ] 為 [技術]。
2. 選擇 **服務** 作為 **DDOS 保護**。
3. 選擇 [資源] 下拉式功能表中的資源。 _您必須選取與受 DDoS 保護標準保護的虛擬網路連結的 DDoS 方案，以與 DRR 互動。_

    ![選擇資源](./media/ddos-rapid-response/choose-resource.png)

4. 在 [下一個 **問題** ] 頁面上，選取 **嚴重性** 為重大影響，並將 **問題類型** 選取為 [遭受攻擊]。

    ![PSeverity 和問題類型](./media/ddos-rapid-response/severity-and-problem-type.png)

5. 完成其他詳細資料，並提交支援要求。

DRR 遵循 Azure Rapid Response 支援模型。 如需快速回應的詳細資訊，請參閱 [支援範圍和回應](https://azure.microsoft.com/en-us/support/plans/response/) 性。

若要深入瞭解，請閱讀「 [DDoS 保護標準」檔](./ddos-protection-overview.md)。

## <a name="next-steps"></a>後續步驟

- 瞭解如何 [透過模擬進行測試](test-through-simulations.md)。
- 瞭解如何 [查看和設定 DDoS 保護遙測](telemetry.md)。
- 瞭解如何 [查看和設定 DDoS 診斷記錄](diagnostic-logging.md)。