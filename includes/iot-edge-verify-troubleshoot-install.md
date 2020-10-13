---
title: 安裝和布建完成後，請確認是否成功並進行疑難排解
description: Include 檔案
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 10/06/2020
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 295769e5d4924e529b296dbb0b9d405ee197c1db
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979438"
---
## <a name="verify-successful-setup"></a>確認安裝成功

檢查 IoT Edge 服務的狀態。 它應該會列為 [執行中]。  

# <a name="linux"></a>[Linux](#tab/linux)

```bash
systemctl status iotedge
```

# <a name="windows"></a>[Windows](#tab/windows)

```powershell
Get-Service iotedge
```

---

檢查服務記錄。

# <a name="linux"></a>[Linux](#tab/linux)

```bash
journalctl -u iotedge --no-pager --no-full
```

# <a name="windows"></a>[Windows](#tab/windows)

如果您剛剛完成安裝 IoT Edge 執行時間，您可能會在執行 **IoTEdge** 與 **IoTEdge**之間的時間內看到錯誤清單。 這是預期的錯誤，因為服務會在設定之前嘗試啟動。

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

---

執行 [疑難排解工具](../articles/iot-edge/troubleshoot.md#run-the-check-command) ，檢查是否有最常見的設定和網路錯誤。

```powershell
iotedge check
```

在您將第一個模組部署至裝置上的 IoT Edge 之前， **$edgeHub** 系統模組將不會部署到裝置。 如此一來，自動檢查就會傳回連線檢查的錯誤 `Edge Hub can bind to ports on host` 。 除非您將模組部署到裝置之後發生此錯誤，否則可能會忽略此錯誤。

最後，列出正在執行的模組：

```powershell
iotedge list
```

在新的安裝之後，您應該會看到執行的唯一模組是 **edgeAgent**。

## <a name="tips-and-troubleshooting"></a>提示與疑難排解

在資源受限的裝置上，強烈建議將 OptimizeForPerformance** 環境變數設定為 false**，如[疑難排解指南](../articles/iot-edge/troubleshoot.md)中的指示所述。

如果您的裝置無法連線到 IoT 中樞，而且您的網路具有 proxy 伺服器，請遵循 [設定 IoT Edge 裝置中的步驟，以透過 proxy 伺服器進行通訊](../articles/iot-edge/how-to-configure-proxy-support.md)。

# <a name="linux"></a>[Linux](#tab/linux)

在 Linux 裝置上，您需要更高的許可權才能執行 `iotedge` 命令。 安裝執行階段之後，請登出您的電腦並重新登入，以自動更新您的權限。 在那之前，請使用， `sudo` 以較高的許可權執行命令。

# <a name="windows"></a>[Windows](#tab/windows)

在執行 Windows 容器的 Windows 裝置上，Moby 容器引擎會安裝為 IoT Edge 的一部分。 Moby 引擎是設計來與 Docker Desktop 平行執行。 `docker`如果您想要直接與裝置上的容器互動，您可以使用命令。 但是，如果 Docker Desktop 也安裝在裝置上，則您必須特別以 Moby 引擎為目標。

例如，若要列出所有 Docker 映射，請使用下列命令：

```powershell
docker images
```

若要列出所有 Moby 映射，請使用 Moby 引擎的指標來修改相同的命令：

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

引擎 URI 會列在安裝腳本的輸出中，您也可以在 yaml 檔案的 [容器執行時間設定] 區段中找到該 URI。

![yaml 中的 moby_runtime uri](./media/iot-edge-verify-troubleshoot-install/moby-runtime-uri.png)

---
