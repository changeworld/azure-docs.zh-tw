---
title: 內置邊緣代理直接方法 - Azure IoT 邊緣
description: 使用 IoT Edge 代理運行時模組中的內置直接方法監視和管理 IoT Edge 部署
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 57b9d46918414cef9e8cbcffb941b98c98f985ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240352"
---
# <a name="communicate-with-edgeagent-using-built-in-direct-methods"></a>使用內置直接方法與邊緣代理通信

使用 IoT Edge 代理模組中包含的直接方法監視和管理 IoT Edge 部署。 直接方法在設備上實現，然後可以從雲調用。 IoT Edge 代理包括直接方法，可説明您遠端監視和管理 IoT Edge 設備。

有關直接方法、如何使用這些方法以及如何在您自己的模組中實現它們的詳細資訊，請參閱[從 IoT 中心瞭解和調用直接方法](../iot-hub/iot-hub-devguide-direct-methods.md)。

這些直接方法的名稱不區分大小寫。

## <a name="ping"></a>Ping

**ping**方法可用於檢查 IoT Edge 是否在設備上運行，或者設備是否與 IoT 中心具有打開的連接。 使用此直接方法 ping IoT Edge 代理並獲取其狀態。 成功的 ping 返回空有效負載和 **"狀態"：200**。

例如：

```azurecli
az iot hub invoke-module-method --method-name 'ping' -n <hub name> -d <device name> -m '$edgeAgent'
```

在 Azure 門戶中，使用方法名稱`ping`和空 JSON 負載`{}`調用 方法。

![在 Azure 門戶中調用直接方法"ping"](./media/how-to-edgeagent-direct-method/ping-direct-method.png)

## <a name="restart-module"></a>重新開機模組

**RestartModule**方法允許遠端系統管理在 IoT 邊緣設備上運行的模組。 如果模組報告失敗狀態或其他不正常行為，則可以觸發 IoT Edge 代理重新開機它。 成功的重新開機命令返回空有效負載和 **"狀態"：200**。

重新開機模組方法在 IoT 邊緣版本 1.0.9 及更高版本中可用。 

您可以在 IoT 邊緣設備上運行的任何模組（包括邊緣代理模組本身）上使用 RestartModule 直接方法。 但是，如果使用此直接方法關閉 EdgeAgent，則不會收到成功結果，因為在模組重新開機時連接中斷。

例如：

```azurecli
az iot hub invoke-module-method --method-name 'RestartModule' -n <hub name> -d <device name> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "id": "<module name>"
    }
'
```

在 Azure 門戶中，使用方法名稱`RestartModule`和以下 JSON 負載調用方法：

```json
{
    "schemaVersion": "1.0",
    "id": "<module name>"
}
```

![在 Azure 門戶中調用直接方法"重新開機模組"](./media/how-to-edgeagent-direct-method/restartmodule-direct-method.png)

## <a name="experimental-methods"></a>實驗方法

新的直接方法選項可作為實驗功能進行測試，包括：

* [上載日誌](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md)：檢索模組日誌並將其上載到 Azure Blob 存儲。
* [獲取任務狀態](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#gettaskstatus)：檢查上載日誌請求的狀態。
* [GetLogs](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#getlogs)：在直接方法的回應中內聯檢索模組日誌。

## <a name="next-steps"></a>後續步驟

[IoT Edge 代理程式和 IoT Edge 中樞模組對應項的屬性](module-edgeagent-edgehub.md)
