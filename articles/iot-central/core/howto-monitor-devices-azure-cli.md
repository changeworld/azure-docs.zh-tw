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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81756661"
---
# <a name="monitor-device-connectivity-using-azure-cli"></a>使用 Azure CLI 監視裝置連線能力

*本主題適用于裝置開發人員和解決方案產生器。*

使用 Azure CLI IoT 擴充功能來查看您的裝置所傳送的訊息，以 IoT Central 並觀察裝置對應項中的變更。 您可以使用此工具來偵測和觀察裝置連線能力，並診斷未到達雲端的裝置訊息問題，或未回應對應項變更的裝置。

[如需詳細資訊，請造訪 Azure CLI 延伸模組參考](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/central?view=azure-cli-latest)

## <a name="prerequisites"></a>先決條件

+ Azure CLI 已安裝，且為2.0.7 或更高版本。 執行來檢查您的 Azure CLI 版本`az --version`。 瞭解如何從[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)檔安裝和更新
+ Azure 中的工作或學校帳戶，在 IoT Central 應用程式中新增為使用者。

## <a name="install-the-iot-central-extension"></a>安裝 IoT Central 擴充功能

從您的命令列執行下列命令以安裝：

```azurecli
az extension add --name azure-iot
```

執行下列程式，檢查延伸模組的版本：

```azurecli
az --version
```

您應該會看到 azure-iot 擴充功能是0.8.1 版或更高版本。 如果不是，請執行：

```azurecli
az extension update --name azure-iot
```

## <a name="using-the-extension"></a>使用延伸模組

下列各節說明您可以在執行`az iot central`時使用的常見命令和選項。 若要查看完整的命令和選項組，請`--help`傳遞`az iot central`至或其任何子命令。

### <a name="login"></a>登入

一開始請先登入 Azure CLI。 

```azurecli
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>取得 IoT Central 應用程式的應用程式識別碼
在 [系統**管理/應用程式設定**] 中，複製 [**應用程式識別碼**]。 您會在稍後的步驟中使用此值。

### <a name="monitor-messages"></a>監視訊息
監視從您的裝置傳送到您的 IoT Central 應用程式的訊息。 輸出包含所有標頭和注釋。

```azurecli
az iot central app monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>檢視裝置屬性
查看指定裝置的目前讀取和讀取/寫入裝置屬性。

```azurecli
az iot central device-twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>後續步驟

如果您是裝置開發人員，建議的下一個步驟是閱讀[Azure IoT Central 中的裝置連線能力](./concepts-get-connected.md)。
