---
title: 教學課程 - 修改 Azure IoT Edge 即時影片分析模組
description: 本教學課程說明如何修改和建置 [影片分析 - 物件和動作偵測] 應用程式範本所使用的即時影片分析閘道模組。
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: c2f6f386f4a8ea062980c0efc97d0cfb4f37f4f2
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92124889"
---
# <a name="tutorial-modify-and-build-the-live-video-analytics-gateway-modules"></a>教學課程：修改並建置即時影片分析閘道模組

本教學課程說明如何修改即時影片分析 (LVA) 模組的 IoT Edge 模組程式碼。

先前的教學課程會使用模組的預建映像。

## <a name="prerequisites"></a>先決條件

若要完成本教學課程中的步驟，您需要：

* [Node.js](https://nodejs.org/en/download/) v10 或更新版本
* 已安裝 [TSLint](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-typescript-tslint-plugin) 擴充功能的 [Visual Studio Code](https://code.visualstudio.com/Download)
* [Docker](https://www.docker.com/products/docker-desktop) 引擎
* 用來裝載模組版本的 [Azure Container Registry](../../container-registry/index.yml)。
* [Azure 媒體服務](../../media-services/index.yml)帳戶。 如果您已完成先前的教學課程，您可以重複使用先前建立的帳戶。

## <a name="clone-the-repository"></a>複製儲存機制

如果您尚未複製存放庫，請使用下列命令將其複製到本機電腦上的適當位置：

```cmd
git clone https://github.com/Azure/live-video-analytics
```

使用 VS Code 開啟本機的 live-video-analytics 存放庫資料夾。

## <a name="edit-the-deploymentamd64json-file"></a>開啟 deployment.amd64.json 檔案

1. 如果您尚未這麼做，請在 **lva-gateway** 存放庫的本機複本中，建立名為 *ref-apps/lva-edge-iot-central-gateway/storage* 的資料夾。 Git 會忽略此資料夾，以避免您不小心簽入任何機密資訊。
1. 將 *setup* 資料夾中的 *deployment.amd64.json* 檔案複製到 *storage* 資料夾。
1. 在 VS Code 中，開啟 storage/deployment.amd64.json 檔案。
1. 編輯 `registryCredentials` 區段，以新增您的 Azure Container Registry 認證。
1. 編輯 `LvaEdgeGatewayModule` 模組區段，以在 `env:amsAccountName:value` 中新增您的映像名稱和 AMS 帳戶名稱。
1. 編輯 `lvaYolov3` 模組區段，並新增您的映像名稱。
1. 編輯 `lvaEdge` 模組區段，並新增您的映像名稱。
1. 如需如何完成設定的詳細資訊，請參閱[在 Azure IoT Central 中建立影片分析應用程式](tutorial-video-analytics-create-app-yolo-v3.md)。

## <a name="build-the-code"></a>建置程式碼

1. 在您第一次嘗試建置程式碼之前，請使用 VS Code 終端機來執行 `npm install` 命令。 此命令會安裝必要的套件，並執行安裝指令碼。

    > [!TIP]
    > 如果您提取較新版本的 GitHub 存放庫，請刪除 *node_modules* 資料夾，然後再次執行 `npm install`。

1. 編輯 *./setup/imageConfig.json* 檔案，以根據您的容器登錄名稱來更新映像名稱：

    ```json
    {
        "arch": "amd64",
        "imageName": "[Server].azurecr.io/lva-edge-gateway"
    }
    ```

1. 使用 VS Code 終端機來執行 `docker login [your server].azurecr.io` 命令。 使用您在部署資訊清單中為模組所提供的相同認證。

1. 使用 VS Code 終端機來執行 **npm version patch** 命令。 此建置指令碼會將映像部署到您的容器登錄。 VS Code 終端機視窗中的輸出會顯示建置是否成功。

1. 每當建置完成時，**LvaEdgeGatewayModule** 映像的版本會遞增。 您需要在部署資訊清單檔案中使用此版本。

## <a name="next-steps"></a>後續步驟

現在您已了解 [影片分析 - 物件和動作偵測] 應用程式範本，以及 LVA IoT Edge 模組，下一步建議您深入了解：

> [!div class="nextstepaction"]
> [使用 Azure IoT Central 建置零售解決方案](overview-iot-central-retail.md)