---
title: Azure IoT Central 的裝置開發 | Microsoft Docs
description: Azure IoT Central 是 IoT 應用程式平台，可簡化 IoT 解決方案的建立流程。 本文概述如何開發裝置，以連線到您的 IoT Central 應用程式。 裝置會使用遙測將串流資料和屬性傳送至報告裝置狀態。 Iot Central 可以在裝置上使用可寫入的屬性和呼叫命令來設定裝置狀態。
author: dominicbetts
ms.author: dobett
ms.date: 05/05/2020
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom:
- mvc
- device-developer
ms.openlocfilehash: 82818c8db326889079948cd2b32b2ed0be6ab50d
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94990749"
---
# <a name="iot-central-device-development-overview"></a>IoT Central 裝置開發總覽

*本文適用對象為裝置開發人員。*

IoT Central 應用程式可讓您在整個生命週期中監視及管理數百萬個裝置。 此總覽適用於實作程式碼以在連線到 IoT Central 裝置上執行的裝置開發人員。

裝置會使用下列基本項目與 IoT Central 應用程式互動：

- _遙測_ 是裝置傳送至 IoT Central 的資料。 例如，來自上線感應器的溫度值串流。
- _屬性_ 是裝置向 IoT Central 報告的狀態值。 例如，裝置目前的韌體版本。 您也可擁有 IoT Central 可在裝置上更新的可寫入屬性，例如目標溫度。
- _命令_ 會從 IoT Central 呼叫，以控制裝置的行為。 例如，您的 IoT Central 應用程式可能會呼叫命令，將裝置重新開機。

解決方案建立器負責設定 IoT Central web UI 中的儀表板和視圖，以視覺化遙測、管理屬性及呼叫命令。

## <a name="types-of-device"></a>裝置類型

下列各節說明您可以連線到 IoT Central 應用程式的主要裝置類型：

### <a name="standalone-device"></a>獨立裝置

獨立裝置會直接連線到 IoT Central。 獨立裝置通常會將來自其上線或已連線感應器的遙測傳送至您的 IoT Central 應用程式。 獨立裝置也可以報告屬性值、接收可寫入的屬性值，以及回應命令。

### <a name="gateway-device"></a>閘道裝置

閘道裝置會管理一或多個連線到您 IoT Central 應用程式的下游裝置。 您可以使用 IoT Central 來設定下游裝置與閘道裝置之間的關聯性。 若要深入了解，請參閱[在 Azure IoT Central 應用程式中定義新的 IoT 閘道裝置類型](./tutorial-define-gateway-device-type.md)。

### <a name="edge-device"></a>邊緣裝置

邊緣裝置會直接連線到 IoT Central，但做為其他裝置 (也稱為 _分葉裝置_) 的媒介。 邊緣裝置通常位於做為媒介的分葉裝置附近。 使用邊緣裝置的案例包括：

- 啟用無法直接連線到 IoT Central 以透過邊緣裝置連線的裝置。 例如，分葉裝置可能會使用藍牙來連線到邊緣裝置，然後透過網際網路連線到 IoT Central。
- 將遙測傳送至 IoT Central 之前，先加以彙總。 這種方法可協助降低將資料傳送至 IoT Central 的成本。
- 在本機控制分葉裝置，以避免與透過網際網路連線到 IoT Central 相關聯的延遲。

邊緣裝置也可以傳送自己的遙測、報告其屬性，以及回應可寫入的屬性更新和命令。

IoT Central 只會看到邊緣裝置，但看不到連線至邊緣裝置的分葉裝置。

若要深入了解，請參閱[將 Azure IoT Edge 裝置新增至 Azure IoT Central 應用程式](./tutorial-add-edge-as-leaf-device.md)。

## <a name="connect-a-device"></a>連接裝置

Azure IoT Central 會使用 [Azure IoT 中樞裝置佈建服務 (DPS)](../../iot-dps/about-iot-dps.md) 來管理所有的裝置註冊和連線。

使用 DPS 可實現如下效果：

- 讓 IoT Central 支援大規模的裝置上線和連線。
- 讓您產生裝置認證並在離線狀態下設定裝置，而不需要透過 IoT Central UI 來註冊裝置。
- 讓您使用自己的裝置識別碼在 IoT Central 中註冊裝置。 使用自己的裝置識別碼可簡化與現有後台系統的整合。
- 讓您以單一且一致的方式將裝置連線到 IoT Central。

若要深入了解，請參閱[連線至 Azure IoT Central](./concepts-get-connected.md)。

### <a name="security"></a>安全性

