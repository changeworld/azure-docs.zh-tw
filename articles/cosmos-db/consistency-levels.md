---
title: Azure Cosmos DB 中的一致性層級
description: Azure Cosmos DB 具有五個一致性層級，有助於在最終一致性、可用性和延遲的取捨之間取得平衡。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: e5966f142ece32f148c56edb5b0ef5dfd88603aa
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380070"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Azure Cosmos DB 中的一致性層級

依賴複寫來取到高可用性或低延遲性或兩者的分散式資料庫，可在讀取一致性與可用性、延遲性及輸送量之間進行基本權衡取捨。 大多數商業上可用的分散式資料庫要求開發人員在兩種極端一致性模型之間進行選擇:*強*一致性和*最終*一致性。 強一致性模型的線性化是數據可程式設計性的黃金標準。 但它增加了更高的寫入延遲(處於穩定狀態)和可用性降低(在故障期間)的價格。 另一方面,最終的一致性提供了更高的可用性和更好的性能,但很難對應用程序進行程式設計。

Azure Cosmos DB 可提供資料一致性的選擇頻譜，而不是兩個極端的選擇。 開發人員可以使用這些選項對高可用性和性能做出精確的選擇和精細權衡。

使用 Azure Cosmos DB,開發人員可以從一致性頻譜上的五個定義良好的一致性級別中進行選擇。 這些級別包括*強*、*有界過時*、*會話*、*一致首碼*和*最終*一致性。 級別定義良好且直觀,可用於特定的實際場景。 每個級別提供[可用性和性能權衡](consistency-levels-tradeoffs.md),並由 SL 支援。 下圖顯示了不同的一致性級別作為頻譜。

![以頻譜形式顯示的一致性](./media/consistency-levels/five-consistency-levels.png)

一致性級別與區域無關,並且保證所有操作,無論提供讀取和寫入的區域、與 Azure Cosmos 帳戶關聯的區域數,還是您的帳戶配置了單個或多個寫入區域。

## <a name="scope-of-the-read-consistency"></a>讀取一致性的範圍

讀取一致性適用於邏輯分區中範圍的單個讀取操作。 讀取作業可由遠端用戶端或預存程序發出。

## <a name="configure-the-default-consistency-level"></a>設定預設一致性層級

