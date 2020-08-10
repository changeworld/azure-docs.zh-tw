---
title: 新功能 IoT 隨插即用預覽版重新整理 | Microsoft Docs
description: 了解 IoT 隨插即用預覽版重新整理版本的新功能。
author: rido-min
ms.author: rmpablos
ms.date: 07/06/2020
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: eliotgra
ms.openlocfilehash: 60ad7f5df4f13d626d7a2c24990c7f48db28d01d
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475221"
---
# <a name="iot-plug-and-play-preview-refresh"></a>IoT 隨插即用預覽版重新整理

本文說明 2020 年 7 月的 IoT 隨插即用預覽版重新整理版本中，SDK、程式庫、工具和服務的主要變更。 先前的 IoT 隨插即用預覽版是 2019 年 8 月。

## <a name="digital-twins-definition-language-dtdl"></a>數位分身定義語言 (DTDL)

此版本新增 [DTDL v2](https://github.com/Azure/opendigitaltwins-dtdl) 的支援，並且淘汰 [DTDL v1](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL/v1-preview)。

下列清單顯示 DTDL v1 和 DTDL v2 之間的主要差異。 在 DTDL v2 中：

- 模型識別碼的前置詞是 `dtmi`，而不是 `urn`。 數位分身模型識別碼 (DTMI) 取代了在 DTDL v1 中所使用具有 `urn` 前置詞的數位分身識別碼。 版本現在會在前面加上 `;`。 例如，先前您是使用 `urn:CompanyName:Model:1`，而現在是使用 `dtmi:CompanyName:Model;1`。
- 將 `@context` 設定為 `dtmi:dtdl:context;2`，而不是 `http://azureiot.com/v1/contexts/IoTModel.json`。
- 使用**介面**類型，而不是 **CapabilityModel** 類型，來建立裝置模型。
- **元件**會取代**介面**執行個體。 您可以將**關聯性**和**元件**定義為**介面**內容的一部分。
- **介面**只可以繼承自另一個**介面**。
- 您可以使用_可擴充的語義類型_來擴充 DTDL。 這個可擴充類型系統提供比硬式編碼的語義類型更大的彈性，例如 DTDL v1 中的溫度和濕度。
- 已移除 **displayUnit** 屬性。
- 您不能在名稱中使用前置或後置底線。 名稱中的前置底線是保留供系統使用。

若要使用 DTDL v1，您必須使用先前版本的 SDK 和 Azure IoT Explorer 0.10.x。 若要使用 DTDL v2，您必須使用最新版本的 SDK 和 Azure IoT Explorer 0.11.x。

## <a name="no-component-and-multiple-component-implementations"></a>沒有元件和多個元件實作

使用一些遙測、命令或屬性的簡單裝置，可以在單一介面中加以描述，而不需要使用元件。 任何現有的裝置都可以藉由宣告**模型識別碼**而成為 IoT 隨插即用，不需要變更現有的裝置實作。

更複雜的裝置可能會將不同介面上的遙測、命令和屬性群組在一起，以管理複雜性並且跨裝置重複使用。 這些裝置必須更新，以遵循 [IoT 隨插即用預覽版訊息慣例](concepts-convention.md)中定義的一組簡單規則。

## <a name="registration-and-discovery"></a>註冊與探索

在此版本中，裝置會使用每個連線上的 IoT 中樞來宣告其**模型識別碼**。 IoT 中樞會快取**模型識別碼**，讓後端解決方案使用裝置對應項 `modelId` 屬性來取出**模型識別碼**。 **模型識別碼**也可以從數位分身中的 `$metadata.$model` 取出。

## <a name="microsoft-defined-interfaces"></a>Microsoft 定義的介面

下列 Microsoft 定義的介面已被取代，而且不會在新的模型存放庫中發佈：

- **urn:azureiot:ModelDiscovery:DigitalTwin:1**
- **urn:azureiot:ModelDiscovery:ModelInformation:1**

下列介面會在新的模型存放庫中發佈：在 URL [https://repo.azureiotrepository.com/Models/dtmi:azure:DeviceManagement:DeviceInformation ；1?api-version=2020-05-01-preview](https://repo.azureiotrepository.com/Models/dtmi:azure:DeviceManagement:DeviceInformation;1?api-version=2020-05-01-preview) 中提供的 `dtmi:azure:DeviceManagement:DeviceInformation;1` 。

## <a name="digitaltwinchangeevents"></a>DigitalTwinChangeEvents

**DigitalTwinChangeEvents** [事件來源](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)的事件結構已變更為使用 **JSON 修補程式**格式。 這項變更是一個中斷性變更，沒有回溯相容性支援。

## <a name="message-routing"></a>訊息路由

遙測訊息在 [SystemProperties](../iot-hub/iot-hub-devguide-messages-construct.md) 集合內具有下列變更。

現在包含 **dt-dataschema** 屬性，這是裝置所註冊的**模型識別碼**。

**dt-subject** 屬性代表傳送遙測訊息的元件。

**iothub-interface-name** 屬性已被取代。

## <a name="device-and-service-sdks"></a>裝置與服務 SDK

SDK 的先前預覽版本沒有回溯相容性。 如果您移至最新的 SDK 預覽版本，則需要變更您的程式碼。

使用以慣例為基礎的方法時，不需要個別的裝置用戶端 SDK。 從此預覽版本開始，現有的 **DigitalTwinClient** 程式庫在所有語言中會被取代。 相反地，IoT 中樞裝置用戶端 SDK 已更新為包含一個選項，以宣告**模型識別碼**。

未使用元件的裝置需要最少的程式碼變更，只要宣告**模型識別碼**即可。 使用多個元件的更複雜裝置可能需要一些可重複使用的函式，才能實作[慣例](concepts-convention.md)。 裝置範例包含一組您可以在裝置實作中重複使用的函式。

### <a name="service-sdks"></a>服務 SDK

服務 SDK 可在 [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/digitaltwins-preview/digitaltwins/service/readme.md) 和 [Python](https://github.com/Azure/azure-iot-sdk-python/blob/digitaltwins-preview/azure-iot-hub/README.md) 中取得。

## <a name="vs-code-extension"></a>VS Code 擴充功能

[Azure IoT Device Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) 擴充功能提供 DTDL v1 的撰寫支援、與舊版模型存放庫的整合，以及程式碼的產生。

如果您需要 VS Code 中的 DTDL v2 撰寫支援，請在 VS Code 中安裝新的 [DTDL 擴充功能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)。 擴充功能不會提供與模型存放庫的整合或程式碼的產生。 現在您可以使用 [Web UI](https://aka.ms/iotmodelrepo) 或 [CLI](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp?view=azure-cli-latest) 來管理存放庫中的模型。

## <a name="digital-twin-service-side-rest-apis"></a>數位分身服務端 REST API

[數位分身服務端 REST API](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin) 和承載已變更。 支援的 API 為：

- 取出數位分身。
- 呼叫命令。
- 使用 **JSON 修補程式**承載來更新數位分身。

在此版本中會繼續支援現有的 REST API。

## <a name="model-repository"></a>模型存放庫

現在有一個單一模型存放庫，其中包含公用已發佈模型和私用 RBAC 保護公司模型。 所有模型都有唯一的識別碼，一旦建立之後就會固定。

此版本不支援先前版本的現有公司模型存放庫。 您可以繼續使用 [Azure IoT 認證](https://preview.catalog.azureiotsolutions.com/products)網站來管理舊的 DTDL v1 模型。 不過，您無法再使用此網站來註冊、測試及認證裝置。

## <a name="azure-iot-central"></a>Azure IoT 中心

目前正在更新 Azure IoT Central，以支援 IoT 隨插即用預覽版重新整理版本。