裝置與 IoT Central 應用程式之間的連線是使用[共用的存取簽章](./concepts-get-connected.md#sas-group-enrollment)或業界標準的 [X.509 憑證](./concepts-get-connected.md#x509-group-enrollment)來保護。

### <a name="communication-protocols"></a>通訊協定

裝置可用來連線到 IoT Central 的通訊協定包括 MQTT、AMQP 和 HTTPS。 IoT Central 會在內部使用 IoT 中樞來啟用裝置連線能力。 如需 IoT 中樞支援裝置連線之通訊協定的詳細資訊，請參閱[選擇通訊協定](../../iot-hub/iot-hub-devguide-protocols.md)。

## <a name="implement-the-device"></a>實作裝置

IoT Central 裝置範本包含「模型」，可指定該類型裝置應該實作的行為。 行為包括遙測、屬性和命令。

> [!TIP]
> 您可以將模型從 IoT Central 匯出為 [數位對應項定義語言 (DTDL) v2](https://github.com/Azure/opendigitaltwins-dtdl) JSON 檔案。

每個模型都有唯一的「裝置對應項模型識別碼」(DTMI)，例如 `dtmi:com:example:Thermostat;1`。 當裝置連線到 IoT Central 時，會傳送所實作之模型的 DTMI。 接著 IoT Central 可以讓正確的裝置範本與裝置產生關聯。

[IoT 隨插即用](../../iot-pnp/overview-iot-plug-and-play.md)定義一組裝置在執行 DTDL 模型時應遵循的慣例。

[Azure IoT 裝置 SDK](#languages-and-sdks) 包含對 IoT 隨插即用慣例的支援。

### <a name="device-model"></a>裝置型號

裝置模型使用 [DTDL](https://github.com/Azure/opendigitaltwins-dtdl) 來定義。 此語言可讓您定義：

- 裝置所傳送的遙測。 定義包括遙測的名稱和資料類型。 例如，裝置會以兩位數傳送溫度遙測。
- 裝置回報至 IoT Central 的屬性。 屬性定義包括其名稱和資料類型。 例如，裝置會以布林值報告閥門的狀態。
- 裝置可以從 IoT Central 接收的屬性。 您也可以將屬性標示為可寫入。 例如，IoT Central 會將兩位數的目標溫度傳送至裝置。
- 裝置會回應的命令。 定義包含命令的名稱，以及任何參數的名稱和資料類型。 例如，裝置會回應重新開機命令，指定重新開機之前要等待的秒數。

DTDL 模型可以是「無元件」或「多元件」模型：

- 無元件模型：簡單的模型不會使用內嵌或串聯的元件。 所有遙測、屬性和命令都會定義為單一「預設元件」。 如需範例，請參閱[控溫器](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)模型。
- 多元件模型。 更複雜的模型，包含兩個或更多元件。 這些元件包括一個單一預設元件，以及一或多個其他的巢狀元件。 如需範例，請參閱[溫度控制器](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)模型。

如需深入了解，請參閱[模型中的 IoT 隨插即用元件](../../iot-pnp/concepts-components.md)

### <a name="conventions"></a>慣例

當裝置與 IoT Central 交換資料時，應遵循 IoT 隨插即用慣例。 慣例包括：

- 在連線到 IoT Central 時傳送 DTMI。
- 將格式正確的 JSON 承載和中繼資料傳送至 IoT Central。
- 從 IoT Central 正確回應可寫入的屬性和命令。
- 遵循元件命令的命名慣例。

> [!NOTE]
> 目前，IoT Central 不會完全支援 DTDL **陣列** 和 **地理空間** 資料類型。

若要深入了解裝置與 IoT Central 交換的 JSON 訊息格式，請參閱[遙測、屬性和命令承載](concepts-telemetry-properties-commands.md)。

若要深入了解 IoT 隨插即用慣例用，請參閱 [IoT 隨插即用慣例](../../iot-pnp/concepts-convention.md)。

### <a name="device-sdks"></a>裝置 SDK

使用其中一個 [Azure IoT 裝置 SDK](#languages-and-sdks) 來實作您裝置的行為。 程式碼應該：

- 向 DPS 註冊裝置，並使用來自 DPS 的資訊來連線到 IoT Central 應用程式中的內部 IoT 中樞。
- 宣告裝置所實作的模型 DTMI。
- 以裝置模型所指定之格式傳送遙測。 IoT Central 會使用裝置範本中的模型決定如何將遙測用於視覺效果和分析。
- 將裝置與 IoT Central 之間的屬性值進行同步。 模型會指定屬性名稱和資料類型，讓 IoT Central 可以顯示資訊。
- 針對模型中指定的命令實作命令處理常式。 模型會指定裝置應使用的命令名稱和參數。

如需裝置範本角色的詳細資訊，請參閱 [什麼是裝置範本？](./concepts-device-templates.md)。

如需範例程式碼，請參閱 [建立及連線 Node.js 用戶端應用程式](./tutorial-connect-device-nodejs.md) 或 [建立和連線 Python 用戶端應用程式](./tutorial-connect-device-python.md)。

### <a name="languages-and-sdks"></a>語言和 SDK

如需有關支援語言和 SDK 的詳細資訊，請參閱[了解及使用 Azure IoT 中樞裝置 SDK](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks)。

## <a name="next-steps"></a>後續步驟

如果您是裝置開發人員，而且想要深入了解一些程式碼，建議的下一個步驟是[建立用戶端應用程式並將其連線到您的 Azure IoT Central 應用程式](./tutorial-connect-device-nodejs.md)。

如果您想要深入了解如何使用 IoT Central，建議的後續步驟是從[建立 Azure IoT Central 應用程式](./quick-deploy-iot-central.md)開始，試用快速入門。
