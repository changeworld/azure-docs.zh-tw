---
title: 使用 Azure IoT Central Explorer 監視裝置連線
description: 透過 IoT Central Explorer CLI 監視裝置訊息，並觀察裝置對應項變更。
author: viv-liu
ms.author: viviali
ms.date: 03/27/2020
ms.topic: how-to
ms.service: iot-central
ms.custom: devx-track-azurecli, device-developer
services: iot-central
manager: corywink
ms.openlocfilehash: 276513e41b1595180acb0a596b236428032d87a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90015971"
---
# <a name="monitor-device-connectivity-using-azure-cli"></a>使用 Azure CLI 監視裝置連線能力

*本主題適用于裝置開發人員和解決方案產生器。*

使用 Azure CLI IoT 擴充功能來查看裝置傳送至 IoT Central 的訊息，並觀察裝置對應項中的變更。 您可以使用此工具來偵測並觀察裝置的連線能力，並診斷未到達雲端或裝置未回應對應項變更的裝置訊息問題。

[如需詳細資訊，請造訪 Azure CLI 延伸模組參考](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/central?view=azure-cli-latest)

## <a name="prerequisites"></a>必要條件

+ Azure CLI 已安裝，且版本2.7.0 或更高版本。 執行，以檢查您 Azure CLI 的版本 `az --version` 。 瞭解如何從[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)檔進行安裝和更新
+ Azure 中的工作或學校帳戶，在 IoT Central 應用程式中新增為使用者。

## <a name="install-the-iot-central-extension"></a>安裝 IoT Central 擴充功能

從您的命令列執行下列命令以安裝：

```azurecli
az extension add --name azure-iot
```

藉由執行下列動作來檢查延伸模組的版本：

```azurecli
az --version
```

您應該會看到 azure-iot 擴充功能是0.9.9 或更高版本。 如果不是，請執行：

```azurecli
az extension update --name azure-iot
```

## <a name="using-the-extension"></a>使用延伸模組

下列各節說明您可以在執行時使用的常見命令和選項 `az iot central` 。 若要查看一組完整的命令和選項，請傳遞 `--help` 至 `az iot central` 或其任何子命令。

### <a name="login"></a>登入

開始登入 Azure CLI。 

```azurecli
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>取得 IoT Central 應用程式的應用程式識別碼
在 [系統 **管理/應用程式設定**] 中，複製 **應用程式識別碼**。 您會在稍後的步驟中使用此值。

### <a name="monitor-messages"></a>監視訊息
監視從您的裝置傳送至 IoT Central 應用程式的訊息。 輸出會包含所有標頭和批註。

```azurecli
az iot central diagnostics monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>檢視裝置屬性
查看指定裝置目前的讀取和讀取/寫入裝置屬性。

```azurecli
az iot central device twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>後續步驟

如果您是裝置開發人員，建議的下一個步驟是閱讀 [Azure IoT Central 中的裝置連線能力](./concepts-get-connected.md)。
