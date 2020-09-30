---
title: IoT 隨插即用架構 |Microsoft Docs
description: 作為解決方案產生器，瞭解 IoT 隨插即用的主要架構元素。
author: ridomin
ms.author: rmpablos
ms.date: 09/15/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 32e67bd7f30fecee3449935a35235844a047957b
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91574311"
---
# <a name="iot-plug-and-play-architecture"></a>IoT 隨插即用架構

IoT 隨插即用可讓解決方案產生器將智慧型裝置與解決方案整合，而不需要任何手動設定。 IoT 隨插即用的核心是一種裝置 _模型_ ，可描述裝置的功能是否已啟用 IoT 隨插即用的應用程式。 此模型會結構化為定義下列各項的一組介面：

- 代表裝置或其他實體的唯讀或可寫入狀態的_屬性_。 例如，裝置序號可能是唯讀屬性，而控溫器上的目標溫度可能是可寫入屬性。
- 屬於裝置所發出之資料的_遙測_，無論這項資料是感應器讀數的一般串流、偶爾發生的錯誤，還是資訊訊息。
- 說明可在裝置上完成之函式或作業的_命令_。 例如，命令可以重新啟動閘道或使用遠端相機拍照。

每個模型和介面都有唯一的識別碼。

下圖顯示 IoT 隨插即用解決方案的重要元素：

:::image type="content" source="media/concepts-architecture/pnp-architecture.png" alt-text="IoT 隨插即用架構":::

## <a name="model-repository"></a>模型存放庫

[模型存放庫](./concepts-model-repository.md)是模型和介面定義的存放區。 您可以使用 [數位 Twins 定義語言 (DTDL) ](https://github.com/Azure/opendigitaltwins-dtdl)來定義模型和介面。

Web UI 可讓您管理模型和介面。

模型存放庫會使用 RBAC，讓您限制對介面定義的存取。

## <a name="devices"></a>裝置

裝置產生器會使用其中一個 [Azure IoT 裝置 sdk](./libraries-sdks.md)來執行程式碼，以在 IoT 智慧型裝置上執行。 裝置 Sdk 可協助裝置產生器：

- 安全地連接到 IoT 中樞。
- 向 IoT 中樞註冊裝置，並宣告模型識別碼，以識別裝置所執行的 DTDL 介面集合。
- 同步處理裝置與 IoT 中樞之間 DTDL 介面中定義的屬性。
- 針對 DTDL 介面中定義的命令，加入命令處理常式。
- 將遙測傳送至 IoT 中樞。

## <a name="iot-edge-gateway"></a>IoT Edge 閘道

IoT Edge 閘道可做為連線無法直接連線到 IoT 中樞 IoT 隨插即用裝置的媒介。 若要深入瞭解，請參閱 [如何使用 IoT Edge 裝置作為閘道](../iot-edge/iot-edge-as-gateway.md)。

## <a name="iot-edge-modules"></a>IoT Edge 模組

_IoT Edge 模組_可讓您在邊緣部署及管理商務邏輯。 Azure IoT Edge 模組是 IoT Edge 管理的最小計算單位，可以包含 Azure 服務 (例如 Azure 串流分析) 或您自己的解決方案特定程式碼。

_IoT Edge 中樞_是組成 Azure IoT Edge 執行時間的其中一個模組。 它藉由公開與 IoT 中樞相同的通訊協定端點來作為 IoT 中樞的本機 Proxy。 此一致性表示用戶端 (不論是裝置或模組) 都可連線到 IoT Edge 執行階段，就像它們對 IoT 中樞所做的。

裝置 Sdk 可協助模組產生器：

- 使用 IoT Edge 中樞安全地連接到您的 IoT 中樞。
- 向 IoT 中樞註冊模組，並宣告模型識別碼，以識別裝置所執行的 DTDL 介面集合。
- 同步處理裝置與 IoT 中樞之間 DTDL 介面中定義的屬性。
- 針對 DTDL 介面中定義的命令，加入命令處理常式。
- 將遙測傳送至 IoT 中樞。

## <a name="iot-hub"></a>IoT 中樞

[Iot 中樞](../iot-hub/about-iot-hub.md) 是雲端託管的服務，可作為 IoT 解決方案與其所管理裝置之間雙向通訊的中央訊息中樞。

IoT 中樞：

- 使裝置所執行的模型識別碼可供後端解決方案使用。
- 維護與每個連線至中樞隨插即用裝置相關聯的數位對應項。
- 將遙測串流轉送至其他服務以進行處理或儲存。
- 將數位對應項變更事件路由至其他服務，以啟用裝置監視。

## <a name="backend-solution"></a>後端解決方案

後端解決方案會藉由與 IoT 中樞內的數位 twins 互動，來監視和控制連線的裝置。 使用其中一個服務 Sdk 來執行您的後端解決方案。 若要瞭解已連線裝置的功能，解決方案後端：

1. 抓取裝置向 IoT 中樞註冊的模型識別碼。
1. 使用模型識別碼，從任何模型存放庫取出介面定義。
1. 使用模型剖析器，從介面定義中解壓縮資訊。

後端解決方案可以使用介面定義中的資訊來：

- 讀取裝置所報告的屬性值。
- 更新裝置上的可寫入屬性。
- 呼叫裝置所執行的命令。
- 瞭解裝置所傳送的遙測格式。

## <a name="next-steps"></a>後續步驟

現在您已大致瞭解 IoT 隨插即用解決方案的架構，接下來的步驟是深入瞭解：

- [模型存放庫](./concepts-model-repository.md)
- [數位對應項模型整合](./concepts-model-discovery.md)
- [針對 IoT 隨插即用進行開發](./concepts-developer-guide-device-csharp.md)
