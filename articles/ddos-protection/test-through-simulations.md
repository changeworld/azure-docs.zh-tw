---
title: Azure DDoS 保護模擬測試
description: 瞭解如何透過模擬進行測試
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: eff738e24b3abce52e80291c55a3ae64c3c8c853
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92905264"
---
# <a name="test-through-simulations"></a>透過模擬測試

最好的做法就是透過執行定期模擬，來測試您認為服務會如何回應攻擊的假設。 在測試期間，會驗證您的服務或應用程式是否會繼續如預期般運作，而且不會對使用者體驗產生任何中斷。 請以技術和處理程序的觀點，找出其中的差距，然後併入 DDoS 回應策略中。 建議在預備環境或非尖峰時間執行這類測試，將對生產環境的影響降到最低。

我們已與 [>breakingpoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud)（自助流量產生器）合作，以建立介面，讓 Azure 客戶可以針對模擬啟用 DDoS 保護的公用端點產生流量。 您可以使用模擬來：

- 驗證 Azure DDoS 保護會如何保護您的 Azure 資源免受 DDoS 攻擊。
- 使遭受 DDoS 攻擊時的事件回應程序達到最佳化。
- 記載 DDoS 合規性。
- 訓練網路安全性小組。

## <a name="prerequisites"></a>Prerequisites

- 在您可以完成本教學課程中的步驟之前，您必須先使用受保護的公用 IP 位址建立 [Azure DDoS 標準保護方案](manage-ddos-protection.md) 。
- 您必須先建立具有 [>breakingpoint Cloud](http://breakingpoint.cloud/)的帳戶。 

## <a name="configure-a-ddos-test-attack"></a>設定 DDoS 測試攻擊

1. 輸入或選取下列值，然後選取 [ **開始測試** ]：

    |設定        |值                                              |
    |---------      |---------                                          |
    |目標 IP 位址           | 輸入您想要測試的其中一個公用 IP 位址。                     |
    |連接埠號碼   | 輸入 443  。                       |
    |DDoS 設定檔 | 選取 [ **TCP SYN 洪水** ]。|
    |測試大小       | 選取 **200K pps、100 Mbps 和8個來源 ip。**                                  |
    |測試持續時間 | 選取 [ **10 分鐘** ]。|

它現在看起來應該像這樣：

![DDoS 攻擊模擬範例： >breakingpoint Cloud](./media/ddos-attack-simulation/ddos-attack-simulation-example-1.png)

## <a name="monitor-and-validate"></a>監視和驗證

1. 登入 https://portal.azure.com 並移至您的訂用帳戶。
1. 選取您測試攻擊的公用 IP 位址。
1. 在 [監視] 下，選取 [計量]。
1. 針對 **[** 計量]，請選取 [遭受 _DDoS 攻擊_ ]。

一旦資源遭受攻擊，您應該會看到值從 **0** 變更為 **1** ，如下圖所示：

![DDoS 攻擊模擬範例：入口網站](./media/ddos-attack-simulation/ddos-attack-simulation-example-2.png)

## <a name="next-steps"></a>下一步

- 瞭解如何 [查看和設定 DDoS 保護遙測](telemetry-monitoring-alerting.md)。
- 瞭解如何 [設定 DDoS 攻擊風險降低報告和流量記錄](reports-and-flow-logs.md)。
- 瞭解如何 [參與 DDoS 快速回應](ddos-rapid-response.md)。