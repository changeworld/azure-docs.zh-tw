---
title: 將 Azure Sphere 裝置連線到 Azure IoT Central 中 | Microsoft Docs
description: 了解如何將 Azure Sphere (DevKit) 裝置連線到 Azure IoT Central 應用程式。
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.author: sandeepu
author: sandeeppujar
ms.date: 04/30/2020
ms.openlocfilehash: a42b483a5ca4b4948f14c1ccbf0b7af6e49224bb
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715050"
---
# <a name="connect-an-azure-sphere-device-to-your-azure-iot-central-application"></a>將 Azure Sphere 裝置連線到 Azure IoT Central 應用程式

*本文適用對象為裝置開發人員。*

本文說明如何將 Azure Sphere (DevKit) 裝置連線到 Azure IoT Central 應用程式。

Azure Sphere 是安全且高階應用程式平台，搭配可供連網裝置使用的內建通訊及安全性功能。 其包含一個安全、已連線的交叉微控制器單元 (MCU)、一個以 Linux 為基礎的自訂高層級作業系統 (OS)，以及一個可提供持續、可更新的安全性的雲端式安全性服務。 如需詳細資訊，請參閱[何謂 Azure Sphere？](https://docs.microsoft.com/azure-sphere/product-overview/what-is-azure-sphere)。

[Azure Sphere 開發套件](https://azure.microsoft.com/services/azure-sphere/get-started/)會提供一切所需供您開始設計和開發 Azure Sphere 應用程式。 Azure IoT Central 搭配 Azure Sphere 可實現 IoT 解決方案的端對端堆疊。 Azure Sphere 會以零程式碼、受控 IoT 應用程式平台的形式提供裝置支援和 IoT Central。

在此操作說明文章中，您會：

- 使用程式庫中的 Azure Sphere DevKit 裝置範本，在 IoT Central 中建立 Azure Sphere 裝置。
- 準備適用於 Azure IoT 的 Azure Sphere DevKit 裝置。
- 將 Azure Sphere DevKit 連線到 Azure IoT Central。
- 在 IoT Central 中檢視來自裝置的遙測。

## <a name="prerequisites"></a>Prerequisites

若要完成本文中的步驟，您需要下列資源︰

- Azure IoT Central 應用程式。
- Visual Studio 2019 (16.4 版或更新版本)。
- [Seeed Studios 所推出的 Azure Sphere MT3620 開發套件](https://docs.microsoft.com/azure-sphere/hardware/mt3620-reference-board-design)。

> [!NOTE]
> 如果您沒有實體裝置，請在完成第一個步驟後跳至最後一節以試用模擬裝置。

## <a name="create-the-device-in-iot-central"></a>在 IoT Central 中建立裝置

若要在 IoT Central 中建立 Azure Sphere 裝置：

1. 在您的 Azure IoT Central 應用程式中，選取 [裝置範本] 索引標籤，然後選取 [+ 新增]。 在 [使用精選裝置範本] 區段中，選取 [Azure Sphere 範例裝置]。

    :::image type="content" source="media/howto-connect-sphere/sphere-create-template.png" alt-text="Azure Sphere DevKit 的裝置範本":::

1. 在裝置範本中，編輯稱為 [概觀] 的檢視，以顯示 [溫度] 和 [按下按鈕]。

1. 選取 [編輯裝置和雲端資料] 檢視類型，以新增另一個會顯示讀取/寫入屬性 [狀態 LED] 的檢視。 將 [狀態 LED] 屬性拖曳至表單右邊的空白虛線矩形。 選取 [儲存]。

## <a name="prepare-the-device"></a>準備裝置

您必須先[設定裝置和開發環境](https://github.com/Azure/azure-sphere-samples/tree/master/Samples/AzureIoT)，才可以將 Azure Sphere DevKit 裝置連線到 IoT Central。

## <a name="connect-the-device"></a>將裝置連線

若要讓範例能夠連線到 IoT Central，您必須[設定 Azure IoT Central 應用程式，然後修改範例的應用程式資訊清單](https://aka.ms/iotcentral-sphere-git-readme)。

## <a name="view-the-telemetry-from-the-device"></a>檢視來自裝置的遙測

當裝置連線到 IoT Central 時，您可以在儀表板上看到遙測。

:::image type="content" source="media/howto-connect-sphere/sphere-view.png" alt-text="Azure Sphere DevKit 的儀表板":::

## <a name="create-a-simulated-device"></a>建立模擬裝置

如果您沒有實體的 Azure Sphere DevKit 裝置，則可以建立模擬裝置來試用 Azure IoT Central 應用程式。

若要建立模擬裝置：

- 選取 [裝置] > [Azure IoT Sphere]
- 選取 [+ 新增]。
- 輸入唯一的 [裝置識別碼] 和自訂的 [裝置名稱]。
- 啟用 [模擬] 設定。
- 選取 [建立]。

## <a name="next-steps"></a>後續步驟

如果您是裝置開發人員，則建議的後續步驟如下：

- 閱讀 [Azure IoT Central 中的裝置連線能力](./concepts-get-connected.md)
- 了解如何[使用 Azure CLI 監視裝置連線能力](./howto-monitor-devices-azure-cli.md)
