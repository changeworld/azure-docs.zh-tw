---
title: 準備您的 IoT Edge 模組技術資產-Azure Marketplace
description: 深入瞭解您的物聯網 (IoT) Edge 模組必須符合的技術和設定需求，才能將其發行至 Azure Marketplace。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 04/03/2020
ms.openlocfilehash: 4d55186874e6f5be67a04e4a04d54a89dc09f9cd
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130437"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>準備 IoT Edge 模組的技術資產

本文說明物聯網 (IoT) Edge 模組在 Azure Marketplace 中發佈之前必須符合的需求。

## <a name="get-started"></a>開始使用

IoT Edge 模組是在 IoT Edge 裝置上執行的 Docker 相容容器。

- 若要深入了解 IoT Edge 模組，請參閱[了解 Azure IoT Edge 模組](../../iot-edge/iot-edge-modules.md)。
- 若要開始使用您的 IoT Edge 模組開發，請參閱 [開發您自己的 IoT Edge 模組](../../iot-edge/module-development.md)。

## <a name="technical-requirements"></a>技術需求

您的 IoT Edge 模組必須符合下列技術需求，才能在 Azure Marketplace 中進行認證和發佈。

### <a name="platform-support"></a>平台支援

您的 IoT Edge 模組必須支援下列其中一個平臺選項：

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>IoT Edge 支援的層級 1 平台

