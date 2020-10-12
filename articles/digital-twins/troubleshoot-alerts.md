---
title: 設定警示
titleSuffix: Azure Digital Twins
description: 瞭解如何啟用 Azure 數位 Twins 計量的警示。
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: ae7e85624f5da06603ddc2675787b84203bc987b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90087198"
---
# <a name="troubleshooting-azure-digital-twins-alerts"></a>針對 Azure 數位 Twins 進行疑難排解：警示

Azure 數位 Twins 會收集服務實例的 [計量](troubleshoot-metrics.md) ，以提供您資源狀態的相關資訊。 您可以使用這些計量來評估 Azure 數位 Twins 服務及其連線資源的整體健康情況。

當您的計量資料中找到重要條件時，**警示**會主動通知您。 它們可讓您在系統使用者注意到問題之前，找出並解決問題。 您可以在 [*Microsoft Azure 的警示總覽*](../azure-monitor/platform/alerts-overview.md)中閱讀更多有關警示的資訊。

## <a name="turn-on-alerts"></a>開啟警示

以下是如何為您的 Azure 數位 Twins 實例啟用警示：

1. 登入 [Azure 入口網站](https://portal.azure.com) ，然後流覽至您的 Azure 數位 Twins 實例。 您可以在入口網站的搜尋列中輸入其名稱來尋找它。 

2. 從功能表選取 [ **警示** ]，然後選取 [ **+ 新增警示規則**]。

3. 在接下來的 [ *建立警示規則* ] 頁面上，您可以依照提示來定義條件、要觸發的動作，以及警示詳細資料。     
    * **範圍** 詳細資料應自動填入實例的詳細資料
    * 您將定義 **條件** 和 **動作群組** 詳細資料，以自訂警示觸發程式和回應
    * 在 [ **警示規則詳細資料** ] 區段中，輸入規則的名稱和選擇性描述。 如果您希望警示在建立後立即變成作用中狀態，您可以選取 [ _在建立時啟用警示規則_ ] 核取方塊。
        - 您也可以在其中選取 _資源群組_ 和 _嚴重性_ 層級。

4. 選取 [ _建立警示規則_ ] 按鈕，以建立您的警示規則。

:::image type="content" source="media/troubleshoot-alerts/create-alert-rule.png" alt-text="顯示 [建立警示規則] 頁面的螢幕擷取畫面，其中包含範圍、條件、動作群組和警示規則詳細資料的區段" lightbox="media/troubleshoot-alerts/create-alert-rule.png":::

如需填寫這些欄位的引導式逐步解說，請參閱 [*Microsoft Azure 中的警示總覽*](../azure-monitor/platform/alerts-overview.md)。 以下是適用于 Azure 數位 Twins 的一些步驟範例。

### <a name="select-conditions"></a>選取條件

以下是來自 *選取條件* 程式的摘要，說明 Azure 數位 Twins 可用的警示信號類型。 在此頁面上，您可以篩選信號的類型，並從清單中選取您想要的信號。

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic.png" alt-text="顯示 [建立警示規則] 頁面的螢幕擷取畫面，其中包含範圍、條件、動作群組和警示規則詳細資料的區段":::

選取信號之後，系統會要求您設定警示的邏輯。 您可以篩選維度、設定警示的臨界值，以及設定條件檢查的頻率。 以下範例說明如何設定當平均路由失敗率度量超過5% 時的警示。

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic-2.png" alt-text="顯示 [建立警示規則] 頁面的螢幕擷取畫面，其中包含範圍、條件、動作群組和警示規則詳細資料的區段":::

### <a name="verify-success"></a>確認是否成功

設定警示之後，他們會在您的實例的 [ *警示* ] 頁面上顯示回來。
 
:::image type="content" source="media/troubleshoot-alerts/alerts-post.png" alt-text="顯示 [建立警示規則] 頁面的螢幕擷取畫面，其中包含範圍、條件、動作群組和警示規則詳細資料的區段" lightbox="media/troubleshoot-alerts/alerts-post.png":::

## <a name="next-steps"></a>後續步驟

* 如需有關 Azure 監視器警示的詳細資訊，請參閱 [*Microsoft Azure 中的警示總覽*](../azure-monitor/platform/alerts-overview.md)。
* 如需 Azure 數位 Twins 計量的詳細資訊，請參閱 [*疑難排解：使用 Azure 監視器來查看計量*](troubleshoot-metrics.md)。
* 若要瞭解如何為您的計量啟用診斷記錄，請參閱 [*疑難排解：設定診斷*](troubleshoot-diagnostics.md)。
