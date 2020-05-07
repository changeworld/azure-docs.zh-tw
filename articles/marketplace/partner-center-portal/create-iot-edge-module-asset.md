---
title: 準備您的 IoT Edge 模組技術資產-Azure Marketplace
description: 瞭解您的物聯網（IoT） Edge 模組必須符合的技術和設定需求，才能將其發佈至 Azure Marketplace。
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: fc35602b55e79f3351da0def800d2a2b2698e250
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82856723"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>準備 IoT Edge 模組的技術資產

本文說明您的物聯網（IoT） Edge 模組在 Azure Marketplace 中發佈之前，技術資產必須符合的需求。

## <a name="get-started"></a>開始使用

IoT Edge 模組是在 IoT Edge 裝置上執行的 Docker 相容容器。

- 若要深入了解 IoT Edge 模組，請參閱[了解 Azure IoT Edge 模組](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules)。
- 若要開始使用您的 IoT Edge 模組開發，請參閱[開發您自己的 IoT Edge 模組](https://docs.microsoft.com/azure/iot-edge/module-development)。

## <a name="technical-requirements"></a>技術需求

您的 IoT Edge 模組必須符合下列技術需求，才能在 Azure Marketplace 中通過認證及發佈。

### <a name="platform-support"></a>平台支援

您的 IoT Edge 模組必須支援下列其中一個平臺選項：

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>IoT Edge 支援的層級 1 平台

您的模組必須支援 IoT Edge 支援的所有第1層平臺（如[Azure IoT Edge 支援](https://docs.microsoft.com/azure/iot-edge/support)中所記錄）。 由於這個選項能提供更好的客戶體驗，建議採用。 符合此準則的模組將會展示。 使用此平台選項的模組必須：

- 提供最新的標記和版本標籤（例如，1.0.1），其為使用[GitHub 資訊清單工具](https://github.com/estesp/manifest-tool)建立的資訊清單標記。

- 使用[合作夥伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace)的 [供應專案清單] 索引標籤，將 [**實用連結**] 區段下的連結新增至 [ [Azure IoT Edge 認證的裝置目錄](https://catalog.azureiotsolutions.com/alldevices?filters={%2218%22:[%221%22]}/)]。

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>IoT Edge 支援的層級 1 平台子集

您的模組必須支援 IoT Edge 支援的一或多個第1層平臺子集（如[Azure IoT Edge 支援](https://docs.microsoft.com/azure/iot-edge/support)中所記錄）。 使用此平台選項的模組必須：

- 提供最新的標記和版本標籤（例如，1.0.1），這是在支援多個平臺時，使用 GitHub[資訊清單工具](https://github.com/estesp/manifest-tool)建立的資訊清單標記。 僅在支援一個平台時方可選用資訊清單標籤。
- 使用[合作夥伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace)的供應專案清單索引標籤，將 [**實用連結**] 區段下的連結新增至 [ [Azure IoT Edge 認證的裝置目錄](https://catalog.azureiotsolutions.com/)] 中的至少一個 IoT Edge 裝置。

:::image type="content" source="media/iot-edge-module-technical-assets-offer-listing.png" alt-text="這是合作夥伴中心內供應專案清單區段的影像":::

### <a name="device-dimensions"></a>裝置尺寸

目標 IoT Edge 裝置上的 IoT Edge 模組維度（例如 CPU、RAM、儲存體和 GPU）必須符合下列需求：

- 模組必須從[Azure IoT Edge 認證的裝置目錄](https://catalog.azureiotsolutions.com/)使用至少一個 IoT Edge 裝置。

- 最低硬體需求必須記載為供應專案描述中的最後一段（在 [[合作夥伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace)] 的 [供應專案清單] 索引標籤底下）。 如果推薦硬體需求與最低硬體需求差異甚大，也可以多列出建議硬體需求。 例如可在供應項目說明的結尾處新增以下區段：

複製此 HTML 文字，或在 [編輯] 視窗中使用對應的 rtf 文字函數。

```html
<p><u>Minimum hardware requirements:</u> Linux x64 and arm32 OS, 1GB of RAM, 500 Mb of storage</p>
```

### <a name="configuration"></a>組態

您的模組必須包含預設的設定，才能以最簡單的方式將部署到 IoT Edge 裝置。 這項資訊可在[合作夥伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace)內方案的 [**技術**設定] 頁面中提供。 容器也可以包含 IoT Edge 模組 SDK，以啟用與 Edge 中樞和 IoT 中樞的通訊。

#### <a name="default-configuration"></a>預設組態

IoT Edge 模組必須能夠以[合作夥伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace)內方案的 [**技術**設定] 頁面中提供的預設設定為開頭。 可用的預設設定如下：

- 預設**路由**
- 預設**模組對應項所需屬性**
- 預設**環境變數**
- 預設**容器建立選項**

在預設值所需的參數不合理的情況下（例如，客戶服務器的 IP 位址），請新增參數作為預設值。 這個值是大寫，並以方括弧括住。 在此例中，請設定下列環境變數：

```
ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>設定文件

IoT Edge 模組的所有設定都必須清楚記載。 例如，您必須記載如何使用其路由、對應項所需屬性、環境變數、createOptions 等等。 您必須提供檔的連結，或讓它成為您的供應專案或方案描述的一部分。 您可以在[合作夥伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace)的**供應專案清單**和**方案清單**頁面中提供這項資訊。

#### <a name="tags-and-versioning"></a>標籤與版本控制

客戶必須能夠輕鬆地部署模組，並自動從 marketplace 取得更新（在開發人員案例中）。 它們也必須能夠使用和凍結其已測試的確切版本（在生產環境中）。

為了符合這些客戶期望並在 marketplace 中發佈，IoT Edge 模組必須符合下列需求

- 包含資訊清單最新的標籤，該標記會指向所有支援平臺上的最新版本。
- 以 x-y 格式建立版本戳記，其中 X、Y 和 Z 是整數。
- 包含「版本」標記（例如1.0.1），其指向所有支援平臺上的特定版本。
- 請勿更新 "version" 標籤，例如1.0.1，因為它們不得變更。

> [!NOTE]
> （選擇性）版本設定可以包含「輪流版本」標記，例如2.0 和1.0。 此種標籤可支援以平行方式維護多個主要版本。

### <a name="telemetry"></a>遙測

使用 IoT 模組 SDK 的模組必須將唯一的模組識別碼設定為 PublisherId，以供遙測之用。 唯一識別碼可協助 Azure Marketplace 識別正在執行的模組實例數目。

使用 IoT 模組 Sdk 中的下列其中一種方法，將 ProductInfo 設定為此識別碼：

- [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo)
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

針對不使用 IoT 模組 SDK 的模組，可以透過合作夥伴中心取得較不精確的深入解析，例如下載次數。

### <a name="security"></a>安全性

IoT Edge 模組必須避免特殊[許可權模組](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities)。 改為要求盡可能最低許可權存取主機。

### <a name="module-iot-sdk"></a>模組 IoT SDK

包含 IoT 模組 SDK 並不是認證的必要條件。 不過加入 IoT 模組 SDK 或許可提升使用者體驗。 例如在支援路由或是送訊息至雲端時均有幫助。

需要 IoT 模組 SDK，才能取得有關正在執行之模組實例數目的遙測資料。

## <a name="recertification-process"></a>重新認證流程

每當有影響其模組的重大變更時，就會通知合作夥伴，例如：

- IoT Edge 支援的第1層 OS/架構支援矩陣
- IoT 模組 SDK
- IoT Edge 執行階段
- IoT Edge 模組認證指導方針

合作夥伴必須在[合作夥伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace)重新發佈供應專案，以更新並重新認證其供應專案。

您的供應專案也會在您更新時 recertified，例如新增新的映射標籤。

## <a name="host-module-in-azure-container-registry"></a>Azure Container Registry 中的主機模組

若要將您的 IoT Edge 模組上傳至 Azure Marketplace，您必須先將它裝載在[Azure Container Registry](https://azure.microsoft.com/services/container-registry/) （ACR）中。 模組必須包含您想要發佈的所有標記，包括資訊清單標記所參考的影像標記。 如需詳細資訊，請參閱[建立 Azure container registry 和推送容器映射](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-acr)教學課程。

## <a name="next-steps"></a>後續步驟

- [建立 IoT Edge 模組供應項目](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation)