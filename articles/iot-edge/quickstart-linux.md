---
title: 快速入門：在 Linux 上建立 Azure IoT Edge 裝置 | Microsoft Docs
description: 在本快速入門中，了解如何在 Linux 上建立 IoT Edge 裝置，然後從 Azure 入口網站遠端部署預先建置的程式碼。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/30/2020
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: bbed5899a860f3ba8ac7047a283966536f6c8a87
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91328610"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-virtual-linux-device"></a>快速入門：將您的第一個 IoT Edge 模組部署至虛擬 Linux 裝置

藉由將容器化程式碼部署至虛擬 Linux IoT Edge 裝置，在本快速入門中測試 Azure IoT Edge。 IoT Edge 可讓您從遠端管理裝置上的程式碼，以便可以將更多的工作負載傳送到邊緣。 在本快速入門中，建議您對 IoT Edge 裝置使用 Azure 虛擬機器，以便您可以快速建立已安裝 IoT Edge 服務的測試機器，然後在完成時將其刪除。

在此快速入門中，您將了解如何：
> [!div class="checklist"]
>
> * 建立 IoT 中樞。
> * 向 IoT 中樞註冊 IoT Edge 裝置。
> * 在虛擬裝置上安裝並啟動 IoT Edge 執行階段。
> * 將模組從遠端部署到 IoT Edge 裝置。

![圖表 - 裝置和雲端的快速入門架構](./media/quickstart-linux/install-edge-full.png)

本快速入門將引導您建立設定為 IoT Edge 裝置的 Linux 虛擬機器。 接著，您可以從 Azure 入口網站將模組部署至裝置。 本快速入門中使用的模組是一個模擬感應器，會產生溫度、溼度和壓力資料。 其他 Azure IoT Edge 教學課程會以您在此所做的工作為基礎，部署其他模組來分析模擬資料以產生商業見解。

如果您沒有使用中的 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

您可以使用 Azure CLI 來完成本快速入門中的許多步驟，而且 Azure IoT 有擴充功能可供啟用其他功能。

將 Azure IoT 擴充功能新增至 Cloud Shell 執行個體。

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="prerequisites"></a>必要條件

雲端資源：

* 一個資源群組，用以管理本快速入門中使用的所有資源。 在本快速入門及之後的教學課程中，我們會使用名為 **IoTEdgeResources** 的範例資源群組。

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

使用 Azure CLI 建立 IoT 中樞，開始進行此快速入門。

![圖表 - 在雲端建立 IoT 中樞](./media/quickstart-linux/create-iot-hub.png)

此快速入門適用於 IoT 中樞的免費層級。 如果您在過去已使用過 IoT 中樞，並已建立中樞，您可以使用該 IoT 中樞。

下列程式碼會在資源群組 **IoTEdgeResources** 中建立免費的 **F1** 中樞。 以 IoT 中樞的唯一名稱取代 `{hub_name}`。 建立 IoT 中樞可能需要幾分鐘的時間。

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
   ```

   如果因您的訂用帳戶中已有免費中樞而發生錯誤，請將 SKU 變更為 **S1**。 每個訂用帳戶只能有一個免費的 IoT 中樞。 如果您收到無法使用 IoT 中樞名稱的錯誤，則表示其他人已經有該名稱的中樞。 請嘗試新的名稱。

## <a name="register-an-iot-edge-device"></a>註冊 IoT Edge 裝置

向新建立的 IoT 中樞註冊 IoT Edge 裝置。

![圖表 - 使用 IoT 中樞身分識別註冊裝置](./media/quickstart-linux/register-device.png)

建立 IoT Edge 裝置的裝置身分識別，使它能與 IoT 中樞通訊。 裝置身分識別存在於雲端，您可以使用唯一的裝置連接字串，讓實體裝置與裝置身分識別建立關聯。

由於 IoT Edge 裝置的行為和管理方式不同於典型 IoT 裝置，請使用 `--edge-enabled` 旗標將此身分識別宣告為 IoT Edge 裝置。

1. 在 Azure Cloud Shell 中輸入下列命令，以在中樞內建立名為 **myEdgeDevice** 的裝置。

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
   ```

   如果您收到有關 iothubowner 原則金鑰的錯誤，請確定 Cloud Shell 正在執行最新版的 azure-iot 擴充功能。

