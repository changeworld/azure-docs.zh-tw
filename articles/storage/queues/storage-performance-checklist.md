---
title: 佇列儲存體的效能和延展性檢查清單 - Azure 儲存體
description: 在開發高效能應用程式中使用佇列儲存體的實證做法檢查清單。
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: queues
ms.custom: devx-track-csharp
ms.openlocfilehash: 3b9aadf7d9cd27763cafb878d0b35d13a140a304
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "89008398"
---
# <a name="performance-and-scalability-checklist-for-queue-storage"></a>佇列儲存體的效能和延展性檢查清單

Microsoft 開發了一些實證做法，以便使用佇列儲存體開發高效能應用程式。 此檢查清單會識別可供開發人員遵循以將效能最佳化的重要做法。 在設計應用程式時和整個過程中，請記住這些做法。

Azure 儲存體具有容量、交易速率和頻寬的延展性和效能目標。 如需 Azure 儲存體帳戶延展性目標的詳細資訊，請參閱[標準儲存體帳戶的延展性與效能目標](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)及[佇列儲存體的延展性與效能目標](scalability-targets.md)。

## <a name="checklist"></a>檢查清單

本文會將效能實證做法整理為您在開發佇列儲存體應用程式時所能遵循的檢查清單。

| 完成 | 類別 | 設計考量 |
| --- | --- | --- |
| &nbsp; |延展性目標 |[您是否可以將應用程式設計成使用的儲存體帳戶數目不超過上限？](#maximum-number-of-storage-accounts) |
| &nbsp; |延展性目標 |[您是否要避免接近容量和交易限制？](#capacity-and-transaction-targets) |
| &nbsp; |網路功能 |[用戶端裝置是否有足夠高的頻寬和足夠低的延遲，以達到所需的效能？](#throughput) |
| &nbsp; |網路功能 |[用戶端裝置是否有高品質網路連結？](#link-quality) |
| &nbsp; |網路功能 |[用戶端應用程式是否位於與儲存體帳戶相同的區域中？](#location) |
| &nbsp; |直接用戶端存取 |[您是否使用共用存取簽章 (SAS) 和跨原始資源共用 (CORS) 來啟用 Azure 儲存體的直接存取？](#sas-and-cors) |
| &nbsp; |.NET 組態 |[您使用 .NET Core 2.1 或更新版本來獲得最佳效能嗎？](#use-net-core) |
| &nbsp; |.NET 組態 |[您是否已設定用戶端使用足夠數量的並行連線？](#increase-default-connection-limit) |
| &nbsp; |.NET 組態 |[針對 .NET 應用程式，您是否已設定 .NET 使用足夠數量的執行緒？](#increase-minimum-number-of-threads) |
| &nbsp; |平行處理原則 |[您是否已確保平行處理原則已適當地受到限制，因此您不會讓用戶端功能超載或接近延展性目標？](#unbounded-parallelism) |
| &nbsp; |工具 |[您是否使用 Microsoft 所提供的最新用戶端程式庫和工具版本？](#client-libraries-and-tools) |
| &nbsp; |重試 |[您是否針對節流錯誤和逾時使用重試原則搭配指數輪詢？](#timeout-and-server-busy-errors) |
| &nbsp; |重試 |[您的應用程式是否避免重試不能再嘗試的錯誤？](#non-retryable-errors) |
| &nbsp; |設定 |[您是否已關閉 Nagle 演算法以提高小型要求的效能？](#disable-nagle) |
| &nbsp; |訊息大小 |[您的訊息是否精簡以提高佇列效能？](#message-size) |
| &nbsp; |大量擷取 |[您是否在單一 Get 作業中擷取多則訊息？](#batch-retrieval) |
| &nbsp; |輪詢頻率 |[您是否夠常輪詢以降低可察覺的應用程式延遲？](#queue-polling-interval) |
| &nbsp; |更新訊息 |[您是否使用「更新訊息」來儲存處理訊息時的進度，讓您避免在發生錯誤時必須重新處理整個訊息？](#use-update-message) |
| &nbsp; |架構 |[您是否透過將長時間執行的工作負載排除在重要路徑外，然後再個別進行調整的方式，使用佇列來讓您的整個應用程式更有彈性？](#application-architecture) |

## <a name="scalability-targets"></a>延展性目標

如果您的應用程式達到或超過任何延展性目標，它可能會遇到增加的交易延遲或節流。 當 Azure 儲存體對您的應用程式進行節流時，該服務會開始傳回 503 (伺服器忙碌) 或 500 (作業逾時) 錯誤碼。 保持在延展性目標的限制範圍內以避免這些錯誤，對於提升應用程式效能很重要。

如需佇列服務的延展性目標詳細資訊，請參閱 [Azure 儲存體延展性和效能目標](/azure/storage/queues/scalability-targets#scale-targets-for-queue-storage)。

### <a name="maximum-number-of-storage-accounts"></a>儲存體帳戶的數目上限

如果您很接近特定訂用帳戶/區域組合允許的儲存體帳戶數目上限，您是否使用多個儲存體帳戶進行分區，以增加每秒的輸入、輸出、I/O 作業 (IOPS) 或容量？ 在此案例中，可能的話，我們建議您善用提高儲存體帳戶的限制，以降低您的工作負載所需的儲存體帳戶數目。 請連絡 [Azure 支援](https://azure.microsoft.com/support/options/)來要求提高儲存體帳戶的限制。 如需詳細資訊，請參閱[宣佈較大型、較大規模的儲存體帳戶](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)。

### <a name="capacity-and-transaction-targets"></a>容量和交易目標

如果您的應用程式即將達到單一儲存體帳戶的延展性目標，您可以考慮採用下列其中一個方法：  

- 如果佇列的延展性目標不足以用於您的應用程式，則使用多個佇列並在其間散發訊息。
- 重新思考會造成您的應用程式達到或超出延展性目標的工作負載。 您是否能夠以不同的方式設計工作負載，以使用較少的頻寬或容量或使用較少的交易？
- 如果您的應用程式必須超出其中一個延展性目標，則建立多個儲存體帳戶，並將您的應用程式資料在這幾個儲存體帳戶中進行分割。 如果您要使用此模式，那麼請確定設計您的應用程式，以便日後可以增加更多儲存體帳戶以取得負載平衡。 除了儲存資料、進行交易或傳輸資料等使用方式外，儲存體帳戶本身不會有其他費用。
- 如果您的應用程式很接近頻寬目標，請考慮在用戶端壓縮資料，以便降低將資料傳送至 Azure 儲存體所需的頻寬。
    雖然壓縮資料可節省頻寬並改善網路效能，但也可能對效能造成負面影響。 針對用戶端上資料壓縮和解壓縮，評估額外處理需求所造成的效能影響。 請記住，儲存壓縮的資料可能會使疑難排解變得更困難，因為使用標準工具來檢視資料可能更具挑戰性。
- 如果您的應用程式很接近延展性目標，則務必使用指數輪詢進行重試。 最好是藉由實作本文所述的建議，嘗試避免達到延展性目標。 不過，使用指數輪詢進行重試會讓您的應用程式無法快速重試，這可能會使節流變差。 如需詳細資訊，請參閱[逾時和伺服器忙碌錯誤](#timeout-and-server-busy-errors)一節。

## <a name="networking"></a>網路功能

應用程式的實體網路限制可能會對效能產生重大影響。 下列各節說明使用者可能會遇到的部分限制。  

### <a name="client-network-capability"></a>用戶端網路功能

如下列各節所述，網路連結的頻寬和品質在應用程式效能中扮演重要角色。

#### <a name="throughput"></a>Throughput

頻寬的問題經常是用戶端的功能。 較大型的 Azure 執行個體擁有較大容量的 NIC，因此，如果您需要單一機器的較高網路限制，您應考慮使用較大型的執行個體或更多的 VM。 如果您從內部部署應用程式存取 Azure 儲存體，則適用相同的規則：了解用戶端裝置的網路功能和與 Azure 儲存體位置的網路連線能力，以及視需要進行改善或設計您的應用程式以便使用其功能。

#### <a name="link-quality"></a>連結品質

與任何網路使用方式一樣，請記住導致錯誤和封包遺失的網路狀況將會減慢有效的輸送量。  使用 WireShark 或 NetMon 可能有助於診斷此問題。  

### <a name="location"></a>位置

在任何分散式環境中，將用戶端放置於伺服器附近可提供最佳的效能。 若要以最低的延遲時間存取 Azure 儲存體，對用戶端而言的最佳位置是在同一個 Azure 區域內。 例如，如果您擁有使用 Azure 儲存體的 Azure Web 應用程式，則將這兩者置於單一區域內 (例如，美國西部或東南亞)。 共置資源可降低延遲和成本，因為單一區域內的頻寬使用量是免費的。  

如果將存取 Azure 儲存體的用戶端應用程式不在 Azure 中託管 (例如行動裝置應用程式或內部部署企業服務)，則將儲存體帳戶置於這些用戶端附近的區域內可以降低延遲。 如果您的用戶端分佈廣闊 (例如，有些在北美洲，有些在歐洲)，則考慮在每個區域使用一個儲存體帳戶。 如果應用程式儲存的資料特定用於個別使用者，且不需要複寫儲存體帳戶之間的資料，則此方法較容易實作。

## <a name="sas-and-cors"></a>SAS 和 CORS

假設您需要授權在使用者的網頁瀏覽器或在行動電話應用程式中執行的程式碼 (例如 JavaScript)，以存取 Azure 儲存體中的資料。 其中一種方法是建置可作為 Proxy 的服務應用程式。 使用者的裝置會向服務進行驗證，然後再授權存取 Azure 儲存體資源。 如此一來，您可以避免在未受到保護的裝置上公開您的儲存體帳戶金鑰。 不過，因為在使用者裝置與 Azure 儲存體之間轉送的所有資料都必須通過服務應用程式，所以此方法會在服務應用程式上加上顯著負荷。

使用共用存取簽章 (SAS)，即可避免使用服務應用程式作為 Azure 儲存體的 Proxy。 使用 SAS，您可以藉由使用有限的存取權杖，讓使用者的裝置能直接對 Azure 儲存體提出要求。 例如，如果使用者想要將相片上傳至您的應用程式，則您的服務應用程式可以產生 SAS，並將它傳送到使用者的裝置。 SAS 權杖可以授與在指定的時間間隔內寫入 Azure 儲存體資源的權限，該時間間隔後 SAS 權杖就會過期。 如需關於 SAS 的詳細資訊，請參閱[使用共用存取簽章 (SAS) 對 Azure 儲存體資源授與有限存取權](../common/storage-sas-overview.md)。  

通常，在某個網域上由網站託管的頁面中，網頁瀏覽器不允許 JavaScript 對另一個網域執行特定作業 (例如寫入作業)。 這項原則稱為同源原則，可防止某頁上的惡意程式碼取得另一個網頁上資料的存取權。 不過，在雲端建置解決方案時，同源原則會是一項限制。 跨原始資源共用 (CORS) 是一項瀏覽器功能，可讓目標網域與信任源自來源網域之要求的瀏覽器進行通訊。

例如，假設在 Azure 中執行的 Web 應用程式向 Azure 儲存體帳戶提出資源要求。 Web 應用程式是來源網域，而儲存體帳戶是目標網域。 您可以為任何 Azure 儲存體服務設定 CORS，以便與網頁瀏覽器進行通訊，而該網頁瀏覽器會向 Azure 儲存體所信任的來源網域提出要求。 如需 CORS 的詳細資訊，請參閱 [Azure 儲存體的跨原始資源共用 (CORS) 支援](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services)。  
  
SAS 和 CORS 都可協助您避免 Web 應用程式上不必要的負載。  

## <a name="net-configuration"></a>.NET 組態

如果使用 .NET Framework，本節會列出數個可用來大幅改善效能的快速組態設定。  如果使用其他語言，請查看類似的概念是否適用於您所選擇的語言。  

### <a name="use-net-core"></a>使用 .NET Core

使用 .NET Core 2.1 或更新版本來開發 Azure 儲存體應用程式，以充分利用效能增強功能。 可能的話，建議使用 .NET Core 3.x。

如需 .NET Core 中效能改進的詳細資訊，請參閱下列部落格文章：

- [.NET Core 3.0 中的效能改進](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [.NET Core 2.1 中的效能改進](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>提高預設的連線限制

在 .NET 中，下列程式碼可將預設的連線限制 (此值在用戶端環境中通常為 2，或在伺服器環境中通常為 10) 提高到 100。 一般而言，您應將此值大約設為應用程式所使用的執行緒數量。  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

在開啟任何連線之前設定連線限制。  

若是其他程式設計語言，請參閱該語言的文件以確定如何設定連線限制。  

如需詳細資訊，請參閱部落格文章 [Web 服務：並行連線](https://blogs.msdn.microsoft.com/darrenj/2005/03/07/web-services-concurrent-connections/)。  

### <a name="increase-minimum-number-of-threads"></a>提高執行緒數目上限

如果您同時使用同步呼叫與非同步工作，則可增加執行緒集區中的執行緒數目：

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

如需詳細資訊，請參閱 [ThreadPool.SetMinThreads](/dotnet/api/system.threading.threadpool.setminthreads) 方法。  

## <a name="unbounded-parallelism"></a>無限制的平行處理原則

雖然平行處理原則很適合用於效能，但請小心使用無限制的平行處理原則，這表示執行緒或平行要求數目並未強加任何限制。 請務必將平行要求限制為上傳或下載資料、存取相同儲存體帳戶中的多個資料分割，或存取相同資料分割中的多個項目。 如果平行處理原則沒有限制，則您的應用程式可超出用戶端裝置的功能或儲存體帳戶的延展性目標，因而產生較長的延遲與節流作業。  

## <a name="client-libraries-and-tools"></a>用戶端程式庫和工具

為了達到最佳效能，請一律使用 Microsoft 所提供的最新用戶端程式庫和工具。 Azure 儲存體的用戶端程式庫適用於各種不同的語言。 Azure 儲存體也支援 PowerShell 和 Azure CLI。 Microsoft 主動開發以效能為考量的這些用戶端程式庫和工具，透過最新的服務版本將他們保持在最新的狀態，並確保這些工具會在內部處理許多已經實證的效能做法。 如需詳細資訊，請參閱 [Azure 儲存體參考文件](/azure/storage/#reference)。

## <a name="handle-service-errors"></a>處理服務錯誤

當服務無法處理要求時，Azure 儲存體會傳回錯誤。 了解特定案例中 Azure 儲存體可能傳回的錯誤，對於效能最佳化很有幫助。

### <a name="timeout-and-server-busy-errors"></a>逾時和伺服器忙碌錯誤

如果您的應用程式很接近延展性限制，Azure 儲存體可能進行節流處理。 在某些情況下，Azure 儲存體可能因某些暫時性狀況而無法處理要求。 在這兩種情況下，服務可能會傳回 503 (伺服器忙碌) 或500 (逾時) 錯誤。 如果服務正在重新平衡資料分割，以允許更高的輸送量，也可能會發生這些錯誤。 用戶端應用程式通常應該重試導致其中一個錯誤的作業。 不過，如果 Azure 儲存體因為您的應用程式超出延展性目標而進行節流，或即使服務因為一些其他原因而無法服務要求，則積極重試的結果可能會使問題雪上加霜。 建議使用指數輪詢重試原則，且用戶端程式庫會預設為此行為。 例如，您的應用程式可能會在 2 秒後進行重試、然後 4 秒、然後 10 秒、然後 30 秒，最後會完全放棄。 如此一來，您的應用程式會大幅降低其在服務上的負荷，而不會使導致節流的行為惡化。  

因為連線能力錯誤不是節流的結果，且被認為是暫時性的，因此可以立即重試連線能力錯誤。  

### <a name="non-retryable-errors"></a>無法重試的錯誤

用戶端程式庫會在認知哪些錯誤可以重試和哪些錯誤不能重試的情況下，處理重試。 不過，如果您直接呼叫 Azure 儲存體 REST API，就會發生一些您不得重試的錯誤。 例如，400 (不正確的要求) 錯誤表示用戶端應用程式傳送了無法處理的要求，因為該要求不是預期的格式。 每次重新傳送此要求都會產生相同的回應，所以重試並沒有用。 如果您直接呼叫 Azure 儲存體 REST API，請注意可能發生的錯誤，以及是否應該重試。

如需 Azure 儲存體錯誤碼的詳細資訊，請參閱[狀態和錯誤碼](/rest/api/storageservices/status-and-error-codes2)。

## <a name="disable-nagle"></a>停用 Nagle

在不同的 TCP/IP 網路中已廣泛採用 Nagle 的演算法，來作為提高網路效能的方法。 不過，它並非是所有情況下的最佳作法 (例如高互動式環境)。 Nagle 的演算法對於 Azure 資料表服務要求的效能有負面的影響，可以的話您應將它停用。

## <a name="message-size"></a>訊息大小

佇列效能和延展性會隨著訊息大小增加而減少。 只將接收端所需的資訊放在訊息中。  

## <a name="batch-retrieval"></a>批次擷取

您可以在單一作業中，從佇列擷取高達 32 則的訊息。 批次擷取可降低與用戶端應用程式之間反覆存取的次數，這在具有高延遲的環境 (例如行動裝置) 中特別有用。  

## <a name="queue-polling-interval"></a>佇列輪詢間隔

大部分的應用程式會輪詢佇列中的訊息，而佇列可以是該應用程式的最大交易來源之一。 明智地選取輪詢間隔：輪詢太過頻繁可能會導致您的應用程式接近佇列的延展性目標。 不過，如果以 200,000 筆交易 0.01 美元計算 (寫入時)，則單一處理器每秒輪詢一次的一個月成本不到 15 分，所以成本通常不是影響您選擇輪詢間隔的因素。  

如需最新成本資訊，請參閱 [Azure 儲存體價格](https://azure.microsoft.com/pricing/details/storage/)。  

## <a name="use-update-message"></a>使用更新訊息

您可以使用 [更新訊息]**** 作業來增加隱藏逾時，或更新訊息的狀態資訊。 相較於在作業的每個步驟完成時，將作業從一個佇列傳遞到下一個佇列的工作流程，使用 [更新訊息]**** 更有效率。 您的應用程式可以將作業狀態儲存到訊息，然後繼續工作，而不是每次步驟完成時，便重新佇列訊息以進行作業的下一個步驟。 請記住，每個 [更新訊息]**** 作業都會算在延展性目標內。

## <a name="application-architecture"></a>應用程式架構

使用佇列，讓您的應用程式架構具延展性。 下列將列出您可以使用佇列，讓應用程式變得較具擴充性的幾個方式：  

- 您可以使用佇列來建立工作的待處理項目，以便在您的應用程式中處理及穩住工作負載。 例如，您可以佇列使用者的要求，以執行需密集使用處理器的工作 (例如重新調整上傳的影像)。
- 您可以使用佇列來脫離應用程式的零件，以便您可以個別進行調整。 例如，Web 前端可以將使用者的調查結果放到佇列中，以供日後分析與儲存。 您可以加入更多的背景工作角色執行個體，以視需要處理佇列資料。  

## <a name="next-steps"></a>後續步驟

- [佇列儲存體的延展性和效能目標](scalability-targets.md)
- [標準儲存體帳戶的延展性和效能目標](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [狀態和錯誤碼](/rest/api/storageservices/Status-and-Error-Codes2)
