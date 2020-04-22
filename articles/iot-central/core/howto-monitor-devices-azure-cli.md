---
title: 使用 Azure IoT Central Explorer 監視裝置連線
description: 透過 IoT Central Explorer CLI 監視裝置訊息，並觀察裝置對應項變更。
author: viv-liu
ms.author: viviali
ms.date: 03/27/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 1a6106a45f5062850ceb12205528a05ed1d494be
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756661"
---
# <a name="monitor-device-connectivity-using-azure-cli"></a>使用 Azure CLI 監視裝置連線能力

*本主題適用於設備開發人員和解決方案生成器。*

使用 Azure CLI IoT 擴展可查看設備發送到 IoT 中心的消息,並觀察設備孿生中的更改。 您可以使用此工具來調試和觀察設備連接,並診斷未到達雲端的設備訊息或未回應孿生更改的設備的問題。

[有關詳細資訊,請造訪 Azure CLI 延伸參考](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/central?view=azure-cli-latest)

## <a name="prerequisites"></a>Prerequisites

+ Azure CLI 已安裝,是版本 2.0.7 或更高版本。 通過運行`az --version`檢查 Azure CLI 的版本。 瞭解如何從[Azure CLI 文件](https://docs.microsoft.com/cli/azure/install-azure-cli)安裝和更新
+ Azure 中作為使用者添加到IoT中央應用程式中的工作或學校帳戶。

## <a name="install-the-iot-central-extension"></a>安裝 IoT 中心延伸

從您的命令列執行下列命令以安裝：

```azurecli
az extension add --name azure-iot
```

執行檢查延伸的版本:

```azurecli
az --version
```

您應該會看到 azure iot 擴展為 0.8.1 或更高。 如果不是,則運行:

```azurecli
az extension update --name azure-iot
```

## <a name="using-the-extension"></a>使用延伸模組

以下各節介紹運行`az iot central`時可以使用的常見命令和選項。 要查看完整的命令和選項集,請查看`--help``az iot central`或其任何子命令。

### <a name="login"></a>登入

首先登錄到 Azure CLI。 

```azurecli
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>取得 IoT 中心應用程式 ID
在 **'管理/ 應用程式設定' 中**,複製**應用程式代碼**。 在後面的步驟中使用此值。

### <a name="monitor-messages"></a>監視訊息
監視從設備發送到IoT中心應用的消息。 輸出包括所有標頭和註釋。

```azurecli
az iot central app monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>檢視裝置屬性
查看給定設備的當前讀/寫設備屬性。

```azurecli
az iot central device-twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>後續步驟

如果您是設備開發人員,建議的下一步是閱讀[Azure IoT 中央 中有關設備連接](./concepts-get-connected.md)的資訊。
