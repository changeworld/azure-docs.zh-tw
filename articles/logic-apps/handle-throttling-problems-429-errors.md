---
title: 處理限制問題,或"429 - 請求太多"錯誤
description: 如何解決 Azure 邏輯應用中的限制問題或「HTTP 429 請求太多」錯誤
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: fbfd52065bc0522668488492de2181f252f86a4e
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272673"
---
# <a name="handle-throttling-problems-429---too-many-requests-errors-in-azure-logic-apps"></a>在 Azure 邏輯應用中處理限制問題(429 - "請求太多'錯誤)

在[Azure 邏輯應用中](../logic-apps/logic-apps-overview.md),邏輯應用在遇到限制時返回[「HTTP 429 請求太多」錯誤](https://developer.mozilla.org/docs/Web/HTTP/Status/429),當請求數超過目標在特定時間可以處理的速率時,就會發生此錯誤。 限制可能會造成延遲數據處理、性能降低和錯誤(如超出指定的重試策略)等問題。

![SQL 伺服器連線器中的限制](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-error.png)

以下是邏輯應用可能會遇到的一些常見限制類型:

* [邏輯應用程式](#logic-app-throttling)
* [連線](#connector-throttling)
* [目的地服務或系統](#destination-throttling)

<a name="logic-app-throttling"></a>

## <a name="logic-app-throttling"></a>邏輯應用限制

Azure 邏輯應用服務有自己的[輸送量限制](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)。 因此,如果邏輯應用超過這些限制,邏輯應用資源將受到限制,而不僅僅是特定的實例或運行。

要查找此等級的限制事件,請在 Azure 門戶中檢查邏輯應用的 **「指標」** 窗格。

1. 在[Azure 門戶](https://portal.azure.com)中,在邏輯應用設計器中打開邏輯應用。

1. 在邏輯應用功能表上,在 **「監視**」下,選擇 **「指標**」。。

1. 在 **「圖表標題**」下,選擇 **「添加指標**」,以便向現有指標添加另一個指標。

1. 在第一個指標欄中,從**METRIC**清單中選擇 **「操作限制事件**」。 在第二個指標欄中,從**METRIC**清單中選擇 **「觸發限制事件**」。

要處理此等級的限制,您有以下選項:

* 限制可同時運行的邏輯應用實例數。

  預設情況下,如果邏輯應用的觸發器條件同時滿足多次,則邏輯應用的多個觸發器實例同時運行或*並行*執行 。 此行為意味著每個觸發器實例在前面的工作流實例完成運行之前觸發。

  儘管可以同時運行的觸發器實例的預設數量是[無限的](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits),但您可以通過[打開觸發器的併發設置](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency)來限制此數位,如有必要,請選擇預設值以外的限制。

* 啟用高輸送量模式。

  邏輯套用對於[5 分鐘滾動間隔內執行的操作數有預設限制](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)。 要將此限制提高到最大操作數,請開啟邏輯應用中[的高輸送量模式](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode)。

* 禁用觸發器中的陣列辯論("拆分")行為。

  如果觸發器傳回要處理的剩餘工作流操作的陣列,則觸發器的[**「拆分開啟」** 設定](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch)將分割陣列項目並為每個陣列項啟動工作流實例,從而有效地觸發多個併發運行,最多達到[**拆分 On**限制](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits)。 要控制限制,請關閉**拆分打開**行為,讓邏輯應用使用單個調用處理整個陣列,而不是處理每個調用的單個項。

* 將操作重構為較小的邏輯應用。

  如前所述,邏輯應用僅限於可在 5[分鐘內執行的預設操作數](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)。 儘管可以通過啟用[高吞吐量模式](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode)來增加此限制,但您也可以考慮是否要將邏輯應用的操作分解為較小的邏輯應用,以便在每個邏輯應用中運行的操作數保持在限制之下。 這樣,您可以減輕單個邏輯應用資源的負擔,並將負載分佈在多個邏輯應用之間。 此解決方案更適合處理大型資料集或在每個迴圈反覆運算中旋轉大量併發運行的操作、迴圈反覆運算或超出操作執行限制的操作。

  例如,此邏輯應用執行所有工作,從 SQL Server 資料庫獲取表並從每個表獲取行。 **對於每個**循環併發遍每個表,以便**Get 行**操作返回每個表的行。 根據這些表中的數據量,這些操作可能會超過操作執行的限制。

  ![邏輯應用「之前」重構](./media/handle-throttling-problems-429-errors/refactor-logic-app-before-version.png)

  重構后,邏輯應用現在是父邏輯應用和子邏輯應用。 父級從 SQL Server 取得表,然後為每個表調用子邏輯應用以取得行:

  ![為一個操作建立邏輯應用](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-1.png)

  下面是父邏輯應用調用的子邏輯應用,用於獲取每個表的行:

  ![為第二個操作建立另一個邏輯應用](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-2.png)

<a name="connector-throttling"></a>

## <a name="connector-throttling"></a>連接器限制

每個連接器都有自己的限制限制,您可以在連接器的技術參考頁面上找到這些限制。 例如[,Azure 服務總線連接器](https://docs.microsoft.com/connectors/servicebus/)具有限制限制,允許每分鐘最多 6,000 個呼叫,而 SQL Server 連接器具有[限制限制,這些限制因操作類型而異](https://docs.microsoft.com/connectors/sql/)。

某些觸發器和操作(如 HTTP)具有[「重試策略」 ,](../logic-apps/logic-apps-exception-handling.md#retry-policies)您可以根據[重試策略限制](../logic-apps/logic-apps-limits-and-config.md#retry-policy-limits)進行自訂以實現異常處理。 此策略指定當原始請求失敗或超時時觸發器或操作重試請求,並生成 408、429 或 5xx 回應時重試請求的頻率。 因此,當限制開始並返回 429 錯誤時,邏輯應用將遵循受支援的重試策略。

要瞭解觸發器或操作是否支援重試策略,請檢查觸發器或操作的設置。 要查看觸發器或操作的重試嘗試,請轉到邏輯應用的執行歷史記錄,選擇要查看的運行,並展開該觸發器或操作以查看有關輸入、輸出和任何重試的詳細資訊,例如:

![檢視操作的執行歷史記錄、重試、輸入和輸出](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-retries.png)

儘管重試歷史記錄提供錯誤資訊,但連接器限制和目標[限制](#destination-throttling)之間可能難以區分。 在這種情況下,您可能需要查看回應的詳細資訊或執行一些限制間隔計算以標識源。

對於全域多租戶 Azure 邏輯應用服務中的邏輯應用,限制發生在*連接*級別。 因此,例如,對於在[整合服務環境(ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中運行的邏輯應用,對於非 ISE 連接,限制仍然會發生,因為它們在全域多租戶邏輯應用服務中運行。 但是,由 ISE 連接器創建的 ISE 連接不會受到限制,因為它們在 ISE 中運行。

要處理此等級的限制,您有以下選項:

* 為單個操作設置多個連接,以便邏輯應用對要處理的數據進行分區。

  對於此選項,請考慮是否可以使用相同的憑據跨多個連接將操作的請求劃分為同一目標來分配工作負荷。

  例如,假設您的邏輯應用從 SQL Server 資料庫獲取表,然後從每個表獲取行。 根據必須處理的行數,可以使用多個連接和多個**For 每個**迴圈將行總數劃分為較小的集進行處理。 此方案使用兩**個 For 每個**迴圈將行總數拆分為兩半。 第一**個 對於 每個**迴圈使用獲取前半部分的運算式。 另一**個 對於 每個**循環使用不同的表示式來取得後半部分,例如:<p>

    * 運算式 1:`take()`函數獲取集合的前面。 有關詳細資訊,請參閱[**`take()`** 函數](workflow-definition-language-functions-reference.md#take)。

      `@take(collection-or-array-name, div(length(collection-or-array-name), 2))`

    * 運算式 2:`skip()`函數刪除集合的前面並返回所有其他項。 有關詳細資訊,請參閱[**`skip()`** 函數](workflow-definition-language-functions-reference.md#skip)。

      `@skip(collection-or-array-name, div(length(collection-or-array-name), 2))`

    下面是一個可視示例,演示如何使用這些運算式:

    ![建立單一個操作建立與使用多個連線](./media/handle-throttling-problems-429-errors/create-multiple-connections-per-action.png)

* 為每個操作設置不同的連接。

  對於此選項,請考慮是否可以通過將每個操作的請求分散到其自己的連接來分配工作負載,即使操作連接到同一服務或系統並使用相同的憑據也是如此。

  例如,假設您的邏輯應用從 SQL Server 資料庫獲取表,並獲取每個表中的每一行。 您可以使用單獨的連接,以便獲取表使用一個連接,而獲取每行使用另一個連接。

  ![建立每個操作建立與使用不同的連線](./media/handle-throttling-problems-429-errors/create-connection-per-action.png)

* 更改[「每個」迴圈](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop)上的併發或並行性。

  預設情況下,「對於每個」迴圈反覆運算同時運行,最多達到[併發限制](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)。 如果連接器在「每個」迴圈內受到限制,則可以減少並行運行的循環反覆運算次數。 如需詳細資訊，請參閱下列主題：
  
  * ['每個'迴圈 - 變更併發或按順序執行](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop)

  * [工作流定義語言架構 ─ 對於每個循環](../logic-apps/logic-apps-workflow-actions-triggers.md#foreach-action)

  * [工作流定義語言架構 - 變更「每個」循環併發](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency)

  * [工作流定義語言架構 - 按順序執行「每個」迴圈](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each)

<a name="destination-throttling"></a>

## <a name="destination-service-or-system-throttling"></a>目標服務或系統限制

雖然連接器有其自己的限制限制,但連接器調用的目標服務或系統也可能具有限制限制。 例如,Microsoft Exchange Server 中的某些 API 具有比 Office 365 Outlook 連接器更嚴格的限制限制。

預設情況下,邏輯應用的實體以及這些實體中的任何循環或分支*並行*執行 。 此行為意味著多個實例可以同時調用同一終結點。 每個實例都不知道對方的存在,因此嘗試重試失敗操作可能會創建[爭用條件](https://en.wikipedia.org/wiki/Race_condition),其中多個調用嘗試同時運行,但要成功,這些調用必須在開始限制之前到達目標服務或系統。

例如,假設您有一個包含 100 個項目的陣列。 您可以使用「每個」迴圈遍曆陣列並開啟迴圈的併發控制項,以便可以將並行疊代數限制為 20 或[目前預設限制](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits)。 在該迴圈中,操作將陣列中的項插入到 SQL Server 資料庫中,該資料庫每秒只允許 15 個調用。 此方案會導致限制問題,因為積壓的重試會累積起來,並且永遠不會運行。

下表描述了當操作的重試間隔為 1 秒時迴圈中發生的情況的時間線:

| 時間點 | 執行的操作數 | 失敗的操作數 | 等待的重試次數 |
|---------------|----------------------------|-----------------------------|---------------------------|
| T = 0 秒 | 20 個刀片 | 5 失敗,由於 SQL 限制 | 5 次重試 |
| T = 0.5 秒 | 15 個插入,由於前 5 次重試等待 | 所有 15 個失敗,由於以前的 SQL 限制仍然有效 0.5 秒 | 20 次重試 <br>(前 5 + 15 新) |
| T = 1 秒 | 20 個刀片 | 5 次失敗加上以前的 20 次重試,由於 SQL 限制 | 25 次重試(前 20 + 5 個新)
|||||

要處理此等級的限制,您有以下選項:

* 創建邏輯應用,以便每個應用處理單個操作。

  * 繼續本節中的 SQL Server 範例,您可以建立一個邏輯應用,將陣列項目放入佇列中,如[Azure 服務總線佇列](../connectors/connectors-create-api-servicebus.md)。 然後創建另一個邏輯應用,該應用僅對該佇列中的每個項執行插入操作。 這樣,在任何特定時間只有一個邏輯應用實例運行,該實例要麼完成插入操作並移動到佇列中的下一個項,要麼實例收到 429 個錯誤,但不會嘗試非生產性重試。

  * 創建一個父邏輯應用,為每個操作調用子項或嵌套邏輯應用。 如果父級需要根據父級的結果調用不同的子應用,則可以使用條件操作或切換操作來確定要調用的子應用。 此模式可以幫助您減少呼叫或操作的數量。

    例如,假設您有兩個邏輯應用,每個應用都有一個輪詢觸發器,每分鐘檢查電子郵件帳戶的特定主題,例如"成功"或"失敗"。 此設置每小時產生 120 次呼叫。 相反,如果創建每分鐘輪詢的單個父邏輯應用,但調用基於主題為"成功"還是"失敗"運行的子邏輯應用,則將輪詢檢查數減少到一半,在這種情況下將輪詢檢查數減少到 60 個。

* 設置批處理。

  如果目標服務支援批處理操作,則可以通過按組或批處理處理專案來解決限制問題,而不是單獨處理。 要實現批次處理解決方案,請建立「批次處理接收方」邏輯應用和「批次處理發送方」邏輯應用。 批發信者收集郵件或專案,直到滿足指定的條件,然後在單個組中發送這些郵件或專案。 批處理接收方接受該組並處理這些消息或項。 有關詳細資訊,請參閱[群組中的批次處理訊息](../logic-apps/logic-apps-batch-process-send-receive-messages.md)。

* 對觸發器和操作(而不是輪詢版本)使用 Webhook 版本。

  原因為何？ 輪詢觸發器繼續在特定時間間隔檢查目標服務或系統。 非常頻繁的間隔(如每一秒)會產生限制問題。 但是,Webhook 觸發器或操作(如[HTTP Webhook)](../connectors/connectors-native-webhook.md)僅創建對目標服務或系統的單個調用,該調用發生在訂閱時間,並且請求目標僅在事件發生時通知觸發器或操作。 這樣,觸發器或操作不必持續檢查目標。
  
  因此,如果目標服務或系統支援 Webhook 或提供具有 Webhook 版本的連接器,則此選項比使用輪詢版本更好。 要標識 Webhook 觸發器和操作,請確認`ApiConnectionWebhook`它們具有 類型或它們不需要指定重複。 有關詳細資訊,請參閱[APIConnectionWebhook 觸發器](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger)和[APIConnectionWebhook 操作](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-action)。

## <a name="next-steps"></a>後續步驟

* 瞭解有關[邏輯應用限制和設定](../logic-apps/logic-apps-limits-and-config.md)的更多資訊
