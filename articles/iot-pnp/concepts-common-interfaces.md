---
title: 通用介面-IoT 隨插即用預覽 |Microsoft Docs
description: IoT 隨插即用開發人員通用介面的描述
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5773ec2b3ea88fa9a507b7c1b0b84bb7ea305a94
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770478"
---
# <a name="iot-plug-and-play-preview-common-interfaces"></a>IoT 隨插即用預覽通用介面

所有 IoT 隨插即用裝置都應該執行一些通用介面。 通用介面可受益于 IoT 解決方案，因為它們提供一致的功能。 [認證](tutorial-build-device-certification.md)需要您的裝置執行數個通用介面。 您可以從公用模型存放庫取得通用介面定義。

## <a name="summary-of-common-interfaces"></a>通用介面的摘要

| Name | 識別碼 | 描述 | 由 Azure IoT SDK 實行 | 必須在功能模型中宣告 |
| -------- | -------- | -------- | -------- | -------- | -------- |
| 模型資訊 | urn： azureiot： ModelDiscovery： ModelInformation：1 | 用於宣告功能模型識別碼和介面的裝置。 所有 IoT 隨插即用裝置都需要。 | 是 | 否 |
| 數位對應項用戶端 SDK 資訊 | urn： azureiot： Client： SDKInformation：1 | 用來將裝置連接到 Azure 的用戶端 SDK。 [認證](tutorial-build-device-certification.md)所需 | 是 | 否 |
| 裝置資訊 | urn： azureiot：]： DeviceInformation：1 | 裝置的硬體和作業系統相關資訊。 [認證](tutorial-build-device-certification.md)所需 | 否 | 是 |
| 模型定義 | urn： azureiot： ModelDiscovery： ModelDefinition：1 | 適用于裝置，以宣告其功能模型和介面的完整定義。 當模型定義不是裝載于模型存放庫中時，必須執行。 | 否 | 是 |
| 數位分身 | urn： azureiot： ModelDiscovery： DigitalTwin：1 | 供解決方案開發人員取得數位對應項的功能模型識別碼和介面識別碼。 IoT 隨插即用裝置不會宣告或執行此介面。 | 否 | 否 |

- 由 Azure IoT SDK 實行-Azure IoT SDK 是否會執行介面中宣告的功能。 使用 Azure IoT SDK 的 IoT 隨插即用裝置不需要執行此介面。
- 必須在功能模型中宣告-如果為 [是]，則必須在此 IoT 隨插即用`"implements":`裝置的裝置功能模型區段內宣告此介面。

## <a name="retrieve-interface-definitions-from-the-public-repository"></a>從公用存放庫取出介面定義

### <a name="cli"></a>CLI

您可以使用適用于 Azure CLI 的 Azure IoT 擴充功能，從公用模型存放庫中取出通用介面。

```azurecli
az iot pnp interface show --interface {InterfaceID}
```

```azurecli
az iot pnp capability-model show --model {ModelID}
```

### <a name="vs-code"></a>VS 程式碼

1. 使用 **Ctrl+Shift+P** 開啟命令選擇區。

1. 輸入**隨插即用**然後選取 [ **IoT 隨插即用：開啟模型存放庫**] 命令。 選擇 [**公用存放庫**]。 公用模型存放庫會在 VS Code 中開啟。

1. 在公用模型存放庫中，于 [搜尋] 欄位中輸入介面名稱。

1. 若要建立介面的本機複本，請在搜尋結果中選取該複本，然後選取 [**下載**]。

## <a name="next-steps"></a>後續步驟

既然您已瞭解一般介面，以下是一些額外的資源：

- [數位對應項定義語言（DTDL）](https://aka.ms/DTDL)
- [C 裝置 SDK](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
