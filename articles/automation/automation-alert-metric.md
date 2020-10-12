---
title: 使用度量警示來監視 Azure 自動化 Runbook
description: 本文說明如何根據 runbook 完成狀態設定計量警示。
services: automation
ms.date: 08/10/2020
ms.topic: article
ms.openlocfilehash: 8767687f0b72d3469bef570770ac81fa8300097f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88055918"
---
# <a name="monitor-runbooks-with-metric-alerts"></a>使用計量警示監視 Runbook

在本文中，您將瞭解如何根據 runbook 完成狀態建立計量 [警示](../azure-monitor/platform/alerts-metric-overview.md) 。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](https://portal.azure.com)

## <a name="create-alert"></a>建立警示

警示可讓您定義要監視的條件，以及符合該條件時所要採取的動作。

1. 按一下 [ **所有服務**]，然後搜尋並選取 [ **自動化帳戶**]，以啟動 Azure 入口網站中的 Azure 自動化服務。

2. 在您的自動化帳戶清單中，選取您要為其建立警示的帳戶。 

3. 在 [ **監視**] 底下，選取 [ **警示** ]，然後選取 [ **+ 新增警示規則**]。 目標的範圍已定義，並且與您的自動化帳戶相關聯。

### <a name="configure-alert-criteria"></a>設定警示準則

1. 按一下 [ **選取條件**]。 選取 [**信號類型**] 的 [**計量**]，然後從清單中選擇 [**作業總計**]。

2. 您可以在 [設定訊號邏輯] 頁面中定義會觸發警示的邏輯。 歷程記錄圖表下會顯示兩個維度，即 [Runbook 名稱] 和 [狀態]。 維度是可用來篩選結果的不同計量屬性。 針對 [Runbook 名稱]，請選取需要警示的 Runbook，或將所有 Runbook 的警示保留為空白。 針對 [狀態]，請從下拉式清單中選取您要監視的狀態。 出現在下拉式清單中的 Runbook 名稱和狀態值，都僅屬於在過去一週執行的作業。

   如果您想要對未顯示在下拉式清單中的狀態或 runbook 發出警示，請按一下維度旁的 [ **加入自訂值** ] 選項。 此動作會開啟一個對話方塊，讓您指定未最近針對該維度發出的自訂值。 如果您輸入屬性不存在的值，則不會觸發警示。

   > [!NOTE]
   > 如果您未指定 **Runbook 名稱** 維度的名稱，如果有任何 runbook 符合狀態準則（包括隱藏的系統 runbook），您將會收到警示。

    例如，若要在 runbook 傳回 _失敗_ 狀態時發出警示，除了指定 runbook 名稱之外，還會針對 **狀態** 維度新增自訂維度值 **失敗**。

    :::image type="content" source="./media/automation-alert-metric/specify-dimension-custom-value.png" alt-text="指定自訂維度值" border="false":::

3. 在 [警示邏輯] 下，定義警示的條件和閾值。 定義的條件會顯示如下的預覽。

4. 在 [ **評估依據**] 底下，選取查詢的時間範圍，以及您想要執行查詢的頻率。 例如，如果您選擇過去 **5 分鐘** 的 **期間**，且 **每隔1分鐘** 的 **頻率**，警示會在過去5分鐘內尋找符合準則的 runbook 數目。 此查詢會每分鐘執行一次，且一旦在 5 分鐘的期間內未再找到您所定義的警示準則，警示就會自行停用。 完成後，請按一下 [完成]。

   ![選取警示的資源](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-the-action-to-take"></a>定義要採取的動作

1. 在 [ **動作群組**] 底下，選取 [ **指定動作群組**]。 動作群組是一組可讓您跨多個警示使用的動作。 其中可包括 (但不限於) 電子郵件通知、Runbook、Webhook 和等多種項目。 若要深入瞭解動作群組，以及建立傳送電子郵件通知的步驟，請參閱 [建立和管理動作群組](../azure-monitor/platform/action-groups.md)。

### <a name="define-alert-details"></a>定義警示詳細資料

1. 在 [ **警示規則詳細資料**] 下，為警示提供易記的名稱和描述。 設定用來比對警示條件的 [嚴重性]。 嚴重性從 0 到 5 分為五種。 無論警示的嚴重性為何，系統皆會同等視之，您可以比對與您的商務邏輯相符的嚴重性。

1. 預設會在建立時啟用規則，除非您在建立時針對 [**啟用警示規則**] 選項選取 [**否**]。 針對在停用狀態下建立的警示，您可以在準備就緒時，于未來啟用警示。 選取 [ **建立警示規則** ] 以儲存您的變更。

## <a name="receive-notification"></a>接收通知

在符合警示準則時，動作群組將會執行已定義的動作。 在本文的範例中，會傳送一則電子郵件。 下圖以範例說明您在警示觸發之後所收到的電子郵件：

![電子郵件警示](./media/automation-alert-activity-log/alert-email.png)

當計量不再超出定義的閾值後，警示即會停用，且動作群組會執行已定義的動作。 如果選取了電子郵件動作類型，則會傳送電子郵件指出狀況已解決。

## <a name="next-steps"></a>後續步驟

* 如需詳細資訊，請參閱[使用警示來觸發 Azure 自動化 Runbook](automation-create-alert-triggered-runbook.md)。
