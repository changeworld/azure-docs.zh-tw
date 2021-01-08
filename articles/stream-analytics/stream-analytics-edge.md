---
title: Azure IoT Edge 串流分析
description: 在 Azure 串流分析中建立 Edge 作業，並將其部署至執行 Azure IoT Edge 的裝置。
ms.service: stream-analytics
author: an-emma
ms.author: raan
ms.topic: conceptual
ms.date: 12/18/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: c2a062b75caa84a0e3c342ca1ce45ccce12f2bb7
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98012609"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>Azure IoT Edge 串流分析
 
Azure IoT Edge 串流分析可讓開發人員在更接近 IoT 裝置的地方部署近乎即時的分析智慧，讓他們可以將裝置產生之資料的完整值解除鎖定。 Azure 串流分析的設計著眼於低延遲、具復原能力、有效率地使用頻寬和合規性。 企業可以部署接近產業作業的控制邏輯，並補充在雲端中完成的大型資料分析。

IoT Edge 上的 Azure 串流分析是在 [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) 架構內執行。 在串流分析中建立作業之後，您就可以使用 IoT 中樞來部署和管理作業。

## <a name="common-scenarios"></a>常見案例

本節說明 IoT Edge 上串流分析的常見案例。 下圖顯示 IoT 裝置與 Azure 雲端之間的資料流程。

:::image type="content" source="media/stream-analytics-edge/edge-high-level-diagram.png" alt-text="IoT Edge 的高層級圖表":::

### <a name="low-latency-command-and-control"></a>低延遲命令和控制

製造安全系統必須回應具有超低延遲的運算元據。 透過 IoT Edge 上的串流分析，您可以近乎即時地分析感應器資料，並在偵測異常狀況以停止電腦或觸發警示時發出命令。

### <a name="limited-connectivity-to-the-cloud"></a>有限的雲端連線能力

諸如遠端採礦設備、互連的船隻或離岸鑽井等任務關鍵系統都必須分析及回應資料，即使是當雲端連線斷斷續續時也一樣。 使用串流分析時，您的串流邏輯會獨立于網路連線而獨立執行，而且您可以選擇要傳送到雲端的內容，以進一步處理或儲存。

### <a name="limited-bandwidth"></a>受限的頻寬

噴射引擎或連線的車輛所產生的資料量可能會很大，在將資料傳送到雲端之前，必須先篩選或預先處理。 使用串流分析，您可以篩選或匯總必須傳送至雲端的資料。

### <a name="compliance"></a>法規遵循

法規合規性可能會要求某些資料在傳送至雲端之前，先在本機進行匿名或彙總。

## <a name="edge-jobs-in-azure-stream-analytics"></a>Azure 串流分析中的 Edge 作業

串流分析 Edge 作業會在部署到 [Azure IoT Edge 裝置](../iot-edge/about-iot-edge.md)的容器中執行。 Edge 作業是由兩個部分所組成：

* 負責作業定義的雲端元件：使用者會在雲端中定義輸入、輸出、查詢和其他設定，例如不按順序的事件。

* 在您的 IoT 裝置上執行的模組。 此模組包含串流分析引擎，並會從雲端接收作業定義。 

串流分析會使用 IoT 中樞將 edge 作業部署到裝置 (s) 。 如需詳細資訊，請參閱 [IoT Edge 部署](../iot-edge/module-deployment-monitoring.md)。

:::image type="content" source="media/stream-analytics-edge/stream-analytics-edge-job.png" alt-text="Azure 串流分析 Edge 作業":::

## <a name="edge-job-limitations"></a>Edge 作業限制

