---
title: 教學課程 - 在 Azure IoT Central 中建立影片分析 IoT Edge 執行個體 (Linux VM)
description: 本教學課程說明如何在 Linux VM 上建立影片分析 IoT Edge 執行個體，以搭配影片分析 - 物件和動作偵測應用程式範本使用。
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: db3d4bb7f4b32c07e75487764acc5e7ba7d05915
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91873273"
---
# <a name="tutorial-create-an-iot-edge-instance-for-video-analytics-linux-vm"></a>教學課程：建立適用於影片分析的 IoT Edge 執行個體 (Linux VM)

Azure IoT Edge 是完全受控的服務，可透過部署和執行下列項目，在本機提供雲端智慧：

* 自訂邏輯
* Azure 服務
* 人工智慧

在 IoT Edge 中，這些服務會直接在跨平台 IoT 裝置上執行，讓您在雲端或離線時，安全且大規模地執行 IoT 解決方案。

本教學課程說明如何在 Azure VM 中準備 IoT Edge 裝置。 IoT Edge 執行個體會執行 Azure IoT Central 影片分析 - 物件和動作偵測應用程式範本所使用的即時影片分析模組。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> * 建立已安裝 Azure IoT Edge 執行階段的 Azure VM
> * 準備 IoT Edge 安裝，以裝載即時影片分析模組，並連線到 IoT Central

## <a name="prerequisites"></a>先決條件

開始之前，您應該先完成先前的[在 Azure IoT Central 中建立影片分析應用程式](./tutorial-video-analytics-create-app-yolo-v3.md)或[在 Azure IoT Central 中建立影片分析 (OpenVINO&trade;)](tutorial-video-analytics-create-app-openvino.md) 教學課程。

您也需要 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，則可以在 [Azure 註冊頁面](https://aka.ms/createazuresubscription)上免費建立一個。

## <a name="deploy-azure-iot-edge"></a>部署 Azure IoT Edge

若要建立已安裝最新 IoT Edge 執行階段和即時影片分析模組的 Azure VM，請選取下列按鈕：

[![iotedge-vm-deploy 的 [部署至 Azure] 按鈕](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Flive-video-analytics%2Fmaster%2Fref-apps%2Flva-edge-iot-central-gateway%2Fvm_deploy%2FedgeModuleVMDeploy.json)

請使用下表中的資訊來完成**自訂部署**表單：

| 欄位 | 值 |
| ----- | ----- |
| 訂用帳戶 | 選取 Azure 訂用帳戶。 |
| 資源群組 | lva-rg - 您在上一個教學課程中建立的資源群組。 |
| 區域       | 美國東部 |
| DNS 標籤首碼 | 為 VM 選擇唯一的 DNS 首碼。 |
| 管理員使用者名稱 | AzureUser |
| 管理員密碼 | 輸入密碼。 記下 scratchpad.txt 檔案中的密碼，稍後會用到。 |
| 範圍識別碼 | 您在上一個教學課程中新增閘道裝置時，從 scratchpad.txt 檔案中記下的**範圍識別碼**。 |
| 裝置識別碼 | lva-gateway-001 - 您在上一個教學課程中建立的閘道裝置。 |
| 裝置金鑰 | 您在上一個教學課程中新增閘道裝置時，從 scratchpad.txt 檔案中記下的裝置主要金鑰。 |
| Iot Central 應用程式主機 | 在上一個教學課程中，您從 scratchpad.txt 檔案中記下的**應用程式 URL**。 例如，traders.azureiotcentral.com。 |
| Iot Central 應用程式 API 權杖 | 您在上一個教學課程中所記下的操作員 API 權杖。 |
| Iot Central 裝置佈建金鑰 | 您在上一個教學課程中，從 scratchpad.txt 檔案中記下的主要群組共用存取簽章權杖。 |
| VM 大小 | *Standard_DS1_v2* |
| Ubuntu OS 版本 | *18.04-LTS* |
| Location | *[resourceGroup().location]* |

選取 [檢閱 + 建立]。 驗證完成之後，請選取 [建立]。 完成部署通常需要約三分鐘的時間。 部署完成時，瀏覽至 Azure 入口網站中的 **lva-rg** 資源群組。

## <a name="ensure-the-iot-edge-runtime-loads-the-modules"></a>確保 IoT Edge 執行階段載入模組

在 Azure 入口網站中，瀏覽至 **lva-rg** 資源群組，然後選取虛擬機器。 然後，在 [支援 + 疑難排解] 區段中，選取 [序列主控台]。

按 **Enter** 以取得 `login:` 提示字元。 使用 AzureUser 作為您在建立 VM 時所選擇的使用者名稱和密碼。

執行下列命令來檢查 IoT Edge 執行階段版本。 在撰寫本文時，版本為 1.0.9：

```bash
sudo iotedge --version
```

使用命令來列出您的 IoT Edge 模組：

```bash
sudo iotedge list
```

部署已設定下列五個 IoT Edge 模組來執行：

* LvaEdgeGatewayModule
* edgeAgent
* edgeHub
* lvaEdge
* lvaYolov3

部署已建立自訂的 IoT Edge 環境，其中包含即時影片分析所需的模組。 部署已更新預設的 **config.yaml**，以確保 IoT Edge 執行階段會使用 IoT 裝置佈建服務連線到 IoT Central。 此部署也會在 **/data/storage** 資料夾中建立名為 **state.json** 的檔案，以提供其他設定資料給模組。 如需詳細資訊，請參閱[建立適用於影片分析的 IoT Edge 執行個體 (Intel NUC)](./tutorial-video-analytics-iot-edge-nuc.md) 教學課程。

若要針對 IoT Edge 裝置進行疑難排解，請參閱[針對您的 IoT Edge 裝置進行疑難排解](https://docs.microsoft.com/azure/iot-edge/troubleshoot)

## <a name="use-the-rtsp-simulator"></a>使用 RTSP 模擬器

如果您沒有真正的相機裝置可連線到您的 IoT Edge 裝置，則可以使用影片分析應用程式範本中的兩個模擬相機裝置。 本節將說明如何在您的 IoT Edge 裝置中使用模擬的影片串流。

這些指示會使用 [Live555 媒體伺服器](http://www.live555.com/mediaServer/)作為 Docker 容器中的 RTSP 模擬器。

> [!NOTE]
> 此存放庫中提及的第三方軟體僅基於資訊與便利性而使用。 Microsoft 不會為第三方軟體背書或提供權利。 如需詳細資訊，請參閱 [Live555 媒體伺服器](http://www.live555.com/mediaServer/)。

使用下列命令，在 IoT Edge VM 上的 Docker 容器中執行 **rtspvideo** 公用程式。 Docker 容器會建立背景 RTSP 串流：

```bash
sudo docker run -d --name live555 --rm -p 554:554 mcr.microsoft.com/lva-utilities/rtspsim-live555:1.2
```

使用下列命令來列出 Docker 容器：

```bash
sudo docker ps
```

此清單包含名為 **live555** 的容器。

## <a name="next-steps"></a>後續步驟

您現在已在 Azure 上執行的 Linux VM 中部署了 IoT Edge 執行階段、LVA 模組和 Live555 模擬串流。

若要管理相機，請遵循下一個教學課程

> [!div class="nextstepaction"]
> [監視和管理 [影片分析 - 物件和動作偵測] 應用程式](./tutorial-video-analytics-manage.md)
