---
title: 準備 IoT 邊緣模組技術資產 - Azure 應用商店
description: 瞭解物聯網 (IoT) 邊緣模組技術資產必須滿足的技術和配置要求,然後才能將它們發佈到 Azure 應用商店。
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 2c0cd47acbd4639ff5eff2af78dcebdfc26270a7
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730705"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>準備 IoT Edge 模組的技術資產

> [!IMPORTANT]
> 我們將IoT Edge模組產品的管理從雲合作夥伴門戶遷移到合作夥伴中心。 在遷移產品/服務之前,請按照雲合作夥伴門戶[準備IoT Edge模組技術資產](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-technical-assets)以管理產品/服務的說明進行操作。

本文介紹了物聯網 (IoT) 邊緣模組技術資產在 Azure 應用商店中發佈之前必須滿足的要求。

## <a name="get-started"></a>開始使用

IoT Edge 模組是在IoT邊緣裝置上運行的與Docker相容的容器。

- 若要深入了解 IoT Edge 模組，請參閱[了解 Azure IoT Edge 模組](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules)。
- 要開始開發 IoT 邊緣模組,請參閱[開發您自己的 IoT 邊緣模組](https://docs.microsoft.com/azure/iot-edge/module-development)。

## <a name="technical-requirements"></a>技術需求

IoT Edge 模組必須滿足以下技術要求才能在 Azure 應用商店中進行認證和發佈。

### <a name="platform-support"></a>平台支援

IoT Edge 模組必須支援以下平台選項之一:

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>IoT Edge 支援的層級 1 平台

您的模組必須支援 IoT 邊緣支援的所有第 1 層平臺(如[Azure IoT 邊緣支援](https://docs.microsoft.com/azure/iot-edge/support)中記錄)。 由於這個選項能提供更好的客戶體驗，建議採用。 將展示符合此標準的模組。 使用此平台選項的模組必須：

- 提供使用[GitHub 清單工具](https://github.com/estesp/manifest-tool)構建的清單標記的最新標記和版本標記(例如 1.0.1)。

- 使用[合作夥伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace)中的產品/服務列表選項卡在 **「有用連結**」部分下向[Azure IoT 邊緣認證設備目錄](https://catalog.azureiotsolutions.com/alldevices?filters={%2218%22:[%221%22]}/)添加連結。

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>IoT Edge 支援的層級 1 平台子集

您的模組必須支援 IoT Edge 支援的第 1 層平臺的子集(至少一個)(如[Azure IoT Edge 支援](https://docs.microsoft.com/azure/iot-edge/support)中所記錄)。 使用此平台選項的模組必須：

- 提供最新的標記和版本標記(例如,1.0.1),這些標記是使用 GitHub[清單工具](https://github.com/estesp/manifest-tool)構建的清單標記(如果支援多個平臺)。 僅在支援一個平台時方可選用資訊清單標籤。
- 使用[合作夥伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace)中的產品/服務列表選項卡將 **「有用連結**」部分下的連結添加到[Azure IoT 邊緣認證裝置目錄中](https://catalog.azureiotsolutions.com/)的至少一個 IoT Edge 裝置。

:::image type="content" source="media/iot-edge-module-technical-assets-offer-listing.png" alt-text="這是合作夥伴中心內優惠清單部分的影像":::

### <a name="device-dimensions"></a>裝置尺寸

目標 IoT 邊緣裝置上的 IoT 邊緣模組尺寸(如 CPU、RAM、儲存和 GPU)必須滿足以下要求:

- 該模組必須與[Azure IoT 邊緣認證設備目錄中](https://catalog.azureiotsolutions.com/)的至少一個 IoT 邊緣設備配合使用。

- 最低硬體要求必須記錄為產品/服務描述中的最後一段(在[合作夥伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace)的產品/服務列表選項卡下)。 如果推薦硬體需求與最低硬體需求差異甚大，也可以多列出建議硬體需求。 例如可在供應項目說明的結尾處新增以下區段：

複製此 HTML 文字或在編輯視窗中使用相應的富文本函數。

```html
<p><u>Minimum hardware requirements:</u> Linux x64 and arm32 OS, 1GB of RAM, 500 Mb of storage</p>
```

### <a name="configuration"></a>設定

您的模組必須包含預設配置設置,以使部署到 IoT Edge 設備盡可能簡單。 此資訊可在[合作夥伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace)中的計畫**技術配置**「頁中提供。 容器還可以包括 IoT 邊緣模組 SDK,以便與邊緣集線器和 IoT 中心進行通信。

#### <a name="default-configuration"></a>預設組態

IoT Edge 模組必須能夠從[合作夥伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace)中計劃**的技術配置**頁中提供的默認設置開始。 可用的預設設定如下：

- 預設**路由**
- 預設**模組雙所需屬性**
- 預設**環境變數**
- 預設**容器建立選項**

在預設值所需的參數沒有意義(例如,客戶伺服器的 IP 位址)的情況下,添加參數作為預設值。 此值為大寫,並包含在括弧中。 在此例中，請設定下列環境變數：

```
ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>設定文件

必須清楚地記錄 IoT Edge 模組的所有配置設置。 例如,必須記錄如何使用其路由、雙所需屬性、環境變數、創建選項等。 您必須提供指向文檔的連結,或將其作為產品/服務或計劃說明的一部分。 您可以在[合作夥伴中心的](https://partner.microsoft.com/dashboard/commercial-marketplace)**「產品/服務」列表**和**計畫清單**頁中提供此資訊。

#### <a name="tags-and-versioning"></a>標籤與版本控制

客戶必須能夠輕鬆部署模組並自動從市場獲取更新(在開發人員方案中)。 他們還必須能夠使用和凍結他們測試的精確版本(在生產方案中)。

為了滿足這些客戶的期望並在市場上發佈,IoT Edge 模組必須滿足以下要求

- 包括指向所有受支持平臺上最新版本的清單最新標記。
- 在 X.Y.Z 窗體中製作版本標記,其中 X、Y 和 Z 是整數。
- 包含一個"版本"標記,如 1.0.1,用於指向所有受支持平臺上的特定版本。
- 不要更新"版本"標記(如 1.0.1),因為它們不能更改。

> [!NOTE]
> 可選,版本控制可以包括"滾動版本"標記,如 2.0 和 1.0。 此種標籤可支援以平行方式維護多個主要版本。

### <a name="telemetry"></a>遙測

出於遙測目的,使用 IoT 模組 SDK 的模組必須將唯一模組識別符設定為 PublisherId.OfferId.SkuId。 唯一標識碼可説明 Azure 應用商店識別正在運行的模組實例數。

使用 IoT 模組 SDK 中的以下方法之一將 ProductInfo 設定此識別碼:

- [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo)
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

對於不使用 IoT 模組 SDK 的模組,合作夥伴中心提供的準確見解(如下載次數)不太精確。

### <a name="security"></a>安全性

IoT 邊緣模組必須避免[特權模組](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities)。 而是要求盡可能少特權地訪問主機。

### <a name="module-iot-sdk"></a>模組 IoT SDK

包含 IoT 模組 SDK 並不是認證的必要條件。 不過加入 IoT 模組 SDK 或許可提升使用者體驗。 例如在支援路由或是送訊息至雲端時均有幫助。

需要 IoT 模組 SDK 來獲取有關正在運行的模組實例數的遙測數據。

## <a name="recertification-process"></a>重新認證流程

每當有影響其模組的重大變化時,都會通知合作夥伴,例如:

- IoT 邊緣支援的第 1 層作業系統/拱形支援矩陣
- IoT 模組 SDK
- IoT Edge 執行階段
- IoT 邊緣模組認證指南

合作夥伴必須透過在[合作夥伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace)重新發佈產品/服務來更新和重新認證其優惠。

如果您更新產品/服務(如添加新圖像標記),您的產品/服務也將重新認證。

## <a name="host-module-in-azure-container-registry"></a>Azure 容器註冊表中的主機模組

要將 IoT 邊緣模組上載到 Azure 應用商店,首先需要將其託管在[Azure 容器註冊錶](https://azure.microsoft.com/services/container-registry/)(ACR) 中。 該模組必須包含要發佈的所有標記,包括清單標記引用的圖像標記。 有關詳細資訊,請參閱教學[「建立 Azure 容器註冊表」並推送容器映像](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-acr)。

## <a name="next-steps"></a>後續步驟

- [建立 IoT Edge 模組供應項目](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation)