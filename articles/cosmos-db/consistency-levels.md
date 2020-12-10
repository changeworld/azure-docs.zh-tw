---
title: Azure Cosmos DB 中的一致性層級
description: Azure Cosmos DB 具有五個一致性層級，有助於在最終一致性、可用性和延遲的取捨之間取得平衡。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.openlocfilehash: 965e4a8cd704670ec06ae6b927b97c3a8b93030c
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938659"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Azure Cosmos DB 中的一致性層級
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

依賴複寫來達到高可用性、低延遲或兩者的分散式資料庫，必須在讀取一致性、可用性、延遲和輸送量之間進行基本取捨，如 [PACLC 定理](https://en.wikipedia.org/wiki/PACELC_theorem)所定義。 強式一致性模型的線性化能力是資料可程式性的黃金標準。 但因為資料必須跨大型距離進行複寫和認可，所以它可從較高的寫入延遲增加陡價。 因為資料無法在每個區域中複寫和認可，所以在失敗期間，強式一致性可能也會受到降低的可用性 (影響) 。 最終一致性提供更高的可用性和更佳的效能，但因為資料在所有區域之間可能不會完全一致，所以對應用程式更難進行程式設計。

現今市場中最正式推出的分散式 NoSQL 資料庫只提供強式和最終一致性。 Azure Cosmos DB 提供五個定義完善的層級。 從最強到最弱，層級如下：

- *強式*
- *限定過期*
- *工作階段*
- *一致前置詞*
- *最終*

每個層級都提供可用性和效能權衡取捨。 下圖顯示各種不同的一致性層級。

:::image type="content" source="./media/consistency-levels/five-consistency-levels.png" alt-text="以頻譜形式顯示的一致性" border="false" :::

一致性層級與區域無關，而且不論是從何種區域提供讀取和寫入的服務、與您的 Azure Cosmos 帳戶相關聯的區域數目，或是您的帳戶是否設定為單一或多個寫入區域，都能保證所有作業。

## <a name="consistency-levels-and-azure-cosmos-db-apis"></a>一致性層級與 Azure Cosmos DB API

Azure Cosmos DB 提供適用于熱門資料庫的有線通訊協定相容 Api 原生支援。 這些包括 MongoDB、Apache Cassandra、Gremlin 和 Azure 資料表儲存體。 使用 Gremlin API 和資料表 API 時，會使用 Azure Cosmos 帳戶上設定的預設一致性層級。 如需 Cassandra API 或適用于 MongoDB 的 API 和 Azure Cosmos DB 的一致性層級之間的一致性層級對應詳細資訊，請參閱 [Cassandra API 一致性對應](cassandra-consistency.md) 和 [mongodb 一致性對應的 api](mongodb-consistency.md)。

## <a name="scope-of-the-read-consistency"></a>讀取一致性的範圍

讀取一致性適用于邏輯分割區內的單一讀取作業。 讀取作業可由遠端用戶端或預存程序發出。

## <a name="configure-the-default-consistency-level"></a>設定預設一致性層級

您隨時可以在 Azure Cosmos 帳戶上設定預設一致性層級。 您帳戶上設定的預設一致性層級會套用到該帳戶下的所有 Azure Cosmos 資料庫和容器。 對容器或資料庫發出的所有讀取和查詢，預設都將使用指定的一致性層級。 若要深入了解，請參閱如何[設定預設一致性層級](how-to-manage-consistency.md#configure-the-default-consistency-level)。 您也可以覆寫特定要求的預設一致性層級，若要深入瞭解，請參閱如何覆 [寫預設一致性層級](how-to-manage-consistency.md?#override-the-default-consistency-level) 一文。

> [!IMPORTANT]
> 變更預設的一致性層級之後，必須重新建立任何 SDK 實例。 這可以藉由重新開機應用程式來完成。 這可確保 SDK 使用新的預設一致性層級。

## <a name="guarantees-associated-with-consistency-levels"></a>與一致性層級相關的保證

Azure Cosmos DB 可保證100% 的讀取要求符合所選一致性層級的一致性保證。 使用 TLA + 規格語言 Azure Cosmos DB 中五個一致性層級的精確定義，是在 [Azure Cosmos-TLA](https://github.com/Azure/azure-cosmos-tla) github 存放庫中提供。

五個一致性層級的語意說明如下：

- **強** 式一致性：強式一致性提供線性化能力保證。 線性化能力指的是同時提供要求的服務。 保證讀取會傳回最新版本的認可項目。 用戶端將絕不會看到未認可或部分的寫入。 保證使用者一律會讀取最新認可的寫入。

  下圖說明與音樂附注的強式一致性。 將資料寫入「美國西部2」區域之後，當您從其他區域讀取資料時，您會取得最新的值：

  :::image type="content" source="media/consistency-levels/strong-consistency.gif" alt-text="強式一致性層級的圖例":::

- **限定過期**：保證讀取會遵守一致前置詞的保證。 讀取可能會落後寫入最多 *"K"* 個版本 (也就是「更新」 ) 的專案，或「 *T* 」時間間隔（以先達到者為准）。 換句話說，當您選擇限定過期時，可以透過兩種方式來設定「過期」：

- 專案 (*K*) 的版本數目
- 時間間隔 (*T*) 讀取可能會延後寫入

針對單一區域帳戶， *K* 和 *T* 的最小值為10個寫入作業或5秒。 若為多區域帳戶， *K* 和 *T* 的最小值為100000寫入作業或300秒。

限定過期提供「過期時段」之外的全域順序總計。 當用戶端在接受寫入的區域內執行讀取作業時，限定過期一致性所提供的保證會與強式一致性的保證相同。 當過期時間範圍適用于時間或更新（以較接近的方式）時，服務會對新的寫入進行節流，以允許複寫趕上並遵守一致性保證。

在過期時段內，限定過期會提供下列一致性保證：

- 具有單一寫入區域之帳戶的相同區域中用戶端的一致性 = 強式
- 具有單一寫入區域 = 一致前置詞之帳戶的不同區域中用戶端的一致性
- 針對具有多個寫入區域之帳戶寫入至單一區域的用戶端一致性 = 一致前置詞
- 針對具有多個寫入區域之帳戶寫入不同區域的用戶端一致性 = 最終

  限定過期通常是由預期低寫入延遲，但需要總全域訂單保證的全域散發應用程式所選擇。 限定過期適用于具有群組共同作業和共用、股票行情指示器、發佈-訂閱/佇列等的應用程式。下圖說明使用音樂附注的限定過期一致性。 將資料寫入「美國西部2」區域之後，「美國東部2」和「澳大利亞東部」區域會根據設定的最大延隔時間或最大作業數目，讀取寫入的值：

  :::image type="content" source="media/consistency-levels/bounded-staleness-consistency.gif" alt-text="限定過期一致性層級的圖例":::

- **會話**：在單一用戶端會話讀取中，保證可以接受一致前置詞、單純讀取、單純寫入、讀取您的寫入，以及寫入後續讀取的保證。 這會假設單一「寫入器」會話或共用多個寫入器的會話權杖。

執行寫入的會話外部用戶端會看到下列保證：

- 具有單一寫入區域 = 一致前置詞之帳戶的相同區域中用戶端的一致性
- 具有單一寫入區域 = 一致前置詞之帳戶的不同區域中用戶端的一致性
- 針對具有多個寫入區域之帳戶寫入至單一區域的用戶端一致性 = 一致前置詞
- 針對具有多個寫入區域之帳戶寫入多個區域的用戶端一致性 = 最終

  會話一致性是單一區域和全域分散式應用程式最普遍使用的一致性層級。 它提供寫入延遲、可用性及讀取輸送量，相當於最終一致性，但也提供一致的保證，可符合撰寫的應用程式在使用者的內容中運作的需求。 下圖說明與音樂筆記的會話一致性。 「美國西部2寫入器」和「美國西部2讀者」會使用相同的會話 (會話 A) 讓它們同時讀取相同的資料。 雖然「澳大利亞東部」區域使用「會話 B」，但它會在稍後接收資料，但其順序與寫入相同。

  :::image type="content" source="media/consistency-levels/session-consistency.gif" alt-text="會話一致性層級的圖例":::

- **一致前置** 詞：傳回的更新會包含所有更新的部分前置詞，而且沒有間距。 一致的前置詞一致性層級可保證讀取永遠不會看到不是順序的寫入。

如果寫入依循序執行 `A, B, C` ，則用戶端 `A` 會看到、 `A,B` 或 `A,B,C` ，但永遠不會有非順序的排列，例如 `A,C` 或 `B,A,C` 。 「一致前置詞」提供寫入延遲、可用性及讀取輸送量，相當於最終一致性，但也提供符合訂單重要性之案例需求的順序保證。

以下是一致前置詞的一致性保證：

- 具有單一寫入區域 = 一致前置詞之帳戶的相同區域中用戶端的一致性
- 具有單一寫入區域 = 一致前置詞之帳戶的不同區域中用戶端的一致性
- 針對具有多個寫入區域 = 一致前置詞的帳戶，寫入至單一區域的用戶端一致性
- 針對具有多個寫入區域之帳戶的用戶端寫入至多個區域的一致性 = 最終

下圖說明與音樂附注一致的前置詞一致性。 在所有區域中，讀取永遠不會看到沒有順序的寫入：

  :::image type="content" source="media/consistency-levels/consistent-prefix.gif" alt-text="一致前置詞的圖例":::

- **最終**：讀取沒有排序保證。 如果沒有任何進一步的寫入，則複本最終會趨於一致。  
最終一致性是最弱的一致性形式，因為用戶端可能會讀取比之前讀取的值還舊的值。 最終一致性是應用程式不需要任何順序保證的理想選擇。 範例包括收藏數、贊或非執行緒批註的計數。 下圖說明與音樂附注的最終一致性。

  :::image type="content" source="media/consistency-levels/eventual-consistency.gif" alt-text="最終一致性 viIllustration":::

## <a name="consistency-guarantees-in-practice"></a>實際上的一致性保證

在實務上，您通常可能會獲得更強的一致性保證。 讀取作業的一致性保證會對應到您所要求之資料庫狀態的時效性和排序。 讀取一致性會繫結到寫入/更新作業的排序和傳播。  

如果資料庫上沒有寫入作業，則使用「 **最終**」、「 **會話**」或「 **一致前置** 詞」一致性層級的讀取作業可能會產生與具有強式一致性層級的讀取作業相同的結果。

如果您的 Azure Cosmos 帳戶設定了強式一致性以外的一致性層級，您可以藉由查看 *機率限定過期* (PBS) 計量，找出用戶端可能會針對您的工作負載取得強式和一致讀取的可能性。 此計量會在 Azure 入口網站公開，若要深入了解，請參閱[監視機率限定過期 (PBS) 計量](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)。

機率性限定過期會顯示您最終一致性的最終結果。 此計量可讓您深入瞭解您在 Azure Cosmos 帳戶上目前設定的一致性層級時，可以獲得更強的一致性的頻率。 換句話說，您可以看到針對寫入和讀取區域的組合取得強式一致性讀取的機率 (以毫秒來測量)。

## <a name="consistency-levels-and-latency"></a>一致性層級和延遲

一律保證適用於所有一致性層級的讀取延遲都會在第 99 個百分位數小於 10 毫秒。 平均讀取延遲（在第50個百分位數）通常是4毫秒或更少。

所有一致性層級的寫入延遲一律保證在第99個百分位數小於10毫秒。 平均寫入延遲 (在第 50 個百分位數) 通常是 5 毫秒或更少。 橫跨數個區域且設定了強式一致性的 Azure Cosmos 帳戶是此保證的例外狀況。

### <a name="write-latency-and-strong-consistency"></a>寫入延遲和強式一致性

針對使用多個區域設定強式一致性的 Azure Cosmos 帳戶，寫入延遲等於兩倍的往返時間 (RTT 在兩個最遠區域之間) ，再加上10毫秒的第99個百分位數。 區域之間的高網路 RTT 會轉譯成 Cosmos DB 要求的較高延遲，因為強式一致性只會在確定已將其認可至帳戶內的所有區域之後，才完成作業。

確切的 RTT 延遲是一個光速距離的函式和 Azure 網路拓撲。 Azure 網路不會針對任何兩個 Azure 區域之間的 RTT 提供任何延遲 Sla，不過它會發行 [Azure 網路來回延遲統計資料](../networking/azure-network-latency.md)。 您 Azure Cosmos 帳戶的複寫延遲會顯示在 Azure 入口網站中。 您可以使用 Azure 入口網站 (移至 [計量] 分頁，選取 [一致性] 索引標籤) 來監視與您的 Azure Cosmos 帳戶相關聯之不同區域之間的複寫延遲。

> [!IMPORTANT]
> 因為高度寫入延遲，所以預設會封鎖區域超過5000英里 (8000 公里) 的帳戶的強式一致性。 若要啟用這項功能，請聯絡支援人員。

## <a name="consistency-levels-and-throughput"></a>一致性層級和輸送量

- 針對強式和限定過期，會對四個複本集中的兩個複本進行讀取， (少數仲裁) 來提供一致性保證。 會話、一致前置詞和最終進行單一複本讀取。 結果是，針對相同數量的要求單位，強式和限定過期的讀取輸送量是其他一致性層級的一半。

- 針對指定類型的寫入作業 (例如，插入、取代、更新插入和刪除)，對於要求單位的寫入輸送量會與所有一致性層級完全相同。

|**一致性層級**|**仲裁讀取**|**仲裁寫入**|
|--|--|--|
|**強式**|本機少數|全球多數|
|**限定過期**|本機少數|本機多數|
|**工作階段**|使用會話權杖的單一複本 () |本機多數|
|**一致前置詞**|單一複本|本機多數|
|**最終**|單一複本|本機多數|

> [!NOTE]
> 針對本機少數讀取進行讀取的 RU/秒成本是較弱一致性層級的兩倍，因為讀取是從兩個複本進行讀取，以提供強式和限定過期的一致性保證。

## <a name="consistency-levels-and-data-durability"></a><a id="rto"></a>一致性層級和資料持久性

在全域分散式資料庫環境內，當發生全區域中斷情況時，一致性層級與資料持久性之間具有直接關聯性。 當您開發商務持續性計劃時，您必須了解應用程式在干擾性事件之後完全復原所需的最大可接受時間。 應用程式完全復原所需的時間，也稱為復原 **時間目標** (**RTO**) 。 您也必須了解在干擾性事件之後復原時，應用程式可忍受遺失的最近資料更新最大期間。 您可能會遺失的更新時間期間稱為 **復原點目標** (**RPO**) 。

下表定義在發生全區域中斷時，一致性模型與資料持久性之間的關聯性。 請務必注意，在分散式系統中，即使有強式一致性，也無法讓分散式資料庫的 RPO 和 RTO 為零，因為 [CAP 定理](https://en.wikipedia.org/wiki/CAP_theorem)。

|**區域 (s)**|**複寫模式**|**一致性層級**|**復原點目標 (RPO)**|**復原時間目標 (RTO)**|
|---------|---------|---------|---------|---------|
|1|單一或多個寫入區域|任何一致性層級|< 240 分鐘|< 1 週|
|> 1|單一寫入區域|工作階段、開頭一致、最終|< 15 分鐘|< 15 分鐘|
|> 1|單一寫入區域|限定過期|*K*  & *T*|< 15 分鐘|
|> 1|單一寫入區域|強式|0|< 15 分鐘|
|> 1|多個寫入區域|工作階段、開頭一致、最終|< 15 分鐘|0|
|> 1|多個寫入區域|限定過期|*K*  & *T*|0|

*K* = *"k"* 版本的數目 (亦即，更新專案) 。

*T* = 自上次更新後的時間間隔 *"T"* 。

針對單一區域帳戶， *K* 和 *T* 的最小值為10個寫入作業或5秒。 若為多區域帳戶， *K* 和 *T* 的最小值為100000寫入作業或300秒。 這會定義使用限定過期時資料的最小 RPO。

## <a name="strong-consistency-and-multiple-write-regions"></a>強式一致性和多個寫入區域

使用多個寫入區域設定的 Cosmos 帳戶無法設定為強式一致性，因為分散式系統無法提供零的 RPO 和 RTO 為零。 此外，對多個寫入區域使用強式一致性沒有任何寫入延遲的優點，因為必須將寫入至任何區域，才能將其寫入至帳戶內所有設定的區域。 這會產生與單一寫入區域帳戶相同的寫入延遲。

## <a name="additional-reading"></a>延伸閱讀

若要深入了解一致性的概念，請閱讀下列文章：

- [Azure Cosmos DB 中五個一致性層級的高階 TLA + 規格](https://github.com/Azure/azure-cosmos-tla)
- [Doug Terry 透過棒球 (video) 來說明複寫的資料一致性](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Doug Terry 透過棒球 (白皮書) 說明複寫的資料一致性](https://www.microsoft.com/research/publication/replicated-data-consistency-explained-through-baseball/)
- [弱式一致複寫資料的工作階段保證](https://dl.acm.org/citation.cfm?id=383631)
- [現代分散式資料庫系統設計的一致性取捨：CAP 是整個過程中的唯一解決方案](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [實際部分仲裁的隨機限定過期 (PBS) (英文)](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [再論最終一致](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure Cosmos DB 中的一致性，請閱讀下列文章：

- [設定預設一致性層級](how-to-manage-consistency.md#configure-the-default-consistency-level)
- [覆寫預設一致性層級](how-to-manage-consistency.md#override-the-default-consistency-level)
- [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)
