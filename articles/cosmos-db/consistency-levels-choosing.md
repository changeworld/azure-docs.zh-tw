---
title: 為 Azure Cosmos DB 應用選擇正確的一致性級別
description: 在 Azure Cosmos DB 中為應用程式選擇正確的一致性層級。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: cea157e272a2bf464141e592b8e742f314a83233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441910"
---
# <a name="choose-the-right-consistency-level"></a>選擇正確的一致性層級 

依賴複寫來取到高可用性或低延遲性或兩者的分散式資料庫，可在讀取一致性與可用性、延遲性及輸送量之間進行基本權衡取捨。 大多數商業上可用的分散式資料庫要求開發人員在兩種極端一致性模型之間進行選擇：*強*一致性和*最終*一致性。 Azure Cosmos DB 允許開發人員在五個定義良好的一致性模型中進行選擇：*強*、*邊界過時*、*會話*、*一致首碼*和*最終*。 這些一致性模型中的每一個都是完善定義且直覺化的模型，可用於特定的真實案例。 五種一致性模型中的每一個都提供精確的[可用性和性能權衡](consistency-levels-tradeoffs.md)，並輔以全面的 SL。 下列簡單考量將可協助您在許多常見案例中做出正確的選擇。

## <a name="sql-api-and-table-api"></a>SQL API 和資料表 API

如果使用 SQL API 或表 API 構建應用程式，請考慮以下幾點：

- 對於許多真實案例，工作階段一致性是最佳的且為建議選項。 有關詳細資訊，請參閱[如何管理應用程式的會話權杖](how-to-manage-consistency.md#utilize-session-tokens)。

- 如果您的應用程式需要強式一致性，建議您使用限定過期一致性層級。

- 如果您需要的一致性保證比起用於寫入的工作階段一致性和個位數毫秒延遲所提供的更嚴格，則建議您使用限定過期一致性層級。  

- 如果您的應用程式需要最終一致性，建議您使用一致前置詞一致性層級。

- 如果您需要的一致性保證不如工作階段一致性所提供的嚴格，建議您使用一致前置詞一致性層級。

- 如果您需要最高可用性和最低延遲，請使用最終的一致性級別。

- 如果您需要更高的資料持續性，但不想犧牲效能，您可以在應用程式層建立自訂的一致性層級。 如需詳細資訊，請參閱[如何在應用程式中實作自訂同步處理](how-to-custom-synchronization.md)。

## <a name="cassandra-mongodb-and-gremlin-apis"></a>卡珊多拉、蒙戈DB和格雷姆林 API

- 如需 Apache Cassandra 中所提供的「讀取一致性層級」與 Cosmos DB 一致性層級之間對應的詳細資訊，請參閱[一致性層級和 Cosmos DB API](consistency-levels-across-apis.md#cassandra-mapping)。

- 如需 MongoDB 的「讀取考量」與 Azure Cosmos DB 一致性層級之間對應的詳細資訊，請參閱[一致性層級和 Cosmos DB API](consistency-levels-across-apis.md#mongo-mapping)。

## <a name="consistency-guarantees-in-practice"></a>實際上的一致性保證

在實踐中，您通常可能會獲得更強的一致性保證。 讀取作業的一致性保證會對應到您所要求之資料庫狀態的時效性和排序。 讀取一致性會繫結到寫入/更新作業的排序和傳播。  

* 將一致性層級設定為**限定過期**時，Cosmos DB 會利用過期時段所限定的延隔時間，來保證用戶端一律會讀取前一個寫入的值。

* 將一致性層級設定為**強式**時，過期時段等於零，並保證用戶端會讀取最新認可的寫入作業值。

* 針對其餘的三個一致性層級，過期時段多半取決於您的工作負載。 例如，如果資料庫上沒有任何寫入作業，則具有**最終**、**工作階段**或**一致前置詞**一致性層級的讀取作業可能會產生與具有強式一致性層級的讀取作業相同的結果。

如果您的 Azure Cosmos 帳戶配置了強一致性級別以外的一致性級別，則可以通過查看*概率限制過時 （PBS）* 指標，找出用戶端獲得工作負載強大且一致的讀取的可能性。 此計量會在 Azure 入口網站公開，若要深入了解，請參閱[監視機率限定過期 (PBS) 計量](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)。

機率性限定過期會顯示您最終一致性的最終結果。 此指標提供了一個瞭解獲得比 Azure Cosmos 帳戶上當前配置的一致性級別更強的一致性的頻率。 換句話說，您可以看到針對寫入和讀取區域的組合取得強式一致性讀取的機率 (以毫秒來測量)。

## <a name="next-steps"></a>後續步驟

在下列文章中深入了解一致性層級：

* [在 Cosmos DB API 上對應的一致性層級](consistency-levels-across-apis.md)
* [各種一致性層級的可用性和效能權衡取捨](consistency-levels-tradeoffs.md)
* [如何管理應用程式的工作階段權杖](how-to-manage-consistency.md#utilize-session-tokens)
* [監視機率限定過期 (PBS) 計量](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)
