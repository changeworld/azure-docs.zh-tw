---
title: Blob 儲存體的效能和擴充性檢查清單-Azure 儲存體
description: 在開發高效能應用程式時，搭配 Blob 儲存體使用的已證明做法檢查清單。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: b94725d4d3eb9fd6f13a39d00486b4ab085b9ef9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80473942"
---
# <a name="performance-and-scalability-checklist-for-blob-storage"></a>Blob 儲存體的效能和延展性檢查清單

Microsoft 針對使用 Blob 儲存體開發高效能應用程式，開發了一些經過證實的作法。 此檢查清單會識別可供開發人員遵循以將效能最佳化的重要做法。 在設計應用程式時和整個過程中，請記住這些做法。

Azure 儲存體具有容量、交易速率和頻寬的延展性和效能目標。 如需 Azure 儲存體擴充性目標的詳細資訊，請參閱[標準儲存體帳戶的擴充性和效能目標](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)和[Blob 儲存體的擴充和效能目標](scalability-targets.md)。

## <a name="checklist"></a>檢查清單

這篇文章將效能經過證實的作法，整理成在開發 Blob 儲存體應用程式時可遵循的檢查清單。

| 完成 | 類別 | 設計考量 |
| --- | --- | --- |
| &nbsp; |延展性目標 |[您是否可以將應用程式設計成使用的儲存體帳戶數目不超過上限？](#maximum-number-of-storage-accounts) |
| &nbsp; |延展性目標 |[您是否要避免接近容量和交易限制？](#capacity-and-transaction-targets) |
| &nbsp; |延展性目標 |[有大量的用戶端同時存取單一 blob 嗎？](#multiple-clients-accessing-a-single-blob-concurrently) |
| &nbsp; |延展性目標 |[您的應用程式是否會在單一 blob 的擴充性目標內保持不動？](#bandwidth-and-operations-per-blob) |
| &nbsp; |資料分割 |[您的命名慣例設計能因應更好的負載平衡嗎？](#partitioning) |
| &nbsp; |網路功能 |[用戶端裝置是否有足夠高的頻寬和足夠低的延遲，以達到所需的效能？](#throughput) |
| &nbsp; |網路 |[用戶端裝置是否有高品質網路連結？](#link-quality) |
| &nbsp; |網路 |[用戶端應用程式是否位於與儲存體帳戶相同的區域中？](#location) |
| &nbsp; |直接用戶端存取 |[您是否使用共用存取簽章 (SAS) 和跨原始資源共用 (CORS) 來啟用 Azure 儲存體的直接存取？](#sas-and-cors) |
| &nbsp; |快取 |[您的應用程式快取經常存取且極少變更的資料嗎？](#reading-data) |
| &nbsp; |快取 |[您的應用程式是否會藉由在用戶端上快取更新，然後在較大的集合中上傳來進行批次處理](#uploading-data-in-batches) |
| &nbsp; |.NET 組態 |[您使用 .NET Core 2.1 或更新版本來獲得最佳效能嗎？](#use-net-core) |
| &nbsp; |.NET 組態 |[您是否已設定用戶端使用足夠數量的並行連線？](#increase-default-connection-limit) |
| &nbsp; |.NET 組態 |[針對 .NET 應用程式，您是否已設定 .NET 使用足夠數量的執行緒？](#increase-minimum-number-of-threads) |
| &nbsp; |平行處理原則 |[您是否已確保平行處理原則已適當地受到限制，因此您不會讓用戶端功能超載或接近延展性目標？](#unbounded-parallelism) |
| &nbsp; |工具 |[您是否使用 Microsoft 所提供的最新用戶端程式庫和工具版本？](#client-libraries-and-tools) |
| &nbsp; |重試 |[您是否針對節流錯誤和逾時使用重試原則搭配指數輪詢？](#timeout-and-server-busy-errors) |
| &nbsp; |重試 |[您的應用程式是否避免重試不能再嘗試的錯誤？](#non-retryable-errors) |
| &nbsp; |複製 blob |[您是以最有效率的方式複製 blob 嗎？](#blob-copy-apis) |
| &nbsp; |複製 blob |[您是否使用最新版的 AzCopy 進行大量複製作業？](#use-azcopy) |
| &nbsp; |複製 blob |[您是否使用 Azure 資料箱系列來匯入大量資料？](#use-azure-data-box) |
| &nbsp; |內容發佈 |[您是否會使用 CDN 進行內容發佈？](#content-distribution) |
| &nbsp; |使用中繼資料 |[您是否將 Blob 相關的常用中繼資料儲存在它的中繼資料內？](#use-metadata) |
| &nbsp; |快速上傳 |[嘗試快速上傳一個 Blob 時，您是否平行上傳區塊？](#upload-one-large-blob-quickly) |
| &nbsp; |快速上傳 |[嘗試快速上傳多個 Blob 時，您是否平行上傳 Blob？](#upload-many-blobs-quickly) |
| &nbsp; |Blob 類型 |[您是否在適當的時機使用分頁 Blob 或區塊 Blob？](#choose-the-correct-type-of-blob) |

## <a name="scalability-targets"></a>延展性目標

如果您的應用程式達到或超過任何延展性目標，它可能會遇到增加的交易延遲或節流。 當 Azure 儲存體對您的應用程式進行節流時，該服務會開始傳回 503 (伺服器忙碌) 或 500 (作業逾時) 錯誤碼。 保持在延展性目標的限制範圍內以避免這些錯誤，對於提升應用程式效能很重要。

如需佇列服務的延展性目標詳細資訊，請參閱 [Azure 儲存體延展性和效能目標](/azure/storage/queues/scalability-targets#scale-targets-for-queue-storage)。

### <a name="maximum-number-of-storage-accounts"></a>儲存體帳戶的數目上限

如果您已接近特定訂用帳戶/區域組合允許的儲存體帳戶數目上限，請評估您的案例，並判斷是否適用下列任何條件：

- 您是否使用儲存體帳戶來儲存非受控磁片，並將這些磁片新增至您的虛擬機器（Vm）？ 在此案例中，Microsoft 建議使用受控磁片。 受控磁片會自動為您調整規模，而不需要建立及管理個別的儲存體帳戶。 如需詳細資訊，請參閱[Azure 受控磁片簡介](../../virtual-machines/windows/managed-disks-overview.md)
- 您是否為每位客戶使用一個儲存體帳戶，以進行資料隔離？ 針對此案例，Microsoft 建議為每個客戶使用 blob 容器，而不是整個儲存體帳戶。 Azure 儲存體現在可讓您以每個容器為基礎來指派角色型存取控制（RBAC）角色。 如需詳細資訊，請參閱[在 Azure 入口網站中使用 RBAC 授與 Azure Blob 和佇列資料的存取權](../common/storage-auth-aad-rbac-portal.md) \(部分機器翻譯\)。
- 您是否使用多個儲存體帳戶來分區，以增加每秒的輸入、輸出、i/o 作業（IOPS）或容量？ 在此案例中，可能的話，我們建議您善用提高儲存體帳戶的限制，以降低您的工作負載所需的儲存體帳戶數目。 請連絡 [Azure 支援](https://azure.microsoft.com/support/options/)來要求提高儲存體帳戶的限制。 如需詳細資訊，請參閱[宣佈較大型、較大規模的儲存體帳戶](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)。

### <a name="capacity-and-transaction-targets"></a>容量和交易目標

如果您的應用程式即將達到單一儲存體帳戶的延展性目標，您可以考慮採用下列其中一個方法：  

- 如果您的應用程式達到交易目標，請考慮使用區塊 blob 儲存體帳戶，其已針對高交易率和低和一致延遲進行優化。 如需詳細資訊，請參閱 [Azure 儲存體帳戶概觀](../common/storage-account-overview.md)。
- 重新思考會造成您的應用程式達到或超出延展性目標的工作負載。 您是否能夠以不同的方式設計工作負載，以使用較少的頻寬或容量或使用較少的交易？
- 如果您的應用程式必須超出其中一個延展性目標，則建立多個儲存體帳戶，並將您的應用程式資料在這幾個儲存體帳戶中進行分割。 如果您要使用此模式，那麼請確定設計您的應用程式，以便日後可以增加更多儲存體帳戶以取得負載平衡。 除了儲存資料、進行交易或傳輸資料等使用方式外，儲存體帳戶本身不會有其他費用。
- 如果您的應用程式很接近頻寬目標，請考慮在用戶端壓縮資料，以便降低將資料傳送至 Azure 儲存體所需的頻寬。
    雖然壓縮資料可節省頻寬並改善網路效能，但也可能對效能造成負面影響。 針對用戶端上資料壓縮和解壓縮，評估額外處理需求所造成的效能影響。 請記住，儲存壓縮的資料可能會使疑難排解變得更困難，因為使用標準工具來檢視資料可能更具挑戰性。
- 如果您的應用程式很接近延展性目標，則務必使用指數輪詢進行重試。 最好是藉由實作本文所述的建議，嘗試避免達到延展性目標。 不過，使用指數輪詢進行重試會讓您的應用程式無法快速重試，這可能會使節流變差。 如需詳細資訊，請參閱[逾時和伺服器忙碌錯誤](#timeout-and-server-busy-errors)一節。

### <a name="multiple-clients-accessing-a-single-blob-concurrently"></a>同時存取單一 blob 的多個用戶端

如果您有大量的用戶端同時存取單一 blob，就必須考慮每個 blob 和每個儲存體帳戶的擴充性目標。 可以存取單一 blob 的用戶端確切數目會因因素而有所不同，例如同時要求 blob 的用戶端數目、blob 的大小，以及網路狀況。

如果可以透過 CDN 散發 blob （例如從網站提供的影像或影片），則您可以使用 CDN。 如需詳細資訊，請參閱標題為[內容發佈](#content-distribution)一節。

在其他案例中（例如資料為機密的科學模擬），您有兩個選項。 第一種是錯開工作負載的存取權，讓 blob 在一段時間內被存取，而不是同時存取。 或者，您可以暫時將 blob 複製到多個儲存體帳戶，以增加每個 blob 和跨儲存體帳戶的總 IOPS。 結果會根據您的應用程式行為而有所不同，因此請務必在設計期間測試並行模式。

### <a name="bandwidth-and-operations-per-blob"></a>每個 blob 的頻寬和作業

單一 blob 每秒最多支援500個要求。 如果您有多個用戶端需要讀取相同的 blob，而且您可能會超過此限制，則請考慮使用區塊 blob 儲存體帳戶。 區塊 blob 儲存體帳戶提供較高的要求率，或每秒 i/o 作業（IOPS）。

您也可以使用內容傳遞網路（CDN）（例如 Azure CDN）在 blob 上散發作業。 如需 Azure CDN 的詳細資訊，請參閱[AZURE cdn 總覽](../../cdn/cdn-overview.md)。  

## <a name="partitioning"></a>資料分割

瞭解 Azure 儲存體分割 blob 資料的方式有助於提升效能。 Azure 儲存體可以更快速地在單一分割區中提供資料，而不是跨越多個資料分割的資料。 藉由適當地命名 blob，您可以改善讀取要求的效率。

Blob 儲存體會使用以範圍為基礎的資料分割配置來進行調整和負載平衡。 每個 blob 都有一個分割區索引鍵，由完整的 blob 名稱（account + container + blob）組成。 分割區索引鍵是用來將 blob 資料分割為範圍。 然後，範圍會在 Blob 儲存體間進行負載平衡。

以範圍為基礎的資料分割表示使用詞法順序的命名慣例（例如， *mypayroll*、 *myperformance*、 *myemployees*等）或時間戳記（*log20160101*、 *log20160102*、 *log20160102*等）較可能會導致分割區位於相同的磁碟分割伺服器上。 ，直到負載增加時，才需要將它們分割成較小的範圍。 在相同的分割區伺服器上共置 blob 可提高效能，因此，效能增強的重要部分牽涉到以最有效的方式來命名 blob。

例如，容器內的所有 Bob 接受單一伺服器的服務，直到這些 Blob 的負載需要進一步重新平衡分割區範圍。 同樣地，一組以詞法順序排列的輕微載入帳戶，可能會由單一伺服器提供服務，直到其中一個或所有帳戶的負載需要分割到多個分割區伺服器為止。

每個負載平衡作業都可能在作業期間影響儲存體呼叫的延遲。 服務處理大量高載流量到分割區的能力，會受到單一分割區伺服器的擴充性的限制，直到負載平衡作業開始並重新平衡資料分割索引鍵範圍為止。

您可以遵循一些最佳做法來降低這類作業的頻率。  

- 可能的話，請針對標準儲存體帳戶使用大於 4 MiB 的 blob 或區塊大小，並針對高階儲存體帳戶使用大於256的 KiB。 較大的 blob 或區塊大小會自動啟動高輸送量區塊 blob。 高輸送量區塊 blob 提供不受磁碟分割命名影響的高效能內嵌。
- 檢查您用於帳戶、容器、blob、資料表和佇列的命名慣例。 請考慮使用最符合您需求的雜湊函數，在帳戶、容器或 blob 名稱前面加上三位數的雜湊。
- 如果您使用時間戳或數位識別碼來組織您的資料，請確定您不是使用僅限附加（或僅在前面）的流量模式。 這些模式不適合以範圍為基礎的資料分割系統。 這些模式可能會導致所有流量進入單一分割區，並將系統限制為有效的負載平衡。

    例如，如果您有每日作業使用具有時間戳記的 blob，例如*yyyymmdd*，則該每日作業的所有流量都會導向至單一分割區伺服器所服務的單一 blob。 請考慮每個 blob 的限制和每個分割區的限制是否符合您的需求，並視需要將此作業分解成多個 blob。 同樣地，如果您在資料表中儲存時間序列資料，則所有流量可能會導向至金鑰命名空間的最後一個部分。 如果您使用數值識別碼，請在識別碼前面加上三位數的雜湊。 如果您使用時間戳，請在時間戳記前面加上秒數值，例如*ssyyyymmdd*。 如果您的應用程式會定期執行清單和查詢作業，請選擇將會限制查詢數目的雜湊函數。 在某些情況下，隨機前置詞可能就已足夠。
  
- 如需 Azure 儲存體中使用之資料分割配置的詳細資訊，請參閱[Azure 儲存體：具有強式一致性的高可用性雲端儲存體服務](https://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf)。

## <a name="networking"></a>網路

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

如需 blob 內容的廣泛散發，請使用內容傳遞網路，例如 Azure CDN。 如需 Azure CDN 的詳細資訊，請參閱 [Azure CDN](../../cdn/cdn-overview.md)。  

## <a name="sas-and-cors"></a>SAS 和 CORS

假設您需要授權在使用者的網頁瀏覽器或在行動電話應用程式中執行的程式碼 (例如 JavaScript)，以存取 Azure 儲存體中的資料。 其中一種方法是建置可作為 Proxy 的服務應用程式。 使用者的裝置會向服務進行驗證，然後再授權存取 Azure 儲存體資源。 如此一來，您可以避免在未受到保護的裝置上公開您的儲存體帳戶金鑰。 不過，因為在使用者裝置與 Azure 儲存體之間轉送的所有資料都必須通過服務應用程式，所以此方法會在服務應用程式上加上顯著負荷。

使用共用存取簽章 (SAS)，即可避免使用服務應用程式作為 Azure 儲存體的 Proxy。 使用 SAS，您可以藉由使用有限的存取權杖，讓使用者的裝置能直接對 Azure 儲存體提出要求。 例如，如果使用者想要將相片上傳至您的應用程式，則您的服務應用程式可以產生 SAS，並將它傳送到使用者的裝置。 SAS 權杖可以授與在指定的時間間隔內寫入 Azure 儲存體資源的權限，該時間間隔後 SAS 權杖就會過期。 如需關於 SAS 的詳細資訊，請參閱[使用共用存取簽章 (SAS) 對 Azure 儲存體資源授與有限存取權](../common/storage-sas-overview.md)。  

通常，在某個網域上由網站託管的頁面中，網頁瀏覽器不允許 JavaScript 對另一個網域執行特定作業 (例如寫入作業)。 這項原則稱為同源原則，可防止某頁上的惡意程式碼取得另一個網頁上資料的存取權。 不過，在雲端建置解決方案時，同源原則會是一項限制。 跨原始資源共用 (CORS) 是一項瀏覽器功能，可讓目標網域與信任源自來源網域之要求的瀏覽器進行通訊。

例如，假設在 Azure 中執行的 Web 應用程式向 Azure 儲存體帳戶提出資源要求。 Web 應用程式是來源網域，而儲存體帳戶是目標網域。 您可以為任何 Azure 儲存體服務設定 CORS，以便與網頁瀏覽器進行通訊，而該網頁瀏覽器會向 Azure 儲存體所信任的來源網域提出要求。 如需 CORS 的詳細資訊，請參閱 [Azure 儲存體的跨原始資源共用 (CORS) 支援](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services)。  
  
SAS 和 CORS 都可協助您避免 Web 應用程式上不必要的負載。  

## <a name="caching"></a>快取

快取在效能方面扮演著重要的角色。 下列各節將討論快取的最佳作法。

### <a name="reading-data"></a>讀取資料

一般來說，讀取資料一次是比較理想的方式讀取兩次。 請考慮已從 Azure 儲存體取出 50 MiB blob 的 web 應用程式範例，以作為使用者的內容。 在理想情況下，應用程式會在本機將 blob 快取到磁片，然後針對後續的使用者要求抓取快取的版本。

若未在快取 blob 後進行修改，其中一種方式就是使用修改時間的條件式標頭來限定 GET 作業。 如果上次修改時間是在 blob 的快取時間之後，則會抓取並重新快取 blob。 否則，會抓取快取的 blob 以獲得最佳效能。

您也可以決定設計應用程式，以假設 blob 在抓取之後的短時間內保持不變。 在此情況下，應用程式不需要檢查 blob 是否在該間隔期間修改。

設定資料、查閱資料和應用程式經常使用的其他資料都是快取的理想候選項目。  

如需使用條件式標頭的詳細資訊，請參閱[指定 Blob 服務作業的條件式標頭](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations)。  

### <a name="uploading-data-in-batches"></a>以批次方式上傳資料

在某些情況下，您可以在本機匯總資料，然後在批次中定期上傳，而不是立即上傳每個資料片段。 例如，假設 web 應用程式保留活動的記錄檔。 應用程式可以將每個活動的詳細資料上傳到資料表（這需要多個儲存體作業），也可以將活動詳細資料儲存至本機記錄檔，然後定期將所有活動詳細資料以分隔的檔案上傳至 blob。 如果每個記錄專案的大小為 1 KB，您可以在單一交易中上傳上千個專案。 單一交易支援上傳大小上限為 64 MiB 的 blob。 應用程式開發人員必須針對用戶端裝置或上傳失敗的可能性進行設計。 如果需要下載一段時間的活動資料，而不是針對單一活動，則建議使用 Blob 儲存體，而不是資料表儲存體。

## <a name="net-configuration"></a>.NET 組態

如果使用 .NET Framework，本節會列出數個可用來大幅改善效能的快速組態設定。  如果使用其他語言，請查看類似的概念是否適用於您所選擇的語言。  

### <a name="use-net-core"></a>使用 .NET Core

使用 .NET Core 2.1 或更新版本來開發 Azure 儲存體應用程式，以充分利用效能增強功能。 可能的話，建議使用 .NET Core 3.x。

如需 .NET Core 中效能改進的詳細資訊，請參閱下列部落格文章：

- [.NET Core 3.0 中的效能改進](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [.NET Core 2.1 中的效能改進](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>提高預設的連線限制

在 .NET 中，下列程式碼會增加預設連接限制（這通常是用戶端環境中的兩個或伺服器環境中的十個）到100。 一般而言，您應將此值大約設為應用程式所使用的執行緒數量。 在開啟任何連線之前設定連線限制。

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

如需其他程式設計語言，請參閱檔，以判斷如何設定連接限制。  

如需詳細資訊，請參閱 blog 文章[Web 服務：並行](https://blogs.msdn.microsoft.com/darrenj/2005/03/07/web-services-concurrent-connections/)連線。  

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

## <a name="copying-and-moving-blobs"></a>複製和移動 blob

Azure 儲存體提供一些解決方案，可讓您在儲存體帳戶內、在儲存體帳戶之間，以及在內部部署系統與雲端之間複製和移動 blob。 本節說明這些選項在效能方面的影響。 如需有效率地將資料傳輸至 Blob 儲存體或從中傳送資料的詳細資訊，請參閱[選擇用於資料傳輸的 Azure 解決方案](../common/storage-choose-data-transfer-solution.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

### <a name="blob-copy-apis"></a>Blob 複製 Api

若要在儲存體帳戶之間複製 blob，請使用[Put Block FROM URL](/rest/api/storageservices/put-block-from-url)作業。 此作業會以同步方式將資料從任何 URL 來源複製到區塊 blob。 `Put Block from URL`當您跨儲存體帳戶遷移資料時，使用作業可以大幅降低所需的頻寬。 因為複製作業會在服務端進行，所以您不需要下載並重新上傳資料。

若要在相同的儲存體帳戶中複製資料，請使用[複製 Blob](/rest/api/storageservices/Copy-Blob)作業。 在相同的儲存體帳戶內複製資料通常會快速完成。  

### <a name="use-azcopy"></a>使用 AzCopy

AzCopy 命令列公用程式是一個簡單且有效率的選項，可讓您在儲存體帳戶之間大量傳送 blob。 AzCopy 已針對此案例進行優化，並可達到高傳輸率。 AzCopy 第10版會使用作業 `Put Block From URL` ，在儲存體帳戶之間複製 blob 資料。 如需詳細資訊，請參閱[使用 AzCopy V10 將資料複製或移動到 Azure 儲存體](/azure/storage/common/storage-use-azcopy-v10)。  

### <a name="use-azure-data-box"></a>使用 Azure 資料箱

若要將大量資料匯入 Blob 儲存體，請考慮使用 Azure 資料箱系列進行離線傳輸。 當您受限於時間、網路可用性或成本時，Microsoft 提供的資料箱裝置是將大量資料移至 Azure 的絕佳選擇。 如需詳細資訊，請參閱[Azure DataBox 檔](/azure/databox/)。

## <a name="content-distribution"></a>內容發佈

有時候，應用程式需要提供相同的內容給許多使用者（例如，網站首頁上所使用的產品示範影片），位於相同或多個區域中。 在此案例中，請使用內容傳遞網路（CDN）（例如 Azure CDN），將 blob 內容分散到地理位置。 不同於在單一區域內存在，且無法以低延遲方式傳遞內容到其他區域的 Azure 儲存體帳戶，Azure CDN 會使用在全球多個資料中心內的伺服器。 此外，CDN 通常可以支援比單一儲存體帳戶高很多的輸出限制。  

如需 Azure CDN 的詳細資訊，請參閱 [Azure CDN](../../cdn/cdn-overview.md)。

## <a name="use-metadata"></a>使用中繼資料

Blob 服務支援 HEAD 要求，其中可以包含 Blob 屬性或中繼資料。 例如，如果您的應用程式需要相片的 Exif （exchangable 影像格式）資料，它可以抓取相片並將它解壓縮。 為了節省頻寬並改善效能，您的應用程式可以在應用程式上傳相片時，將 Exif 資料儲存在 blob 的中繼資料中。 然後，您就可以只使用 HEAD 要求來取出中繼資料中的 Exif 資料。 僅抓取中繼資料，而不是 blob 的完整內容可節省大量頻寬，並減少解壓縮 Exif 資料所需的處理時間。 請記住，每個 blob 可以儲存 8 KiB 的中繼資料。  

## <a name="upload-blobs-quickly"></a>快速上傳 blob

若要快速上傳 blob，請先判斷您是否將上傳一個 blob 或多個 blob。 使用下列指引，根據您的案例來判斷所要使用的正確方法。  

### <a name="upload-one-large-blob-quickly"></a>快速上傳一個大型 blob

若要快速上傳單一大型 blob，用戶端應用程式可以平行上傳其區塊或分頁，以留意個別 blob 和儲存體帳戶整體的擴充性目標。 Azure 儲存體用戶端程式庫支援以平行方式上傳。 例如，您可以使用下列屬性來指定 .NET 或 JAVA 中允許的並行要求數目。 其他支援語言的用戶端程式庫會提供類似的選項。

- 針對 .NET，請設定[BlobRequestOptions. ParallelOperationThreadCount](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount)屬性。
- 針對 JAVA/Android，請呼叫[BlobRequestOptions. blobrequestoptions.setconcurrentrequestcount （最終整數 concurrentRequestCount）](/java/api/com.microsoft.azure.storage.blob.blobrequestoptions.setconcurrentrequestcount)方法。

### <a name="upload-many-blobs-quickly"></a>快速上傳多個 blob

若要快速上傳多個 Blob，請平行上傳 Blob。 以平行方式上傳會比一次上傳單一 blob （平行區塊上傳）更快，因為它會將上傳分散到儲存體服務的多個磁碟分割。 AzCopy 依預設會執行平行上傳，在此案例中我們建議使用此方法。 如需詳細資訊，請參閱[開始使用 AzCopy](../common/storage-use-azcopy-v10.md)。  

## <a name="choose-the-correct-type-of-blob"></a>選擇正確的 blob 類型

Azure 儲存體支援區塊 blob、附加 blob 和分頁 blob。 在指定使用的案例中，您的 Blob 類型選擇將會影響解決方案的效能和延展性。

當您想要有效率地上傳大量資料時，區塊 blob 是適當的。 例如，將相片或影片上傳至 Blob 儲存體的用戶端應用程式會以區塊 blob 為目標。

附加 blob 類似于區塊 blob，因為它們是由區塊所組成。 當您修改附加 blob 時，區塊只會新增至 blob 的結尾。 當應用程式需要將資料新增至現有的 blob 時，附加 blob 適用于記錄等案例。

分頁 blob 適用于應用程式需要在資料上執行隨機寫入的情況。 例如，Azure 虛擬機器磁片會儲存為分頁 blob。 如需詳細資訊，請參閱[瞭解區塊 blob、附加 blob 和分頁 blob](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)。  

## <a name="next-steps"></a>後續步驟

- [Blob 儲存體的延展性和效能目標](scalability-targets.md)
- [標準儲存體帳戶的延展性和效能目標](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [狀態和錯誤碼](/rest/api/storageservices/Status-and-Error-Codes2)
