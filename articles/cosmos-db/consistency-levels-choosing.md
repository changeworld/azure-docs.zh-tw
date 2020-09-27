---
title: 為您的 Azure Cosmos DB 應用程式選擇正確的一致性層級
description: 在 Azure Cosmos DB 中為應用程式選擇正確的一致性層級。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/23/2020
ms.reviewer: sngun
ms.openlocfilehash: 0a79f6883a150cb95724a7be30dcbd2e8e03f01f
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2020
ms.locfileid: "91396071"
---
# <a name="how-to-choose-the-right-consistency-level-for-your-azure-cosmos-db-application"></a>如何為您的 Azure Cosmos DB 應用程式選擇正確的一致性層級？ 

依賴複寫來取到高可用性或低延遲性或兩者的分散式資料庫，可在讀取一致性與可用性、延遲性及輸送量之間進行基本權衡取捨。 大部分的正式發行資料庫都要求開發人員在兩個極端的一致性模型之間做選擇： *強* 式一致性和 *最終* 一致性。 Azure Cosmos DB 可讓開發人員在五個定義完善的一致性模型之間做選擇： *強*式、 *限定過期*、 *會話*、 *一致前置* 詞和 *最終*。 這些一致性模型中的每一個都是完善定義且直覺化的模型，可用於特定的真實案例。 這五個一致性模型中的每一個都能提供精確的 [可用性和效能取捨](consistency-levels-tradeoffs.md) ，並且由全方位 sla 所支援。 您可以在帳戶層級設定預設一致性，並 [在要求層](how-to-manage-consistency.md#override-the-default-consistency-level)級進行覆寫。 下列簡單考量將可協助您在許多常見案例中做出正確的選擇。

## <a name="sql-api-and-table-api"></a>SQL API 和資料表 API

如果您的應用程式是使用 SQL API 或資料表 API 建立的，請考慮下列幾點：

- 對於許多真實案例，工作階段一致性是最佳的且為建議選項。 如需詳細資訊，請參閱 [如何管理應用程式的會話權杖](how-to-manage-consistency.md#utilize-session-tokens)。

- 如果您的應用程式需要強式一致性，建議您使用限定過期一致性層級。

- 如果您需要的一致性保證比起用於寫入的工作階段一致性和個位數毫秒延遲所提供的更嚴格，則建議您使用限定過期一致性層級。  

- 如果您的應用程式需要最終一致性，建議您使用一致前置詞一致性層級。

- 如果您需要的一致性保證不如工作階段一致性所提供的嚴格，建議您使用一致前置詞一致性層級。

- 如果您需要最高可用性和最低延遲，請使用最終一致性層級。

- 如果您需要更高的資料持續性，但不想犧牲效能，您可以在應用程式層建立自訂的一致性層級。 如需詳細資訊，請參閱[如何在應用程式中實作自訂同步處理](how-to-custom-synchronization.md)。

## <a name="cassandra-mongodb-and-gremlin-apis"></a>Cassandra、MongoDB 及 Gremlin Api

- 如需 Apache Cassandra 中所提供的「讀取一致性層級」與 Cosmos DB 一致性層級之間對應的詳細資訊，請參閱[一致性層級和 Cosmos DB API](consistency-levels-across-apis.md#cassandra-mapping)。

- 如需 MongoDB 的「讀取考量」與 Azure Cosmos DB 一致性層級之間對應的詳細資訊，請參閱[一致性層級和 Cosmos DB API](consistency-levels-across-apis.md#mongo-mapping)。

## <a name="consistency-guarantees-in-practice"></a>實際上的一致性保證

在實務上，您通常可能會獲得更強的一致性保證。 讀取作業的一致性保證會對應到您所要求之資料庫狀態的時效性和排序。 讀取一致性會繫結到寫入/更新作業的排序和傳播。  

* 將一致性層級設定為**限定過期**時，Cosmos DB 會利用過期時段所限定的延隔時間，來保證用戶端一律會讀取前一個寫入的值。

* 將一致性層級設定為**強式**時，過期時段等於零，並保證用戶端會讀取最新認可的寫入作業值。

* 針對其餘的三個一致性層級，過期時段多半取決於您的工作負載。 例如，如果資料庫上沒有任何寫入作業，則具有**最終**、**工作階段**或**一致前置詞**一致性層級的讀取作業可能會產生與具有強式一致性層級的讀取作業相同的結果。

如果您的 Azure Cosmos 帳戶設定了強式一致性以外的一致性層級，您可以藉由查看 *機率限定過期* (PBS) 計量，找出用戶端可能會針對您的工作負載取得強式和一致讀取的可能性。 此計量會在 Azure 入口網站公開，若要深入了解，請參閱[監視機率限定過期 (PBS) 計量](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)。

機率性限定過期會顯示您最終一致性的最終結果。 此計量可讓您深入瞭解您在 Azure Cosmos 帳戶上目前設定的一致性層級時，可以獲得更強的一致性的頻率。 換句話說，您可以看到針對寫入和讀取區域的組合取得強式一致性讀取的機率 (以毫秒來測量)。

## <a name="next-steps"></a>後續步驟

在下列文章中深入了解一致性層級：

* [在 Cosmos DB API 上對應的一致性層級](consistency-levels-across-apis.md)
* [各種一致性層級的可用性和效能權衡取捨](consistency-levels-tradeoffs.md)
* [如何管理應用程式的工作階段權杖](how-to-manage-consistency.md#utilize-session-tokens)
* [監視機率限定過期 (PBS) 計量](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)
