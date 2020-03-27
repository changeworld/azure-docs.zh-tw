---
title: 教學課程：IoT Visual Alerts 範例
titleSuffix: Azure Cognitive Services
description: 在此教學課程中，您會搭配 IoT 裝置使用自訂視覺以辨識及回報來自相機影片摘要的視覺狀態。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 9f3802ada79ee87d1a04634f7caac3b1b4286dce
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "74978027"
---
# <a name="tutorial-use-custom-vision-with-an-iot-device-to-report-visual-states"></a>教學課程：搭配 IoT 裝置使用自訂視覺來回報視覺狀態。

此範例應用程式會說明如何使用自訂視覺來將具有相機的裝置定型以使偵測視覺狀態。 您可以使用從自訂視覺服務匯出的 ONNX 模型來在 IoT 裝置上執行此偵測案例。

視覺狀態會描述影像的內容：空的房間或是有人的房間、空的車道或是停著卡車的車道等等。 在下列影像中，您可以看到應用程式會在相機前放置香蕉或蘋果時做出正確偵測。

![標記相機前方水果之 UI 的動畫](./media/iot-visual-alerts-tutorial/scoring.gif)

此教學課程將為您示範如何：
> [!div class="checklist"]
> * 設定範例應用程式來使用您自己的自訂視覺和 IoT 中樞資源。
> * 使用應用程式來將您的自訂視覺專案定型。
> * 使用應用程式來即時為新影像評分，並將結果傳送到 Azure。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。 

## <a name="prerequisites"></a>Prerequisites

* [!INCLUDE [create-resources](includes/create-resources.md)]
    > [!IMPORTANT]
    > 此專案必須是 **Compact** 影像分類專案，因為我們稍後會將模型匯出至 ONNX。
