---
title: Blob 儲存效能與可擴充性檢查表 - Azure 儲存
description: 用於開發高性能應用程式的已驗證實踐清單。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: b94725d4d3eb9fd6f13a39d00486b4ab085b9ef9
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473942"
---
# <a name="performance-and-scalability-checklist-for-blob-storage"></a>Blob 儲存體的效能和延展性檢查清單

Microsoft 開發了許多經過驗證的實踐,用於使用 Blob 存儲開發高性能應用程式。 此檢查清單會識別可供開發人員遵循以將效能最佳化的重要做法。 在設計應用程式時和整個過程中，請記住這些做法。

Azure 儲存體具有容量、交易速率和頻寬的延展性和效能目標。 有關 Azure 儲存可伸縮性目標的詳細資訊,請參閱[標準儲存帳戶的可伸縮性和性能目標](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)以及[Blob 儲存的可伸縮性和效能目標](scalability-targets.md)。

## <a name="checklist"></a>檢查清單

本文將經過驗證的性能實踐組織到開發 Blob 存儲應用程式時可以遵循的清單中。

| 完成 | 類別 | 設計考量 |
| --- | --- | --- |
| &nbsp; |延展性目標 |[您是否可以將應用程式設計成使用的儲存體帳戶數目不超過上限？](#maximum-number-of-storage-accounts) |
| &nbsp; |延展性目標 |[您是否要避免接近容量和交易限制？](#capacity-and-transaction-targets) |
| &nbsp; |延展性目標 |[大量用戶端是同時訪問單個 Blob 嗎?](#multiple-clients-accessing-a-single-blob-concurrently) |
| &nbsp; |延展性目標 |[您的應用程式是否保持在單個 Blob 的可伸縮性目標之內?](#bandwidth-and-operations-per-blob) |
| &nbsp; |資料分割 |[您的命名慣例設計能因應更好的負載平衡嗎？](#partitioning) |
| &nbsp; |網路功能 |[用戶端裝置是否有足夠高的頻寬和足夠低的延遲，以達到所需的效能？](#throughput) |
| &nbsp; |網路功能 |[用戶端裝置是否有高品質網路連結？](#link-quality) |
| &nbsp; |網路功能 |[用戶端應用程式是否位於與儲存體帳戶相同的區域中？](#location) |
| &nbsp; |直接用戶端存取 |[您是否使用共用存取簽章 (SAS) 和跨原始資源共用 (CORS) 來啟用 Azure 儲存體的直接存取？](#sas-and-cors) |
| &nbsp; |Caching |[您的應用程式快取的資料是否經常存取且很少更改?](#reading-data) |
| &nbsp; |Caching |[您的應用程式是否通過在用戶端上緩存更新,然後以更大的集上載更新?](#uploading-data-in-batches) |
| &nbsp; |.NET 組態 |[您使用 .NET Core 2.1 或更新版本來獲得最佳效能嗎？](#use-net-core) |
| &nbsp; |.NET 組態 |[您是否已設定用戶端使用足夠數量的並行連線？](#increase-default-connection-limit) |
| &nbsp; |.NET 組態 |[針對 .NET 應用程式，您是否已設定 .NET 使用足夠數量的執行緒？](#increase-minimum-number-of-threads) |
| &nbsp; |平行處理原則 |[您是否已確保平行處理原則已適當地受到限制，因此您不會讓用戶端功能超載或接近延展性目標？](#unbounded-parallelism) |
| &nbsp; |工具 |[您是否使用 Microsoft 所提供的最新用戶端程式庫和工具版本？](#client-libraries-and-tools) |
| &nbsp; |重試 |[您是否針對節流錯誤和逾時使用重試原則搭配指數輪詢？](#timeout-and-server-busy-errors) |
| &nbsp; |重試 |[您的應用程式是否避免重試不能再嘗試的錯誤？](#non-retryable-errors) |
| &nbsp; |複製 Blob |[您是否以最有效的方式複製 blob?](#blob-copy-apis) |
| &nbsp; |複製 Blob |[您是否使用最新版本的 AzCopy 進行批次複製操作?](#use-azcopy) |
| &nbsp; |複製 Blob |[您是否使用 Azure 資料框系列匯入大量資料?](#use-azure-data-box) |
| &nbsp; |內容發佈 |[您是否會使用 CDN 進行內容發佈？](#content-distribution) |
| &nbsp; |使用中繼資料 |[您是否將 Blob 相關的常用中繼資料儲存在它的中繼資料內？](#use-metadata) |
| &nbsp; |快速上傳 |[嘗試快速上傳一個 Blob 時，您是否平行上傳區塊？](#upload-one-large-blob-quickly) |
| &nbsp; |快速上傳 |[嘗試快速上傳多個 Blob 時，您是否平行上傳 Blob？](#upload-many-blobs-quickly) |
| &nbsp; |Blob 類型 |[您是否在適當的時機使用分頁 Blob 或區塊 Blob？](#choose-the-correct-type-of-blob) |

## <a name="scalability-targets"></a>延展性目標

如果您的應用程式達到或超過任何延展性目標，它可能會遇到增加的交易延遲或節流。 當 Azure 儲存體對您的應用程式進行節流時，該服務會開始傳回 503 (伺服器忙碌) 或 500 (作業逾時) 錯誤碼。 保持在延展性目標的限制範圍內以避免這些錯誤，對於提升應用程式效能很重要。

如需佇列服務的延展性目標詳細資訊，請參閱 [Azure 儲存體延展性和效能目標](/azure/storage/queues/scalability-targets#scale-targets-for-queue-storage)。

### <a name="maximum-number-of-storage-accounts"></a>儲存體帳戶的數目上限

如果您接近特定訂閱/區域組合允許的最大儲存帳戶數,請評估您的方案並確定是否適用以下任何條件:

- 您是否使用儲存帳戶來儲存非託管磁碟並將這些磁碟添加到虛擬機器 (VM)? 對於此方案,Microsoft 建議使用託管磁碟。 託管磁碟可自動為您擴展,無需創建和管理單個儲存帳戶。 有關詳細資訊,請參閱[Azure 託管磁碟簡介](../../virtual-machines/windows/managed-disks-overview.md)
- 您是否使用每個客戶的一個儲存帳戶進行數據隔離? 對於此方案,Microsoft 建議為每個客戶使用 Blob 容器,而不是整個存儲帳戶。 Azure 儲存現在允許您基於每個容器分配基於角色的存取控制 (RBAC) 角色。 如需詳細資訊，請參閱[在 Azure 入口網站中使用 RBAC 授與 Azure Blob 和佇列資料的存取權](../common/storage-auth-aad-rbac-portal.md) \(部分機器翻譯\)。
- 您是否使用多個儲存帳戶分片來增加每秒 (IOPS) 或容量的入口、出口、I/O 操作( IOPS)? 在此案例中，可能的話，我們建議您善用提高儲存體帳戶的限制，以降低您的工作負載所需的儲存體帳戶數目。 請連絡 [Azure 支援](https://azure.microsoft.com/support/options/)來要求提高儲存體帳戶的限制。 如需詳細資訊，請參閱[宣佈較大型、較大規模的儲存體帳戶](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)。

### <a name="capacity-and-transaction-targets"></a>容量和交易目標

如果您的應用程式即將達到單一儲存體帳戶的延展性目標，您可以考慮採用下列其中一個方法：  

- 如果應用程式達到事務目標,請考慮使用塊 Blob 存儲帳戶,這些帳戶針對高事務速率和低一致的延遲進行了優化。 如需詳細資訊，請參閱 [Azure 儲存體帳戶概觀](../common/storage-account-overview.md)。
- 重新思考會造成您的應用程式達到或超出延展性目標的工作負載。 您是否能夠以不同的方式設計工作負載，以使用較少的頻寬或容量或使用較少的交易？
- 如果您的應用程式必須超出其中一個延展性目標，則建立多個儲存體帳戶，並將您的應用程式資料在這幾個儲存體帳戶中進行分割。 如果您要使用此模式，那麼請確定設計您的應用程式，以便日後可以增加更多儲存體帳戶以取得負載平衡。 除了儲存資料、進行交易或傳輸資料等使用方式外，儲存體帳戶本身不會有其他費用。
- 如果您的應用程式很接近頻寬目標，請考慮在用戶端壓縮資料，以便降低將資料傳送至 Azure 儲存體所需的頻寬。
    雖然壓縮資料可節省頻寬並改善網路效能，但也可能對效能造成負面影響。 針對用戶端上資料壓縮和解壓縮，評估額外處理需求所造成的效能影響。 請記住，儲存壓縮的資料可能會使疑難排解變得更困難，因為使用標準工具來檢視資料可能更具挑戰性。
- 如果您的應用程式很接近延展性目標，則務必使用指數輪詢進行重試。 最好是藉由實作本文所述的建議，嘗試避免達到延展性目標。 不過，使用指數輪詢進行重試會讓您的應用程式無法快速重試，這可能會使節流變差。 如需詳細資訊，請參閱[逾時和伺服器忙碌錯誤](#timeout-and-server-busy-errors)一節。

### <a name="multiple-clients-accessing-a-single-blob-concurrently"></a>多個客戶端同時存取單一個 Blob

如果同時存取單個 Blob 的客戶端數量眾多,則需要同時考慮每個 Blob 和每個儲存帳戶的可伸縮性目標。 可以存取單個 Blob 的確切用戶端數將因各種因素而異,例如同時請求 blob 的用戶端數、blob 的大小和網路條件。

如果 Blob 可以通過 CDN(如從網站提供的圖像或影片)進行分發,則可以使用 CDN。 有關詳細資訊,請參閱標題為[「內容分發」的部分](#content-distribution)。

在其他方案中,例如數據保密的科學類比,您有兩個選項。 第一種是錯開工作負荷的訪問,以便在一段時間內訪問 Blob,而不是同時訪問。 或者,您可以臨時將 Blob 複製到多個儲存帳戶,以增加每個 Blob 和跨存儲帳戶的總 IOPS。 結果因應用程式的行為而異,因此請確保在設計期間測試併發模式。

### <a name="bandwidth-and-operations-per-blob"></a>每個 Blob 的頻寬和操作

單個 Blob 每秒最多支援 500 個請求。 如果有多個用戶端需要讀取同一 Blob,並且可能超過此限制,請考慮使用塊 Blob 存儲帳戶。 塊 Blob 儲存帳戶提供更高的請求率,或每秒 I/O 操作 (IOPS)。

您還可以使用內容傳遞網路 (CDN)(如 Azure CDN)在 Blob 上分發操作。 有關 Azure CDN 的詳細資訊,請參閱[Azure CDN 概述](../../cdn/cdn-overview.md)。  

## <a name="partitioning"></a>資料分割

瞭解 Azure 儲存如何分區 Blob 數據對於提高性能非常有用。 Azure 儲存可以比跨多個分區的數據更快地在單個分區中提供數據。 通過適當地命名 blob,可以提高讀取請求的效率。

Blob 儲存使用基於範圍的分區方案進行縮放和負載平衡。 每個 Blob 都有一個由完整 Blob 名稱(帳戶_容器_blob)組成的分區鍵。 分區鍵用於將 Blob 資料分區到範圍中。 然後,跨 Blob 存儲對範圍進行負載平衡。

基於範圍的分區意味著使用詞法排序(例如 *,myPayroll、**我的性能*、*我的員工*等)或時間戳 *(log20160101、log20160102、log20160102*等)的命名約定更有可能導致分區在同一分區伺服器上同時位於*log20160102**log20160102*同一分區伺服器上。 ,直到增加負載需要它們拆分為更小的範圍。 在同一分區伺服器上共同定位 Blob 可增強性能,因此性能增強的一個重要部分涉及以最有效地組織 blob 的方式命名 blob。

例如，容器內的所有 Bob 接受單一伺服器的服務，直到這些 Blob 的負載需要進一步重新平衡分割區範圍。 同樣,一組按詞法順序排列其名稱的輕載入帳戶可能由單個伺服器提供服務,直到其中一個或所有這些帳戶上的負載要求它們跨多個分區伺服器拆分。

每個負載平衡作業都可能在作業期間影響儲存體呼叫的延遲。 服務處理分區突然突發流量的能力受到單個分區伺服器的可伸縮性的限制,直到負載平衡操作啟動並重新平衡分區密鑰範圍。

您可以遵循一些最佳做法來降低這類作業的頻率。  

- 如果可能,對於標準存儲帳戶使用大於 4 MiB 的 blob 或塊大小,對於高級存儲帳戶,使用大於 256 KiB 的 blob 或塊大小。 較大的 Blob 或塊大小會自動啟動高通量塊 Blob。 高輸送量塊 Blob 提供不受分區命名影響的高性能引入。
- 檢查用於帳戶、容器、blob、表和佇列的命名約定。 請考慮使用最適合您需求的哈希函數,使用三位哈希來對帳戶、容器或 blob 名稱進行首碼。
- 如果使用時間戳或數位識別符組織數據,請確保不使用僅追加(或僅預尾)流量模式。 這些模式不適用於基於範圍的分區系統。 這些模式可能導致所有流量進入單個分區,並限制系統進行有效的負載平衡。

    例如,如果具有使用具有時間戳(如*yyymmd)* 的 Blob 的每日操作,則該日常操作的所有流量將定向到單個 Blob,該 Blob 由單個分區伺服器提供。 考慮每個 Blob 限制和每個分區限制是否滿足您的需要,並考慮根據需要將此操作分解為多個 Blob。 同樣,如果在表中存儲時間序列數據,則所有流量可能都定向到密鑰命名空間的最後一部分。 如果使用數位 ID,則使用三位哈希來前置碼 ID。 如果使用時間戳,則用秒值(例如*syyyymmd)* 前綴時間戳。 如果應用程式例行執行清單和查詢操作,請選擇一個哈希函數來限制查詢數。 在某些情況下,隨機首碼可能就足夠了。
  
- 有關 Azure 儲存中使用的分割區方案的詳細資訊,請參閱 Azure[儲存:具有強一致性的高度可用雲端儲存服務](https://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf)。

## <a name="networking"></a>網路功能

應用程式的實體網路限制可能會對效能產生重大影響。 下列各節說明使用者可能會遇到的部分限制。  

### <a name="client-network-capability"></a>用戶端網路功能

如下列各節所述，網路連結的頻寬和品質在應用程式效能中扮演重要角色。

#### <a name="throughput"></a>Throughput

頻寬的問題經常是用戶端的功能。 較大型的 Azure 執行個體擁有較大容量的 NIC，因此，如果您需要單一機器的較高網路限制，您應考慮使用較大型的執行個體或更多的 VM。 如果您從內部部署應用程式存取 Azure 儲存體，則適用相同的規則：了解用戶端裝置的網路功能和與 Azure 儲存體位置的網路連線能力，以及視需要進行改善或設計您的應用程式以便使用其功能。

#### <a name="link-quality"></a>連結品質

與任何網路使用方式一樣，請記住導致錯誤和封包遺失的網路狀況將會減慢有效的輸送量。  使用 WireShark 或 NetMon 可能有助於診斷此問題。  

### <a name="location"></a>Location

在任何分散式環境中，將用戶端放置於伺服器附近可提供最佳的效能。 若要以最低的延遲時間存取 Azure 儲存體，對用戶端而言的最佳位置是在同一個 Azure 區域內。 例如，如果您擁有使用 Azure 儲存體的 Azure Web 應用程式，則將這兩者置於單一區域內 (例如，美國西部或東南亞)。 共置資源可降低延遲和成本，因為單一區域內的頻寬使用量是免費的。  

如果將存取 Azure 儲存體的用戶端應用程式不在 Azure 中託管 (例如行動裝置應用程式或內部部署企業服務)，則將儲存體帳戶置於這些用戶端附近的區域內可以降低延遲。 如果您的用戶端分佈廣闊 (例如，有些在北美洲，有些在歐洲)，則考慮在每個區域使用一個儲存體帳戶。 如果應用程式儲存的資料特定用於個別使用者，且不需要複寫儲存體帳戶之間的資料，則此方法較容易實作。

要廣泛分發 Blob 內容,請使用內容傳遞網路(如 Azure CDN)。 如需 Azure CDN 的詳細資訊，請參閱 [Azure CDN](../../cdn/cdn-overview.md)。  

## <a name="sas-and-cors"></a>SAS 和 CORS

假設您需要授權在使用者的網頁瀏覽器或在行動電話應用程式中執行的程式碼 (例如 JavaScript)，以存取 Azure 儲存體中的資料。 其中一種方法是建置可作為 Proxy 的服務應用程式。 使用者的裝置會向服務進行驗證，然後再授權存取 Azure 儲存體資源。 如此一來，您可以避免在未受到保護的裝置上公開您的儲存體帳戶金鑰。 不過，因為在使用者裝置與 Azure 儲存體之間轉送的所有資料都必須通過服務應用程式，所以此方法會在服務應用程式上加上顯著負荷。

使用共用存取簽章 (SAS)，即可避免使用服務應用程式作為 Azure 儲存體的 Proxy。 使用 SAS，您可以藉由使用有限的存取權杖，讓使用者的裝置能直接對 Azure 儲存體提出要求。 例如，如果使用者想要將相片上傳至您的應用程式，則您的服務應用程式可以產生 SAS，並將它傳送到使用者的裝置。 SAS 權杖可以授與在指定的時間間隔內寫入 Azure 儲存體資源的權限，該時間間隔後 SAS 權杖就會過期。 如需關於 SAS 的詳細資訊，請參閱[使用共用存取簽章 (SAS) 對 Azure 儲存體資源授與有限存取權](../common/storage-sas-overview.md)。  

通常，在某個網域上由網站託管的頁面中，網頁瀏覽器不允許 JavaScript 對另一個網域執行特定作業 (例如寫入作業)。 這項原則稱為同源原則，可防止某頁上的惡意程式碼取得另一個網頁上資料的存取權。 不過，在雲端建置解決方案時，同源原則會是一項限制。 跨原始資源共用 (CORS) 是一項瀏覽器功能，可讓目標網域與信任源自來源網域之要求的瀏覽器進行通訊。

例如，假設在 Azure 中執行的 Web 應用程式向 Azure 儲存體帳戶提出資源要求。 Web 應用程式是來源網域，而儲存體帳戶是目標網域。 您可以為任何 Azure 儲存體服務設定 CORS，以便與網頁瀏覽器進行通訊，而該網頁瀏覽器會向 Azure 儲存體所信任的來源網域提出要求。 如需 CORS 的詳細資訊，請參閱 [Azure 儲存體的跨原始資源共用 (CORS) 支援](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services)。  
  
SAS 和 CORS 都可協助您避免 Web 應用程式上不必要的負載。  

## <a name="caching"></a>Caching

緩存在性能中起著重要的作用。 以下各節討論緩存最佳做法。

### <a name="reading-data"></a>讀取資料

通常,讀取數據一次比讀取數據兩次更可取。 請考慮從 Azure 儲存中檢索到 50 MiB Blob 以用作使用者內容的 Web 應用程式的範例。 理想情況下,應用程式將 Blob 本地緩存到磁碟,然後檢索後續使用者請求的緩存版本。

如果 Blob 自緩存以來尚未修改,則避免檢索 Blob 的一種方法是使用條件標頭限定 GET 操作以進行修改。 如果上次修改的時間是在緩存 Blob 之後,則檢索並重新緩存 Blob。 否則,將檢索緩存的 Blob 以獲得最佳性能。

您還可以決定設計應用程式,假定 Blob 在檢索後將在短時間內保持不變。 在這種情況下,應用程式不需要檢查該期間是否修改了 Blob。

配置數據、查找數據以及應用程式經常使用的其他數據是緩存的良好候選數據。  

有關使用條件標頭的詳細資訊,請參閱[為 Blob 服務操作指定條件標頭](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations)。  

### <a name="uploading-data-in-batches"></a>大量上傳資料

在某些情況下,您可以在本地聚合數據,然後定期將數據上載到批處理中,而不是立即上傳每一段資料。 例如,假設 Web 應用程式保留活動的紀錄檔。 應用程式可以上傳每個活動的詳細資訊,因為它發生在一個表(這需要許多儲存操作),或者它可以保存活動詳細資訊到本地日誌檔,然後定期將所有活動詳細資訊作為分隔檔上傳到 Blob。 如果每個日誌條目的大小為 1 KB,則可以在單個事務中上載數千個條目。 單個事務支援上載大小高達 64 MiB 的 Blob。 應用程式開發人員必須針對用戶端設備或上傳失敗的可能性進行設計。 如果需要在一段時間內下載活動數據,而不是單個活動,則建議在表存儲中使用 Blob 存儲。

## <a name="net-configuration"></a>.NET 組態

如果使用 .NET Framework，本節會列出數個可用來大幅改善效能的快速組態設定。  如果使用其他語言，請查看類似的概念是否適用於您所選擇的語言。  

### <a name="use-net-core"></a>使用 .NET Core

使用 .NET Core 2.1 或更新版本來開發 Azure 儲存體應用程式，以充分利用效能增強功能。 可能的話，建議使用 .NET Core 3.x。

如需 .NET Core 中效能改進的詳細資訊，請參閱下列部落格文章：

- [.NET Core 3.0 中的效能改進](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [.NET Core 2.1 中的效能改進](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>提高預設的連線限制

在 .NET 中,以下代碼將預設連接限制(通常在用戶端環境中為兩個,伺服器環境中為 10)增加到 100。 一般而言，您應將此值大約設為應用程式所使用的執行緒數量。 在開啟任何連線之前設定連線限制。

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

有關其他程式設計語言,請參閱文檔以確定如何設置連接限制。  

有關詳細資訊,請參閱部落格文章[Web 服務:併發連線](https://blogs.msdn.microsoft.com/darrenj/2005/03/07/web-services-concurrent-connections/)。  

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

## <a name="copying-and-moving-blobs"></a>複製並移動的 Blob

Azure 儲存提供了許多解決方案,用於複製和行動儲存帳戶中的Blob、儲存帳戶之間以及本地系統和雲端之間。 本節從其中一些選項對性能的影響的角度介紹其中一些選項。 有關將資料有效地傳輸到 Blob 儲存或從 Blob 儲存傳輸的資訊,請參考[選擇用於資料傳輸的 Azure 解決方案](../common/storage-choose-data-transfer-solution.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

### <a name="blob-copy-apis"></a>Blob 複製 API

要跨儲存帳戶複製 Blob,請使用[「從 URL 放置區塊](/rest/api/storageservices/put-block-from-url)」 。 此操作將資料從任何 URL 源同步複製到塊 Blob 中。 跨存儲`Put Block from URL`帳戶遷移數據時,使用該操作可以顯著減少所需的頻寬。 由於複製操作發生在服務端,因此無需下載和重新上傳數據。

要複製同一存儲帳戶中的資料,請使用[複製 Blob](/rest/api/storageservices/Copy-Blob)操作。 複製同一存儲帳戶中的數據通常快速完成。  

### <a name="use-azcopy"></a>使用 AzCopy

AzCopy 命令列實用程式是一種簡單而高效的選項,用於批量將 blob 傳輸到存儲帳戶、從存儲帳戶中和跨存儲帳戶。 AzCopy 針對此方案進行了優化,並可實現高傳輸率。 AzCopy 版本`Put Block From URL`10 使用該操作跨存儲帳戶複製 Blob 資料。 有關詳細資訊,請參閱使用[AzCopy v10 將資料複製或移動到 Azure 儲存](/azure/storage/common/storage-use-azcopy-v10)。  

### <a name="use-azure-data-box"></a>使用 Azure 資料框

要將大量資料導入 Blob 儲存,請考慮使用 Azure 資料框系列進行離線傳輸。 當您受到時間、網路可用性或成本的限制時,Microsoft 提供的數據盒設備是將數據移動到 Azure 的一個不錯的選擇。 有關詳細資訊,請參閱 Azure[資料盒文件](/azure/databox/)。

## <a name="content-distribution"></a>內容發佈

有時,應用程式需要為位於相同或多個區域的許多使用者(例如,網站主頁中使用的產品演示視頻)提供相同的內容。 在這種情況下,使用內容傳遞網路 (CDN)(如 Azure CDN)在地理位置分發 Blob 內容。 不同於在單一區域內存在，且無法以低延遲方式傳遞內容到其他區域的 Azure 儲存體帳戶，Azure CDN 會使用在全球多個資料中心內的伺服器。 此外，CDN 通常可以支援比單一儲存體帳戶高很多的輸出限制。  

如需 Azure CDN 的詳細資訊，請參閱 [Azure CDN](../../cdn/cdn-overview.md)。

## <a name="use-metadata"></a>使用中繼資料

Blob 服務支援 HEAD 請求,其中可以包括 blob 屬性或中繼資料。 例如,如果應用程式需要照片中的 Exif(可 ex 可掃描圖像格式)數據,則可以檢索照片並提取照片。 為了節省頻寬並提高性能,應用程式可以在應用程式上傳照片時將 Exif 數據存儲在 Blob 的中。 然後,您只能使用 HEAD 請求檢索中資料中的 Exif 資料。 僅檢索元資料而不是 Blob 的全部內容可節省大量頻寬,並減少提取 Exif 資料所需的處理時間。 請記住,每個 Blob 可以存儲 8 個 B 元元。  

## <a name="upload-blobs-quickly"></a>快速上傳 blob

要快速上載 Blob,請先確定是上載一個 blob 還是上載多個 Blob。 使用下列指引，根據您的案例來判斷所要使用的正確方法。  

### <a name="upload-one-large-blob-quickly"></a>快速上傳大型檢視

要快速上載單個大型 Blob,用戶端應用程式可以並行上載其塊或頁面,同時注意單個 Blob 和整個儲存帳戶的可伸縮性目標。 Azure 儲存用戶端庫支援並行上載。 例如,可以使用以下屬性指定 .NET 或 Java 中允許的併發請求數。 其他受支持語言的用戶端庫提供了類似的選項。

- 對於 .NET,設置[Blob 請求選項.並行操作線程計數](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount)屬性。
- 對於 Java/Android,調用[BlobRequestOptions.set 並發請求計數(最終整數併發請求計數)](/java/api/com.microsoft.azure.storage.blob.blobrequestoptions.setconcurrentrequestcount)方法。

### <a name="upload-many-blobs-quickly"></a>快速上傳多個執行

若要快速上傳多個 Blob，請平行上傳 Blob。 並行上傳比一次上傳單個 Blob 時使用並行塊上載更快,因為它將上傳跨存儲服務的多個分區展開。 AzCopy 依預設會執行平行上傳，在此案例中我們建議使用此方法。 有關詳細資訊,請參閱從[AzCopy 開始](../common/storage-use-azcopy-v10.md)。  

## <a name="choose-the-correct-type-of-blob"></a>選擇正確的 blob 類型

Azure 儲存支援塊 Blob、追加 Blob 和頁面 Blob。 在指定使用的案例中，您的 Blob 類型選擇將會影響解決方案的效能和延展性。

當您要高效地上載大量數據時,阻止 Blob 是合適的。 例如,將照片或視頻上載到 Blob 儲存的用戶端應用程式將針對塊 Blob。

追加 Blob 類似於塊 blob,因為它們由塊組成。 修改追加 Blob 時,塊僅添加到 blob 的末尾。 當應用程式需要將數據添加到現有 Blob 時,附加 blob 可用於日誌記錄等方案。

如果應用程式需要對資料執行隨機寫入,則頁面 blob 是合適的。 例如,Azure 虛擬機器磁碟儲存為頁面 blob。 有關詳細資訊,請參閱[瞭解塊 blob、追加 blob 和頁面 blob。](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)  

## <a name="next-steps"></a>後續步驟

- [Blob 儲存體的延展性和效能目標](scalability-targets.md)
- [標準儲存體帳戶的延展性和效能目標](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [狀態和錯誤碼](/rest/api/storageservices/Status-and-Error-Codes2)
