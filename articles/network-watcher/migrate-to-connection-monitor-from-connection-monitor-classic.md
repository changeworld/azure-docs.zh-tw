---
title: 從連線監視器遷移至連線監視器
titleSuffix: Azure Network Watcher
description: 瞭解如何從連線監視器遷移至連線監視器。
services: network-watcher
documentationcenter: na
author: vinynigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: vinigam
ms.openlocfilehash: eed68ff7e6b002e7a5985b336686b646a1a9acee
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95522941"
---
# <a name="migrate-to-connection-monitor-from-connection-monitor-classic"></a>從連線監視器遷移至連線監視器 (傳統) 

您可以只需按幾下，就能將現有的連線監視器遷移至新的已改善連線監視器，而不需要停機。 若要深入瞭解這些優點，請參閱 [連接監視器](./connection-monitor-overview.md)。

## <a name="key-points-to-note"></a>要注意的重點

遷移有助於產生下列結果：

* 代理程式和防火牆設定的運作方式相同。 不需要任何變更。 
* 現有的連接監視器會對應到連線監視器 > 測試群組 > 測試格式。 您可以藉由選取 [ **編輯**] 來查看和修改新連線監視器的內容、下載範本以變更連線監視器，以及透過 Azure Resource Manager 提交。 
* 具有網路監看員擴充功能的 Azure 虛擬機器，會將資料傳送到工作區和計量。 連線監視器可讓您透過新的計量 (ChecksFailedPercent 和 RoundTripTimeMs) ，而不是舊的計量 (ProbesFailedPercent 和 AverageRoundtripMs) 提供資料。 
* 資料監視：
   * **警示**：自動遷移至新的計量。
   * **儀表板和** 整合：需要手動編輯計量集。 
    
## <a name="prerequisites"></a>必要條件

如果您是使用自訂工作區，請確定您的訂用帳戶和 Log Analytics 工作區的區域中已啟用網路監看員。 

## <a name="migrate-the-connection-monitors"></a>遷移連接監視器

1. 若要將較舊的連線監視器遷移至較新的連接監視器，請選取 [連線 **監視器**]，然後選取 [ **遷移連線監視器**]。

    ![顯示將連線監視器遷移至連線監視器的螢幕擷取畫面。](./media/connection-monitor-2-preview/migrate-cm-to-cm-preview.png)
    
1. 選取您的訂用帳戶和您想要遷移的連接監視，然後選取 [ **遷移選取** 的]。 

只要按幾下滑鼠，就可以將現有的連線監視器遷移至連線監視器。 

您現在可以自訂連線監視器屬性、變更預設工作區、下載範本，以及檢查遷移狀態。 

開始遷移之後，會進行下列變更： 
* Azure Resource Manager 的資源變更為較新的連線監視器。
    * 連線監視器的名稱、區域和訂用帳戶會維持不變。 資源識別碼不會受到影響。
    * 除非已自訂連線監視器，否則在訂用帳戶和連線監視器的區域中會建立預設的 Log Analytics 工作區。 此工作區是監視資料的儲存位置。 測試結果資料也會儲存在計量中。
    * 每項測試都會遷移至名為 *defaultTestGroup* 的測試群組。
    * 來源和目的地端點是在新的測試群組中建立及使用。 預設名稱為 *defaultSourceEndpoint* 和 *defaultDestinationEndpoint*。
    * 目的地埠和探查間隔會移至名為 *defaultTestConfiguration* 的測試設定。 此通訊協定是根據埠值設定。 成功閾值和其他選擇性屬性會保留空白。
* 計量警示會遷移至連線監視器計量警示。 計量不同，因此是變更。 如需詳細資訊，請參閱 [使用連線監視器進行網路連線監視](./connection-monitor-overview.md#metrics-in-azure-monitor)。
* 已遷移的連線監視器不會再顯示為較舊的連線監視器解決方案。 它們現在僅適用于連線監視器。
* 任何外部整合（例如 Power BI 和 Grafana 中的儀表板，以及與安全性資訊和事件管理)  (的整合，都必須以手動方式遷移）。 這是遷移安裝程式所需執行的唯一手動步驟。

## <a name="next-steps"></a>後續步驟

若要深入瞭解連接監視器，請參閱：
* [從網路效能監控遷移至連線監視器](/azure/network-watcher/migrate-to-connection-monitor-from-network-performance-monitor)
* [使用 Azure 入口網站建立連線監視器](./connection-monitor-create-using-portal.md)