---
title: IoT 隨插即用橋接器 |Microsoft Docs
description: 瞭解 IoT 隨插即用橋接器，以及如何使用它來將連接至 Windows 或 Linux 閘道的現有裝置連線為 IoT 隨插即用裝置。
author: usivagna
ms.author: ugans
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 224f86a40fa812003463301f97bcae07de907f3c
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91579982"
---
# <a name="iot-plug-and-play-bridge"></a>IoT 隨插即用橋接器

IoT 隨插即用橋接器是開放原始碼應用程式，可將連接至 Windows 或 Linux 閘道的現有裝置連線為 IoT 隨插即用裝置。 在 Windows 或 Linux 電腦上安裝並設定應用程式之後，您可以使用它來將連接的裝置連線到 IoT 中樞。 您可以使用橋接器將 IoT 隨插即用介面對應至附加裝置正在傳送的遙測資料、使用裝置屬性，以及叫用命令。

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-high-level.png" alt-text="左側有幾個現有的感應器附加 (有線和無線) 到包含 IoT 隨插即用橋接器的 Windows 或 Linux 電腦。IoT 隨插即用橋接器接著會連接到右側的 IoT 中樞":::

IoT 隨插即用橋接器可以在任何 IoT 裝置、產業電腦、伺服器或執行 Windows 10 或 Linux 的閘道上部署為獨立可執行檔。 您也可以將它編譯成您的應用程式程式碼。 簡單的設定 JSON 檔案會告訴 IoT 隨插即用橋接器，哪些連結的裝置/週邊設備應公開至 Azure。

## <a name="supported-protocols-and-sensors"></a>支援的通訊協定和感應器

IoT 隨插即用橋接器預設支援下列類型的週邊設備，以及介面卡檔的連結：

|周邊設備|Windows|Linux|
|---------|---------|---------|
|[藍牙 LE](https://aka.ms/iot-pnp-bridge-bluetooth)       |是|否|
|[相機](https://aka.ms/iot-pnp-bridge-camera)               |是|否|
|[Modbus](https://aka.ms/iot-pnp-bridge-modbus)                |是|是|
|[MQTT](https://aka.ms/iot-pnp-bridge-mqtt)                    |是|是|
|[串列](https://aka.ms/iot-pnp-bridge-serial)                |是|是|
|[Windows USB 週邊設備](https://aka.ms/iot-pnp-bridge-usb)  |是|不適用|

>[!Important]
>開發人員可以透過 **[這裡 IoT 隨插即用 bridge 開發人員檔](https://aka.ms/iot-pnp-bridge-dev-doc)** 中的指示，擴充 IoT 隨插即用橋接器以支援其他裝置通訊協定。

## <a name="prerequisites"></a>必要條件

### <a name="os-platform"></a>作業系統平台

支援的作業系統平臺和版本如下：

|平台  |支援的版本  |
|---------|---------|
|Windows 10 |     所有 Windows Sku 都受到支援。 例如： IoT Enterprise、Server、Desktop、IoT Core。 *針對相機健康情況監視功能，建議使用20H1 或更新版本的組建。所有其他功能都可在所有 Windows 10 組建上取得。*  |
|Linux     |在 Ubuntu 18.04 上測試及支援，其他散發套件上的功能尚未經過測試。         |
||

### <a name="hardware"></a>硬體

- 任何支援上述作業系統 Sku 和版本的硬體平臺。
- 以原生方式支援序列、USB、藍牙和相機週邊設備和感應器。 您可以擴充 IoT 隨插即用橋接器，以支援任何自訂的周邊或感應器 ([請參閱上方) 的周邊區段](#iot-plug-and-play-bridge) 。

### <a name="development-environment"></a>開發環境

若要建立、擴充和開發您需要的 IoT 隨插即用橋接器：  

- 支援編譯 c + + 的開發環境，例如： [Visual Studio (的社區、Professional 或 Enterprise) ](https://visualstudio.microsoft.com/downloads/)-請確定您在安裝 Visual Studio 時，包含使用 c + + 的桌面開發工作負載。
- [CMake](https://cmake.org/download/) -當您安裝 CMake 時，請選取此選項 `Add CMake to the system PATH` 。
- 如果您是在 Windows 上建立的，您也必須下載 Windows 17763 SDK： [https://developer.microsoft.com/windows/downloads/windows-10-sdk](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
- [Azure IoT 中樞裝置 C SDK](https://github.com/Azure/azure-iot-sdk-c)。 此存放庫中包含的組建腳本會自動為您複製必要的 Azure IoT C SDK。

### <a name="azure-iot-products-and-tools"></a>Azure IoT 產品和工具

- **Azure IoT 中樞** -您將需要 azure 訂用帳戶中的 [azure IoT 中樞](https://docs.microsoft.com/azure/iot-hub/) ，才能將您的裝置連線至。 如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。 如果您沒有 IoT 中樞，請 [依照下列指示建立一個](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-using-cli)。

> [!Note]
> IoT 隨插即用目前只能在美國中部、北歐和日本東部區域中建立的 IoT 中樞上使用。 基本層 IoT 中樞並未包含 IoT 隨插即用支援。 若要與您的 IoT 隨插即用裝置互動，您可以使用 Azure IoT explorer 工具。 針對您的作業系統[下載並安裝最新版的 Azure IoT 總管](./howto-use-iot-explorer.md)。

## <a name="iot-plug-and-play-bridge-architecture"></a>IoT 隨插即用 bridge 架構

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-components.png" alt-text="左側有幾個現有的感應器附加 (有線和無線) 到包含 IoT 隨插即用橋接器的 Windows 或 Linux 電腦。IoT 隨插即用橋接器接著會連接到右側的 IoT 中樞":::

## <a name="download-iot-plug-and-play-bridge"></a>下載 IoT 隨插即用橋接器

您可以在 [IoT 隨插即用橋接器版](https://aka.ms/iot-pnp-bridge-releases) 中，使用支援的介面卡下載橋接器的預先建立版本，並展開最新版本的資產清單。 為您的作業系統下載應用程式的最新版本。

您也可以 [在 GitHub 上](https://aka.ms/bridge)下載並查看 IoT 隨插即用橋接器的原始程式碼。

## <a name="next-steps"></a>後續步驟

現在您已大致瞭解 IoT 隨插即用橋接器的架構，接下來的步驟是深入瞭解：

- [如何使用 IoT 隨插即用橋接器](./howto-use-iot-pnp-bridge.md)
- [請參閱 IoT 隨插即用橋接器的 GitHub 開發人員參考](https://aka.ms/iot-pnp-bridge-dev-doc)
- [在 GitHub 上 IoT 隨插即用 bridge](https://aka.ms/iotplugandplaybridge)
