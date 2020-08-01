---
title: IoT 隨插即用架構 |Microsoft Docs
description: 作為解決方案產生器，瞭解 IoT 隨插即用的主要架構元素。
author: ridomin
ms.author: rmpablos
ms.date: 07/06/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f656de0bb2e5244e137ae21a6d7af88f3430b12c
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475680"
---
# <a name="iot-plug-and-play-preview-architecture"></a>IoT 隨插即用預覽版架構

IoT 隨插即用 Preview 可讓解決方案產生器將智慧型裝置與解決方案整合，而不需要任何手動設定。 IoT 隨插即用的核心是一種裝置_型號_，可描述裝置的 iot 隨插即用啟用應用程式的功能。 此模型是結構化的一組介面，可定義：

- 代表裝置或其他實體之唯讀或可寫入狀態的_屬性_。 例如，裝置序號可能是唯讀屬性，而控溫器上的目標溫度可能是可寫入的屬性。
- _遙測_資料是由裝置所發出，不論資料是感應器讀數的一般串流、偶爾發生的錯誤，或資訊訊息。
- 說明可在裝置上完成之函式或作業的_命令_。 例如，命令可以重新啟動閘道或使用遠端相機拍照。

每個模型和介面都有唯一的識別碼。

下圖顯示 IoT 隨插即用解決方案的主要元素：

:::image type="content" source="media/concepts-architecture/pnp-architecture.png" alt-text="IoT 隨插即用架構":::

## <a name="model-repository"></a>模型存放庫

[模型儲存](./concepts-model-repository.md)機制是模型和介面定義的存放區。 您可以使用[數位 Twins 定義語言（DTDL）](https://github.com/Azure/opendigitaltwins-dtdl)來定義模型和介面。

Web UI 可讓您管理模型和介面。

模型存放庫會使用 RBAC，讓您限制對介面定義的存取。

## <a name="devices"></a>裝置

「裝置建立器」會使用其中一個[Azure IoT 裝置 sdk](./libraries-sdks.md)，來執行要在 IoT 智慧型裝置上執行的程式碼。 裝置 Sdk 可協助裝置產生器：

- 安全地連接到 IoT 中樞。
- 向 IoT 中樞註冊裝置，並宣告模型識別碼，以識別裝置所執行介面的集合。
- 更新裝置所執行之 DTDL 介面中定義的屬性。 這些屬性是使用可管理 IoT 中樞同步處理的數位 twins 來執行。
- 為裝置所執行之 DTDL 介面中定義的命令新增命令處理常式。
- 將遙測傳送至 IoT 中樞。

## <a name="iot-hub"></a>IoT 中樞

[IoT 中樞](../iot-hub/about-iot-hub.md)是一種雲端託管服務，可做為您的 IoT 解決方案與其所管理裝置之間雙向通訊的中央訊息中樞。

IoT 中樞：

- 讓裝置所執行的模型識別碼可供後端解決方案使用。
- 維護與連線到中樞的每個隨插即用裝置相關聯的數位對應項。
- 將遙測串流轉送到其他服務以進行處理或儲存。
- 將數位對應項變更事件路由至其他服務，以啟用裝置監視。

## <a name="backend-solution"></a>後端解決方案

後端解決方案會藉由與 IoT 中樞中的數位 twins 互動，來監視和控制已連線的裝置。 使用其中一個服務 Sdk 來執行您的後端解決方案。 若要瞭解已連線裝置的功能，解決方案後端：

1. 抓取裝置向 IoT 中樞註冊的模型識別碼。
1. 會使用模型識別碼來抓取任何模型存放庫中的介面定義。
1. 使用模型剖析器，從介面定義中解壓縮資訊。

後端解決方案可以使用介面定義中的資訊來執行下列動作：

- 讀取裝置所報告的屬性值。
- 更新裝置上可寫入的屬性。
- 呼叫裝置所執行的命令。
- 瞭解裝置所傳送的遙測格式。

## <a name="next-steps"></a>後續步驟

既然您已大致瞭解 IoT 隨插即用解決方案的架構，接下來的步驟是深入瞭解：

- [模型存放庫](./concepts-model-repository.md)
- [數位對應項模型整合](./concepts-model-discovery.md)
- [針對 IoT 隨插即用進行開發](./concepts-developer-guide.md)
