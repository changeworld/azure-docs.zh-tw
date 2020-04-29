---
title: 處理節流問題或「429-要求太多」錯誤
description: 如何解決節流問題，或在 Azure Logic Apps 中的「HTTP 429 太多要求」錯誤
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: fbfd52065bc0522668488492de2181f252f86a4e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81272673"
---
# <a name="handle-throttling-problems-429---too-many-requests-errors-in-azure-logic-apps"></a>處理 Azure Logic Apps 中的節流問題（429-「要求太多」錯誤）

在[Azure Logic Apps](../logic-apps/logic-apps-overview.md)中，當遇到節流時，邏輯應用程式會傳回「 [HTTP 429 太多要求」錯誤](https://developer.mozilla.org/docs/Web/HTTP/Status/429)，這會在要求數目超過目的地可在一段特定時間內處理的速率時發生。 節流可能會造成問題，例如延遲資料處理、降低效能速度，以及超過指定的重試原則等錯誤。

![SQL Server 連接器中的節流](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-error.png)

以下是您的邏輯應用程式可能會遇到的一些常見節流類型：

* [邏輯應用程式](#logic-app-throttling)
* [連接子](#connector-throttling)
* [目的地服務或系統](#destination-throttling)

<a name="logic-app-throttling"></a>

## <a name="logic-app-throttling"></a>邏輯應用程式節流

Azure Logic Apps 服務有自己的[輸送量限制](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)。 因此，如果您的邏輯應用程式超過這些限制，則邏輯應用程式資源會受到節流，而不只是特定的實例或執行。

若要尋找此層級的節流事件，請在 Azure 入口網站中檢查邏輯應用程式的 [**計量**] 窗格。

1. 在[Azure 入口網站](https://portal.azure.com)中，于邏輯應用程式設計工具中開啟邏輯應用程式。

1. 在邏輯應用程式功能表的 [**監視**] 底下，選取 [**計量**]。

1. 在 [**圖表標題**] 底下，選取 [**新增度量**]，以便將另一個計量新增至現有的。

1. **在第一個度量列的**[計量] 清單中，選取 [動作] [**節流事件**]。 在第二個**度量列的**[計量] 清單中，選取 [**觸發節流事件**]。

若要處理此層級的節流，您有下列選項：

* 限制可同時執行的邏輯應用程式實例數目。

  根據預設，如果邏輯應用程式的觸發條件同時符合一次以上，則邏輯應用程式的多個觸發程式實例會以並行或*平行方式*執行。 這個行為表示每個觸發程式實例會在前一個工作流程實例執行完成之前引發。

  雖然可同時執行的觸發程式實例預設數目不[受限制](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits)，但您可以[開啟觸發程式的並行設定](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency)來限制此數目，如有必要，請選取預設值以外的限制。

* 啟用高輸送量模式。

  邏輯應用程式具有[可在5分鐘輪流間隔內執行的動作數目的預設限制](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)。 若要將此限制提高到動作數目上限，請在邏輯應用程式上開啟[高輸送量模式](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode)。

* 停用觸發程式中的陣列解除批次處理（「分割依據」）行為。

  如果觸發程式針對其餘要處理的工作流程動作傳回陣列，觸發程式的[**分割**設定](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch)會分割陣列專案，並啟動每個陣列專案的工作流程實例，並有效地觸發多個並存執行，直到[**分割**限制](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits)為止。 若要控制節流，請關閉**分割**行為，並讓邏輯應用程式使用單一呼叫來處理整個陣列，而不是針對每個呼叫處理單一專案。

* 將動作重構成較小的邏輯應用程式。

  如先前所述，邏輯應用程式限制為[可在5分鐘期間內執行的預設動作數目](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)。 雖然您可以藉由啟用[高輸送量模式](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode)來增加此限制，但您也可以考慮是否要將邏輯應用程式的動作細分成較小的邏輯應用程式，讓每個邏輯應用程式中執行的動作數目維持在限制之下。 如此一來，您可以降低單一邏輯應用程式資源的負擔，並將負載分散到多個邏輯應用程式。 此解決方案適用于處理大型資料集或加速多個並存執行的動作、迴圈反覆運算或每個迴圈反復專案中的動作，而這些動作會超過動作執行限制。

  例如，此邏輯應用程式會執行所有工作，從 SQL Server 資料庫取得資料表，並從每個資料表取得資料列。 **For each**迴圈會同時逐一查看每個資料表，讓 [**取得資料列**] 動作傳回每個資料表的資料列。 根據這些資料表中的資料量，這些動作可能會超過動作執行的限制。

  ![邏輯應用程式「之前」重構](./media/handle-throttling-problems-429-errors/refactor-logic-app-before-version.png)

  重構之後，邏輯應用程式現在是父系和子邏輯應用程式。 父系會從 SQL Server 取得資料表，然後針對每個資料表呼叫子邏輯應用程式，以取得資料列：

  ![建立一個動作的邏輯應用程式](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-1.png)

  以下是父邏輯應用程式呼叫的子邏輯應用程式，以取得每個資料表的資料列：

  ![為第二個動作建立另一個邏輯應用程式](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-2.png)

<a name="connector-throttling"></a>

## <a name="connector-throttling"></a>連接器節流

每個連接器都有自己的節流限制，您可以在連接器的技術參考頁面上找到它。 例如， [Azure 服務匯流排連接器](https://docs.microsoft.com/connectors/servicebus/)的節流限制允許每分鐘最多6000個呼叫，而 SQL Server 連接器具有[根據作業類型而有所不同的節流限制](https://docs.microsoft.com/connectors/sql/)。

有些觸發程式和動作（例如 HTTP）具有「[重試原則](../logic-apps/logic-apps-exception-handling.md#retry-policies)」，您可以根據[重試原則限制](../logic-apps/logic-apps-limits-and-config.md#retry-policy-limits)來進行自訂，以執行例外狀況處理。 此原則會指定當原始要求失敗時，觸發程式或動作重試要求的頻率，以及產生408、429或5xx 回應的時間。 因此，當節流啟動並傳回429錯誤時，Logic Apps 會遵循支援的重試原則。

若要瞭解觸發程式或動作是否支援重試原則，請檢查觸發程式或動作的設定。 若要查看觸發程式或動作的重試次數，請移至邏輯應用程式的 [執行歷程記錄]，選取您要檢查的執行，然後展開該觸發程式或動作，以查看有關輸入、輸出和任何重試的詳細資料，例如：

![視圖動作的執行歷程記錄、重試、輸入和輸出](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-retries.png)

雖然重試歷程記錄提供錯誤資訊，但您可能無法區分連接器節流和[目的地節流](#destination-throttling)。 在此情況下，您可能必須查看回應的詳細資料，或執行一些節流間隔計算來識別來源。

對於全域、多租使用者 Azure Logic Apps 服務中的邏輯應用程式，會在*連接*層級進行節流。 因此，例如，對於在[整合服務環境（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中執行的邏輯應用程式，即使是在全域、多租使用者 Logic Apps 服務中執行，仍會針對非 ISE 連線進行節流。 不過，ise 連接器所建立的 ISE 連線不會進行節流處理，因為它們是在您的 ISE 中執行。

若要處理此層級的節流，您有下列選項：

* 針對單一動作設定多個連線，讓邏輯應用程式分割資料以進行處理。

  針對此選項，請考慮是否可以使用相同的認證，將動作的要求分割到相同目的地的多個連接，藉以散發工作負載。

  例如，假設您的邏輯應用程式會從 SQL Server 資料庫取得資料表，然後從每個資料表取得資料列。 根據您必須處理的資料列數目，您可以使用多個連接和多個**For each**迴圈，將資料列總數分割成較小的集合進行處理。 此案例使用兩個**For each**迴圈來分割一半的資料列總數。 第一個**for each**迴圈會使用可取得前一半的運算式。 另一個**for each**迴圈會使用不同的運算式來取得第二個部分，例如：<p>

    * 運算式1： `take()`函式會取得集合的前端。 如需詳細資訊，請[ **`take()`** ](workflow-definition-language-functions-reference.md#take)參閱函式。

      `@take(collection-or-array-name, div(length(collection-or-array-name), 2))`

    * 運算式2：此`skip()`函式會移除集合的前方，並傳回所有其他專案。 如需詳細資訊，請[ **`skip()`** ](workflow-definition-language-functions-reference.md#skip)參閱函式。

      `@skip(collection-or-array-name, div(length(collection-or-array-name), 2))`

    以下顯示如何使用這些運算式的 visual 範例：

    ![針對單一動作建立和使用多個連接](./media/handle-throttling-problems-429-errors/create-multiple-connections-per-action.png)

* 為每個動作設定不同的連接。

  針對此選項，請考慮是否可以透過將每個動作的要求分散至自己的連接來散發工作負載，即使動作連線到相同的服務或系統，並使用相同的認證也一樣。

  例如，假設您的邏輯應用程式會從 SQL Server 資料庫取得資料表，並取得每個資料表中的每個資料列。 您可以使用不同的連接，讓取得資料表使用一個連接，而讓每個資料列使用另一個連接。

  ![為每個動作建立並使用不同的連接](./media/handle-throttling-problems-429-errors/create-connection-per-action.png)

* 在["For each" 迴圈](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop)上變更並行或平行處理原則。

  根據預設，"For each" 迴圈反覆運算會在相同時間執行，直到[並行限制](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)為止。 如果您的連接器在「For each」迴圈內受到節流處理，您可以減少平行執行的迴圈反覆運算數目。 如需詳細資訊，請參閱下列主題：
  
  * ["For each" 迴圈-變更並行或循序執行](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop)

  * [工作流程定義語言架構-適用于每個迴圈](../logic-apps/logic-apps-workflow-actions-triggers.md#foreach-action)

  * [工作流程定義語言架構-變更 "For each" 迴圈並行](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency)

  * [工作流程定義語言架構-循序執行 "For each" 迴圈](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each)

<a name="destination-throttling"></a>

## <a name="destination-service-or-system-throttling"></a>目的地服務或系統節流

當連接器有自己的節流限制時，連接器所呼叫的目的地服務或系統可能也會有節流限制。 例如，Microsoft Exchange Server 中的某些 Api 比 Office 365 Outlook 連接器具有更嚴格的節流限制。

根據預設，邏輯應用程式的實例以及在這些實例內的任何迴圈或分支，會*以平行方式*執行。 此行為表示多個實例可以同時呼叫相同的端點。 每個實例都不知道另一個存在，因此嘗試重試失敗的動作可能會建立[競爭情況](https://en.wikipedia.org/wiki/Race_condition)，其中多個呼叫會嘗試同時執行，但若要成功，這些呼叫必須到達目的地服務或系統，才會開始進行節流。

例如，假設您有一個具有100專案的陣列。 您可以使用「for each」迴圈逐一查看陣列，並開啟迴圈的並行存取控制，讓您可以將平行反覆運算的數目限制為20或[目前的預設限制](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits)。 在該迴圈內，動作會將陣列中的專案插入 SQL Server 的資料庫中，這只允許每秒15個呼叫。 此案例會導致節流問題，因為重試的待處理專案會建立，且永遠不會執行。

下表描述動作的重試間隔為1秒時，迴圈中所發生狀況的時間軸：

| 時間點 | 執行的動作數目 | 失敗的動作數目 | 等待的重試次數 |
|---------------|----------------------------|-----------------------------|---------------------------|
| T + 0 秒 | 20個插入 | 5因為 SQL 限制而失敗 | 5次重試 |
| T + 0.5 秒 | 15個插入，因為先前的5次重試等候 | 全部15次失敗，因為先前的 SQL 限制仍會在另一個0.5 秒內生效 | 20次重試 <br>（前 5 + 15 個新的） |
| T + 1 秒 | 20個插入 | 5失敗加上先前20次的重試，因為 SQL 限制 | 25次重試（前 20 + 5 個新的）
|||||

若要處理此層級的節流，您有下列選項：

* 建立邏輯應用程式，讓每個都處理單一作業。

  * 繼續進行本節中的範例 SQL Server 案例，您可以建立邏輯應用程式，將陣列專案放入佇列中，例如 Azure 服務匯流排的[佇列](../connectors/connectors-create-api-servicebus.md)。 接著，您會建立另一個邏輯應用程式，只針對該佇列中的每個專案執行插入作業。 如此一來，在任何特定時間都只會執行一個邏輯應用程式實例，這會完成插入作業並移至佇列中的下一個專案，或實例取得429錯誤，但不會嘗試免除沒有效率重試。

  * 建立父邏輯應用程式，以呼叫每個動作的子系或嵌套邏輯應用程式。 如果父系需要根據父系的結果呼叫不同的子應用程式，您可以使用條件動作或切換動作來決定要呼叫的子應用程式。 此模式可協助您減少呼叫或作業的數目。

    例如，假設您有兩個邏輯應用程式，每個都有一個輪詢觸發程式，每分鐘檢查一次特定主題的電子郵件帳戶，例如「成功」或「失敗」。 此設定會導致每小時120呼叫。 相反地，如果您建立單一父系邏輯應用程式，每分鐘輪詢一次，但呼叫的子邏輯應用程式是根據主旨是「成功」或「失敗」而執行，則您可以將輪詢檢查的數目剪成一半，或在此情況下將其設為60。

* 設定批次處理。

  如果目的地服務支援批次作業，您可以藉由處理群組或批次中的專案（而不是個別）來解決節流。 若要執行批次處理解決方案，請建立「批次接收者」邏輯應用程式和「批次傳送者」邏輯應用程式。 批次傳送者會收集訊息或專案，直到符合您指定的準則，然後將這些訊息或專案傳送到單一群組中。 批次接收者會接受該群組，並處理這些訊息或專案。 如需詳細資訊，請參閱[批次處理群組中的訊息](../logic-apps/logic-apps-batch-process-send-receive-messages.md)。

* 使用適用于觸發程式和動作的 webhook 版本，而不是輪詢版本。

  原因為何？ 輪詢觸發程式會繼續以特定的間隔檢查目的地服務或系統。 非常頻繁的間隔（例如每秒）可能會建立節流問題。 不過，webhook 觸發程式或動作（例如[HTTP webhook](../connectors/connectors-native-webhook.md)）只會建立對目的地服務或系統的單一呼叫，這會在訂用帳戶期間發生，並要求目的地只有在發生事件時才會通知觸發程式或動作。 如此一來，觸發程式或動作就不需要持續檢查目的地。
  
  因此，如果目的地服務或系統支援 webhook，或提供具有 webhook 版本的連接器，則此選項比使用輪詢版本更好。 若要識別 webhook 觸發程式和動作，請確認它們`ApiConnectionWebhook`的類型為，或不需要您指定週期。 如需詳細資訊，請參閱[APIConnectionWebhook 觸發](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger)程式和[APIConnectionWebhook 動作](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-action)。

## <a name="next-steps"></a>後續步驟

* 深入瞭解[Logic Apps 限制和](../logic-apps/logic-apps-limits-and-config.md)設定
