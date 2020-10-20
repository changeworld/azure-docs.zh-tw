---
title: Azure 串流分析概觀
description: 了解串流分析，這是可協助您即時分析物聯網 (IoT) 資料流的受控服務。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 10/9/2020
ms.openlocfilehash: 10ffe7c2544be2bf2c09ae39f323b1e643063d27
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91892875"
---
# <a name="what-is-azure-stream-analytics"></a>Azure 串流分析是什麼？

Azure 串流分析是即時分析與處理複雜事件的引擎，用來同時分析和處理多個來源的大量快速串流資料。 它可以從多個輸入來源 (包括裝置、感應器、點選流、社交媒體摘要和應用程式) 中擷取的資訊，識別模式和關聯性。 這些模式可以用來觸發動作並啟動工作流程，例如建立警示、將資訊提供給報告工具，或是儲存轉換資料以供之後使用。 此外，Azure IoT Edge 執行階段也提供串流分析，能夠處理 IoT 裝置上的資料。 

下列案例是您何時可以使用 Azure 串流分析的範例：

* 從 IoT 裝置分析即時遙測資料流
* Web 記錄/點選流分析
* 車隊管理和自駕車的地理空間分析
* 高價值資產的遠端監視和預測性維護
* 即時分析銷售點資料以控制庫存和偵測異常

## <a name="how-does-stream-analytics-work"></a>串流分析如何運作？

Azure 串流分析作業是由輸入、查詢及輸出所組成。 串流分析會擷取 Azure 事件中樞 (包括來自 Apache Kafka 的 Azure 事件中樞)、Azure IoT 中樞或 Azure Blob 儲存體的資料。 查詢以 SQL 查詢語言為基礎，可在一段時間內輕鬆地篩選、排序、彙總和聯結串流處理資料。 您也可以使用 JavaScript 和 C# 使用者定義函式 (UDF) 來擴充此 SQL 語言。 透過簡單的語言建構和/或設定執行彙總作業時，您可以輕鬆調整事件排序選項和時間範圍的持續長度。

每個作業都具有所轉換資料的一或多個輸出，並可控制所要採取的動作以回應您所分析的資訊。 例如，您可以：

* 將資料傳送至服務如 Azure Functions、服務匯流排主題或佇列，以觸發通訊或自訂工作流程下游。
* 將資料傳送至 Power BI 儀表板以即時顯示在儀表板上。
* 將資料儲存至其他 Azure 儲存體服務 (例如 Azure Data Lake、Azure Synapse Analytics 等等)，以便根據歷程記錄資料定型機器學習服務模型，或執行批次分析。

下圖顯示如何將資料傳送至串流分析、加以分析並傳送，以進行儲存或呈現等其他動作：

![串流分析流程簡介](./media/stream-analytics-introduction/stream-analytics-e2e-pipeline.png)

## <a name="key-capabilities-and-benefits"></a>主要功能和優點

Azure 串流分析的設計訴求是方便使用、具靈活性、可靠，以及適用於任何作業大小。 其可在多個 Azure 區域中使用，並在 IoT Edge 或 Azure Stack 上執行。

## <a name="ease-of-getting-started"></a>輕鬆開始使用

