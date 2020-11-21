---
title: 平衡多個實例之間的資料分割負載-Azure 事件中樞 |Microsoft Docs
description: 說明如何使用事件處理器和 Azure 事件中樞 SDK，平衡多個應用程式實例之間的分割負載。
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: af307058d0eda6b96c0811bccc245c09e2bdd27d
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025039"
---
# <a name="balance-partition-load-across-multiple-instances-of-your-application"></a>在應用程式的多個實例之間平衡資料分割負載
若要調整您的事件處理應用程式，您可以執行應用程式的多個實例，並讓它本身之間的負載平衡。 在較舊的版本中， [EventProcessorHost](event-hubs-event-processor-host.md) 允許您在接收時平衡程式的多個實例和檢查點事件之間的負載。 在較新的版本中 (5.0 之前的版本) 、 **EventProcessorClient** ( .Net 和 JAVA) ，或 **EventHubConsumerClient** (Python 和 JavaScript) 可讓您進行相同的作業。 使用事件可讓開發模型變得更簡單。 您可以藉由註冊事件處理常式來訂閱您感興趣的事件。 如果您使用的是舊版用戶端程式庫，請參閱下列遷移指南： [.net](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)、 [JAVA](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/migration-guide.md)、 [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/servicebus/azure-servicebus/migration_guide.md)和 [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/migrationguide.md)。

本文說明使用多個實例從事件中樞讀取事件的範例案例，然後提供事件處理器用戶端功能的詳細資料，可讓您一次從多個分割區接收事件，並與其他使用相同事件中樞和取用者群組的取用者進行負載平衡。

> [!NOTE]
> 分割取用者概念是針對事件中樞調整大小的關鍵。 與[競爭取用者](/previous-versions/msp-n-p/dn568101(v=pandp.10))模式相比，分割取用者模式可藉由消除爭奪瓶頸，以及加速端對端平行處理原則來具有高度縮放能力。

## <a name="example-scenario"></a>範例案例

針對範例案例，請設想有一家監視 100,000 戶家庭的住家安全公司。 每分鐘，它會從各種不同的感應器（例如動作偵測器、門/視窗開啟感應器、玻璃斷路偵測器等等）取得資料，並安裝在每個首頁。 該公司會提供網站給住戶，讓他們可近乎即時地監視房子內的活動。

每個感應器會將資料推送至事件中樞。 事件中樞已設定 16 個分割區。 在使用端，您需要可讀取這些事件的機制、將它們合併 (篩選準則、匯總等) 並將匯總傾印到儲存體 blob，然後將匯總投射到方便使用的網頁。

## <a name="write-the-consumer-application"></a>撰寫取用者應用程式

在分散式環境中設計取用者時，案例必須處理下列需求：

1. **縮放：** 建立多個取用者，而每個取用者都會負責從幾個事件中樞分割區讀取資料。
2. **負載平衡：** 以動態方式增加或減少取用者。 例如，對每個家庭新增感應器類型 (例如一氧化碳偵測器) 時，事件數目就會增加。 在此情況下，操作人員會增加取用者執行個體的數目。 然後，取用者集區可以重新平衡他們擁有的分割區數目，讓新增的取用者共同分攤負載。
3. **失敗時無縫繼續：** 如果取用者 (取用 **者 a**) 失敗 (例如，裝載取用者的虛擬機器突然損毀) ，則其他取用者可以挑選取用 **者 a** 所擁有的分割區並繼續進行。 此外，接續點 (稱為「檢查點」或「位移」) 應剛好位在 **取用者 A** 失敗的位置，或在此稍微前面一點的位置。
4. **使用事件：** 當前三個點處理取用者的管理時，必須有程式碼來取用事件並對其有所説明。 例如，匯總並上傳至 blob 儲存體。

## <a name="event-processor-or-consumer-client"></a>事件處理器或取用者用戶端

您不需要建立自己的解決方案來滿足這些需求。 Azure 事件中樞 Sdk 提供這種功能。 在 .NET 或 JAVA Sdk 中，您可以使用 (EventProcessorClient) 的事件處理器用戶端，並在 Python 和 JavaScript Sdk 中使用 EventHubConsumerClient。 在舊版的 SDK 中，它是支援這些功能的事件處理器主機 (EventProcessorHost) 。

在大部分的生產案例中，我們建議您使用事件處理器用戶端來讀取和處理事件。 處理器用戶端旨在提供健全的體驗，讓您以高效能且容錯的方式處理事件中樞所有分割區的事件，同時提供檢查其進度的方法。 事件處理器用戶端可以在指定事件中樞的取用者群組內容中合作進行。 用戶端會自動管理工作的散發和平衡，因為群組會有實例可用或無法使用。

## <a name="partition-ownership-tracking"></a>分割區的擁有權追蹤

事件處理器實例通常會擁有並處理來自一或多個分割區的事件。 分割區的擁有權會平均分散到與事件中樞和取用者群組組合相關聯的所有作用中事件處理器實例。 