您隨時可以在 Azure Cosmos 帳戶上設定預設一致性層級。 在帳戶上配置的預設一致性級別適用於該帳戶下的所有 Azure Cosmos 資料庫和容器。 對容器或資料庫發出的所有讀取和查詢，預設都將使用指定的一致性層級。 若要深入了解，請參閱如何[設定預設一致性層級](how-to-manage-consistency.md#configure-the-default-consistency-level)。

## <a name="guarantees-associated-with-consistency-levels"></a>與一致性層級相關的保證

Azure Cosmos DB 會提供全方位 SLA，保證 100% 的讀取要求都將符合任何所選一致性層級的一致性保證。 如果滿足與一致性層級相關聯的所有一致性保證，即表示讀取要求符合一致性 SLA。 使用 TLA+ 規範語言在[Azure-cosmos-tla](https://github.com/Azure/azure-cosmos-tla) GitHub 儲存庫中提供了 Azure Cosmos DB 中五個一致性級別的精確定義。

五個一致性層級的語意說明如下：

- **強**:強一致性提供了線性可數保證。 線性可線性性是指同時提供請求。 保證讀取會傳回最新版本的認可項目。 用戶端將絕不會看到未認可或部分的寫入。 保證使用者一律會讀取最新認可的寫入。

  下圖說明瞭與音符的強烈一致性。 將資料寫入「美國西部 2」區域後,當您從其他區域讀取資料時,您將獲得最新值:

  ![video](media/consistency-levels/strong-consistency.gif)

- **有邊界的過時:** 讀取保證遵守一致首碼保證。 讀取可能滯後於專案(即"更新")或 *"T"* 時間間隔的 *"K"* 版本(即"更新")的寫入。 換句話說,當您選擇有限制的過時時,「過時」可以通過兩種方式進行配置:

- 項目的版本數(*K)*
- 讀取可能滯後於寫入的時間間隔 (*T*)

有界陳舊提供了"陳舊視窗"之外的整體全球秩序。 當用戶端在接受寫入的區域內執行讀取操作時,由有限過時一致性提供的保證與強一致性提供的保證相同。

在陳舊視窗中,邊界過時提供以下一致性保證:

- 單個主帳號同一區域中的用戶端的一致性 = 強
- 單主帳戶不同區域中的用戶端的一致性 = 一致前置前置
- 用戶端寫入多個主科目的單個區域的一致性 = 一致前綴
- 多主帳戶寫入不同區域的用戶端的一致性 = 最終

  全球分散式應用程式經常選擇有界過時,這些應用程式期望低寫入延遲,但需要全域訂單總保證。 邊界過時非常適合具有群組協作和共用、股票代碼、發佈-訂閱/排隊等功能的應用程式。下圖說明瞭與音符的有限過時一致性。 將資料寫入「美國西部 2」區域後,「東美國2」和「澳大利亞東部」區域根據配置的最大延遲時間或最大操作讀取寫入值:

  ![video](media/consistency-levels/bounded-staleness-consistency.gif)

- **會話**:在單個用戶端會話中,保證讀取遵循一致前綴、單調讀取、單調寫入、讀寫和寫入-跟隨讀取保證。 這將假定單個「寫入器」作業階段或共用多個寫入器的作業階段權杖。

執行寫入的作業階段外部的用戶端將看到以下保證:

- 單個主帳戶同一區域中的用戶端的一致性 = 一致前置前置
- 單主帳戶不同區域中的用戶端的一致性 = 一致前置前置
- 用戶端寫入多個主科目的單個區域的一致性 = 一致前綴
- 用戶端為多主帳戶寫入多個區域的一致性 = 最終

  會話一致性是單個區域和全球分散式應用程序廣泛使用的一致性級別。 它提供了與最終一致性相當的寫入延遲、可用性和讀取輸送量,但也提供了一致性保證,以滿足編寫的應用程式在使用者上下文中運行的需求。 下圖說明了會話與音符的一致性。 "西美國2個編寫器"和"西美國2閱讀器"使用相同的會話(會話A),所以他們同時讀取相同的數據。 "澳大利亞東部"區域使用"會話 B",因此,它稍後接收數據,但順序與寫入相同。

  ![video](media/consistency-levels/session-consistency.gif)

- **一致前置字**:傳回的更新包含所有更新的一些前置碼,沒有間隙。 一致的首碼一致性級別保證讀取永遠不會看到順序外寫入。

如果寫入以 `A, B, C` 順序執行，則用戶端會看到 `A`、`A,B` 或 `A,B,C`，但是永遠不會看到沒有順序的情形，像是 `A,C` 或 `B,A,C`。 一致前置碼提供與最終一致性類似的寫入延遲、可用性和讀取輸送量,但也提供滿足訂單很重要的方案所需的順序保證。 

以下是一致首碼的一致性保證:

- 單個主帳戶同一區域中的用戶端的一致性 = 一致前置前置
- 單主帳戶不同區域中的用戶端的一致性 = 一致前置前置
- 用戶端寫入多個主科目的單個區域的一致性 = 一致前綴
- 用戶端為多主帳戶寫入多個區域的一致性 = 最終

下圖說明瞭一致性首碼與音符的一致性。 在所有區域中,讀取永遠不會看到順序順序寫入:

  ![video](media/consistency-levels/consistent-prefix.gif)

- **最終**:沒有讀取的訂購保證。 如果不再有任何寫入，複本最終將會聚合。  
最終的一致性是最弱的一致性形式,因為用戶端可能會讀取比之前讀取的值更舊的值。 如果應用程式不需要任何訂購保證,則最終一致性是理想的。 示例包括轉推、讚或非線程註釋的計數。 下圖說明瞭與音符的最終一致性。

  ![video](media/consistency-levels/eventual-consistency.gif)

## <a name="additional-reading"></a>其他閱讀資料

若要深入了解一致性的概念，請閱讀下列文章：

- [Azure Cosmos DB 中五個一致性層級的高階 TLA + 規格](https://github.com/Azure/azure-cosmos-tla)
- [複製資料一致性解釋通過棒球(視頻)由道格特裡](https://www.youtube.com/watch?v=gluIh8zd26I)
- [複製的數據一致性解釋通過棒球(白皮書)由道格特裡](https://www.microsoft.com/research/publication/replicated-data-consistency-explained-through-baseball/)
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
