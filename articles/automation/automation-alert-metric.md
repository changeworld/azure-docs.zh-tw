---
title: 使用度量警示來監視 Azure 自動化 Runbook
description: 此文章說明如何根據計量來監視 Runbook。
services: automation
ms.date: 11/01/2018
ms.topic: article
ms.openlocfilehash: 20aaee5b699e9721bf9083030604df1385da1915
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83828741"
---
# <a name="monitor-runbooks-with-metric-alerts"></a>使用計量警示監視 Runbook

在本文中，您將了解如何根據 Runbook 的完成狀態來建立警示。

## <a name="sign-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 登入 Azure

## <a name="create-alert"></a>建立警示

警示可讓您定義要監視的條件，以及符合該條件時所要採取的動作。

在 Azure 入口網站中，瀏覽至您的自動化帳戶。 在 [監視] 之下選取 [警示]，然後按一下 [+ 新增警示規則]。 目標範圍已定義為您的自動化帳戶。

### <a name="configure-alert-criteria"></a>設定警示準則

1. 按一下 [新增準則]。 選取 [計量] 作為 [訊號類型]，然後從資料表中選擇 [作業總數]。

2. 您可以在 [設定訊號邏輯] 頁面中定義會觸發警示的邏輯。 歷程記錄圖表下會顯示兩個維度，即 [Runbook 名稱] 和 [狀態]。 維度是可用來篩選結果的不同計量屬性。 針對 [Runbook 名稱]，請選取需要警示的 Runbook，或將所有 Runbook 的警示保留為空白。 針對 [狀態]，請從下拉式清單中選取您要監視的狀態。 出現在下拉式清單中的 Runbook 名稱和狀態值，都僅屬於在過去一週執行的作業。

   如果您要使用警示的狀態或 Runbook 未顯示在下拉式清單中，請按一下維度旁的 **\+** 。 此動作會開啟一個對話方塊，讓您輸入最近未針對該維度發出的自訂值。 如果您輸入屬性不存在的值，則不會觸發警示。

   > [!NOTE]
   > 如果您未套用 **RunbookName** 維度的名稱，如果有任何 Runbook 符合狀態準則 (包括隱藏的系統 Runbook)，您就會收到警示。

3. 在 [警示邏輯] 下，定義警示的條件和閾值。 定義的條件會顯示如下的預覽。

4. 在 [評估依據] 下選取查詢的時間範圍，以及該查詢的執行頻率。 例如，如果您選擇 [過去 5 分鐘] 作為 [期間]，並選擇 [每隔 1 分鐘] 作為 [頻率]，則警示會尋找過去 5 分鐘內符合準則的 Runbook 數目。 此查詢會每分鐘執行一次，且一旦在 5 分鐘的期間內未再找到您所定義的警示準則，警示就會自行停用。 完成後，請按一下 [完成]。

   ![選取警示的資源](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-alert-details"></a>定義警示詳細資料

1. 在 **2.定義警示詳細資料**下，為警示指定易記名稱和說明。 設定用來比對警示條件的 [嚴重性]。 嚴重性從 0 到 5 分為五種。 無論警示的嚴重性為何，系統皆會同等視之，您可以比對與您的商務邏輯相符的嚴重性。

1. 區段底部有一個按鈕，可讓您在完成規則時加以啟用。 依預設會在建立規則時加以啟用。 如果選取 [否]，可以建立警示，而此時建立的警示會處於 [已停用] 狀態。 您可以在 Azure 監視器的 [規則] 頁面中，於準備就緒後選取警示，並按一下 [啟用] 以啟用該警示。

### <a name="define-the-action-to-take"></a>定義要採取的動作

1. 在 [3. 定義動作群組] 下，按一下 [+ 新增動作群組]。 動作群組是一組可讓您跨多個警示使用的動作。 其中可包括 (但不限於) 電子郵件通知、Runbook、Webhook 和等多種項目。 若要深入了解動作群組，請參閱[建立及管理動作群組](../azure-monitor/platform/action-groups.md)

1. 在 [動作群組名稱] 方塊中，為它提供易記名稱和簡短名稱。 使用這個群組傳送通知時，會使用簡短名稱來取代完整的動作群組名稱。

1. 在 [動作] 區段中的 [動作類型] 下，選取 [電子郵件/SMS/推播/語音]。

1. 在 [電子郵件/SMS/推播/語音] 頁面上為其命名。 請勾選 [電子郵件] 核取方塊，並輸入要使用的有效電子郵件地址。

   ![設定電子郵件動作群組](./media/automation-alert-activity-log/add-action-group.png)

1. 在 [電子郵件/SMS/推播/語音] 頁面上按一下 [確定] 加以關閉，然後按一下 [確定] 以關閉 [新增動作群組] 頁面。 此頁面中指定的名稱會儲存為 [動作名稱]。

1. 完成時，按一下 [儲存]。 此動作會建立一項規則，在完成的 Runbook 處於特定狀態時對您發出警示。

> [!NOTE]
> 將電子郵件地址新增至動作群組時會傳送電子郵件通知，指出該地址已新增至動作群組。

## <a name="receive-notification"></a>接收通知

在符合警示準則時，動作群組將會執行已定義的動作。 在本文的範例中，會傳送一則電子郵件。 下圖以範例說明您在警示觸發之後所收到的電子郵件：

![電子郵件警示](./media/automation-alert-activity-log/alert-email.png)

當計量不再超出定義的閾值後，警示即會停用，且動作群組會執行已定義的動作。 如果選取了電子郵件動作類型，則會傳送電子郵件指出狀況已解決。

## <a name="next-steps"></a>後續步驟

* 如需詳細資訊，請參閱[使用警示來觸發 Azure 自動化 Runbook](automation-create-alert-triggered-runbook.md)。
