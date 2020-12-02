---
title: 教學課程：設定環境 - Azure IoT Edge 上的 Machine Learning
description: 教學課程：針對用來在邊緣進行機器學習之模組的開發和部署準備您的環境。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/12/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3a3518ba68c9474fc4a34390e6fd9a7d1e88f6c6
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94959589"
---
# <a name="tutorial-set-up-an-environment-for-machine-learning-on-iot-edge"></a>教學課程：在 IoT Edge 上為機器學習設定環境

本文將協助您準備開發和部署環境。 首先，請使用所需的所有工具設定開發機器。 接下來，在 Azure 中建立必要的雲端資源。

## <a name="prerequisites"></a>必要條件

此文章是關於在 IoT Edge 上使用 Azure Machine Learning 的系列文章之一。 本系列中的每篇文章皆以先前文章中的工作為基礎。 如果您是被直接引導至此文章，請參閱本系列中的[第一篇文章](tutorial-machine-learning-edge-01-intro.md)。

## <a name="set-up-the-development-vm"></a>設定開發 VM

此步驟通常是由雲端開發人員來執行。 某一部分的軟體可能也會對資料科學家有所幫助。

我們已建立一個 PowerShell 指令碼，此指令碼會建立已設定許多必要項目的 Azure 虛擬機器。 我們建立的 VM 必須能處理[巢狀虛擬化](../virtual-machines/windows/nested-virtualization.md)，這也是我們選擇 [Standard_D8s_v3](../virtual-machines/dv3-dsv3-series.md) 電腦大小的原因。

開發 VM 將會搭配下列項目進行設定：

