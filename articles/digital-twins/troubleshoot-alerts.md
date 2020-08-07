---
title: 設定警示
titleSuffix: Azure Digital Twins
description: 請參閱如何啟用 Azure 數位 Twins 計量的警示。
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: 1b296cd942e36817da2832467ab603ebd833f825
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87908866"
---
# <a name="troubleshooting-azure-digital-twins-alerts"></a>針對 Azure 數位 Twins 進行疑難排解：警示

Azure 數位 Twins 會收集您的服務實例的[計量](troubleshoot-metrics.md)，以提供資源狀態的相關資訊。 您可以使用這些計量來評估 Azure 數位 Twins 服務及其連線資源的整體健全狀況。

當您的計量資料中發現重要條件時，**警示**會主動通知您。 它們可讓您在系統使用者注意到問題之前，找出並解決問題。 如需有關警示的詳細資訊，請參閱[*Microsoft Azure 中的警示總覽*](../azure-monitor/platform/alerts-overview.md)。

## <a name="turn-on-alerts"></a>開啟警示

以下說明如何為您的 Azure 數位 Twins 實例啟用警示：

1. 登入[Azure 入口網站](https://portal.azure.com)，然後流覽至您的 Azure 數位 Twins 實例。 您可以在入口網站的搜尋列中輸入其名稱來尋找它。 

2. 從功能表選取 [**警示**]，然後選取 [ **+ 新增警示規則**]。

    :::image type="content" source="media/troubleshoot-alerts/alerts-pre.png" alt-text="顯示 [警示] 頁面和要新增之按鈕的螢幕擷取畫面。尚未設定任何警示" lightbox="media/troubleshoot-alerts/alerts-pre.png":::

3. 在接下來的 [*建立警示規則*] 頁面上，您可以遵循提示來定義條件、要觸發的動作，以及警示詳細資料。     
    * **範圍**詳細資料應該會自動填入您實例的詳細資料。
    * 您將定義**條件**和**動作群組**詳細資料，以自訂警示觸發程式和回應。

    :::image type="content" source="media/troubleshoot-alerts/create-alert-rule.png" alt-text="顯示 [建立警示規則] 頁面和 [範圍]、[條件] 和 [動作群組] 區段的螢幕擷取畫面" lightbox="media/troubleshoot-alerts/create-alert-rule.png":::

如需填寫這些欄位的引導式逐步解說，請參閱[*Microsoft Azure 中的警示總覽*](../azure-monitor/platform/alerts-overview.md)。 以下是 Azure 數位 Twins 的步驟範例。

以下是來自「*選取條件*」流程的摘錄，其中說明哪些類型的警示信號可供 Azure 數位 Twins 使用。 在此頁面上，您可以篩選信號的類型，並從清單中選取您想要的信號。

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic.png" alt-text="顯示第一個 [設定信號邏輯] 頁面的螢幕擷取畫面。[信號類型] 方塊上有醒目提示可選取計量或活動記錄，以及可選取的計量清單":::

選取信號之後，系統會要求您設定警示的邏輯。 您可以篩選維度、設定警示的臨界值，以及設定條件的檢查頻率。 以下是設定當平均路由失敗率度量高於5% 時的警示的範例。

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic-2.png" alt-text="顯示第二個 [設定信號邏輯] 頁面的螢幕擷取畫面。此頁面會顯示計量歷程記錄、有一個區域可篩選維度（例如事件方格作業），以及一個用於定義警示邏輯（例如「平均大於5」）的區段":::
 
設定警示之後，它們會顯示在您實例的 [*警示*] 頁面上。
 
:::image type="content" source="media/troubleshoot-alerts/alerts-post.png" alt-text="顯示 [警示] 頁面和要新增之按鈕的螢幕擷取畫面。已設定一個警示" lightbox="media/troubleshoot-alerts/alerts-post.png":::


## <a name="next-steps"></a>後續步驟

* 如需有關 Azure 監視器警示的詳細資訊，請參閱[*Microsoft Azure 中的警示總覽*](../azure-monitor/platform/alerts-overview.md)。
* 如需 Azure 數位 Twins 計量的詳細資訊，請參閱[*疑難排解：使用 Azure 監視器來查看計量*](troubleshoot-metrics.md)。
* 若要瞭解如何為您的計量啟用診斷記錄，請參閱[*疑難排解：設定診斷*](troubleshoot-diagnostics.md)。