* 您也需要在 Azure 上[建立 IoT 中樞資源](https://ms.portal.azure.com/#create/Microsoft.IotHub)。
* [Visual Studio 2015 或更新版本](https://www.visualstudio.com/downloads/)
* (選擇性) 執行 Windows 10 IoT 核心版 17763 版或更高版本的 IoT 裝置。 您也可以直接從您的電腦執行應用程式。
   * 針對 Raspberry Pi 2 和 3，您可以直接從 IoT 儀表板應用程式設定 Windows 10。 針對如 DrangonBoard 的其他裝置，您將必須使用 [eMMC 方法](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup#flashing-with-emmc-for-dragonboard-410c-other-qualcomm-devices)來對它進行刷新。 如果您需要設定新裝置的協助，請參閱 Windows IoT 文件中的[設定您的裝置](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup)。

## <a name="about-the-visual-alerts-app"></a>關於 Visual Alerts 應用程式

IoT Visual Alerts 應用程式會以連續迴圈的方式執行，並視需要在四個不同的狀態之間切換：

* **No Model** \(沒有模型\)：一種無作業狀態。 應用程式會持續睡眠一秒鐘，然後檢查相機。
* **Capturing Training Images** \(擷取定型影像\)：在此狀態中，應用程式會擷取圖片，並以定型影像的形式將它上傳到目標自訂視覺專案。 應用程式接著會睡眠 500 毫秒並重複作業，直到擷取到設定的目標影像數目為止。 然後它會觸發自訂視覺模型的定型。
* **Waiting For Trained Model** \(等候已定型的模型\)：在此狀態中，應用程式會於每秒呼叫自訂視覺 API，以檢查目標專案是否包含已定型的反覆項目。 當它找到時，便會將相對應的 ONNX 模型下載到本機檔案，並切換到 **Scoring** \(評分\) 狀態。
* **Scoring** \(評分\)：在此狀態中，應用程式會使用 Windows ML 來將來自相機的單一畫面針對本機 ONNX 模型進行評估。 產生的影像分類會顯示在畫面上，並以訊息的形式傳送到 IoT 中樞。 應用程式接著會睡眠一秒鐘，然後再對新影像進行評分。

## <a name="understand-the-code-structure"></a>了解程式碼結構

下列檔案會負責處理應用程式的主要功能。

| 檔案 | 描述 |
|-------------|-------------|
| [MainPage.xaml](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/MainPage.xaml) \(英文\) | 此檔案會定義 XAML 使用者介面。 它會裝載網路攝影機控制項，並包含用於狀態更新的標籤。|
| [MainPage.xaml.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/MainPage.xaml.cs) \(英文\) | 此程式碼會控制 XAML UI 的行為。 它包含狀態電腦的處理程式碼。|
| [CustomVision\CustomVisionServiceWrapper.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/CustomVision/CustomVisionServiceWrapper.cs) \(英文\) | 此類別是處理與自訂視覺服務之間整合的包裝函式。|
| [CustomVision\CustomVisionONNXModel.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/CustomVision/CustomVisionONNXModel.cs) \(英文\) | 此類別是處理與 Windows ML 之間的整合，以載入 ONNX 模型並針對它將影像評分的包裝函式。|
| [IoTHub\IotHubWrapper.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/IoTHub/IotHubWrapper.cs) \(英文\) | 此類別是處理與 IoT 中樞之間的整合，以將評分結果上傳到 Azure 的包裝函式。|

## <a name="set-up-the-visual-alerts-app"></a>設定 Visual Alerts 應用程式

遵循這些步驟來使 IoT Visual Alerts 應用程式在您的電腦或 IoT 裝置上執行。

1. 複製或下載 GitHub 上的 [IoTVisualAlerts 範例](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/tree/master/IoTVisualAlerts) \(英文\)。
1. 在 Visual Studio 中開啟 _IoTVisualAlerts.sln_ 解決方案
1. 整合您的自訂視覺專案：
    1. 在 _CustomVision\CustomVisionServiceWrapper.cs_ 指令碼中，以您的定型金鑰更新 `ApiKey` 變數。
    1. 然後以與您的金鑰相關聯的端點 URL 更新 `Endpoint` 變數。
    1. 以您想要使用之自訂視覺專案的相對應識別碼更新 `targetCVSProjectGuid` 變數。 
1. 設定 IoT 中樞資源：
    1. 在 _IoTHub\IotHubWrapper.cs_ 指令碼中，以適用於您裝置的適當連接字串更新 `s_connectionString` 變數。 
    1. 在 Azure 入口網站上，載入您的 IoT 中樞執行個體，按一下 [總管]  底下的 [IoT 裝置]  ，選取您的目標裝置 (或是需要建立一個)，然後在 [主要連接字串]  底下尋找連接字串。 該字串將會包含您的 IoT 中樞名稱、裝置識別碼及共用存取金鑰；它具有下列格式：`{your iot hub name}.azure-devices.net;DeviceId={your device id};SharedAccessKey={your access key}`。

## <a name="run-the-app"></a>執行應用程式

如果您是要在電腦上執行應用程式，請在 Visual Studio 中選取適用於目標裝置的 [本機電腦]  ，然後針對目標平台選取 [x64]  或 [x86]  。 然後按 F5 以執行程式。 應用程式應該會啟動，並顯示來自相機的即時摘要及狀態訊息。

如果您是要部署至具有 ARM 處理器的 IoT 裝置，您將必須選取 [ARM]  作為目標平台，並選取 [遠端電腦]  作為目標裝置。 出現提示時，請提供您裝置的 IP 位址 (它必須與您的電腦位於相同的網路)。 您可以在開啟裝置並將它連線至網路之後，從 Windows IoT 預設應用程式取得 IP 位址。 按 F5 以執行程式。

當您首次執行應用程式時，它將不會具有任何視覺狀態上的知識。 它將會顯示狀態訊息，表示沒有可用的模型。 

## <a name="capture-training-images"></a>擷取定型影像

若要設定模型，您必須將應用程式置於 **Capturing Training Images** \(擷取定型影像\) 狀態。 採取下列其中一個步驟：
* 如果您是在電腦上執行應用程式，請使用 UI 右上角的按鈕。
* 如果您是在 IoT 裝置上執行應用程式，請透過 IoT 中樞在裝置上呼叫 `EnterLearningMode` 方法。 您可以透過 Azure 入口網站上 IoT 中樞功能表中的裝置項目，或是使用類似 [IoT 中樞 Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) \(英文\) 的工具來呼叫它。
 
當應用程式進入 **Capturing Training Images** \(擷取定型影像\) 狀態時，它每秒將會擷取約兩個影像，直到抵達目標影像數目為止。 根據預設，這會是 30 個影像，但您可以將所需數目以引數的方式傳遞至 `EnterLearningMode` IoT 中樞方法來設定此參數。 

在應用程式正在擷取影像時，您必須以相機拍攝您想要偵測的視覺狀態類型 (例如，空的房間、有人的房間、空的桌子、有玩具卡車的桌子等等)。

## <a name="train-the-custom-vision-model"></a>對自訂視覺模型進行定型

在應用程式擷取完影像之後，它會上傳那些影像並切換到 **Waiting For Trained Model** \(等候已定型的模型\) 狀態。 此時，您必須移至[自訂視覺入口網站](https://www.customvision.ai/) \(英文\) 並根據新的定型影像建置模型。 下列動畫會顯示此程序的範例。

![動畫：標記數個香蕉影像](./media/iot-visual-alerts-tutorial/labeling.gif)

若要以您自己的案例重複此程序：

1. 登入[自訂視覺入口網站](http://customvision.ai) \(英文\)。
1. 尋找您的目標專案，它現在應該會具有應用程式上傳的所有定型影像。
1. 針對您想要識別的每個視覺狀態，請選取適當的影像並手動套用標籤。
    * 例如，如果您的目標是要區分空的房間和有人的房間，我們建議標記五個或更多有人的影像作為新的類別 **People**，然後標記五個或更多沒有人的影像作為 **Negative** 標記。 這將能協助模型區分這兩種狀態。
    * 作為另一個範例，如果您的目標是要估計架子上已擺滿多少東西，則可以使用 **EmptyShelf**、**PartiallyFullShelf** 及 **FullShelf** 等標籤。
1. 完成之後，請選取 [定型]  按鈕。
1. 定型完成之後，應用程式將會偵測到有已定型的反覆項目可供使用。 它將會開始把已定型的模型匯出到 ONNX，然後將它下載到裝置。

## <a name="use-the-trained-model"></a>使用定型模型

在應用程式下載已定型的模型之後，它將會切換到 **Scoring** \(評分\) 狀態，並開始以連續迴圈的方式為來自相機的影像評分。

針對每個所擷取到的影像，應用程式將會在畫面上顯示名次最高的標籤。 如果它無法辨識視覺狀態，便會顯示**沒有相符項目**。 應用程式也會將這些訊息傳送到 IoT 中樞，且如果偵測到某個類別，該訊息將會包含標籤、信賴分數，以及稱為 `detectedClassAlert` 的屬性；該屬性可由想要根據屬性進行快速訊息路由傳送的 IoT 中樞用戶端使用。

此外，範例會使用 [Sense HAT 程式庫](https://github.com/emmellsoft/RPi.SenseHat) \(英文\) 來偵測它是否正在具有 Sense HAT 單元的 Raspberry Pi 上執行；這能使它可以利用該單元作為輸出顯示，並在偵測到類別時將所有燈設定為紅色，以及在未偵測到任何類別時關閉所有燈。

## <a name="reuse-the-app"></a>重複使用應用程式

如果您想要將應用程式重設為其原始狀態，您可以按一下 UI 右上角的按鈕，或是透過 IoT 中樞叫用 `DeleteCurrentModel` 方法。

在任何時候，您都可以再次按一下 UI 右上角的按鈕或是呼叫 `EnterLearningMode` 方法，來重複上傳定型影像的步驟。

如果您正在裝置上執行應用程式，並需要再次擷取 IP 位址 (例如，以用來透過 [Windows IoT Remote Client](https://www.microsoft.com/p/windows-iot-remote-client/9nblggh5mnxz#activetab=pivot:overviewtab) \(英文\) 建立遠端連線)，您可以透過 IoT 中樞呼叫 `GetIpAddress` 方法。

## <a name="clean-up-resources"></a>清除資源

如果您不想要繼續維護自訂視覺專案，請刪除它。 在[自訂視覺網站](https://customvision.ai) \(英文\) 上，瀏覽至 [Projects]  \(專案\)，然後選取您新專案下方的資源回收筒。

![螢幕擷取畫面，內有標示為 [我的新專案] 的面板，並有 [資源回收筒] 圖示](./media/csharp-tutorial/delete_project.png)

## <a name="next-steps"></a>後續步驟

在此教學課程中，您已設定並執行能在 IoT 裝置上偵測視覺狀態資訊，並將結果傳送到 IoT 中樞的應用程式。 接下來，請進一步探索原始程式碼，或是進行下列其中一個建議的修改。

> [!div class="nextstepaction"]
> [IoTVisualAlerts 範例 (GitHub)](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/tree/master/IoTVisualAlerts) \(英文\)

* 新增 IoT 中樞方法以將應用程式直接切換到**等候已定型的模型**狀態。 如此一來，您便可以使用不是由裝置本身所擷取的影像來對模型進行定型，然後透過命令將新模型推送到裝置。
* 遵循[將即時感應器資料視覺化](https://docs.microsoft.com/azure/iot-hub/iot-hub-live-data-visualization-in-power-bi) \(部分機器翻譯\) 教學課程以建立 Power BI 儀表板，來將由範例所傳送的 IoT 中樞警示視覺化。
* 遵循 [IoT 遠端監視](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps) \(部分機器翻譯\) 教學課程以建立能在偵測到視覺狀態時回應 IoT 中樞警示的邏輯應用程式。
