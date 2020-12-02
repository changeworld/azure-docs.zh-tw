---
title: 教學課程：產生模擬裝置資料 - Azure IoT Edge 上的 Machine Learning
description: 教學課程 - 建立能產生模擬遙測的虛擬裝置，以稍後使用這些資料來對機器學習模型進行定型。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/20/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ef6e86bbb55a2c51779f2dcf60f802ce549516cb
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94959674"
---
# <a name="tutorial-generate-simulated-device-data"></a>教學課程：產生模擬裝置資料

在本文中，我們會使用機器學習定型資料來模擬將遙測傳送至 Azure IoT 中樞的裝置。 如簡介中所述，本教學課程會使用[渦輪風扇引擎降級模擬資料集](https://c3.nasa.gov/dashlink/resources/139/)來模擬來自一組飛機引擎的資料，以進行定型和測試。

在我們的實驗性情節中，我們知道：

* 資料包含多個多變量的時間序列。
* 每個資料集分別分割為定型和測試子集。
* 每個時間序列分別來自不同的引擎。
* 每個引擎在一開始分別具有不同程度的初始損耗和製造變化。

針對此教學課程，我們會使用單一資料集 (FD003) 的已定型資料子集。

在現實中，每個引擎都會是獨立的 IoT 裝置。 我們認為您應該沒有已連線至網際網路的一系列渦輪風扇引擎可供使用，因此我們將會針對這些裝置建置軟體的替代品。

模擬器是 C# 程式，其會使用 IoT 中樞 API 來以程式設計方式向 IoT 中樞註冊虛擬裝置。 我們接著會從由 NASA 所提供的資料子集讀取每個裝置的資料，並使用模擬 IoT 裝置將它傳送至您的 IoT 中樞。 這部分教學課程的所有程式碼都可以在存放庫的 DeviceHarness 目錄中找到。

DeviceHarness 專案是以 C# 撰寫的 .NET Core 專案，並包含四個類別：

* **Program：** 負責處理使用者輸入和整體協調之執行的進入點。
* **TrainingFileManager：** 負責讀取及剖析選取的資料檔案。
* **CycleData：** 代表檔案中轉換為訊息格式的單一資料列。
* **TurbofanDevice：** 負責建立與資料中的單一裝置 (時間序列) 相對應的 IoT 裝置，並將資料傳輸至 IoT 中樞。

完成此文章中所描述的工作所需花費的時間大約是 20 分鐘。

在真實世界中與本步驟中內容相對應的工作，通常會由裝置開發人員或雲端開發人員執行。

## <a name="prerequisites"></a>必要條件

此文章是關於在 IoT Edge 上使用 Azure Machine Learning 的系列文章之一。 本系列中的每篇文章皆以先前文章中的工作為基礎。 如果您是被直接引導至此文章，請參閱本系列中的[第一篇文章](tutorial-machine-learning-edge-01-intro.md)。

## <a name="configure-visual-studio-code-and-build-deviceharness-project"></a>設定 Visual Studio Code 並建置 DeviceHarness 專案

1. 開啟開發 VM 的遠端桌面工作階段。

1. 在 Visual Studio Code 中，開啟 `C:\source\IoTEdgeAndMlSample\DeviceHarness` 資料夾。

1. 由於您是第一次在這部電腦上使用擴充，某些擴充將會更新並安裝其相依性。 系統可能會提示您更新擴充。 若是如此，請選取 [重新載入視窗]  。

   如果輸出視窗中出現 OmniSharp 錯誤，您就必須將 C# 擴充功能解除安裝。

1. 系統將會提示您為 DeviceHarness 加入所需的資產。 選取 [是]  以加入它們。

   * 該通知可能需要幾秒才會出現。
   * 如果您錯過此通知，請按一下右下角的鈴鐺圖示。

   ![VS Code 擴充功能快顯視窗](media/tutorial-machine-learning-edge-03-generate-data/add-required-assets.png)

1. 選取 [還原]  來還原套件相依性。

   ![VS Code [還原] 提示](media/tutorial-machine-learning-edge-03-generate-data/restore-package-dependencies.png)

   若未收到這些通知，請關閉 Visual Studio Code，並刪除 `C:\source\IoTEdgeAndMlSample\DeviceHarness` 中的 bin 和 obj 目錄，再開啟 Visual Studio Code，然後重新開啟 DeviceHarness 資料夾。

1. 藉由觸發建置 (使用 **Ctrl** + **Shift** + **B** 或 [終端機]   > [執行建置工作]  )，確認您的環境是否已正確設定。

1. 系統會提示您選取要執行的建置工作。 選取 [組建]  。

1. 建置會執行並輸出成功訊息。

   ![建置成功輸出訊息](media/tutorial-machine-learning-edge-03-generate-data/build-success.png)

1. 您可以將此建置設定為預設的建置工作，方法是選取 [終端機]   > [設定預設建置工作]  ，然後從提示中選擇 [建置]  。

## <a name="connect-to-iot-hub-and-run-deviceharness"></a>連線至 IoT 中樞並執行 DeviceHarness

建置專案之後，請連線至您的 IoT 中樞以存取連接字串，並監視資料產生的進度。

### <a name="sign-in-to-azure-in-visual-studio-code"></a>在 Visual Studio Code 中登入 Azure

1. 開啟命令選擇區 (`Ctrl + Shift + P` 或 [檢視]   > [命令選擇區]  )，在 Visual Studio Code 中登入您的 Azure 訂用帳戶。

1. 搜尋 [Azure:  登入] 命令。

   瀏覽器視窗隨即開啟，並提示您提供認證。 當系統將您重新導向至成功頁面之後，您便可以關閉瀏覽器。

### <a name="connect-to-your-iot-hub-and-retrieve-hub-connection-string"></a>連線至 IoT 中樞並擷取中樞連接字串

1. 在 Visual Studio Code 總管的底部區段中，選取 [Azure IoT 中樞]  框架加以展開。

1. 再展開的框架中，按一下 [選取 IoT 中樞]  。

1. 當系統提示時，選取您的 Azure 訂用帳戶，然後選取您的 IoT 中樞。

1. 按一下 [Azure IoT 中樞]  右側的 [...]  ，以取得更多動作。 選取 [複製 IoT 中樞連接字串]  。

   ![複製 IoT 中樞連接字串](media/tutorial-machine-learning-edge-03-generate-data/copy-hub-connection-string.png)

### <a name="run-the-deviceharness-project"></a>執行 DeviceHarness 專案

1. 選取 [檢視]   > [終端機]  以開啟 Visual Studio Code 終端機。

   若未看到提示，請按 Enter 鍵。

1. 在終端機中輸入 `dotnet run`。

1. 當系統提示您提供 IoT 中樞連接字串時，請貼上在上一節中所複製的連接字串。

1. 在 [Azure IoT 中樞裝置]  框架中，按一下重新整理按鈕。

   ![重新整理 IoT 中樞裝置清單](media/tutorial-machine-learning-edge-03-generate-data/refresh-hub-device-list.png)

1. 請注意被加入 IoT 中樞的裝置，且那些裝置會顯示為綠色以指出有資料正從該裝置傳送過來。 裝置將訊息傳送至 IoT 中樞之後，裝置會中斷連線，並呈現為藍色。

1. 您可以檢視傳送至中樞的訊息，方法是以滑鼠右鍵按一下任何裝置，然後選取 [開始監視內建事件端點]  。 訊息將會出現在 Visual Studio Code 的輸出窗格中。

1. 若要停止監視，請按一下 [Azure IoT 中樞]  輸出窗格，並選擇 [停止監視內建事件端點]  。

1. 讓應用程式執行完成；這需要花費幾分鐘的時間。

## <a name="check-iot-hub-for-activity"></a>檢查 IoT 中樞中的活動

由 DeviceHarness 傳送的資料會送至您的 IoT 中樞，您可以在 Azure 入口網站中加以驗證。

1. 開啟 [Azure 入口網站](https://portal.azure.com/)，然後瀏覽至先前為本教學課程建立的 IoT 中樞。

1. 從左窗格功能表的 [監視]  底下，選取 [計量]  。

1. 在 [圖表定義] 頁面上，按一下 [計量]  下拉式清單，向下捲動清單，然後選取 [路由：傳遞至儲存體的資料]  。 此圖表應該會顯示資料路由至儲存體的尖峰時間。

   ![圖表顯示資料傳遞至儲存體的尖峰時間](media/tutorial-machine-learning-edge-03-generate-data/iot-hub-usage.png)

## <a name="validate-data-in-azure-storage"></a>在 Azure 儲存體中驗證資料

我們剛剛傳送到 IoT 中樞的資料，是被路由至我們在上一篇文章中所建立的儲存體容器中。 讓我們來看看這些位於儲存體帳戶中的資料。

1. 在 Azure 入口網站中，瀏覽至您的儲存體帳戶。

1. 從儲存體帳戶導覽器，選取 [儲存體總管 (預覽)]  。

1. 在儲存體總管中選取 [Blob 容器]  ，然後選取 `devicedata`。

1. 在內容窗格中，按一下具有 IoT 中樞名稱的資料夾，然後依序按一下年、月、日及小時的資料夾。 您將會看見代表資料寫入時間之分鐘的數個資料夾。

   ![檢視 Blob 儲存體中的資料夾](media/tutorial-machine-learning-edge-03-generate-data/confirm-data-storage-results.png)

1. 按一下其中一個資料夾，以找出標記為 **00** 和 **01** (對應至分割區) 的資料檔案。

1. 檔案會以 [Avro](https://avro.apache.org/) 格式寫入。 按兩下其中一個檔案會開啟另一個瀏覽器索引標籤，並轉譯部分資料。 如果系統提示您以某個程式開啟該檔案，您可以選擇 VS Code，如此即可正確轉譯。

1. 您目前還不需要嘗試讀取或解譯這些資料；我們將會在下一篇文章中這麼做。

## <a name="next-steps"></a>後續步驟

在本文中，我們已使用 .NET Core 專案建立一組虛擬 IoT 裝置，並透過這些裝置將資料傳送至 IoT 中樞和 Azure 儲存體容器中。 此專案模擬了真實的案例，也就是讓實體 IoT 裝置將資料傳送至 IoT 中樞，進而傳送至準備好的儲存體中。 這項資料包含感應器讀數、操作設定、失敗信號和模式等等。 收集到足夠的資料之後，我們會用這項資料對模型進行定型，使該模型能預測該裝置的剩餘使用年限 (RUL)。 我們將在下一篇文章中示範這項機器學習。

請前往下一篇文章以使用資料對機器學習模型進行定型。

> [!div class="nextstepaction"]
> [定型和部署 Azure Machine Learning 模型](tutorial-machine-learning-edge-04-train-model.md)
