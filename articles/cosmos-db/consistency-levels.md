---
title: Azure Cosmos DB 中的一致性層級
description: Azure Cosmos DB 具有五個一致性層級，有助於在最終一致性、可用性和延遲的取捨之間取得平衡。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 5ba3fc70a2ccfbe342e222dbb475658629ec60a4
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85851705"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Azure Cosmos DB 中的一致性層級

依賴複寫來取到高可用性或低延遲性或兩者的分散式資料庫，可在讀取一致性與可用性、延遲性及輸送量之間進行基本權衡取捨。 大部分的商用分散式資料庫會要求開發人員在兩個極端的一致性模型之間進行選擇：*強*式一致性和*最終*一致性。 強式一致性模型的線性化能力是資料可程式性的黃金標準。 但它增加了較高寫入延遲（穩定狀態）和降低可用性（失敗時）的價格。 相反地，最終一致性提供更高的可用性和更佳的效能，但讓應用程式變得很困難。

Azure Cosmos DB 可提供資料一致性的選擇頻譜，而不是兩個極端的選擇。 開發人員可以使用這些選項，針對高可用性和效能做出精確的選擇和細微的取捨。

有了 Azure Cosmos DB，開發人員可以在一致性頻譜上選擇五個定義完善的一致性層級。 這些層級包括*強*式、*限定過期*、*會話*、*一致前置*詞和*最終*一致性。 這些層級是妥善定義且直覺化的，可用於特定的真實案例。 每個層級都提供[可用性和效能取捨](consistency-levels-tradeoffs.md)，並受到 sla 的支援。 下圖顯示不同的一致性層級做為頻譜。

:::image type="content" source="./media/consistency-levels/five-consistency-levels.png" alt-text="以頻譜形式顯示的一致性" border="false" :::

一致性層級與區域無關，不論是從何種區域提供讀取和寫入、與您的 Azure Cosmos 帳戶相關聯的區域數目，或是您的帳戶設定為單一或多個寫入區域，皆可保證所有作業。

## <a name="scope-of-the-read-consistency"></a>讀取一致性的範圍

「讀取一致性」適用于邏輯分割區內的單一讀取作業。 讀取作業可由遠端用戶端或預存程序發出。

## <a name="configure-the-default-consistency-level"></a>設定預設一致性層級

