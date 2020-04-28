---
title: 使用案例搭配 Azure Cosmos DB 進行內建分析。
description: 瞭解如何在不同的使用案例中，使用內建分析搭配 Azure Cosmos DB。
author: markjbrown
ms.author: mjbrown
ms.topic: conceptual
ms.service: cosmos-db
ms.date: 04/20/2020
ms.reviewer: sngun
ms.openlocfilehash: 9b1c3435222ada52c01f21c2c242dc886f566a81
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192845"
---
# <a name="use-cases-for-built-in-analytics-with-azure-cosmos-db"></a>使用案例進行內建分析與 Azure Cosmos DB

下列使用案例可透過在 Azure Cosmos DB 中使用內建的分析與 Apache Spark 來達成：

## <a name="htap-scenarios"></a>HTAP 案例

Azure Cosmos DB 中的內建分析可以用來：

* 在交易處理期間偵測詐騙。
* 在流覽電子商務商店時提供建議給購物者。
* 警示操作員在製造設備的重要部分即將發生的失敗。
* 直接在運算元據上內嵌即時分析，以建立快速且可操作的深入解析。

Azure Cosmos DB 使用原生內建 Apache Spark 支援混合式交易/分析處理（HTAP）案例。 Azure Cosmos DB 移除傳統系統所附的操作和分析分離。

## <a name="globally-distributed-data-lake-without-requiring-any-etl"></a>全域散發的 data lake，而不需要任何 ETL

使用原生內建 Apache Spark，Azure Cosmos DB 提供快速、簡單且可調整的方式來建立全域散發的 data lake，以提供單一系統映射。 全域散發的多模型資料，讓您不需要投資昂貴的 ETL 管線並視需要調整，革新資料小組分析其資料集的方式。

## <a name="time-series-analytics-over-historic-data"></a>歷程記錄資料的時間序列分析

在某些情況下，您可能需要根據資料來回答問題，就像在過去完成的事件的特定時間點。 例如，若要取得特定日期的 CRM 活動狀態計數。 如果您在一周前執行報表，則狀態的計數會依據該時間點每個活動的狀態。 今天執行相同的報表，將會提供其狀態與今天相同的活動計數，這在過去一周以來可能已變更，因為它們會通過生命週期，從開啟到關閉。 因此，您需要在案例生命週期的每個階段報告快照集。

有了 Azure Cosmos DB，使用者就能夠執行時間移動的概念、能夠查詢和執行資料反而要的分析，並要求資料在歷程記錄中的特定時間點。 這表示使用者可以輕鬆地同時查看資料的目前和歷史視圖，並對其執行分析。

## <a name="globally-distributed-machine-learning-and-ai"></a>全域散發的機器學習和 AI

當企業對抗快速成長的資料量，以及擴展的各種資料類型和格式時，取得更深入且更精確的深入解析的能力，在全球各地的 pb 規模中會變得近乎不可能。 使用原生內建 Apache Spark，Azure Cosmos DB 提供全域散發的分析平臺，提供廣泛的機器學習演算法程式庫。 您可以使用互動式 Jupyter 筆記本來建立和定型模型，以及叢集管理功能。 這些功能可讓您視需要布建高度調整和自動彈性的 Spark 叢集。

## <a name="deep-learning-on-multi-model-globally-distributed-data"></a>深入瞭解多模型的全域散發資料

深度學習是提供 big data 預測性分析解決方案的理想方式，因為資料量和複雜度會持續成長。 透過深度學習，企業可以利用非結構化和半結構化資料的強大功能，來提供利用 AI、自然語言處理等技術的使用案例。

## <a name="reporting-integrating-with-power-apps-power-bi"></a>報告（與 Power Apps 整合，Power BI）

Power BI 提供具有自助商業智慧功能的互動式視覺效果，讓使用者能夠自行建立報表和儀表板。 藉由使用內建 Spark 連接器，您可以將 Power BI Desktop 連接到 Azure Cosmos DB 中的 Apache Spark 叢集。 此連接器可讓您使用直接查詢將處理卸載至 Azure Cosmos DB 中的 Apache Spark，這在您有大量資料而不想載入 Power BI，或想要執行近乎即時的分析時，這非常好用。

## <a name="iot-analytics-at-global-scale"></a>全球規模的 IoT 分析

從增加的網路感應器產生的資料，對先前不透明的系統和處理常式會有前所未有的可見度。 關鍵在於在此資訊 torrent 中尋找可操作的深入解析，而不論 IoT 裝置在全球各地的散佈位置為何。 Azure Cosmos DB 讓 IOT 公司能夠即時分析世界各地的高速度感應器和時間序列資料。 它可讓您控管互連世界的真正價值，以提供更好的客戶體驗、營運效率和新的收益機會。

## <a name="stream-processing-and-event-analytics"></a>串流處理和事件分析 

從記錄檔到感應器資料，企業日益需要處理資料的「資料流程」。 此資料會抵達穩定的資料流程，通常會同時從多個來源。 雖然您可以將這些資料流程儲存在磁片上並加以分析反而要，但有時在資料抵達時處理和採取動作可能是很合理或很重要的。 例如，可以即時處理與財務交易相關的資料串流，以識別和拒絕潛在的詐騙交易。

## <a name="interactive-analytics"></a>互動式分析

除了執行預先定義的查詢來建立銷售、產能或股票價格的靜態儀表板，您可能也會想要以互動方式流覽資料。 互動式分析可讓您提出問題、查看結果、根據回應改變初始問題，或深入探索結果。 Azure Cosmos DB 中的 Apache Spark 可讓您快速回應和調整，以支援互動式查詢。

## <a name="data-exploration-using-jupyter-notebooks"></a>使用 Jupyter 筆記本的資料探索

當您有新的資料集時，在深入探索執行中的模型和測試之前，您必須檢查您的資料。 換句話說，您需要執行探索式資料分析。 資料探索可以通知數個決策。 例如，您可以找到詳細資料，例如適用于資料的方法、資料是否符合特定的模型假設、是否應清除資料、加以重構等等。藉由使用 Azure Cosmos DB 的原生內建 Jupyter 筆記本和 Apache Spark，您可以對交易式和分析資料進行快速且有效的探索式資料分析。

## <a name="next-steps"></a>後續步驟

若要開始使用這些使用案例，請前往下列文章：

* [Azure Cosmos DB 中的內建 Jupyter 筆記本](cosmosdb-jupyter-notebooks.md)
* [如何啟用 Azure Cosmos 帳戶的筆記本](enable-notebooks.md)
* [建立筆記本來分析資料並將其視覺化](create-notebook-visualize-data.md)