2. 檢視裝置的連接字串，此字串將作為連結實體裝置與其在 IoT 中樞內的身分識別。 其包含 IoT 中樞的名稱、您的裝置名稱，以及用來驗證兩者間連線的共用金鑰。 當您在下一節中設定 IoT Edge 裝置時，我們將再次參考此連接字串。

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
   ```

   ![從 CLI 輸出中檢視連接字串](./media/quickstart/retrieve-connection-string.png)

## <a name="configure-your-iot-edge-device"></a>設定您的 IoT Edge 裝置

建立其中具有 Azure IoT Edge 執行階段的虛擬機器。

![圖表 - 在裝置上啟動執行階段](./media/quickstart-linux/start-runtime.png)

IoT Edge 執行階段會在所有 IoT Edge 裝置上部署。 它有三個元件。 *IoT Edge 安全性精靈*會在每次 IoT Edge 裝置開機時啟動，並藉由啟動 IoT Edge 代理程式來啟動該裝置。 *IoT Edge 代理程式*有助於在 IoT Edge 裝置 (包括 IoT Edge 中樞) 上部署及監視模組。 *IoT Edge 中樞*會管理 IoT Edge 裝置上的模組通訊，以及裝置與 IoT 中樞之間的通訊。

在執行階段設定期間，您必須提供裝置連接字串。 這是您從 Azure CLI 擷取到的字串。 這個字串會讓實體裝置與 Azure 中的 IoT Edge 裝置身分識別建立關聯。

### <a name="deploy-the-iot-edge-device"></a>部署 IoT Edge 裝置

本節將使用 Azure Resource Manager 範本來建立新的虛擬機器，並在其中安裝 IoT Edge 執行階段。 如果您想要改為使用自己的 Linux 裝置，您可以遵循[在 Linux 上安裝 Azure IoT Edge 執行階段](how-to-install-iot-edge-linux.md)中的安裝步驟，然後再返回本快速入門。

使用下列 CLI 命令，根據預建的 [iotedge-vm-deploy](https://github.com/Azure/iotedge-vm-deploy) 範本來建立您的 IoT Edge 裝置。

* 針對 Bash 或 Cloud Shell 使用者，請將下列命令複製到文字編輯器 (以您的資訊取代預留位置文字)，然後複製到您的 Bash 或 Cloud Shell 視窗：

   ```azurecli-interactive
   az deployment group create --resource-group IoTEdgeResources --template-uri "https://aka.ms/iotedge-vm-deploy" --parameters dnsLabelPrefix='my-edge-vm' --parameters adminUsername='azureUser' --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name <REPLACE_WITH_HUB_NAME> -o tsv) --parameters authenticationType='password' --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```

* 針對 PowerShell 使用者，請將下列命令複製到您的 PowerShell 視窗，然後以您自己的資訊取代預留位置文字：

   ```azurecli
   az deployment group create `
   --resource-group IoTEdgeResources `
   --template-uri "https://aka.ms/iotedge-vm-deploy" `
   --parameters dnsLabelPrefix='my-edge-vm1' `
   --parameters adminUsername='azureUser' `
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name <REPLACE_WITH_HUB_NAME> -o tsv) `
   --parameters authenticationType='password' `
   --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```

此範本會取用下列參數：

| 參數 | 描述 |
| --------- | ----------- |
| **resource-group** | 將在其中建立資源的資源群組。 使用我們在本文中使用的預設 **IoTEdgeResources**，或提供您訂用帳戶中現有資源群組的名稱。 |
| **template-uri** | 我們所用 Resource Manager 範本的指標。 |
| **dnsLabelPrefix** | 將用來建立虛擬機器主機名稱的字串。 使用範例 **my-edge-vm** 或提供新的字串。 |
| **adminUsername** | 虛擬機器管理帳戶的使用者名稱。 使用範例 **azureUser** 或提供新的使用者名稱。 |
| **deviceConnectionString** | IoT 中樞中裝置身分識別的連接字串，用來設定虛擬機器上的 IoT Edge 執行階段。 此參數內的 CLI 命令會為您抓取連接字串。 以您的 IoT 中樞名稱取代預留位置文字。 |
| **authenticationType** | 管理帳戶的驗證方法。 本快速入門會使用**密碼**驗證，但您也可以將此參數設定為 **sshPublicKey**。 |
| **adminPasswordOrKey** | 管理帳戶的 SSH 金鑰密碼或值。 以安全密碼取代預留位置文字。 您的密碼長度必須至少有 12 個字元，而且有下列四項中的三項：小寫字元、大寫字元、數字和特殊字元。 |

部署完成之後，您應該會在 CLI 中收到 JSON 格式的輸出，其中包含連線到虛擬機器的 SSH 資訊。 複製 [輸出] 區段的 [公用 SSH] 項目值：

   ![從輸出取出公用 SSH 值](./media/quickstart-linux/outputs-public-ssh.png)

### <a name="view-the-iot-edge-runtime-status"></a>檢視 IoT Edge 執行階段狀態

此快速入門中的其餘命令，會在您的 IoT Edge 裝置本身上執行，讓您可以查看在裝置上發生的事。 如果您使用的是虛擬機器，請使用您所設定的管理使用者名稱和部署命令所輸出的 DNS 名稱，立即連線到該機器。 您也可在 Azure 入口網站中您虛擬機器的 [概觀] 頁面上找到 DNS 名稱。 使用下列命令連線到您的虛擬機器。 以您自己的值取代 `{admin username}` 和 `{DNS name}`。

   ```console
   ssh {admin username}@{DNS name}
   ```

連線到您的虛擬機器之後，請確認 IoT Edge 裝置上已成功安裝並設定執行階段。

1. 確認 IoT Edge 安全性精靈正以系統服務的形式執行。

   ```bash
   sudo systemctl status iotedge
   ```

   ![查看以系統服務形式執行的 IoT Edge 精靈](./media/quickstart-linux/iotedged-running.png)

   >[!TIP]
   >您必須要有提高的權限才能執行 `iotedge` 命令。 當您在安裝 IoT Edge 執行階段之後登出機器，並第一次重新登入時，您的權限將會自動更新。 在那之前，請在這些命令前面使用 `sudo`。

2. 如果您需要對服務進行疑難排解，請擷取服務記錄。

   ```bash
   journalctl -u iotedge
   ```

3. 檢視在 IoT Edge 裝置上執行的所有模組。 由於服務只有第一次會啟動，您應該只會看到 **edgeAgent** 模組正在執行。 EdgeAgent 模組預設會執行，且有助於安裝及啟動您部署至裝置的任何其他模組。

   ```bash
   sudo iotedge list
   ```

   ![檢視裝置上的一個模組](./media/quickstart-linux/iotedge-list-1.png)

IoT Edge 裝置現已設定完成。 並已準備好執行雲端部署的模組。

## <a name="deploy-a-module"></a>部署模組

從雲端管理您的 Azure IoT Edge 裝置，以部署會將遙測資料傳送到 IoT 中樞的模組。

![圖表 - 將模組從雲端部署到裝置](./media/quickstart-linux/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>檢視產生的資料

在此快速入門中，您可以建立新的 IoT Edge 裝置，並在其中安裝 IoT Edge 執行階段。 然後，您已使用 Azure 入口網站來部署 IoT Edge 模組，使其無須變更裝置本身就能在裝置上執行。

在此案例中，您推送的模組會產生可用於稍後測試的環境資料範例。 模擬感應器會同時監視機器本身和機器的周邊環境。 例如，此感應器可能位於伺服器機房中、廠房中或風力發電機上。 訊息包含周圍溫度和溼度、機器溫度和壓力，以及時間戳記。 IoT Edge 教學課程會使用此模組所建立的資料作為分析的測試資料。

在您的 IoT Edge 裝置上再次開啟命令提示字元，或從 Azure CLI 使用 SSH 連線。 確認從雲端部署的模組正在 IoT Edge 裝置上執行：

   ```bash
   sudo iotedge list
   ```

   ![在您的裝置上檢視三個模組](./media/quickstart-linux/iotedge-list-2.png)

檢視從溫度感應器模組傳送的訊息：

   ```bash
   sudo iotedge logs SimulatedTemperatureSensor -f
   ```

   >[!TIP]
   >IoT Edge 命令在在參考模組名稱時會區分大小寫。

   ![從您的模組中檢視資料](./media/quickstart-linux/iotedge-logs.png)

您也可以使用[適用於 Visual Studio Code 的 Azure IoT 中樞擴充功能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)，查看送達 IoT 中樞的訊息。

## <a name="clean-up-resources"></a>清除資源

如果您想要繼續進行 IoT Edge 教學課程，您可以使用在本快速入門中註冊和設定的裝置。 否則，您可以刪除您建立的 Azure 資源，以避免產生費用。

如果您是在新的資源群組中建立虛擬機器和 IoT 中樞，您可以刪除該群組和所有相關聯的資源。 再次檢查資源群組的內容，確定沒有您想要保留的內容。 如果您不想刪除整個群組，可改為刪除個別資源。

> [!IMPORTANT]
> 刪除資源群組是無法回復的動作。

移除 **IoTEdgeResources** 群組。 可能需要幾分鐘才能刪除資源群組。

```azurecli-interactive
az group delete --name IoTEdgeResources
```

您可以檢視資源群組清單來確認已移除資源群組。

```azurecli-interactive
az group list
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立 IoT Edge 裝置，並使用 Azure IoT Edge 雲端介面將程式碼部署至裝置上。 現在，您已有測試裝置，可產生其環境的相關原始資料。

下一個步驟是設定您的本機開發環境，以便您可以開始建立執行您商務邏輯的 IoT Edge 模組。

> [!div class="nextstepaction"]
> [開始開發適用於 Linux 裝置的 IoT Edge 模組](tutorial-develop-for-linux.md)
