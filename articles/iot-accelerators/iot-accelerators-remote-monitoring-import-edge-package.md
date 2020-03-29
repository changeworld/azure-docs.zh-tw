---
title: 遠端監視解決方案匯入 Edge 套件 - Azure | Microsoft Docs
description: 本文說明如何將 IoT Edge 套件匯入至遠端監視解決方案加速器
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/10/2018
ms.topic: conceptual
ms.openlocfilehash: 34222f396ed3c43932371aa9f64a459bb2a5dd0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "61442856"
---
# <a name="import-an-iot-edge-package-into-your-remote-monitoring-solution-accelerator"></a>在遠端監視解決方案加速器中匯入 IoT Edge 套件

部署資訊清單會定義要部署至 IoT Edge 裝置的模組。 本文假設貴組織的開發人員已經建立部署資訊清單。 若要深入了解開發人員如何建立資訊清單，請參閱下列其中一篇 IoT Edge 操作說明文章：

- [從 Azure 入口網站部署 Azure IoT Edge 模組](../iot-edge/how-to-deploy-modules-portal.md)
- [使用 Azure CLI 部署 Azure IoT Edge 模組](../iot-edge/how-to-deploy-modules-cli.md)
- [從 Visual Studio Code 部署 Azure IoT Edge 模組](../iot-edge/how-to-deploy-modules-vscode.md)

開發人員會建立部署資訊清單，並且在開發環境中測試。 當您準備就緒，您可以將此資訊清單匯入至遠端監視解決方案加速器。

## <a name="export-a-manifest"></a>匯出資訊清單

使用 Azure 入口網站，從開發環境匯出部署資訊清單：

1. 在 Azure 入口網站中，瀏覽至您用來開發和測試 IoT Edge 裝置的 IoT 中樞。 按一下 [IoT Edge]****，然後按一下 [IoT Edge 部署]****：![IoT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/iotedge.png)

1. 按一下具有您想使用之部署組態的部署。 [部署詳細資料]**** 頁面會顯示：![IoT Edge 部署詳細資料](media/iot-accelerators-remote-monitoring-import-edge-package/deploymentdetails.png)

1. 按一下 **"下載 IoT 邊緣"清單**：![下載部署清單](media/iot-accelerators-remote-monitoring-import-edge-package/download.png)

1. 將 JSON 檔案儲存為稱為 **deploymentmanifest.json** 的本機檔案。

您現在有一個包含部署資訊清單的檔案。 在下一節中，您將匯入此資訊清單，作為遠端監視解決方案中的套件。

## <a name="import-a-package"></a>匯入套件

請遵循下列步驟將 Edge 部署資訊清單當作套件匯入您的解決方案中：

1. 在遠端監視 Web UI 中瀏覽至 [套件]**** 頁面：![套件頁面](media/iot-accelerators-remote-monitoring-import-edge-package/packagespage.png)

1. 按一下 [+ 新增套件]****，選擇 [Edge 資訊清單]**** 作為套件類型，然後按一下 [瀏覽]****，以選取您在上一節中儲存的 **deploymentmanifest.json** 檔案：![選取資訊清單](media/iot-accelerators-remote-monitoring-import-edge-package/selectmanifest.png)

1. 按一下 [上傳]**** 將套件新增至遠端監視解決方案：![上傳的套件](media/iot-accelerators-remote-monitoring-import-edge-package/uploadedpackage.png)

您現在已將 IoT Edge 部署資訊清單當作套件上傳。 在 [部署]**** 頁面上，您可以將此套件部署到已連線的 IoT Edge 裝置。

## <a name="next-steps"></a>後續步驟

您現在已了解如何從遠端監視解決方案將模組部署到 IoT Edge 裝置，下一個步驟是深入了解 [IoT Edge](../iot-edge/about-iot-edge.md)。