每個事件處理器都會獲得唯一的識別碼，並藉由新增或更新檢查點存放區中的專案來宣告分割區的擁有權。 所有事件處理器實例都會定期與此存放區進行通訊，以更新自己的處理狀態，以及瞭解其他使用中的實例。 這項資料接著會用來平衡作用中處理器之間的負載。 新的實例可以加入處理集區來擴大規模。 當實例關閉時，無論是因為失敗或縮小，資料分割擁有權都會正常地傳輸至其他作用中的處理器。

檢查點存放區中的分割擁有權記錄可追蹤事件中樞命名空間、事件中樞名稱、取用者群組、事件處理器識別碼 (也稱為擁有者) 、資料分割識別碼和上次修改時間。



| 事件中樞命名空間               | 事件中樞名稱 | **取用者群組** | 擁有者                                | 資料分割識別碼 | 上次修改時間  |
| ---------------------------------- | -------------- | :----------------- | :----------------------------------- | :----------- | :------------------ |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 3be3f9d3-9d9e-4c50-9491-85ece8334ff6 | 0            | 2020-01-15T01：22：15 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | f5cc5176-ce96-4bb4-bbaa-a0e3a9054ecf | 1            | 2020-01-15T01：22：17 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 72b980e9-2efc-4ca7-ab1b-ffd7bece8472 | 2            | 2020-01-15T01：22：10 |
|                                    |                | :                  |                                      |              |                     |
|                                    |                | :                  |                                      |              |                     |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 844bd8fb-1f3a-4580-984d-6324f9e208af | 15           | 2020-01-15T01：22：00 |

每個事件處理器實例都會取得分割區的擁有權，並開始處理最後已知 [檢查點](# Checkpointing)的資料分割。 如果處理器失敗 (VM) 關閉，則其他實例會藉由查看上次修改時間來偵測它。 其他實例會嘗試取得先前由非使用中實例所擁有之資料分割的擁有權，而檢查點存放區可保證只有其中一個實例會成功宣告分割區的擁有權。 因此，在任何指定的時間點，最多會有一個從分割區接收事件的處理器。

## <a name="receive-messages"></a>接收訊息

當您建立事件處理器時，您會指定將處理事件和錯誤的函式。 每次呼叫處理事件的函式都會從特定的資料分割傳遞單一事件。 您必須負責處理這個事件。 如果您想要確保取用者至少處理一次訊息，您需要使用重試邏輯來撰寫自己的程式碼。 但請留意有害訊息。

建議您以相對快速的方式進行。 也就是說，請儘量少處理。 如果您需要寫入至儲存體並進行一些路由，最好使用兩個取用者群組，並有兩個事件處理器。

## <a name="checkpointing"></a>檢查點檢查

*檢查點* 是一種處理常式，可讓事件處理器標示或認可資料分割內最後一個成功處理事件的位置。 標記檢查點通常是在處理事件的函式中進行，而且是在取用者群組內的每個分割區上進行。 

如果事件處理器與分割區中斷連接，另一個實例可以繼續處理檢查點上的分割區，此資料分割位於先前由該取用者群組中該分割區的最後一個處理器所認可的檢查點。 當處理器連線時，它會將位移傳遞給事件中樞，以指定要開始讀取的位置。 如此一來，您可以使用檢查點將事件標示為「完成」下游應用程式，並在事件處理器中斷時提供復原功能。 您可以從這個檢查點處理常式指定較低的位移，以返回較舊的資料。 

執行檢查點以將事件標示為已處理時，會使用事件的位移和序號來新增或更新檢查點存放區中的專案。 使用者應決定更新檢查點的頻率。 在每個成功處理的事件之後更新，可能會影響效能和成本，因為它會觸發對基礎檢查點存放區的寫入操作。 此外，每個單一事件的檢查點都表示佇列訊息模式，服務匯流排佇列可能是比事件中樞更好的選項。 事件中樞背後的構想是，在大型規模中您能取得「至少一次」的傳遞。 藉由讓下游系統具有等冪性，您可以輕鬆地從失敗或重新開機中復原，進而導致相同的事件被多次接收。

> [!NOTE]
> 如果您在支援不同于 Azure 上的儲存體 Blob SDK 版本的環境中使用 Azure Blob 儲存體作為檢查點存放區，您必須使用程式碼將儲存體服務 API 版本變更為該環境所支援的特定版本。 例如，如果您是 [在 Azure Stack Hub 2002 版上執行事件中樞](/azure-stack/user/event-hubs-overview)，則儲存體服務的最高可用版本是2017-11-09 版。 在此情況下，您必須使用程式碼將儲存體服務 API 版本的目標設為2017-11-09。 如需如何以特定儲存體 API 版本為目標的範例，請參閱 GitHub 上的下列範例： 
> - [.Net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/)。 
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/)
> - [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript) 或  [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/)

## <a name="thread-safety-and-processor-instances"></a>執行緒安全性和處理器執行個體

根據預設，針對指定的資料分割，會依序呼叫處理事件的函式。 後續事件和此函式的呼叫會從幕後的相同分割區佇列中開啟，因為事件抽取會繼續在其他執行緒的背景中執行。 您可以同時處理來自不同分割區的事件，也可以同步處理跨分割區存取的任何共用狀態。

## <a name="next-steps"></a>下一步
請參閱下列快速入門：

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
