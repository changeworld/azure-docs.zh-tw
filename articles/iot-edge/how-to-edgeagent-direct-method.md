---
title: 內建 edgeAgent 直接方法-Azure IoT Edge
description: 使用 IoT Edge 代理程式執行時間模組中的內建直接方法來監視和管理 IoT Edge 部署
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 57b9d46918414cef9e8cbcffb941b98c98f985ff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80240352"
---
# <a name="communicate-with-edgeagent-using-built-in-direct-methods"></a>使用內建直接方法與 edgeAgent 通訊

使用 IoT Edge agent 模組中包含的直接方法，監視和管理 IoT Edge 部署。 直接方法會在裝置上執行，然後可以從雲端叫用。 IoT Edge 代理套裝程式含可協助您從遠端監視和管理 IoT Edge 裝置的直接方法。

如需直接方法、如何使用它們，以及如何在自己的模組中執行這些方法的詳細資訊，請參閱 [瞭解並從 IoT 中樞叫用直接方法](../iot-hub/iot-hub-devguide-direct-methods.md)。

這些直接方法的名稱會以不區分大小寫的方式處理。

## <a name="ping"></a>Ping

**Ping**方法適用于檢查裝置上是否有 IoT Edge 正在執行，或裝置是否有 IoT 中樞的開啟連線。 使用這個直接方法來偵測 IoT Edge 代理程式，並取得其狀態。 成功的 ping 會傳回空的承載和 **"status"： 200**。

例如：

```azurecli
az iot hub invoke-module-method --method-name 'ping' -n <hub name> -d <device name> -m '$edgeAgent'
```

在 Azure 入口網站中，以方法名稱和空的 JSON 承載叫用方法 `ping` `{}` 。

![在 Azure 入口網站中叫用直接方法 ' ping '](./media/how-to-edgeagent-direct-method/ping-direct-method.png)

## <a name="restart-module"></a>重新開機模組

**RestartModule**方法可讓您從遠端系統管理在 IoT Edge 裝置上執行的模組。 如果模組報告失敗狀態或其他狀況不良的行為，您可以觸發 IoT Edge 代理程式來重新開機它。 成功的重新開機命令會傳回空的承載和 **"status"： 200**。

RestartModule 方法可在 IoT Edge 版本1.0.9 和更新版本中取得。 

您可以在 IoT Edge 裝置上執行的任何模組（包括 edgeAgent 模組本身）上使用 RestartModule 直接方法。 但是，如果您使用此直接方法來關閉 edgeAgent，您將不會收到成功的結果，因為在模組重新開機時，連接會中斷。

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

在 Azure 入口網站中，使用方法名稱和下列 JSON 承載來叫用方法 `RestartModule` ：

```json
{
    "schemaVersion": "1.0",
    "id": "<module name>"
}
```

![在 Azure 入口網站中叫用直接方法 ' RestartModule '](./media/how-to-edgeagent-direct-method/restartmodule-direct-method.png)

## <a name="experimental-methods"></a>實驗方法

新的直接方法選項可作為測試的實驗性功能，包括：

* [UploadLogs](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md)：取出模組記錄，並將它們上傳至 Azure Blob 儲存體。
* [GetTaskStatus](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#gettaskstatus)：檢查上傳記錄要求的狀態。
* [GetLogs](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#getlogs)：取出直接方法回應中內嵌的模組記錄。

## <a name="next-steps"></a>後續步驟

[IoT Edge 代理程式和 IoT Edge 中樞模組對應項的屬性](module-edgeagent-edgehub.md)
