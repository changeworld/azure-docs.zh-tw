---
title: Azure IoT Edge 串流分析
description: 在 Azure 串流分析中建立 Edge 作業，並將其部署至執行 Azure IoT Edge 的裝置。
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: how-to
ms.date: 10/29/2020
ms.custom: seodec18
ms.openlocfilehash: 7a084b2d0582f53d4372ba3332194629ad29a4ec
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93041876"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>Azure IoT Edge 串流分析
 
IoT Edge 上的 Azure 串流分析 (ASA) 讓開發人員能夠在更接近 IoT 裝置的地方部署近乎即時的分析智慧，從而使裝置產生的資料充分發揮價值。 Azure 串流分析的設計著眼於低延遲、具復原能力、有效率地使用頻寬和合規性。 企業現在可以部署接近產業作業的控制邏輯，並補充在雲端中完成的巨量資料分析。  

IoT Edge 上的 Azure 串流分析是在 [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) 架構內執行。 在 ASA 中建立作業之後，您可以使用 IoT 中樞來部署及管理它。

## <a name="scenarios"></a>案例
![高階 IoT Edge 圖](media/stream-analytics-edge/ASAedge-highlevel-diagram.png)

* **低延遲命令與控制** ：例如，製造安全系統必須回應具有超低延遲的作業資料。 透過 IoT Edge 上的 ASA，您可以近乎即時地分析感應器資料，並在偵測到異常時發出命令來停止電腦或觸發程序的警示。
*   **有限的雲端連線能力** ：諸如遠端採礦設備、互連的船隻或離岸鑽井等任務關鍵系統都必須分析及回應資料，即使是當雲端連線斷斷續續時也一樣。 利用 ASA，您的串流邏輯就會獨立執行網路連線，從而能夠選擇傳送至雲端進行進一步處理或儲存體的項目。
* **受限頻寬** ：噴射引擎或連線的車輛所產生的資料量可能會很大，在將資料傳送到雲端之前，必須先篩選或預先處理。 您可以使用 ASA 來篩選或彙總必須傳送至雲端的資料。
* **合規性** ：法規合規性可能會要求某些資料在傳送至雲端之前，先在本機進行匿名或彙總。

## <a name="edge-jobs-in-azure-stream-analytics"></a>Azure 串流分析中的 Edge 作業
### <a name="what-is-an-edge-job"></a>什麼是 "Edge" 作業？

ASA Edge 作業在部署到 [Azure IoT Edge 裝置](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)的容器中執行。 它們是由兩個部分組成：
1.  負責作業定義的雲端部分：使用者在雲端中定義輸入、輸出、查詢和其他設定 (出問題的事件等)。
2.  在您的 IoT 裝置上執行的模組。 它包含 ASA 引擎，並會從雲端接收作業定義。 

ASA 會使用 IoT 中樞將 Edge 作業部署到裝置。 關於 [IoT Edge 部署的詳細資訊可以在這裡看到](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring)。

![Azure 串流分析 Edge 作業](media/stream-analytics-edge/stream-analytics-edge-job.png)


### <a name="installation-instructions"></a>安裝指示
下表說明高階步驟。 後面幾節提供詳細資訊。

| 步驟 | 注意 |
| --- | --- |
| **建立儲存體容器** | 儲存體容器是用來儲存您的作業定義，您的 IoT 裝置可在其中加以存取。 <br>  您可以重複使用任何現有的儲存體容器。 |
| **建立 ASA Edge 作業** | 建立新作業，請選取 **Edge** 作為 **裝載環境** 。 <br> 這些作業會從雲端建立/管理，並在您自己的 IoT Edge 裝置上執行。 |
| **在您的裝置上設定 IoT Edge 環境** | 適用於 [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) 或 [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) 的指示。|
| **在 IoT Edge 裝置上部署 ASA** | ASA 作業定義已匯出至先前建立的儲存體容器。 |

您可以遵循[此逐步教學課程](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)，在 IoT Edge 在部署您的第一個 ASA 作業。 下列影片應可協助您了解在 IoT Edge 裝置上執行串流分析作業的程序：  


> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T157/player]

#### <a name="create-a-storage-container"></a>建立儲存體容器
需要儲存體容器，才能匯出 ASA 已編譯查詢和作業組態。 它可用來設定包含您特定查詢的 ASA Docker 映像。 
1. 依照[這些指示](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)從 Azure 入口網站建立儲存體帳戶。 您可以保留所有預設選項將此帳戶用於 ASA。
2. 在新建立的儲存體帳戶中，建立 blob 儲存體容器：
    1. 按一下 [Blob]，然後按一下 [+ 容器]。 
    2. 輸入名稱，並將容器保留為 **私人** 。

#### <a name="create-an-asa-edge-job"></a>建立 ASA Edge 作業
> [!Note]
> 本教學課程著重於使用 Azure 入口網站建立 ASA 作業。 您也可以[使用 Visual Studio 外掛程式建立 ASA Edge 作業](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)

1. 從 Azure 入口網站中，建立新的「串流分析作業」。 [在這裡導向連結以建立新的 ASA 作業](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob)。

