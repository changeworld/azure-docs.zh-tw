---
title: IoT Edge 版本流覽和歷程記錄-Azure IoT Edge
description: 探索 IoT Edge 的新功能，其中包含最新版本的新特性和功能的相關資訊。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/08/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: cdb84f817f63e6401d17f18319e161f4c3477293
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024648"
---
# <a name="azure-iot-edge-versions-and-release-notes"></a>Azure IoT Edge 版本和版本資訊

Azure IoT Edge 是從裝載于 GitHub 上的開放原始碼 IoT Edge 專案所建立的產品。 所有新版本都可在 [Azure IoT Edge 專案](https://github.com/Azure/azure-iotedge)中取得。 您可以在 [開放原始碼 IoT Edge 專案](https://github.com/Azure/iotedge)上提出投稿和錯誤報表。

## <a name="documented-versions"></a>記載的版本

本網站上的 IoT Edge 檔適用于兩個不同版本的產品，因此您可以選擇適用于 IoT Edge 環境的內容。 目前，這兩個支援的版本為：

* **IoT Edge 1.0.10** 透過最新正式推出的版本來涵蓋所有特性和功能： [1.0.10](https://github.com/Azure/azure-iotedge/releases/tag/1.0.10)。
* **IoT Edge 1.2 (preview)** 包含最新預覽版本中功能和功能的其他內容： [1.2-rc1](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc1)
  * 雖然 IoT Edge 1.2 目前為預覽狀態，但您必須安裝候選版。 如需詳細資訊，請參閱 [離線或特定版本安裝](how-to-install-iot-edge.md?tabs=linux#offline-or-specific-version-installation)。

## <a name="version-history"></a>版本歷程記錄

下表提供 IoT Edge 套件版本的最新版本歷程記錄，並強調說明針對每個版本所做的檔更新。

| 版本資訊和資產 | 類型 | Date | 重點 |
| ------------------------ | ---- | ---- | ---------- |
| [1.2-rc1](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc1) | 預覽 | 2020 年 11 月 | [閘道後方的 IoT Edge 裝置](how-to-connect-downstream-iot-edge-device.md?view=iotedge-2020-11&preserve-view=true)<br>[IoT Edge MQTT broker](how-to-publish-subscribe.md?view=iotedge-2020-11&preserve-view=true) |
| [1.0.10](https://github.com/Azure/azure-iotedge/releases/tag/1.0.10) | 穩定 | 2020 年 10 月 | [UploadSupportBundle 直接方法](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics)<br>[上傳執行時間計量](how-to-access-built-in-metrics.md)<br>[路由優先順序和存留時間](module-composition.md#priority-and-time-to-live)<br>[模組啟動順序](module-composition.md#configure-modules)<br>[X. x.509 手動布建](how-to-manual-provision-x509.md) |
| [1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) | 穩定 | 2020 年 3 月 | [X.509 使用 DPS 自動布建](how-to-auto-provision-x509-certs.md)<br>[RestartModule 直接方法](how-to-edgeagent-direct-method.md#restart-module)<br>[支援-套件組合命令](troubleshoot.md#gather-debug-information-with-support-bundle-command) |

## <a name="next-steps"></a>下一步

* [查看所有 Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)
* [在意見反應論壇中建立或審核功能要求](https://feedback.azure.com/forums/907045-azure-iot-edge)