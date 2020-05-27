---
title: 快速入門：在 Linux 上建立 Azure IoT Edge 裝置 | Microsoft Docs
description: 在本快速入門中，了解如何建立 IoT Edge 裝置，然後從 Azure 入口網站遠端部署預先建置的程式碼。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/06/2019
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: e015999d8c1f60340fb30609c6563f770c5c824f
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726208"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-virtual-linux-device"></a>快速入門：將您的第一個 IoT Edge 模組部署至虛擬 Linux 裝置

藉由將容器化程式碼部署至虛擬 IoT Edge 裝置，在本快速入門中測試 Azure IoT Edge。 IoT Edge 可讓您從遠端管理裝置上的程式碼，以便可以將更多的工作負載傳送到邊緣。 在本快速入門中，建議您對 IoT Edge 裝置使用 Azure 虛擬機器，以便您可以快速建立已安裝所有必要條件的測試機器，然後在完成時將它刪除。

在此快速入門中，您將了解如何：

1. 建立 IoT 中樞。
2. 向 IoT 中樞註冊 IoT Edge 裝置。
3. 在虛擬裝置上安裝並啟動 IoT Edge 執行階段。
4. 將模組從遠端部署到 IoT Edge 裝置。

![圖表 - 裝置和雲端的快速入門架構](./media/quickstart-linux/install-edge-full.png)

本快速入門將引導您建立設定為 IoT Edge 裝置的 Linux 虛擬機器。 接著，您可以從 Azure 入口網站將模組部署至裝置。 您在本快速入門中部署的模組是一個模擬感應器，會產生溫度、溼度和壓力資料。 其他 Azure IoT Edge 教學課程會以您在此所做的工作為基礎，部署模組來分析模擬資料以產生商業見解。

如果您沒有使用中的 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

您可以使用 Azure CLI 來完成本快速入門中的許多步驟，而且 Azure IoT 有擴充功能可供啟用其他功能。

將 Azure IoT 擴充功能新增至 Cloud Shell 執行個體。

   ```azurecli-interactive
   az extension add --name azure-iot
   ```
   