2. 在 [建立] 畫面中，選取 **Edge** 作為 **裝載環境** (請參閱下圖)

   ![在 Edge 上建立串流分析作業](media/stream-analytics-edge/create-asa-edge-job.png)
3. 作業定義
    1. **定義輸入資料流** 。 定義適用於您作業的一或多個輸入資料流。
    2. 定義參考資料 (選擇性)。
    3. **定義輸出資料流** 。 定義適用於您作業的一或多個輸出資料流。 
    4. **定義查詢** 。 使用內嵌編輯器在雲端中定義 ASA 查詢。 編譯器會自動檢查針對 ASA Edge 啟用的語法。 您也可以上傳範例資料來測試您的查詢。 

4. 在 [IoT Edge 設定] 功能表中設定儲存體容器資訊。

5. 設定選擇性設定
    1. **事件順序** 。 您可以在入口網站中設定順序錯亂的事件。 您可以在[這裡](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)取得文件。
    2. **地區設定** 。 設定國際化格式。



> [!Note]
> 建立部署時，ASA 會將作業定義匯出至儲存體容器中。 這個作業定義在部署的持續期間都會保持相同。 因此，如果您需要更新 Edge 上執行的作業，就必須在 ASA 中編輯作業，然後在 IoT 中樞中建立新的部署。


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>在您的裝置上設定 IoT Edge 環境
可以在執行 Azure IoT Edge 的裝置上部署 Edge 作業。
若要這麼做，您必須遵循這些步驟：
- 建立 IoT 中樞。
- 在 Edge 裝置上安裝 Docker 和 IoT Edge 執行階段。
- 在 IoT 中樞中將您的裝置設定為 **IoT Edge 裝置** 。

適用於 [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) 或 [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) 的 IoT Edge 文件中會說明這些步驟。  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>在 IoT Edge 裝置上部署 ASA
##### <a name="add-asa-to-your-deployment"></a>將 ASA 新增至您的部署
- 在 Azure 入口網站中開啟 IoT 中樞，瀏覽至 **IoT Edge** ，然後在此部署的目標裝置上按一下。
- 選取 [設定模組]，然後選取 [+ 新增] 並選擇 [Azure 串流分析模組]。
- 選取訂用帳戶與您建立的 ASA Edge 作業。 按一下 [儲存]。
![在您的部署中新增 ASA 模組](media/stream-analytics-edge/add-stream-analytics-module.png)


> [!Note]
> 在此步驟中，ASA 會在儲存體容器中建立一個名為 "EdgeJobs" 的資料夾 (如果該資料夾不存在)。 針對每個部署，會在 "EdgeJobs" 資料夾中建立新的子資料夾。
> 當您將作業部署到 IoT Edge 裝置時，ASA 會建立作業定義檔的共用存取簽章 (SAS)。 SAS 索引鍵會使用裝置對應項安全地傳輸至 IoT Edge 裝置。 這個索引鍵的到期日是從其建立起的三年。 當您更新 IoT Edge 作業時，SAS 將會變更，但映像版本則不會變更。 在 **更新** 後，請遵循部署工作流程，並在裝置上記錄更新通知。


如需 IoT Edge 部署的詳細資訊，請參閱[此頁面](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring)。