目標是在 IoT Edge 與業和雲端作業之間進行同位檢查。 Edge 和雲端都支援大部分 SQL 查詢語言功能。 不過，edge 作業不支援下列功能：
* JavaScript 中的使用者定義函式 (UDF)。 您可以在[適用於 IoT Edge 的 C# 作業](./stream-analytics-edge-csharp-udf.md) (預覽版) 中找到 UDF。
* 使用者定義彙總 (UDA)。
* Azure ML 函式。
* 輸入/輸出的 AVRO 格式。 目前僅支援 CSV 和 JSON。
* 下列 SQL 運算子：
    * PARTITION BY
    * GetMetadataPropertyValue
* 延遲傳入原則

### <a name="runtime-and-hardware-requirements"></a>執行階段與硬體需求
若要在 IoT Edge 上執行串流分析，您需要可執行 [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/)的裝置。 

串流分析和 Azure IoT Edge 使用 **Docker** 容器來提供可在多個主機作業系統上執行的便攜解決方案， (Windows、Linux) 。

IoT Edge 的串流分析會以 Windows 和 Linux 映射形式提供，可在 x86-64 或 ARM (Advanced RISC 電腦) 架構上執行。 


## <a name="input-and-output"></a>輸入和輸出

串流分析 Edge 作業可以從 IoT Edge 裝置上執行的其他模組取得輸入和輸出。 若要連線進出特定模組，您可以在部署期間設定路由組態。 [IoT Edge 模組複合文件](../iot-edge/module-composition.md)中會說明詳細資訊。

輸入與輸出都支援 CSV 和 JSON 格式。

針對您在串流分析作業中建立的每個輸入和輸出資料流程，系統會在您部署的模組上建立對應的端點。 這些端點可用於部署的路由。

支援的資料流程輸入類型為：
* Edge 中樞
* 事件中樞
* IoT 中樞

支援的資料流程輸出類型為：
* Edge 中樞
* SQL Database
* 事件中樞
* Blob 儲存體/ADLS Gen2

參考輸入支援參考檔案類型。 其他輸出可以使用下游的雲端作業來觸達。 例如，裝載於 Edge 中的串流分析作業會將輸出傳送到 Edge 中樞，然後 Edge 中樞會將輸出傳送到 IoT 中樞。 您可以使用第二個雲端裝載的 Azure 串流分析作業搭配來自 IoT 中樞的輸入，並輸出至 Power BI 或另一個輸出類型。

## <a name="license-and-third-party-notices"></a>授權和第三方通知
* [Azure IoT Edge 串流分析授權](https://go.microsoft.com/fwlink/?linkid=862827)。 
* [Azure IoT Edge 串流分析的第三方通知](https://go.microsoft.com/fwlink/?linkid=862828)。

## <a name="azure-stream-analytics-module-image-information"></a>建立 Azure 串流分析模組映像資訊 

此版本資訊的上次更新時間為2020-09-21：

- 映像：`mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-amd64`
   - 基底映射： mcr.microsoft.com/dotnet/core/runtime:2.1.13-alpine
   - 平台：
      - 架構：amd64
      - 作業系統：Linux
 
- 映像：`mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-arm32v7`
   - 基底映射： mcr.microsoft.com/dotnet/core/runtime:2.1.13-bionic-arm32v7
   - 平台：
      - 架構：arm
      - 作業系統：Linux
 
- 映像：`mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-arm64`
   - 基底映射： mcr.microsoft.com/dotnet/core/runtime:3.0-bionic-arm64v8
   - 平台：
      - 架構： arm64
      - 作業系統：Linux
      
      
## <a name="get-help"></a>取得說明
如需進一步的協助，請嘗試 [Azure 串流分析的 Microsoft 問與答頁面](/answers/topics/azure-stream-analytics.html)。

## <a name="next-steps"></a>後續步驟

* [Azure IoT Edge 的詳細資訊](../iot-edge/about-iot-edge.md)
* [IoT Edge 的串流分析教學課程](../iot-edge/tutorial-deploy-stream-analytics.md)
* [使用 Visual Studio 工具來開發串流分析 Edge 作業](./stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [使用 API 實作適用於串流分析的 CI/CD](stream-analytics-cicd-api.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/
