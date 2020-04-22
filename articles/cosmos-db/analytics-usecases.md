---
title: 使用 Azure Cosmos DB 進行內建分析的用例。
description: 瞭解如何在不同的用例中使用 Azure Cosmos DB 的內建分析。
author: markjbrown
ms.author: mjbrown
ms.topic: conceptual
ms.service: cosmos-db
ms.date: 09/26/2019
ms.reviewer: sngun
ms.openlocfilehash: adf1db2d737de7004b5ab71c8e0e44d61d1da8c9
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768511"
---
# <a name="use-cases-for-built-in-analytics-with-azure-cosmos-db"></a>使用 Azure Cosmos DB 進行內建分析的案例

通過在 Azure Cosmos DB 中使用與 Apache Spark 的內建分析,可以實現以下用例:

## <a name="htap-scenarios"></a>HTAP 專案

Azure Cosmos DB 中的內建分析可用於:

* 檢測交易處理過程中的欺詐。
* 在購物者瀏覽電子商務商店時提供建議。
* 提醒操作員關鍵製造設備即將出現故障。
* 通過將即時分析直接嵌入操作數據,創建快速、可操作的洞察。

Azure Cosmos DB 透過使用本機內建的 Apache Spark 支援混合事務/分析處理 (HTAP) 方案。 Azure Cosmos DB 消除了傳統系統附帶的操作和分析分離。

## <a name="globally-distributed-data-lake-without-requiring-any-etl"></a>全球分散式資料湖,無需任何 ETL

Azure Cosmos DB 具有本機內建的 Apache Spark,提供了一種快速、簡單且可擴展的方式來構建提供單個系統映射的全球分散式資料湖。 全球分佈的多模型數據無需投資昂貴的 ETL 管道並按需擴展,從而徹底改變了數據團隊分析其數據集的方式。

## <a name="time-series-analytics-over-historic-data"></a>歷史資料的時間序列分析

在某些情況下,您可能需要根據數據回答問題,因為過去完成的事件在特定時間點。 例如,獲取特定日期的 CRM 活動狀態的計數。 如果一周前運行了報表,則狀態計數將根據該時間點每個活動的狀態進行。 今天運行同一份報告將告訴你那些狀態與今天一樣的活動的計數,這些活動自上周以來可能發生了變化,因為它們經歷了從開放到關閉的生命週期。 因此,您需要在案例生命週期的每個階段報告快照。

使用 Azure Cosmos DB,用戶可以實現時間旅行的概念,能夠追溯性地查詢和運行數據分析,並詢問數據如何看待歷史記錄中的特定時間點。 這意味著用戶可以輕鬆查看數據的當前視圖和歷史視圖,並在數據上運行分析。

## <a name="globally-distributed-machine-learning-and-ai"></a>全球分散式機器學習和 AI

隨著企業應對快速增長的數據量和不斷增長的各種數據類型和格式,在全球 PB 範圍內獲得更深入、更準確的見解的能力幾乎是不可能的。 Azure Cosmos DB 具有本機內建的 Apache Spark,提供全球分散式分析平臺,提供廣泛的機器學習演演演算法庫。 您可以使用互動式 Jupyter 筆記本來建構和訓練模型以及群集管理功能。 這些功能使您能夠按需預配高度調諧和自動彈性Spark群集。

## <a name="deep-learning-on-multi-model-globally-distributed-data"></a>對多模型全球分散式數據的深入瞭解

隨著數據量和複雜性的持續增長,深度學習是提供大數據預測分析解決方案的理想方式。 通過深度學習,企業可以利用非結構化和半結構化數據的強大功能來提供利用 AI、自然語言處理等技術的用例。

## <a name="reporting-integrating-with-power-apps-power-bi"></a>報告(與電源應用整合,電源 BI)

Power BI 提供具有自助服務商業智慧功能的互動式可視化,使最終用戶能夠自行創建報表和儀錶板。 通過使用內建 Spark 連接器,可以將 Power BI 桌面連接到 Azure Cosmos DB 中的 Apache Spark 群集。 此連接器允許您使用直接查詢在 Azure Cosmos DB 中卸載處理到 Apache Spark,當您有大量不想載入到 Power BI 的資料或要執行接近即時分析的數據時,這非常出色。

## <a name="iot-analytics-at-global-scale"></a>全球規模的物聯網分析

不斷增長的網路感測器產生的數據為以前不透明的系統和流程帶來了前所未有的可見性。 關鍵是在這個資訊洪流中尋找可操作的見解,而不管 IoT 設備在全球的分佈位置如何。 Azure Cosmos DB 允許 IOT 公司在世界各地即時分析高速感測器和時間序列數據。 它使您能夠利用互聯世界的真正價值,提供改進的客戶體驗、運營效率和新的收入機會。

## <a name="stream-processing-and-event-analytics"></a>流程處理與事件分析 

從日誌檔到感測器數據,企業越來越需要應對數據的"流"。 此數據以穩定流到達,通常同時來自多個源。 雖然將這些數據流存儲在磁碟上並對其進行回顧性分析是可行的,但有時在數據到達時處理和操作數據可能是明智的或重要的。 例如,可以即時處理與金融交易相關的數據流,以識別和拒絕潛在的欺詐易。

## <a name="interactive-analytics"></a>互動式分析

除了運行預定義的查詢以創建銷售、生產率或股票價格的靜態儀錶板外,您可能還希望以交互方式流覽數據。 互動式分析允許您提出問題、查看結果、根據回應更改初始問題或深入了解結果。 Azure Cosmos DB 中的 Apache Spark 通過快速回應和調整支援互動式查詢。

## <a name="data-exploration-using-jupyter-notebooks"></a>使用朱派特筆記本進行數據探索

當您有一個新的數據集時,在深入瞭解正在運行的模型和測試之前,您需要檢查數據。 換句話說,您需要執行探索性數據分析。 數據探索可以為多個決策提供資訊。 例如,您可以查找詳細資訊,例如適合在數據上使用的方法、數據是否符合某些建模假設、是否應清理數據、重組等。通過使用 Azure Cosmos DB 的本機內建 Jupyter 筆記本和 Apache Spark,您可以對事務和分析數據進行快速有效的探索性數據分析。

## <a name="next-steps"></a>後續步驟

要開始使用這些用例,請造訪以下文章:

* [Azure 宇宙 DB 中的內建猶太筆記本](cosmosdb-jupyter-notebooks.md)
* [如何為 Azure Cosmos 帳號啟用筆記本](enable-notebooks.md)
* [建立筆記本以分析及視覺化資料](create-notebook-visualize-data.md)