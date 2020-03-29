---
title: Azure 安全中心的跨租戶管理 |微軟文檔
description: 瞭解如何設置跨租戶管理，以便使用 Azure 委派的資源管理管理安全中心中多個租戶的安全狀況。
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
ms.openlocfilehash: 2aeb2ab4cfb4ed5e8652638aaced320cc7119d3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919476"
---
# <a name="cross-tenant-management-in-security-center"></a>安全中心的跨租戶管理

跨租戶管理使您能夠通過利用[Azure 委派的資源管理](../lighthouse/concepts/azure-delegated-resource-management.md)來查看和管理安全中心中多個租戶的安全狀態。 從單個視圖高效地管理多個租戶，而無需登錄到每個租戶的目錄。

- 服務提供者可以從自己的租戶管理資源的安全狀態，適用于多個客戶。

- 具有多個租戶的組織的安全團隊可以從單個位置查看和管理其安全狀態。

## <a name="set-up-cross-tenant-management"></a>設置跨租戶管理

通過使用[Azure 委派的資源管理](../lighthouse/concepts/azure-delegated-resource-management.md)，通過將對託管租戶資源的訪問委派給您自己的租戶來設置跨租戶管理。

> [!NOTE]
> Azure 委派的資源管理是 Azure Lighthouse 的重要元件之一。

## <a name="how-does-cross-tenant-management-work-in-security-center"></a>跨租戶管理如何在安全中心工作

您可以查看和管理多個租戶中的訂閱，就像管理單個租戶中的多個訂閱一樣。

從頂部功能表列中，按一下篩選器圖示，並從每個租戶的目錄中選擇要查看的訂閱。

  ![篩選租戶](./media/security-center-cross-tenant-management/cross-tenant-filter.png)

觀點和操作基本相同。 以下是一些範例：

- **管理安全性原則**：從一個角度，使用[策略](tutorial-security-policy.md)管理許多資源的安全狀態，使用安全建議執行操作，並收集和管理與安全相關的資料。
- **改善安全分數和合規性狀態**：跨租戶可見度使您能夠查看所有租戶的整體安全狀況，以及最佳改進每個租戶[的安全得分](security-center-secure-score.md)和[合規性狀況](security-center-compliance-dashboard.md)的位置和方式。
- **修正建議**：一次監視和修正來自各個租戶的許多資源[的建議](security-center-recommendations.md)。 然後，您可以立即解決在所有租戶中呈現最高風險的漏洞。
- **管理警報**：檢測不同租戶的[警報](security-center-alerts-overview.md)。 對不符合可操作[補救步驟](security-center-managing-and-responding-alerts.md)的資源採取行動。

- **管理高級雲防禦功能等**：管理各種威脅防護服務，如[及時 （JIT） VM 訪問](security-center-just-in-time.md)、[自我調整網路強化](security-center-adaptive-network-hardening.md)、[自我調整應用程式控制](security-center-adaptive-application.md)等。
 
## <a name="next-steps"></a>後續步驟
本文介紹了跨租戶管理在安全中心的工作原理。 如要深入了解資訊安全中心，請參閱下列主題：

* [使用 Azure 安全中心增強安全狀態](security-center-monitoring.md)- 瞭解如何監視 Azure 資源的運行狀況。
* [Azure 資訊安全中心常見問題集](faq-general.md) - 尋找有關使用服務的常見問題。
* [瞭解企業方案中的 Azure 燈塔](https://docs.microsoft.com/azure/lighthouse/concepts/enterprise)- 瞭解 Azure 燈塔如何在使用多個 Azure AD 租戶的企業中簡化跨租戶管理。