* Windows 10
* [Chocolatey](https://chocolatey.org/) \(英文\)
* [適用於 Windows 的 Docker Desktop](https://www.docker.com/products/docker-desktop) \(英文\)
* [Git for Windows](https://gitforwindows.org/)
* [適用於 Windows 的 Git 認證管理員](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) \(英文\)
* [.NET Core SDK](https://dotnet.microsoft.com/)
* [Python 3](https://www.python.org/) \(英文\)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure PowerShell](/powershell/azure/?view=azps-1.1.0)
* [VS Code 擴充](https://marketplace.visualstudio.com/search?target=VSCode) \(英文\)
  * [Azure IoT 工具組](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  * [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
  * [PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

開發人員 VM 並非絕對必要，因為所有的開發工具都可以在本機電腦上執行。 不過，我們強烈建議您使用 VM 來確保環境能保持一致。

建立及設定虛擬機器只需要大約 30 分鐘的時間。

1. 將[機器學習和 IoT Edge](https://github.com/Azure-Samples/IoTEdgeAndMlSample) 範例存放庫複製或下載到您的本機電腦。

1. 以系統管理員身分開啟 PowerShell，並瀏覽至您下載程式碼的根目錄下包含的 **\IoTEdgeAndMlSample\DevVM** 目錄。 我們會將來源的根目錄稱為 `srcdir`。

    ```powershell
    cd c:\srcdir\IoTEdgeAndMlSample\DevVM
    ```

   DevVM 目錄包含建立適合完成此教學課程之 Azure 虛擬機器的所需檔案。

1. 執行下列命令來允許執行指令碼。 出現提示時，請選擇 [全部皆是]。

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. 執行 Create-AzureDevVM.ps1。

    ```powershell
    .\Create-AzureDevVm.ps1
    ```

    出現提示時，請提供下列資訊：

    * **Azure 訂用帳戶識別碼**：您的訂用帳戶識別碼 (可在入口網站中的 [Azure 訂用帳戶](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)內找到)。
    * **資源群組名稱**：Azure 中新的或現有資源群組的名稱。
    * **位置**：選擇要建立虛擬機器的 Azure 位置。 例如，「美國西部 2」或「北歐」。 如需詳細資訊，請參閱 [Azure 的地點](https://azure.microsoft.com/global-infrastructure/locations/)。
    * **使用者名稱**：為 VM 的系統管理員帳戶提供易記的名稱。
    * **密碼**：設定 VM 系統管理員帳戶的密碼。

   指令碼會執行下列步驟，這需要花費數分鐘的時間：

    1. 安裝 [Azure PowerShell Az 模組](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)。
    1. 提示您登入 Azure。
    1. 確認建立 VM 的資訊。 按 **y** 或 **Enter** 鍵以繼續作業。
    1. 建立資源群組 (若尚不存在)。
    1. 部署虛擬機器。
    1. 在 VM 上啟用 Hyper-V。
    1. 安裝開發及複製範例存放庫所需的軟體。
    1. 重新啟動 VM。
    1. 在桌面上建立 RDP 檔案以連線至 VM。

   如果系統提示您輸入 VM 的名稱以重新啟動 VM，您可以從指令碼輸出中複製其名稱。 輸出也會顯示用來連線至 VM 的 RDP 檔案路徑。

### <a name="set-auto-shutdown-schedule"></a>設定自動關機排程

為了協助您降低成本，在建立開發 VM 時，自動關機排程設定為 1900 PST。 您可能需要根據所在位置和排程更新此設定。 更新關機排程：

1. 在 Azure 入口網站中，瀏覽至指令碼所建立的 VM。

1. 從左窗格功能表的 [作業] 底下，選取 [自動關機]。

1. 視需要調整 [已排程關機] 和 [時區]，然後選取 [儲存]。

## <a name="connect-to-the-development-vm"></a>連線至開發 VM

在建立好 VM 之後，我們現在必須安裝完成此教學課程所需的軟體。

1. 按兩下指令碼在桌面上建立的 RDP 檔案。

1. 系統會顯示對話方塊，說明遠端連線的發行者為未知的發行者。 這是可接受的情況，因此請選取 [連線]。

1. 提供您輸入用來建立 VM 的系統管理員密碼，然後按一下 [確定]。

1. 系統會提示您接受 VM 的憑證。 選取 [是]。

## <a name="install-visual-studio-code-extensions"></a>安裝 Visual Studio Code 延伸模組

在您連線至開發電腦之後，請為 Visual Studio Code 加入一些有用的擴充，來使開發體驗變得更加輕鬆。

1. 連線至開發 VM，開啟 PowerShell 視窗，然後瀏覽至 **C:\source\IoTEdgeAndMlSample\DevVM** 目錄。 此目錄是由建立 VM 的指令碼所建立的。

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\DevVM
    ```

1. 執行下列命令來允許執行指令碼。 出現提示時，請選擇 [全部皆是]。

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. 執行 Visual Studio Code 擴充功能指令碼。

    ```powershell
    .\Enable-CodeExtensions.ps1
    ```

1. 指令碼將會執行幾分鐘的時間來安裝 VS Code 擴充：

    * Azure IoT Tools
    * Python
    * C#
    * Docker
    * PowerShell

## <a name="set-up-iot-hub-and-storage"></a>設定 IoT 中樞和儲存體

這些步驟通常是由雲端開發人員來執行。

Azure IoT 中樞會處理 IoT 裝置和雲端之間的安全通訊，因此可說是任何 IoT 應用程式的核心。 它是 IoT Edge 機器學習解決方案作業的主要協調點。

* IoT 中樞會使用路由將來自 IoT 裝置的傳入資料導向其他下游服務。 我們將利用 IoT 中樞路由，將裝置資料傳送至 Azure 儲存體。 在 Azure 儲存體中，Azure Machine Learning 會使用裝置資料對我們的剩餘使用年限 (RUL) 分類器進行定型。

* 稍後在教學課程中，我們將會使用 IoT 中樞來設定及管理 Azure IoT Edge 裝置。

在此節中，您會使用指令碼來建立 Azure IoT 中樞及 Azure 儲存體帳戶。 接著，在 Azure 入口網站中，您可以設定將中樞接收到的資料轉送至 Azure 儲存體容器的路由。 這些步驟需要約 10 分鐘來完成。

1. 連線至開發 VM，開啟 PowerShell 視窗，然後瀏覽至 **IoTHub** 目錄。

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\IoTHub
    ```

1. 執行建立指令碼。 使用和您在建立開發 VM 時相同的值來設定訂用帳戶識別碼、位置及資源群組。

    ```powershell
    .\New-HubAndStorage.ps1 -SubscriptionId <subscription id> -Location <location> -ResourceGroupName <resource group>
    ```

    * 系統將提示您登入 Azure。
    * 指令碼會確認建立中樞和儲存體帳戶的資訊。 按 **y** 或 **Enter** 鍵以繼續作業。

執行指令碼約需兩分鐘。 完成後，指令碼會輸出 IoT 中樞和儲存體帳戶的名稱。

## <a name="review-route-to-storage-in-iot-hub"></a>檢閱針對 IoT 中樞中儲存體的路由

作為建立 IoT 中樞的一部分，我們在上一節中所執行的指令碼也已建立自訂端點和路由。 IoT 中樞路由包含查詢運算式和端點。 如果訊息符合運算式，資料便會沿著路由被傳送至相關聯的端點。 端點可以是事件中樞、服務匯流排佇列及主題。 在此案例中，端點是儲存體帳戶中的 Blob 容器。 我們將使用 Azure 入口網站來檢閱由指令碼所建立的路由。

1. 開啟 [Azure 入口網站](https://portal.azure.com)，並移至您將在本教學課程中使用的資源群組。

1. 在資源清單中，選取指令碼所建立的 IoT 中樞。 其名稱會以隨機字元結尾，例如 `IotEdgeAndMlHub-jrujej6de6i7w`。

1. 從左窗格功能表的 [訊息] 底下，選取 [訊息路由]。

1. 在 [訊息路由] 頁面上，選取 [自訂端點] 索引標籤。

1. 展開 [儲存體] 區段：

   ![確認 turbofanDeviceStorage 位於自訂端點清單中](media/tutorial-machine-learning-edge-02-prepare-environment/custom-endpoints.png)

   我們看到 **turbofanDeviceStorage** 位於自訂端點清單中。 請留意此端點具有下列特性：

   * 它會指向您所建立的 Blob 儲存體容器 (其名稱為 `devicedata`，如 **容器名稱** 所示)。
   * 其 **檔案名稱格式** 會以分割區作為名稱中的最後一個元素。 我們認為這個格式對於我們稍後會在教學課程中搭配 Azure Notebooks 進行的檔案作業來說較為方便。
   * 其 [狀態] 應該是良好。

1. 選取 [路由] 索引標籤。

1. 選取名為 **turbofanDeviceDataToStorage** 的路由。

1. 在 [路由詳細資料] 頁面上您可以看到，路由的端點是 **turbofanDeviceStorage** 端點。

   ![檢閱 turbofanDeviceDataToStorage 路由的詳細資料](media/tutorial-machine-learning-edge-02-prepare-environment/route-details.png)

1. 查看 **路由查詢**，其已設定為 **true**。 此設定表示所有裝置遙測訊息都將符合此路由，這會使所有訊息都傳送至 **turbofanDeviceStorage** 端點。

1. 由於未進行任何編輯，請關閉此頁面即可。

## <a name="next-steps"></a>後續步驟

在此文章中，我們已建立 IoT 中樞，並設定針對 Azure 儲存體帳戶的路由。 接下來，我們將會透過 IoT 中樞將來自一組模擬裝置的資料傳送至儲存體帳戶。 稍後在教學課程中，當我們已設定 IoT Edge 裝置和模組之後，我們將會重新造訪路由，並進一步查看路由查詢。

如需這部分 IoT Edge 上的 Machine Learning 教學課程所涵蓋之步驟的詳細資訊，請參閱：

* [Azure IoT 基礎](../iot-fundamentals/index.yml)
* [使用 IoT 中樞設定訊息路由](../iot-hub/tutorial-routing.md)
* [使用 Azure 入口網站建立 IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)

請前往下一篇文章以建立模擬裝置以進行監視。

> [!div class="nextstepaction"]
> [產生裝置資料](tutorial-machine-learning-edge-03-generate-data.md)