您可以輕鬆地開始使用 Azure 串流分析。 只要按幾下就能連線到多個來源與接收，建立端對端管線。 串流分析可以連線到 [Azure 事件中樞](/azure/event-hubs/)和 [Azure IoT 中樞](/azure/iot-hub/)進行串流資料擷取，以及連線到 [Azure Blob 儲存體](/azure/storage/common/storage-introduction)以取得歷史資料。 作業輸入也可以包含來自 Azure Blob 儲存體或 [SQL 資料庫](stream-analytics-use-reference-data.md#azure-sql-database)的靜態或變更緩慢的參考資料，您可以將參考資料加入到串流資料，以執行查閱作業。

串流分析可將作業輸出路由至許多儲存體系統，例如 [Azure Blob 儲存體](/azure/storage/common/storage-introduction)、[Azure SQL Database](/azure/sql-database/)、[Azure Data Lake Store](/azure/data-lake-store/) 和 [Azure CosmosDB](/azure/cosmos-db/introduction)。 您也可以使用 Azure Synapse Analytics 或 HDInsight 對資料流輸出執行批次分析，也可以將輸出傳送至另一個服務 (例如事件中樞) 以供取用，或用於即時呈現視覺效果的 [Power BI](https://docs.microsoft.com/power-bi/)。

如需串流分析輸出的完整清單，請參閱[了解來自 Azure 串流分析的輸出](stream-analytics-define-outputs.md)。

## <a name="programmer-productivity"></a>程式設計人員生產力

Azure 串流分析使用已擴增的 SQL 查詢語言，搭配強大的時態性限制式即可分析移動中的資料。 使用 Azure PowerShell、Azure CLI、[串流分析 Visual Studio 工具](stream-analytics-tools-for-visual-studio-install.md)、[串流分析 Visual Studio Code 擴充功能](quick-create-visual-studio-code.md)，或 Azure Resource Manager 範本等開發人員工具，也可以建立作業。 使用開發人員工具可讓您離線開發轉換查詢，並使用 [CI/CD 管線](stream-analytics-tools-for-visual-studio-cicd.md)將作業提交至 Azure。

串流分析查詢語言可讓您藉由提供各式各樣的函式來分析串流資料，以執行 CEP (複雜事件處理)。 此查詢語言支援簡單資料操作、彙總和分析函式、[地理空間函式](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-geospatial-functions)、[模式比對](https://docs.microsoft.com/stream-analytics-query/match-recognize-stream-analytics)及[異常偵測](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-machine-learning-anomaly-detection)。 您可以在入口網站中或使用我們的開發工具來編輯查詢，然後使用從即時資料流所擷取的範例資料來測試查詢。

您可以透過定義和叫用其他函式來延伸查詢語言的功能。 您可以在 Azure Machine Learning 中定義函式呼叫以利用 Azure Machine Learning 解決方案，並整合 JavaScript 或 C# 使用者定義的函式 (UDF) 或使用者定義的彙總以在串流分析查詢中執行複雜的計算。

## <a name="fully-managed"></a>完全受控

Azure 串流分析是 Azure 上完全受控的 (PaaS) 供應項目。 您不必佈建任何硬體或基礎結構、更新 OS 或軟體。 Azure 串流分析完全管理您的作業，因此您可以專注於商務邏輯，而不是基礎結構。


## <a name="run-in-the-cloud-or-on-the-intelligent-edge"></a>在雲端或在智慧邊緣執行

Azure 串流分析可以在雲端執行以進行大規模分析，或在 IoT Edge 或 Azure Stack 上執行以進行超低延遲分析。 Azure 串流分析會在雲端和智慧邊緣上使用相同的工具和查詢語言，讓開發人員能夠建置真正的混合式架構進行串流處理。 

## <a name="low-total-cost-of-ownership"></a>低擁有權總成本

作為雲端服務，串流分析已進行成本最佳化。 不會有預付費用 - 您只需針對[您耗用的串流單位](stream-analytics-streaming-unit-consumption.md)付費。 不需要承諾或佈建叢集，您可以根據業務需求相應增加或減少作業。

## <a name="mission-critical-ready"></a>任務關鍵性就緒

Azure 串流分析適用於全球多個區域，其設計訴求是藉由支援可靠性、安全性和合規性需求來執行任務關鍵性工作負載。

### <a name="reliability"></a>可靠性

Azure 串流分析可保證僅只一次的事件處理，以及至少一次的事件傳遞，因此永遠不會遺失事件。 如[事件傳遞保證](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)所述，保證選取的輸出恰好處理一次。

Azure 串流分析具有可在事件傳遞失敗時進行復原的內建功能。 串流分析也會提供內建檢查點來維護作業的狀態，並提供可重複出現的結果。

串流分析為受控服務，可保證事件處理在分鐘層級細微性上有 99.9% 的可用性。 如需詳細資訊，請參閱[串流分析 SLA](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/) 頁面。 

### <a name="security"></a>安全性

就安全性而言，Azure 串流分析會將所有傳入和傳出的通訊加密，並支援 TLS 1.2。 內建的檢查點也會加密。 串流分析不會儲存傳入資料，因為所有處理都在記憶體內進行。 在[串流分析叢集](https://docs.microsoft.com/azure/stream-analytics/cluster-overview)中執行作業時，串流分析也支援 Azure 虛擬網路 (VNET)。

### <a name="compliance"></a>法規遵循

Azure 串流分析會遵循如 [Azure 合規性概觀](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)中所述的多個合規性認證。 

## <a name="performance"></a>效能

串流分析每秒可以處理數百萬個事件，並可極低延遲地傳遞結果。 它可讓您相應增加及向外延展，以處理大型的即時和複雜事件處理應用程式。 串流分析藉由下列方式來支援更高的效能：進行分割，並允許在多個串流節點上平行化及執行複雜的查詢。 Azure 串流分析是以 [Trill](https://github.com/Microsoft/Trill) 為基礎，這是與 Microsoft Research 合作開發的高效能記憶體內部串流分析引擎。

## <a name="next-steps"></a>後續步驟

您現在已大致了解 Azure 串流分析。 接下來，您可以深入了解並建立您的第一個串流分析作業：

* [使用 Azure 入口網站建立串流分析作業](stream-analytics-quick-create-portal.md)。
* [使用 Azure PowerShell 建立串流分析作業](stream-analytics-quick-create-powershell.md)。
* [使用 Visual Studio 建立串流分析作業](stream-analytics-quick-create-vs.md)。
* [使用 Visual Studio Code 建立串流分析作業](quick-create-visual-studio-code.md)。