##### <a name="configure-routes"></a>設定路由
IoT Edge 提供方法以宣告方式在模組之間及模組與 IoT 中樞之間路由訊息。 [這裡](https://docs.microsoft.com/azure/iot-edge/module-composition)說明完整語法。
ASA 作業中建立的輸入和輸出名稱可用來作為路由的端點。  

###### <a name="example"></a>範例

```json
{
    "routes": {
        "sensorToAsa":   "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/ASA/inputs/temperature\")",
        "alertsToCloud": "FROM /messages/modules/ASA/* INTO $upstream",
        "alertsToReset": "FROM /messages/modules/ASA/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")"
    }
}

```
此範例會顯示下圖中所述情節的路由。 它包含名為 " **ASA** " 的 Edge 作業，以及名為 " **temperature** " 的輸入和名為 " **alert** " 的輸出。
![訊息路由的圖表定義](media/stream-analytics-edge/edge-message-routing-example.png)

這個範例會定義下列路由：
- 來自 **tempSensor** 的每個訊息都會傳送至名為 **ASA** 的模組到名為 **temperature** 的輸入，
- 所有 **ASA** 模組的輸出都會傳送至連結到此裝置 ($upstream) 的 IoT 中樞，
- 所有 **ASA** 模組的輸出都會傳送至 **tempSensor** 的 **控制項** 端點。


## <a name="technical-information"></a>技術資訊
### <a name="current-limitations-for-iot-edge-jobs-compared-to-cloud-jobs"></a>相較於雲端作業，IoT Edge 作業的目前限制
目標是在 IoT Edge 與業和雲端作業之間進行同位檢查。 支援大部份的 SQL 查詢語言功能，使其讓能夠在雲端和 IoT Edge 上執行相同的邏輯。
不過，Edge 作業尚未支援下列功能：
* JavaScript 中的使用者定義函式 (UDF)。 您可以在[適用於 IoT Edge 的 C# 作業](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge-csharp-udf) (預覽版) 中找到 UDF。
* 使用者定義彙總 (UDA)。
* Azure ML 函式。
* 在單一步驟中使用超過 14 個彙總。
* 輸入/輸出的 AVRO 格式。 目前僅支援 CSV 和 JSON。
* 下列 SQL 運算子：
    * PARTITION BY
    * GetMetadataPropertyValue
* 延遲傳入原則

### <a name="runtime-and-hardware-requirements"></a>執行階段與硬體需求
若要在 IoT Edge 上執行 ASA，您需要可以執行 [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) 的裝置。 

ASA 和 Azure IoT Edge 使用 **Docker** 容器來提供可攜式解決方案，這些解決方案能在多個主機作業系統 (Windows、Linux) 上執行。

IoT Edge 上的 ASA 可供作為 Windows 和 Linux 映像，能在 x86-64 或 ARM (進階 RISC 機器) 架構上執行。 


### <a name="input-and-output"></a>輸入和輸出
#### <a name="input-and-output-streams"></a>輸入和輸出資料流
ASA Edge 作業可以從 IoT Edge 裝置上執行的其他模組取得輸入和輸出。 若要連線進出特定模組，您可以在部署期間設定路由組態。 [IoT Edge 模組複合文件](https://docs.microsoft.com/azure/iot-edge/module-composition)中會說明詳細資訊。

輸入與輸出都支援 CSV 和 JSON 格式。

針對您在 ASA 作業中建立的每一個輸入和輸出資料流，在部署的模組上已建立對應的端點。 這些端點可用於部署的路由。

目前，唯一支援的串流輸入和串流輸出類型為 Edge 中樞。 參考輸入支援參考檔案類型。 其他輸出可以使用下游的雲端作業來觸達。 例如，裝載於 Edge 中的串流分析作業會將輸出傳送到 Edge 中樞，然後 Edge 中樞會將輸出傳送到 IoT 中樞。 您可以使用第二個雲端裝載的 Azure 串流分析作業，搭配來自 IoT 中樞的輸入和 Power BI 的輸出或其他輸出類型。



##### <a name="reference-data"></a>參考資料
參考資料 (也稱為查詢資料表) 基本上是靜態或不常變更的有限資料集。 可用來執行查閱或與資料流相互關聯。 若要使用 Azure 串流分析作業中的參考資料，您通常會在查詢中使用[參考資料聯結](https://docs.microsoft.com/stream-analytics-query/reference-data-join-azure-stream-analytics)。 如需詳細資訊，請參閱[使用參考資料在串流分析中進行查閱](stream-analytics-use-reference-data.md)。

目前僅支援本機參考資料。 當作業部署到 IoT Edge 裝置時，它會從使用者定義的檔案路徑載入參考資料。

若要使用 Edge 上的參考資料建立作業：

1. 建立作業的新輸入。

2. 選擇 [參考資料] 作為 [來源類型]。

3. 在裝置上備妥參考資料檔案。 針對 Windows 容器，請將參考資料檔案放在本機磁碟機上，並將本機磁碟機與 Docker 容器共用。 針對 Linux 容器，請建立 Docker 磁碟區，並在磁碟區上填入資料檔案。

4. 設定檔案路徑。 針對 Windows 主機 OS 與 Windows 容器，使用絕對路徑：`E:\<PathToFile>\v1.csv`。 針對 Windows 主機 OS 與 Linux 容器或 Linux OS 與 Linux 容器，使用磁碟區中的路徑：`<VolumeName>/file1.txt`。

![IoT Edge 上的 Azure 串流分析作業的新參考資料輸入](./media/stream-analytics-edge/Reference-Data-New-Input.png)

IoT Edge 上的參考資料更新會由部署觸發。 觸發之後，ASA 模組會挑選更新的資料，且不會停止執行中的作業。

有兩種方式可更新參考資料：
* 使用 Azure 入口網站更新您 ASA 作業中的參考資料路徑。
* 更新 IoT Edge 部署。

## <a name="license-and-third-party-notices"></a>授權和第三方通知
* [Azure IoT Edge 串流分析授權](https://go.microsoft.com/fwlink/?linkid=862827)。 
* [Azure IoT Edge 串流分析的第三方通知](https://go.microsoft.com/fwlink/?linkid=862828)。

## <a name="azure-stream-analytics-module-image-information"></a>建立 Azure 串流分析模組映像資訊 

此版本資訊上次更新日期為 2019 年 6 月 27日：

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
如需進一步的協助，請嘗試 [Azure 串流分析的 Microsoft 問與答頁面](https://docs.microsoft.com/answers/topics/azure-stream-analytics.html)。

## <a name="next-steps"></a>後續步驟

* [Azure Iot Edge 的詳細資訊](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)
* [ASA IoT Edge 教學課程](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [使用 Visual Studio 工具來開發串流分析 Edge 作業](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)
* [使用 API 實作適用於串流分析的 CI/CD](stream-analytics-cicd-api.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