您的模組必須支援 [Azure IoT Edge 支援](../../iot-edge/support.md)) 所記錄 IoT Edge (所支援的所有第1層平臺。 由於這個選項能提供更好的客戶體驗，建議採用。 符合此準則的模組將會展示。 使用此平台選項的模組必須：

- 提供最新的標記和版本標籤 (例如，1.0.1) ，也就是以 [GitHub 資訊清單工具](https://github.com/estesp/manifest-tool)建立的資訊清單標記。

- 使用 [合作夥伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace) 中的 [供應專案清單] 索引標籤，將 [ **實用連結** ] 區段下的連結新增至 [Azure IoT Edge 認證的裝置目錄](https://catalog.azureiotsolutions.com/alldevices?filters={%2218%22:[%221%22]}/)。

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>IoT Edge 支援的層級 1 平台子集

您的模組必須支援一個子集 (IoT Edge (所支援的第1層平臺) ，Azure IoT Edge [支援](../../iot-edge/support.md)) 中所記錄。 使用此平台選項的模組必須：

- 如果支援多個平臺，請提供最新的標記和版本戳記 (例如，1.0.1) ，也就是使用 GitHub [資訊清單工具](https://github.com/estesp/manifest-tool) 建立的資訊清單標記。 僅在支援一個平台時方可選用資訊清單標籤。
- 使用 [合作夥伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace) 中的 [供應專案清單] 索引標籤，將 [ **實用連結** ] 區段下的連結新增至 [Azure IoT Edge 認證裝置目錄](https://catalog.azureiotsolutions.com/)中的至少一個 IoT Edge 裝置。

:::image type="content" source="media/iot-edge-module-technical-assets-offer-listing.png" alt-text="這是合作夥伴中心中供應專案清單區段的影像":::

### <a name="device-dimensions"></a>裝置尺寸

IoT Edge 模組維度 (例如目標 IoT Edge 裝置上的 CPU、RAM、存放裝置和 GPU) 必須符合下列需求：

- 模組必須與 [Azure IoT Edge 認證裝置目錄](https://catalog.azureiotsolutions.com/)中至少一個 IoT Edge 裝置搭配運作。

- 您必須在 [合作夥伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace)) 的 [供應專案清單] 索引標籤下，將最低硬體需求記載為供應專案的描述 (的最後一段。 如果推薦硬體需求與最低硬體需求差異甚大，也可以多列出建議硬體需求。 例如可在供應項目說明的結尾處新增以下區段：

複製此 HTML 文字，或在編輯視窗中使用對應的 rich text 函數。

```html
<p><u>Minimum hardware requirements:</u> Linux x64 and arm32 OS, 1GB of RAM, 500 Mb of storage</p>
```

### <a name="configuration"></a>組態

您的模組必須包含預設的設定，才能盡可能以最簡單的方式將部署到 IoT Edge 的裝置。 您可以在 [合作夥伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace)的方案的 **技術** 設定頁面中提供這項資訊。 容器也可能包含 IoT Edge 模組 SDK，以啟用與 Edge 中樞和 IoT 中樞的通訊。

#### <a name="default-configuration"></a>預設組態

IoT Edge 模組必須能夠以 [合作夥伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace)方案的 [ **技術** 設定] 頁面中提供的預設設定為開頭。 可用的預設設定如下：

- 預設 **路由**
- 預設 **模組對應項所需屬性**
- 預設 **環境變數**
- 預設 **容器建立選項**

在預設值所需的參數沒有意義的情況下 (例如，客戶服務器) 的 IP 位址，請將參數新增為預設值。 此值為大寫，並以括弧括住。 在此例中，請設定下列環境變數：

```
ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>設定文件

IoT Edge 模組的所有設定都必須清楚記載。 例如，您必須記錄如何使用其路由、對應項所需屬性、環境變數、createOptions 等等。 您必須提供檔的連結，或將它設為供應專案或方案描述的一部分。 您可以在 [合作夥伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace)的供應專案 **清單** 和 **方案清單** 頁面中提供這項資訊。

#### <a name="tags-and-versioning"></a>標籤與版本控制

客戶必須能夠輕鬆地部署模組，並在開發人員案例) 中自動取得 marketplace (的更新。 它們也必須能夠使用和凍結其在生產案例中 (測試的確切版本) 。

為了符合這些客戶期望併發布在 marketplace 中，IoT Edge 模組必須符合下列需求

- 包含在所有支援平臺上指向最新版本的資訊清單最新標記。
- 以 X. Z 格式製作版本戳記，其中 X、Y 和 Z 是整數。
- 在所有支援的平臺上包含指向特定版本的 "version" 標記，例如1.0.1。
- 請勿更新 "version" 標記，例如1.0.1，因為它們不得變更。

> [!NOTE]
> （選擇性）版本設定可以包含「輪流版本」標記，例如2.0 和1.0。 此種標籤可支援以平行方式維護多個主要版本。

### <a name="telemetry"></a>遙測

使用 IoT 模組 SDK 的模組必須將唯一模組識別碼設定為 PublisherId OfferId，以供遙測之用。 唯一識別碼可協助 Azure Marketplace 識別正在執行的模組實例數目。

使用 IoT 模組 Sdk 的下列其中一種方法，將 ProductInfo 設定為此識別碼：

- [C#](/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo)
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

針對未使用 IoT 模組 SDK 的模組，可透過合作夥伴中心取得較不精確的深入解析，例如下載數目。

### <a name="security"></a>安全性

IoT Edge 模組必須避免具有特殊 [許可權的模組](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities)。 相反地，請盡可能要求最低許可權的主機存取權。

### <a name="module-iot-sdk"></a>模組 IoT SDK

包含 IoT 模組 SDK 並不是認證的必要條件。 不過加入 IoT 模組 SDK 或許可提升使用者體驗。 例如在支援路由或是送訊息至雲端時均有幫助。

需要有 IoT 模組 SDK，才能取得有關正在執行的模組實例數目的遙測資料。

## <a name="recertification-process"></a>重新認證流程

每當有影響其模組的重大變更時，就會通知合作夥伴，例如：

- IoT Edge 支援的第1層 OS/架構支援矩陣
- IoT 模組 SDK
- IoT Edge 執行階段
- IoT Edge 模組認證指導方針

合作夥伴必須在 [合作夥伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace)中重新發佈供應專案，以更新並重新認證它們的供應專案。

如果您的供應專案有更新，您的供應專案也會重新認證，例如新增映射標記。

## <a name="host-module-in-azure-container-registry"></a>Azure Container Registry 中的主機模組

若要將 IoT Edge 模組上傳至 Azure Marketplace，您必須先將它裝載于 [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) (ACR) 。 此模組必須包含您想要發佈的所有標記，包括資訊清單標記所參考的影像標記。 如需詳細資訊，請參閱教學課程： [建立 Azure container registry 並推送容器映射](../../container-instances/container-instances-tutorial-prepare-acr.md)。

## <a name="next-steps"></a>後續步驟

- [建立 IoT Edge 模組供應項目](azure-iot-edge-module-creation.md)