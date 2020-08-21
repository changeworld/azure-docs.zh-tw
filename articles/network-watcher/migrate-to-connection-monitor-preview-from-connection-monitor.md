---
title: '從連線監視器遷移至連線監視器 (預覽版) '
titleSuffix: Azure Network Watcher
description: 瞭解如何從連線監視器 (預覽) 遷移至連線監視器。
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/20/2020
ms.author: vinigam
ms.openlocfilehash: ddf6e326df876229d32ef15983f76879836f1fca
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88701470"
---
# <a name="migrate-to-connection-monitor-preview-from-connection-monitor"></a>從連線監視器遷移至連線監視器 (預覽版) 

您可以只要按一下就能將現有的連線監視器遷移至全新和改良的連線監視器 (預覽) ，而且不需要停機。 若要深入瞭解這些權益，您可以閱讀連線 [監視器 (預覽版) ](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview)

## <a name="key-points-to-note"></a>要注意的重點

* 代理程式和防火牆設定的運作方式， (不需要觸控)  
* 現有的連線監視器將會對應至連線監視器 (Preview) > 測試組 > 測試格式。 使用者可以按一下 [ *編輯* ] 以查看和修改新連線監視器的內容，並下載範本以變更連線監視器，並透過 Azure Resource Manager 提交。 
* 具有網路監看員擴充功能的 Azure 虛擬機器，會將資料傳送到工作區和計量。 連線監視器可讓您透過新的計量取得資料 (ChecksFailedPercent (Preview) 和 RoundTripTimeMs (Preview) # A5 取代「停止舊計量」 (ProbesFailedPercent 和 AverageRoundtripMs)  
* 監視資料
    * 警示–將在遷移過程中遷移至新的計量
    * 儀表板和整合–您將必須手動編輯計量集。 
    
## <a name="prerequisites"></a>先決條件

如果使用自訂工作區，請確定已在 Log Analytics 工作區的訂用帳戶和區域中啟用網路監看員 

## <a name="steps-to-migrate-from-connection-monitor-to-connection-monitor-preview"></a>從連線監視器遷移至連線監視器 (預覽) 的步驟

1. 按一下 [連線監視器]，流覽至 [遷移連線監視器]，將連線監視器從較舊的解決方案遷移至較新的解決方案。

    ![顯示將連線監視器遷移至連線監視器預覽的螢幕擷取畫面](./media/connection-monitor-2-preview/migrate-cm-to-cm-preview.png)
    
1. 選取 [訂用帳戶和連線監視器]，然後按一下 [遷移選取的]。 按一下 [將現有的連接監視器遷移至連線監視器] (預覽)  
1. 您可以自訂連線監視器屬性、變更預設工作區、下載範本，以及檢查遷移狀態。 
1. 開始遷移後，會發生下列變更： 
    1. 針對較新的連接監視器 Azure Resource Manager 資源變更
        1. 連線監視器的名稱、區域和訂用帳戶會維持不變。 因此，資源識別碼不會有任何影響。
        1. 除非自訂，否則會在連線監視的區域和訂用帳戶中建立預設的 Log Analytics 工作區。 此工作區是監視資料的儲存位置。 測試結果資料也會儲存在計量中。
        1. 每項測試都會遷移至名為 * defaultTestGroup * 的測試群組
        1.  來源和目的地端點是在建立的測試群組中建立及使用。 預設名稱為 *defaultSourceEndpoint* 和 *defaultDestinationEndpoint*
        1. 目的地埠和探查間隔會移至名為 *defaultTestConfiguration*的測試設定。 根據埠值，會設定通訊協定。 成功閾值和其他選擇性屬性會保留空白。
    1. 計量警示會遷移至連線監視器 (預覽版) 計量警示。 計量不同 <link to metric section in the doc> ，因此變更
    1. 已遷移的連線監視器將不會顯示在舊版連線監視器解決方案中，現在只能在連線監視器 (預覽版中使用) 
    1. 任何外部整合（例如 Power BI 中的儀表板、Grafana、與 SIEM 系統的整合）都必須由使用者直接遷移。 這是使用者在遷移其設定時，唯一需要執行的手動步驟。

## <a name="next-steps"></a>後續步驟

* 瞭解 [如何從網路效能監控遷移至連線監視器 (Preview) ](migrate-to-connection-monitor-preview-from-network-performance-monitor.md)
* 瞭解 [如何使用 Azure 入口網站建立連線監視器 (預覽版) ](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview-create-using-portal)
