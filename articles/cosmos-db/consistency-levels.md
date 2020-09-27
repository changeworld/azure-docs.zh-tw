---
title: Azure Cosmos DB 中的一致性層級
description: Azure Cosmos DB 具有五個一致性層級，有助於在最終一致性、可用性和延遲的取捨之間取得平衡。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 8f482c4fe6817c75079ceb98e981c846c395ad13
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2020
ms.locfileid: "91396020"
---
# <a name="what-are-consistency-levels-in-azure-cosmos-db"></a>Azure Cosmos DB 中的一致性層級為何？

依賴複寫來取到高可用性或低延遲性或兩者的分散式資料庫，可在讀取一致性與可用性、延遲性及輸送量之間進行基本權衡取捨。 大部分的正式發行資料庫都要求開發人員在兩個極端的一致性模型之間做選擇： *強* 式一致性和 *最終* 一致性。 強式一致性模型的線性化能力是資料可程式性的黃金標準。 但是，它增加了較高寫入延遲的價格 (穩定狀態) 並降低) 失敗期間的可用性 (。 相反地，最終一致性提供更高的可用性和更佳的效能，但會使應用程式難以進行程式設計。

Azure Cosmos DB 可提供資料一致性的選擇頻譜，而不是兩個極端的選擇。 開發人員可以使用這些選項，在高可用性和效能方面做出精確的選擇和細微的取捨。

有了 Azure Cosmos DB，開發人員可以在一致性頻譜上選擇五個定義完善的一致性層級。 這些層級包括 *強*式、 *限定過期*、 *會話*、 *一致前置*詞和 *最終* 一致性。 這些層級是妥善定義且直覺的，可用於特定的真實世界案例。 每個層級都提供 [可用性和效能權衡取捨](consistency-levels-tradeoffs.md) ，並受到 sla 的支援。 下圖顯示各種不同的一致性層級。

:::image type="content" source="./media/consistency-levels/five-consistency-levels.png" alt-text="以頻譜形式顯示的一致性" border="false" :::

一致性層級與區域無關，而且不論是從何種區域提供讀取和寫入的服務、與您的 Azure Cosmos 帳戶相關聯的區域數目，或是您的帳戶是否設定為單一或多個寫入區域，都能保證所有作業。

## <a name="scope-of-the-read-consistency"></a>讀取一致性的範圍

讀取一致性適用于邏輯分割區內的單一讀取作業。 讀取作業可由遠端用戶端或預存程序發出。

## <a name="configure-the-default-consistency-level"></a>設定預設一致性層級

您隨時可以在 Azure Cosmos 帳戶上設定預設一致性層級。 您帳戶上設定的預設一致性層級會套用到該帳戶下的所有 Azure Cosmos 資料庫和容器。 對容器或資料庫發出的所有讀取和查詢，預設都將使用指定的一致性層級。 若要深入了解，請參閱如何[設定預設一致性層級](how-to-manage-consistency.md#configure-the-default-consistency-level)。 您也可以覆寫特定要求的預設一致性層級，若要深入瞭解，請參閱如何覆 [寫預設一致性層級](how-to-manage-consistency.md?#override-the-default-consistency-level) 一文。

## <a name="guarantees-associated-with-consistency-levels"></a>與一致性層級相關的保證

Azure Cosmos DB 會提供全方位 SLA，保證 100% 的讀取要求都將符合任何所選一致性層級的一致性保證。 如果滿足與一致性層級相關聯的所有一致性保證，即表示讀取要求符合一致性 SLA。 使用 TLA + 規格語言 Azure Cosmos DB 中五個一致性層級的精確定義，是在 [Azure Cosmos-TLA](https://github.com/Azure/azure-cosmos-tla) github 存放庫中提供。

五個一致性層級的語意說明如下：

- **強**式一致性：強式一致性提供線性化能力保證。 線性化能力指的是同時提供要求的服務。 保證讀取會傳回最新版本的認可項目。 用戶端將絕不會看到未認可或部分的寫入。 保證使用者一律會讀取最新認可的寫入。

  下圖說明與音樂附注的強式一致性。 將資料寫入「美國西部2」區域之後，當您從其他區域讀取資料時，您會取得最新的值：

  :::image type="content" source="media/consistency-levels/strong-consistency.gif" alt-text="影片":::

- **限定過期**：保證讀取會遵守一致前置詞的保證。 讀取可能會落後寫入最多 *"K"* 個版本 (也就是「更新」 ) 的專案，或「 *T* 」時間間隔（以先達到者為准）。 換句話說，當您選擇限定過期時，可以透過兩種方式來設定「過期」：

- 專案 (*K*) 的版本數目
- 時間間隔 (*T*) 讀取可能落後寫入的時間

限定過期提供「過期時段」之外的全域順序總計。 當用戶端在接受寫入的區域內執行讀取作業時，限定過期一致性所提供的保證會與強式一致性的保證相同。

在過期時段內，限定過期會提供下列一致性保證：

- 單一主機帳戶相同區域中用戶端的一致性 = 強式
- 單一主帳戶之不同區域中用戶端的一致性 = 一致的前置詞
- 針對多宿主帳戶寫入單一區域的用戶端一致性 = 一致前置詞
- 針對多宿主帳戶寫入不同區域的用戶端的一致性 = 最終

  限定過期通常是由預期低寫入延遲，但需要總全域訂單保證的全域散發應用程式所選擇。 限定過期適用于具有群組共同作業和共用、股票行情指示器、發佈-訂閱/佇列等的應用程式。下圖說明使用音樂附注的限定過期一致性。 將資料寫入「美國西部2」區域之後，「美國東部2」和「澳大利亞東部」區域會根據設定的最大延隔時間或最大作業數目，讀取寫入的值：

  :::image type="content" source="media/consistency-levels/bounded-staleness-consistency.gif" alt-text="影片":::

- **會話**：在單一用戶端會話讀取中，保證可以接受一致前置詞、單純讀取、單純寫入、讀取您的寫入，以及寫入後續讀取的保證。 這會假設單一「寫入器」會話或共用多個寫入器的會話權杖。

執行寫入的會話外部用戶端會看到下列保證：

- 單一主帳戶之相同區域中的用戶端一致性 = 一致前置詞
- 單一主帳戶之不同區域中用戶端的一致性 = 一致的前置詞
- 針對多宿主帳戶寫入單一區域的用戶端一致性 = 一致前置詞
- 針對多宿主帳戶寫入多個區域的用戶端的一致性 = 最終

  會話一致性是單一區域和全域分散式應用程式最普遍使用的一致性層級。 它提供寫入延遲、可用性及讀取輸送量，相當於最終一致性，但也提供一致的保證，可符合撰寫的應用程式在使用者的內容中運作的需求。 下圖說明與音樂筆記的會話一致性。 「美國西部2寫入器」和「美國西部2讀者」會使用相同的會話 (會話 A) 讓它們同時讀取相同的資料。 雖然「澳大利亞東部」區域使用「會話 B」，但它會在稍後接收資料，但其順序與寫入相同。

  :::image type="content" source="media/consistency-levels/session-consistency.gif" alt-text="影片":::

- **一致前置**詞：傳回的更新會包含所有更新的部分前置詞，而且沒有間距。 一致的前置詞一致性層級可保證讀取永遠不會看到不是順序的寫入。

如果寫入依循序執行 `A, B, C` ，則用戶端 `A` 會看到、 `A,B` 或 `A,B,C` ，但永遠不會有非順序的排列，例如 `A,C` 或 `B,A,C` 。 「一致前置詞」提供寫入延遲、可用性及讀取輸送量，相當於最終一致性，但也提供符合訂單重要性之案例需求的順序保證。 

以下是一致前置詞的一致性保證：

- 單一主帳戶之相同區域中的用戶端一致性 = 一致前置詞
- 單一主帳戶之不同區域中用戶端的一致性 = 一致的前置詞
- 針對多宿主帳戶寫入單一區域的用戶端一致性 = 一致前置詞
- 針對多宿主帳戶寫入多個區域的用戶端的一致性 = 最終

下圖說明與音樂附注一致的前置詞一致性。 在所有區域中，讀取永遠不會看到沒有順序的寫入：

  :::image type="content" source="media/consistency-levels/consistent-prefix.gif" alt-text="影片":::

- **最終**：讀取沒有排序保證。 如果沒有任何進一步的寫入，則複本最終會趨於一致。  
最終一致性是最弱的一致性形式，因為用戶端可能會讀取比之前讀取的值還舊的值。 最終一致性是應用程式不需要任何順序保證的理想選擇。 範例包括收藏數、贊或非執行緒批註的計數。 下圖說明與音樂附注的最終一致性。

  :::image type="content" source="media/consistency-levels/eventual-consistency.gif" alt-text="影片":::

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

- [為應用程式選擇正確的一致性層級](consistency-levels-choosing.md)
- [Azure Cosmos DB API 間的一致性層級](consistency-levels-across-apis.md)
- [各種一致性層級的可用性和效能權衡取捨](consistency-levels-tradeoffs.md)
- [設定預設一致性層級](how-to-manage-consistency.md#configure-the-default-consistency-level)
- [覆寫預設一致性層級](how-to-manage-consistency.md#override-the-default-consistency-level)