[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="prerequisites"></a>Prerequisites

雲端資源：

* 一個資源群組，用以管理本快速入門中使用的所有資源。

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

IoT Edge 裝置：

* 當作 IoT Edge 裝置的 Linux 裝置或虛擬機器。 您應該使用 Microsoft 提供的 [Azure IoT Edge on Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu) 虛擬機器，它會預先安裝在裝置上執行 IoT Edge 所需的所有項目。 請使用下列命令接受使用規定並建立此虛擬機器：

   ```azurecli-interactive
   az vm image terms accept --urn microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest
   az vm create --resource-group IoTEdgeResources --name EdgeVM --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys
   ```

   建立和啟動新的虛擬機器可能需要幾分鐘的時間。

   當您建立新的虛擬機器時，請記住在建立命令輸出中提供的 **publicIpAddress**。 您稍後將在本快速入門中使用此公用 IP 位址來連線到虛擬機器。

* 如果您想要在自己的裝置上執行 Azure IoT Edge 執行階段，請依照[在 Linux 上安裝 Azure IoT Edge 執行階段](how-to-install-iot-edge-linux.md)的指示操作。

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

使用 Azure CLI 建立 IoT 中樞，開始進行此快速入門。

![圖表 - 在雲端建立 IoT 中樞](./media/quickstart-linux/create-iot-hub.png)

此快速入門適用於 IoT 中樞的免費層級。 如果您在過去已使用過 IoT 中樞，並已建立可用的中樞，您可以使用該 IoT 中樞。 每個訂用帳戶只能有一個免費的 IoT 中樞。

下列程式碼會在資源群組 **IoTEdgeResources** 中建立免費的 **F1** 中樞。 以 IoT 中樞的唯一名稱取代 `{hub_name}`。

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
   ```

   如果因您的訂用帳戶中已有免費中樞而發生錯誤，請將 SKU 變更為 **S1**。 如果您收到無法使用 IoT 中樞名稱的錯誤，則表示其他人已經有該名稱的中樞。 請嘗試新的名稱。

## <a name="register-an-iot-edge-device"></a>註冊 IoT Edge 裝置

向新建立的 IoT 中樞註冊 IoT Edge 裝置。

![圖表 - 使用 IoT 中樞身分識別註冊裝置](./media/quickstart-linux/register-device.png)

建立 IoT Edge 裝置的裝置身分識別，使它能與 IoT 中樞通訊。 裝置身分識別存在於雲端，您可以使用唯一的裝置連接字串，讓實體裝置與裝置身分識別建立關聯。

由於 IoT Edge 裝置的行為和管理方式不同於典型 IoT 裝置，請使用 `--edge-enabled` 旗標將此身分識別宣告為 IoT Edge 裝置。

1. 在 Azure Cloud Shell 中輸入下列命令，以在中樞內建立名為 **myEdgeDevice** 的裝置。

   ```azurecli-interactive
   az iot hub device-identity create --hub-name {hub_name} --device-id myEdgeDevice --edge-enabled
   ```

   如果您收到有關 iothubowner 原則金鑰的錯誤，請確定 Cloud Shell 正在執行最新版的 azure-iot 擴充功能。

2. 擷取裝置的連接字串，此字串將作為連結實體裝置與其在 IoT 中樞內的身分識別。

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name {hub_name}
   ```

3. 從 JSON 輸出複製 `connectionString` 金鑰值並加以儲存。 此值示裝置連接字串。 您將在下一節中使用此連接字串來設定 IoT Edge 執行階段。

   ![從 CLI 輸出中擷取連接字串](./media/quickstart/retrieve-connection-string.png)

## <a name="configure-your-iot-edge-device"></a>設定您的 IoT Edge 裝置

在您的 IoT Edge 裝置上啟動 Azure IoT Edge 執行階段。

![圖表 - 在裝置上啟動執行階段](./media/quickstart-linux/start-runtime.png)

IoT Edge 執行階段會在所有 IoT Edge 裝置上部署。 它有三個元件。 **IoT Edge 安全性精靈**會在每次 IoT Edge 裝置開機時啟動，並藉由啟動 IoT Edge 代理程式來啟動該裝置。 **IoT Edge 代理程式**有助於在 IoT Edge 裝置 (包括 IoT Edge 中樞) 上部署及監視模組。 **IoT Edge 中樞**會管理 IoT Edge 裝置上的模組通訊，以及裝置與 IoT 中樞之間的通訊。

在執行階段設定期間，您必須提供裝置連接字串。 請使用您從 Azure CLI 擷取到的字串。 這個字串會讓實體裝置與 Azure 中的 IoT Edge 裝置身分識別建立關聯。

### <a name="set-the-connection-string-on-the-iot-edge-device"></a>設定 IoT Edge 裝置的連接字串

如果您是使用必要條件中所述的「Azure IoT Edge on Ubuntu」，則您的裝置已經安裝 IoT Edge 執行階段。 您只需要使用在上一節中擷取的裝置連接字串來設定裝置。 您可以從遠端設定，而不需要連線到虛擬機器。 執行下列命令，將 `{device_connection_string}` 取代為您自己的字串。

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script "/etc/iotedge/configedge.sh '{device_connection_string}'"
   ```

如果您是在本機電腦或 ARM32 或 ARM64 裝置上執行 IoT Edge，則您需要在您的裝置上安裝 IoT Edge 執行階段及其必要項目。 遵循[在 Linux 上安裝 Azure IoT Edge 執行階段](how-to-install-iot-edge-linux.md)中的指示，然後回到本快速入門。

### <a name="view-the-iot-edge-runtime-status"></a>檢視 IoT Edge 執行階段狀態

此快速入門中的其餘命令，會在您的 IoT Edge 裝置本身上執行，讓您可以查看在裝置上發生的事。 如果您是使用虛擬機器，現在請使用建立命令所輸出的公用 IP 位址來連線到該機器。 您也可在 Azure 入口網站中您虛擬機器的 [概觀] 頁面上找到該公用 IP 位址。 使用下列命令連線到您的虛擬機器。 如果您使用的使用者名稱，與必要條件中建議的不同，請將 `{azureuser}` 取代為該名稱。 以您的電腦位址取代 `{publicIpAddress}`。

   ```console
   ssh azureuser@{publicIpAddress}
   ```

確認執行階段已在您的 IoT Edge 裝置上成功安裝並設定。

>[!TIP]
>您必須要有提高的權限才能執行 `iotedge` 命令。 當您在安裝 IoT Edge 執行階段之後登出機器，並第一次重新登入時，您的權限將會自動更新。 在那之前，請在這些命令前面使用 `sudo`。

1. 確認 IoT Edge 安全性精靈正以系統服務的形式執行。

   ```bash
   sudo systemctl status iotedge
   ```

   ![查看以系統服務形式執行的 IoT Edge 精靈](./media/quickstart-linux/iotedged-running.png)

2. 如果您需要對服務進行疑難排解，請擷取服務記錄。

   ```bash
   journalctl -u iotedge
   ```

3. 檢視在您的裝置上執行的模組。

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

在此案例中，您推送的模組建立了可用於測試的範例資料。 模擬溫度感應器模組產生了後續可用於測試的環境資料。 模擬感應器會同時監視機器本身和機器的周邊環境。 例如，此感應器可能位於伺服器機房中、廠房中或風力發電機上。 訊息包含周圍溫度和溼度、機器溫度和壓力，以及時間戳記。 IoT Edge 教學課程會使用此模組所建立的資料作為分析的測試資料。

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

移除 **IoTEdgeResources** 群組。

```azurecli-interactive
az group delete --name IoTEdgeResources
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立 IoT Edge 裝置，並使用 Azure IoT Edge 雲端介面將程式碼部署至裝置上。 現在，您已有測試裝置，可產生其環境的相關原始資料。

下一個步驟是設定您的本機開發環境，以便您可以開始建立執行您商務邏輯的 IoT Edge 模組。

> [!div class="nextstepaction"]
> [開始開發適用於 Linux 裝置的 IoT Edge 模組](tutorial-develop-for-linux.md)