您隨時可以在 Azure Cosmos 帳戶上設定預設一致性層級。 您帳戶上設定的預設一致性層級會套用到該帳戶下的所有 Azure Cosmos 資料庫和容器。 對容器或資料庫發出的所有讀取和查詢，預設都將使用指定的一致性層級。 若要深入了解，請參閱如何[設定預設一致性層級](how-to-manage-consistency.md#configure-the-default-consistency-level)。 您也可以覆寫特定要求的預設一致性層級，若要深入瞭解，請參閱如何覆[寫預設一致性層級](how-to-manage-consistency.md?#override-the-default-consistency-level)一文。

## <a name="guarantees-associated-with-consistency-levels"></a>與一致性層級相關的保證

Azure Cosmos DB 會提供全方位 SLA，保證 100% 的讀取要求都將符合任何所選一致性層級的一致性保證。 如果滿足與一致性層級相關聯的所有一致性保證，即表示讀取要求符合一致性 SLA。 [Cosmos-TLA](https://github.com/Azure/azure-cosmos-tla) GitHub 存放庫中提供了使用 TLA + 規格語言 Azure Cosmos DB 中五個一致性層級的精確定義。

五個一致性層級的語意說明如下：

- **強**式：強式一致性提供線性化能力保證。 線性化能力是指同時提供要求服務。 保證讀取會傳回最新版本的認可項目。 用戶端將絕不會看到未認可或部分的寫入。 保證使用者一律會讀取最新認可的寫入。

  下圖說明具有「音樂筆記」的強式一致性。 將資料寫入「美國西部2」區域之後，當您從其他區域讀取資料時，您會取得最新的值：

  :::image type="content" source="media/consistency-levels/strong-consistency.gif" alt-text="影片":::

- **限定過期**：保證讀取會接受一致前置詞保證。 讀取可能會落後寫入最多「 *K* 」個專案版本（也就是「更新」）或「 *T* 」時間間隔（以先達到者為准）。 換句話說，當您選擇限定過期時，可以透過兩種方式來設定「過期」：

- 專案的版本數（*K*）
- 讀取可能落後寫入的時間間隔（*T*）

限定過期提供「過期時段」以外的整體全域順序。 當用戶端在接受寫入的區域內執行讀取作業時，限定過期一致性所提供的保證會與強式一致性的保證相同。

在過期時間範圍內，限定過期會提供下列一致性保證：

- 單一主帳戶的相同區域中的用戶端一致性 = 強式
- 單一主帳戶的不同區域中的用戶端一致性 = 一致的前置詞
- 針對多宿主帳戶的單一區域寫入用戶端的一致性 = 一致的前置詞
- 針對多宿主帳戶寫入不同區域的用戶端一致性 = 最終

  限定過期通常是由全域散發的應用程式選擇，其預期會有低寫入延遲，但需要整體全域順序保證。 限定過期適用于具有群組共同作業和共用、股票行情指示器、發佈-訂閱/佇列等的應用程式。下圖說明具有音樂便箋的限定過期一致性。 將資料寫入「美國西部2」區域之後，「美國東部2」和「澳大利亞東部」區域會根據設定的最大延隔時間或最大作業數，讀取寫入的值：

  :::image type="content" source="media/consistency-levels/bounded-staleness-consistency.gif" alt-text="影片":::

- **會話**：在單一用戶端會話讀取中，保證會接受一致前置詞、單純讀取、單純寫入、讀取您的寫入，以及寫入後接讀取保證。 這會假設單一「寫入器」會話，或共用多個寫入器的會話權杖。

執行寫入的會話外的用戶端會看到下列保證：

- 單一主帳戶的相同區域中的用戶端一致性 = 一致的前置詞
- 單一主帳戶的不同區域中的用戶端一致性 = 一致的前置詞
- 針對多宿主帳戶的單一區域寫入用戶端的一致性 = 一致的前置詞
- 針對多宿主帳戶寫入多個區域的用戶端一致性 = 最終

  會話一致性是單一區域和全域散發應用程式最普遍使用的一致性層級。 它提供相當於最終一致性的寫入延遲、可用性及讀取輸送量，但也提供一致性保證，以符合撰寫要在使用者內容中操作之應用程式的需求。 下圖說明與「音樂筆記」的會話一致性。 「美國西部2寫入器」和「美國西部2讀取器」使用相同的會話（會話 A），因此兩者都會同時讀取相同的資料。 「澳大利亞東部」區域會使用「會話 B」，因此，它會在稍後接收資料，但與寫入的順序相同。

  :::image type="content" source="media/consistency-levels/session-consistency.gif" alt-text="影片":::

- **一致前置**詞：傳回的更新包含所有更新的部分前置詞，沒有間距。 一致前置詞一致性層級可確保讀取永遠不會看到不按照順序的寫入。

如果寫入是按照循序執行 `A, B, C` ，則用戶端 `A` 會看到、 `A,B` 或 `A,B,C` ，但是永遠不會依序排列（例如或） `A,C` `B,A,C` 。 一致前置詞提供寫入延遲、可用性及讀取輸送量，相當於最終一致性，但也提供符合訂單重要性之案例需求的順序保證。 

以下是一致前置詞的一致性保證：

- 單一主帳戶的相同區域中的用戶端一致性 = 一致的前置詞
- 單一主帳戶的不同區域中的用戶端一致性 = 一致的前置詞
- 針對多宿主帳戶的單一區域寫入用戶端的一致性 = 一致的前置詞
- 針對多宿主帳戶寫入多個區域的用戶端一致性 = 最終

下圖說明與音樂便箋一致的前置詞一致性。 在所有區域中，讀取永遠不會看到不按照順序的寫入：

  :::image type="content" source="media/consistency-levels/consistent-prefix.gif" alt-text="影片":::

- **最終**：讀取沒有排序保證。 如果不再有任何寫入，複本最終將會聚合。  
最終一致性是最弱的一致性形式，因為用戶端可能會讀取早于之前讀取的值。 最終一致性是應用程式不需要任何排序保證的理想選擇。 範例包括轉、贊或非執行緒批註的計數。 下圖說明與音樂便箋的最終一致性。

  :::image type="content" source="media/consistency-levels/eventual-consistency.gif" alt-text="影片":::

## <a name="additional-reading"></a>其他閱讀資料

若要深入了解一致性的概念，請閱讀下列文章：

- [Azure Cosmos DB 中五個一致性層級的高階 TLA + 規格](https://github.com/Azure/azure-cosmos-tla)
- [Doug Terry 透過棒球（影片）說明複寫的資料一致性](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Doug Terry 透過棒球（白皮書）說明複寫的資料一致性](https://www.microsoft.com/research/publication/replicated-data-consistency-explained-through-baseball/)
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
