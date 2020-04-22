---
title: 通用介面 - IoT 隨插即用預覽 |微軟文件
description: 物聯網隨插即用開發者通用介面的說明
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5773ec2b3ea88fa9a507b7c1b0b84bb7ea305a94
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770478"
---
# <a name="iot-plug-and-play-preview-common-interfaces"></a>IoT 插即用預覽通用介面

所有 IoT 隨插即用設備都有望實現一些通用介面。 通用介面使IoT解決方案受益,因為它們提供了一致的功能。 [認證](tutorial-build-device-certification.md)要求您的設備實現多個通用介面。 可以從公共模型存儲庫檢索通用介面定義。

## <a name="summary-of-common-interfaces"></a>常見介面摘要

| 名稱 | ID | 描述 | 由 Azure IoT SDK 實作 | 必須在功能模型中聲明 |
| -------- | -------- | -------- | -------- | -------- | -------- |
| 模型資訊 | urn:azureiot:模型發現:模型資訊:1 | 供設備聲明功能型號 ID 和介面。 所有 IoT 隨插即用裝置都需要。 | 是 | 否 |
| 數位雙用戶端 SDK 資訊 | urn:azureiot:用戶端:SDK資訊:1 | 用於將設備與 Azure 連接的用戶端 SDK。 [認證](tutorial-build-device-certification.md)所需的 | 是 | 否 |
| 裝置資訊 | urn:azureiot:設備管理:設備資訊:1 | 有關設備的硬體和作業系統資訊。 [認證](tutorial-build-device-certification.md)所需的 | 否 | 是 |
| 模型定義 | urn:azureiot:模型發現:模型定義:1 | 供設備聲明其功能模型和介面的完整定義。 當模型定義未託管在模型存儲庫中時,必須實現。 | 否 | 是 |
| 數位分身 | urn:azureiot:模型發現:數位孿生:1 | 供解決方案開發人員檢索數位孿生的功能模型 ID 和介面 ID。 此介面不是由 IoT 隨插即用裝置聲明或實現的。 | 否 | 否 |

- 由 Azure IoT SDK 實現 - Azure IoT SDK 是否實現在介面中聲明的功能。 使用 Azure IoT SDK 的 IoT 隨插即用裝置不需要實現此介面。
- 必須在功能模型中聲明 - 如果為"是",則必須在此`"implements":`IoT 隨插即用裝置的裝置功能模型部分中聲明此介面。

## <a name="retrieve-interface-definitions-from-the-public-repository"></a>從公共儲存庫檢索介面定義

### <a name="cli"></a>CLI

可以使用 Azure CLI 的 Azure IoT 擴展從公共模型儲存庫檢索公共介面。

```azurecli
az iot pnp interface show --interface {InterfaceID}
```

```azurecli
az iot pnp capability-model show --model {ModelID}
```

### <a name="vs-code"></a>VS 程式碼

1. 使用 **Ctrl+Shift+P** 開啟命令選擇區。

1. 輸入**隨插即用**,然後選擇**IoT 隨插即用:打開模型儲存庫**命令。 選擇**公共儲存函式庫**。 公用模型存放庫會在 VS Code 中開啟。

1. 在公共模型存儲庫中,在搜索欄位中輸入介面名稱。

1. 要創建介面的本地複本,請在搜尋結果中選擇它,然後選擇 **「下載**」。

## <a name="next-steps"></a>後續步驟

現在,您已經瞭解了通用介面,下面是一些其他資源:

- [數字雙定義語言 (DTDL)](https://aka.ms/DTDL)
- [C 裝置 SDK](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [物聯網 REST API](https://docs.microsoft.com/rest/api/iothub/device)
