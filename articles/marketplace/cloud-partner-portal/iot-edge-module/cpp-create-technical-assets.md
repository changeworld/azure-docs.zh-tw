---
title: 創建 Azure IoT 邊緣模組技術資產 |Azure 應用商店
description: 建立 IoT Edge 模組的技術資產。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: b36d03695c107054549fba716f4cc27ecb7bba26
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983478"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>準備 IoT Edge 模組的技術資產

>[!Important]
>從 2020 年 4 月 13 日起,我們將開始將 IoT Edge 模組產品的管理轉移到合作夥伴中心。 遷移後,您將在合作夥伴中心創建和管理您的優惠。 按照[創建IoT Edge模組產品/服務](https://aka.ms/AzureIoTTechAsset)中的說明進行操作,以管理遷移的優惠。

本文會說明 IoT Edge 模組技術資產在發佈到 Azure Marketplace 之前，必須先達到的需求。

## <a name="understanding-iot-edge-modules-and-getting-started"></a>了解 IoT Edge 模組並開始使用

IoT Edge 模組是專門設計在 IoT Edge 裝置上執行的 Docker 相容容器。

- 若要深入了解 IoT Edge 模組，請參閱[了解 Azure IoT Edge 模組](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules)。
- 若要開始開發 IoT Edge 模組，請參閱[了解開發 IoT Edge 模組的需求和工具](https://docs.microsoft.com/azure/iot-edge/module-development)。

## <a name="technical-requirements"></a>技術需求

必須達到下列技術需求，IoT Edge 模組方能通過認證並在 Azure Marketplace 上發佈。

### <a name="platform-support"></a>平台支援

您的 IoT Edge 模組必須支援下列其中一個平台選項。

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>IoT Edge 支援的層級 1 平台

支援 IoT Edge 支援的所有層級 1 平台 (如 [Azure IoT Edge 支援](https://docs.microsoft.com/azure/iot-edge/support)中所記錄)。 由於這個選項能提供更好的客戶體驗，建議採用。 會再提供符合此標準的模組範本。 使用此平台選項的模組必須：

- 提供 `latest` 標籤與版本標籤 (例如：`1.0.1`)，後者為使用 GitHub [資訊清單工具](https://github.com/estesp/manifest-tool)建置的資訊清單標籤。
- 使用 [Marketplace 索引標籤](./cpp-marketplace-tab.md)，新增[相容 IoT Edge 認證裝置](https://aka.ms/iot-edge-certified)的連結。 此連結會解析至 `https://aka.ms/iot-edge-certified` 網站，客戶可在其中瀏覽或搜尋通過認證的裝置。 此網站亦稱為 [Azure IoT Edge 認證](https://catalog.azureiotsolutions.com/)裝置目錄。

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>IoT Edge 支援的層級 1 平台子集
  
支援受 IoT Edge 支援的 (至少一個) 層級 1 平台子集 (如 [Azure IoT Edge 支援](https://docs.microsoft.com/azure/iot-edge/support)中所記錄)。 使用此平台選項的模組必須：

- 提供 `latest` 標籤與版本標籤 (例如：`1.0.1`)，後者為支援多個平台時，使用 GitHub [資訊清單工具](https://github.com/estesp/manifest-tool)建置的資訊清單標籤。 僅在支援一個平台時方可選用資訊清單標籤。
- 使用 [Marketplace 索引標籤](./cpp-marketplace-tab.md)，在 [Azure IoT Edge 認證](https://catalog.azureiotsolutions.com/)裝置目錄中至少提供一個 IoT Edge 裝置的連結。

### <a name="device-dimensions"></a>裝置尺寸

目標 IoT Edge 裝置的 IoT Edge 模組尺寸 (CPU/RAM/Storage/GPU 等等)，必須符合下列需求：

- 模組必須**至少搭配一個 [Azure IoT Edge 認證](https://catalog.azureiotsolutions.com/)裝置目錄中的 IoT Edge 認證裝置**使用。
- **最低硬體需求**必須列在供應項目說明 ([Marketplace索引標籤](./cpp-marketplace-tab.md)中) 的最後一段。 如果推薦硬體需求與最低硬體需求差異甚大，也可以多列出建議硬體需求。 例如可在供應項目說明的結尾處新增以下區段：

  ```html
    <p><u>Minimum hardware requirements:</u> Linux x64 and arm32  OS, 1GB of RAM, 500 Mb of storage</p>
  ```

### <a name="configuration"></a>組態

也包含預設的組態設定，盡可能以簡單明瞭的方式來部署 IoT Edge 裝置。 容器還可以包含 IoT Edge 模組 SDK，以啟用與 edgeHub 和 IoT 中樞的通訊。

#### <a name="default-configuration"></a>預設組態

IoT Edge 模組一開始必能使用 [Cloud Partner 入口網站的 [SKU] 索引標籤](./cpp-skus-tab.md)提供的預設設定。 可用的預設設定如下：

- 預設**路由**
- 預設**雙對所需屬性**
- 預設**環境變數**
- 預設**建立選項**

在此案例中，預設值所需的參數無任何意義 (例如：客戶伺服器的 IP 位址)，您可新增參數做為預設值。 此值會以方括號括住並大寫顯示。 在此例中，請設定下列環境變數：

```
    ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>設定文件

必須清楚地記錄 IoT Edge 模組的所有配置設定(如何使用其路由、雙所需屬性、環境變數、創建選項等)。提供指向文件的連結,或者文檔必須是您的產品/sku 說明的一部分。

### <a name="tags-and-versioning"></a>標籤與版本控制

客戶必須能夠輕鬆部署模組並自動從市場獲取更新(在開發人員方案中)。他們還必須能夠使用和凍結他們測試的精確版本(在生產方案中)。

為了符合客戶的上述期望並在市集中發佈，IoT Edge 模組必須符合下列需求：

- 包含指向所有受支援平台最新版本的資訊清單`latest`標籤。
- 版本標籤格式應為 X.Y.Z，其中 X、Y 和 Z 是整數。
- 包含指向所有受支援平台特定版本的「版本」標籤，例如 `1.0.1`。
- 諸如 `1.0.1` 等「版本」標籤為固定標籤，因此請勿更新。

>[!Note]
>或者，版本控制亦可加入 `2.0` 和 `1.0` 等「累積版本」標籤。 此種標籤可支援以平行方式維護多個主要版本。

### <a name="telemetry"></a>遙測

為了遙測之用，使用 IoT 模組 SDK 的模組必須將唯一模組識別碼設為 `PublisherId.OfferId.SkuId`。 唯一識別碼可讓 Azure Marketplace 識別執行中的模組執行個體數目。

 請以 IoT Module SDK 使用下列方法，將 `ProductInfo` 設為此識別碼：

- [C\#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo) 
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

對於未使用 IoT 模組 SDK 的模組，由 Cloud Partner 入口網站提供的深入解析會較不精確，例如下載數。

### <a name="security"></a>安全性

IoT Edge 模組必須盡可能減少所要求的主機存取特殊權限。 應避免採用[特殊權限模組](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities)。

### <a name="module-iot-sdk"></a>模組 IoT SDK

包含 IoT 模組 SDK 並不是認證的必要條件。 不過加入 IoT 模組 SDK 或許可提升使用者體驗。 例如在支援路由或是送訊息至雲端時均有幫助。

必須使用 IoT 模組 SDK，才能取得執行中模組執行個體數量的相關遙測資料。


## <a name="recertification-process"></a>重新認證流程

<!-- Add legal time windows-->
每當出現會影響合作夥伴模組的中斷性變更時，對方就會收到通知，例如：

- 受 IoT Edge 支援的層級 1 os/arch 支援矩陣
- IoT 模組 SDK
- IoT Edge 執行階段
- IoT Edge 模組認證方針

合作夥伴必須更新模組，並使用 Cloud Partner 入口網站工具重新認證這些模組。

## <a name="host-your-iot-edge-module-in-an-azure-container-registry"></a>將您的 IoT Edge 模組裝載在 Azure Container Registry 中

若要將您的 IoT Edge 模組上傳至 Cloud Partner 入口網站，必須先將該模組裝載於 [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) (ACR)。 模組必須包含您想要發佈的所有標籤，包括資訊清單標籤所參考的映像標籤。


## <a name="next-steps"></a>後續步驟

- [建立 IoT Edge 模組供應項目](./cpp-create-offer.md) (英文)
