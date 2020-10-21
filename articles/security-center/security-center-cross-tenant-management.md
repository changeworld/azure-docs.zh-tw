---
title: Azure 資訊安全中心中的跨租使用者管理 |Microsoft Docs
description: 瞭解如何使用 Azure 委派的資源管理設定跨租使用者管理，以管理安全性中心內多個租使用者的安全性狀態。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 7d51291a-4b00-4e68-b872-0808b60e6d9c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2019
ms.author: memildin
ms.openlocfilehash: 68338341d47b8fa15d0bd88ea23e951210105fc4
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92340779"
---
# <a name="cross-tenant-management-in-security-center"></a>安全性中心的跨租使用者管理

跨租使用者管理可讓您利用 [Azure 委派的資源管理](../lighthouse/concepts/azure-delegated-resource-management.md) ，在「安全性中心」內查看和管理多個租使用者的安全性狀況。 從單一視圖有效率地管理多個租使用者，而不需要登入每個租使用者的目錄。

- 服務提供者可以從自己的租使用者中管理多個客戶資源的安全性狀態。

- 有多個租使用者的組織安全性小組，可以從單一位置查看和管理其安全性狀態。

## <a name="set-up-cross-tenant-management"></a>設定跨租用戶管理

使用 [Azure 委派的資源管理](../lighthouse/concepts/azure-delegated-resource-management.md)，將受控租使用者資源的存取權委派給您自己的租使用者，以設定跨租使用者管理。

> [!NOTE]
> Azure 委派的資源管理是 Azure Lighthouse 的重要元件之一。

## <a name="how-does-cross-tenant-management-work-in-security-center"></a>跨租使用者管理如何在安全性中心內運作

您可以透過在單一租使用者中管理多個訂用帳戶的相同方式，來檢查和管理多個租使用者的訂用帳戶。

從頂端功能表列中，按一下篩選圖示，然後從您想要查看的每個租使用者目錄中選取訂用帳戶。

  ![篩選租使用者](./media/security-center-cross-tenant-management/cross-tenant-filter.png)

視圖和動作基本上是相同的。 以下是一些範例：

- **管理安全性原則**：從一個觀點來看，使用 [原則](tutorial-security-policy.md)來管理許多資源的安全性狀態、採取具有安全性建議的動作，以及收集和管理安全性相關資料。
- **改善安全分數和合規性**狀態：跨租使用者可見度可讓您查看所有租使用者的整體安全性狀態，以及在何處及如何針對每個租使用者獲得最佳的 [安全分數](secure-score-security-controls.md) 和 [合規性](security-center-compliance-dashboard.md) 狀態。
- **補救建議**：一次從不同的租使用者監視及修復許多資源的 [建議](security-center-recommendations.md) 。 然後，您可以立即處理在所有租使用者之間呈現最高風險的弱點。
- **管理警示**：偵測不同租使用者中的 [警示](security-center-alerts-overview.md) 。 使用可採取動作的 [補救步驟](security-center-managing-and-responding-alerts.md)，對不符合規範的資源採取動作。

- **管理先進的雲端防禦功能和更多功能**：管理各種威脅防護服務，例如 [及時 (JIT) VM 存取](security-center-just-in-time.md)、彈性 [網路強化](security-center-adaptive-network-hardening.md)、 [適應性應用](security-center-adaptive-application.md)程式控制等等。
 
## <a name="next-steps"></a>後續步驟
本文說明跨租使用者管理如何在安全性中心內運作。 如要深入了解資訊安全中心，請參閱下列主題：

* [使用 Azure 資訊安全中心強化您的安全性](security-center-monitoring.md) 狀態-瞭解如何監視 Azure 資源的健康情況。
* [AZURE 資訊安全中心常見問題](faq-general.md) -尋找有關使用服務的常見問題。
* [瞭解企業案例中的 Azure Lighthouse](../lighthouse/concepts/enterprise.md) -探索 Azure Lighthouse 如何簡化企業內使用多個 Azure AD 租使用者的跨租使用者管理。