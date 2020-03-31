---
title: 預測性維護解決方案加速器概觀 - Azure | Microsoft Docs
description: Azure IoT 預測性維護解決方案加速器概述，用於預測業務方案可能發生故障的點。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 0661503dce7ac2707065f60c3952da866ce9dcf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73827421"
---
# <a name="predictive-maintenance-solution-accelerator-overview"></a>預測性維護解決方案加速器概觀

預測性維護解決方案加速器是一個端對端解決方案，適用於預測可能發生失敗之時間點的商務案例。 您可以針對最佳化維護等活動，主動使用此解決方案加速器。 此解決方案結合了主要 Azure IoT 解決方案加速器服務，例如 IoT 中樞和 [Azure Machine Learning][lnk-machine-learning] 工作區。 此工作區包含以公開範例資料集為基礎的模型，用來預測飛機引擎的剩餘使用年限 (RUL)。 此解決方案完整提供 IoT 商務案例的實作作為起點，讓您規劃和實作能滿足特定商務需求的解決方案。

[GitHub](https://github.com/Azure/azure-iot-predictive-maintenance) 有提供了預測性維護解決方案加速器程式碼。

## <a name="logical-architecture"></a>邏輯架構

下圖概述解決方案加速器的邏輯元件：

![邏輯架構][img-architecture]

藍色項目是在您佈建解決方案加速器的區域中所佈建的 Azure 服務。 您可以在其中部署解決方案加速器的地區清單會顯示於[佈建頁面][lnk-azureiotsolutions]。

綠色項目是模擬的飛機引擎。 您可以在[模擬裝置](#simulated-devices)一節中進一步了解這些模擬裝置。

灰色項目是可實作「裝置管理」** 功能的元件。 目前的預測性維護解決方案加速器版本不會佈建這些資源。 若要深入了解裝置管理，請參閱[遠端監視解決方案加速器][lnk-remote-monitoring]。

## <a name="azure-resources"></a>Azure 資源

在 Azure 入口網站中，瀏覽至具有您所選之解決方案名稱的資源群組以檢視已佈建的資源。

![加速器資源][img-resource-group]

當您佈建解決方案加速器時，您會收到一封電子郵件，其中包含機器學習服務工作區的連結。 您也可以從 [Microsoft Azure IoT 解決方案加速器][lnk-azureiotsolutions]頁面瀏覽至 Machine Learning 工作區。 當解決方案處於**就緒**狀態時，此頁面上會出現一個圖格。

![機器學習服務模型][img-machine-learning]

## <a name="simulated-devices"></a>模擬的裝置

在解決方案加速器中，模擬的裝置是飛機引擎。 此解決方案已佈建兩個對應至單一飛機的引擎。 每個引擎會發出四種類型的遙測：感應器 9、感應器 11、感應器 14 和感應器 15 會提供 Machine Learning 模型來計算該引擎的 RUL 所需的資料。 每個模擬的裝置會將下列遙測訊息傳送至 IoT 中樞：

*週期計數*。 週期是已完成持續期間介於 2 小時與 10 小時之間的飛行。 在飛行期間，每半小時擷取一次遙測資料。

*遙測*。 有四個可記錄引擎屬性的感應器。 這些感應器會一般會標示為感應器 9、感應器 11、感應器 14 和感應器 15。 這 4 個感應器會傳送足以從 RUL 模型取得有用結果的遙測。 用於解決方案加速器中的模型是根據包含實際引擎感應器資料的公用資料集建立而來。 如需有關如何從原始資料集建立模型的詳細資訊，請參閱 [Cortana Intelligence Gallery 預測性維護範本][lnk-cortana-analytics]。

模擬的裝置可以處理從解決方案中 IoT 中樞傳送的下列命令：

| Command | 描述 |
| --- | --- |
| StartTelemetry |控制模擬的狀態。<br/>傳送遙測以啟動裝置 |
| StopTelemetry |控制模擬的狀態。<br/>傳送遙測以停止裝置 |

IoT 中樞會提供裝置命令通知。

## <a name="azure-stream-analytics-job"></a>Azure 串流分析作業

**作業：遙測**使用兩個語句對傳入設備遙測流進行操作：

* 第一個陳述式會從裝置選取所有遙測資料，然後將此資料傳送至 bob 儲存體。 從這裡，它會呈現在 Web 應用程式中。
* 第二個陳述式會透過兩分鐘的滑動視窗計算感應器平均值，然後透過事件中樞將此資料傳送至**事件處理器**。

## <a name="event-processor"></a>事件處理器
**事件處理器主機**會在 Azure Web 作業中執行。 **事件處理器** 需要一個完整的週期來處理平均感應器值。 接著將這些值傳遞至定型的模型，以計算引擎的 RUL。 API 可供存取 Machine Learning 工作區中屬於解決方案一部分的模型。

## <a name="machine-learning"></a>Machine Learning
Machine Learning 元件使用的模型衍生自從真實飛機引擎收集而來的資料。 您可以在[azureiotsolutions.com][lnk-azureiotsolutions]頁面上的解決方案磁貼導航到機器學習工作區。 當解決方案處於**就緒**狀態時，就會出現此圖格。

Azure Machine Learning 模型可以作為範本，示範如何運用透過 IoT 解決方案加速器服務收集而來的遙測資料。 Microsoft 已根據公開可用的資料<sup>1</sup>，以及如何使用模型的逐步指引，建置了一套飛機引擎的[迴歸模型\[\]][lnk_regression_model]。

Azure IoT 預測性維護解決方案加速器會利用從這個範本建立的迴歸模型。 此模型會部署到您的 Azure 訂用帳戶，並透過自動產生的 API 提供。 此解決方案包含 4 部 (全部共 100 部) 引擎測試資料與 4 個 (全部共 21 個) 感應器資料流的子集。 此資料足以從定型的模型提供精確的結果。

*\[1\] A. 薩克塞納和K.戈貝爾（2008年）。"渦輪風扇發動機降解類比資料集"，美國宇航局艾梅斯預測資料存儲庫（，https://c3.nasa.gov/dashlink/resources/139/)美國宇航局艾姆斯研究中心，莫菲特菲爾德，加利福尼亞州*

## <a name="next-steps"></a>後續步驟
您現在已看到預測性維護解決方案加速器的主要元件，您可加以自訂。

您也可以探索 IoT 解決方案加速器的一些其他功能：

* [IoT 解決方案加速器的常見問題集][lnk-faq]
* [從頭建立 IoT 安全性][lnk-security-groundup]

[img-architecture]: media/iot-accelerators-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-accelerators-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-accelerators-predictive-walkthrough/machine-learning.png

[lnk-remote-monitoring]: quickstart-predictive-maintenance-deploy.md
[lnk-cortana-analytics]: https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsolutions]: https://www.azureiotsolutions.com/
[lnk-faq]: iot-accelerators-faq.md
[lnk-security-groundup]:/azure/iot-fundamentals/iot-security-ground-up
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/
[lnk_regression_model]: https://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
