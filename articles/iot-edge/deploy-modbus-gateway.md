---
title: 使用閘道來轉譯 Modbus 通訊協定 - Azure IoT Edge | Microsoft Docs
description: 允許使用 Modbus TCP 的裝置藉由建立 IoT Edge 閘道裝置來與 Azure IoT 中樞通訊
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: kgremban
ms.openlocfilehash: 23fbbd87230ea0a0147dc9d90c77729f4d531e98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511139"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>透過 IoT Edge 裝置閘道連線 Modbus TCP 裝置

如果要將使用 Modbus TCP 或 RTU 協定的設備連接到 Azure IoT 中心，則可以使用 IoT 邊緣設備作為閘道。 閘道裝置會從您的 Modbus 裝置讀取資料，然後使用支援的通訊協定將該資料傳達至雲端。

![Modbus 設備通過 IoT 邊緣閘道連接到 IoT 中心](./media/deploy-modbus-gateway/diagram.png)

本文說明如何針對 Modbus 模組建立自己的容器映像 (您也可以使用預先建置的範例)，然後將它部署到將作為閘道的 IoT Edge 裝置。

本文假設您使用 Modbus TCP 通訊協定。 如需有關如何設定此模組以支援 Modbus RTU 的詳細資訊，請參閱 GitHub 上的 [Azure IoT Edge Modbus 模組](https://github.com/Azure/iot-edge-modbus)專案。

## <a name="prerequisites"></a>Prerequisites

* Azure IoT Edge 裝置。 有關如何設置一個演練，請參閱在 Windows 或[Linux](quickstart-linux.md)[上部署 Azure IoT 邊緣](quickstart.md)。
* IoT Edge 裝置的主索引鍵連接字串。
* 支援 Modbus TCP 的實體或模擬 Modbus 裝置。 您需要瞭解其 IPv4 位址。

## <a name="prepare-a-modbus-container"></a>準備 Modbus 容器

如果您想要測試 Modbus 閘道功能，Microsoft 有可供您使用的範例模組。 可以從 Azure 應用商店[、Modbus](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview)或映射 URI 訪問模組`mcr.microsoft.com/azureiotedge/modbus:1.0`。

如果您想要建立自己的模組並針對您的環境進行自訂，GitHub 上有提供開放原始碼 [Azure IoT Edge Modbus 模組](https://github.com/Azure/iot-edge-modbus)專案。 請遵循該專案中的方針，建立您自己的容器映像。 要創建容器映射，請參閱[Visual Studio 中開發 C# 模組](how-to-visual-studio-develop-csharp-module.md)或在[視覺化工作室代碼中開發模組](how-to-vs-code-develop-module.md)。 這些文章提供有關創建新模組和將容器映射發佈到註冊表的說明。

## <a name="try-the-solution"></a>嘗試解決方案

本節將 Microsoft 的示例 Modbus 模組部署到 IoT 邊緣設備。

1. 在 [Azure 入口網站](https://portal.azure.com/)上，移至您的 IoT 中樞。

2. 移至 [IoT Edge]****，然後按一下您的 IoT Edge 裝置。

3. 選取 [設定模組]****。

4. 在**IoT 邊緣模組**部分中，添加 Modbus 模組：

   1. 按一下 **"添加**下拉清單"並選擇 **"市場模組**"。
   2. 搜索`Modbus`並選擇 Microsoft 的**Modbus TCP 模組**。
   3. 該模組已自動為 IoT 中心配置，並顯示在 IoT 邊緣模組清單中。 路由也會自動設定。 選擇 **"審閱" = 創建**。
   4. 查看部署清單並選擇 **"創建**"。

5. 在清單中選擇 Modbus`ModbusTCPModule`模組 ，然後選擇 **"模組孿生設置"** 選項卡。模組孿生所需屬性所需的 JSON 是自動填滿的。

6. 在 JSON 中查找**從屬連接**屬性，並將其值設置為 Modbus 設備的 IPv4 位址。

7. 選擇 **"更新**"。

8. 選擇 **"審閱 " 創建**，查看部署，然後選擇 **"創建**"。

9. 返回裝置的詳細資料頁面，選取 [重新整理]****。 您應該看到新`ModbusTCPModule`模組與 IoT Edge 運行時一起運行。

## <a name="view-data"></a>檢視資料

查看通過 Modbus 模組的資料：

```cmd/sh
iotedge logs modbus
```

您還可以使用 Visual Studio 代碼的[Azure IoT 中心副檔名](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)（以前的 Azure IoT 工具組擴展），查看設備正在發送的遙測資料。

## <a name="next-steps"></a>後續步驟

* 要瞭解有關 IoT Edge 設備如何充當閘道的資訊，請參閱[創建充當透明閘道的 IoT Edge 設備](./how-to-create-transparent-gateway.md)。
* 有關 IoT 邊緣模組的工作原理的詳細資訊，請參閱[瞭解 Azure IoT 邊緣模組](iot-edge-modules.md)。
