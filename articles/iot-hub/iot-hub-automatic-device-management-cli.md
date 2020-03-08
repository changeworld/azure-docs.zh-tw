---
title: 使用 Azure IoT 中樞（CLI）大規模的自動裝置管理 |Microsoft Docs
description: 使用 Azure IoT 中樞自動設定來管理多個 IoT 裝置或模組
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: chrisgre
ms.openlocfilehash: 381f550f6d64dee3c7649a040c1e24b7c9d42f2c
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78669447"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-cli"></a>使用 Azure CLI 自動進行 IoT 裝置和模組管理

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Azure IoT 中樞中的自動裝置管理，可將管理大型裝置機群的許多重複且複雜的工作自動化。 使用自動裝置管理時，您可以根據裝置的內容，定義所需的設定，然後讓 IoT 中樞在裝置進入範圍時進行更新。 此更新是使用_自動裝置_設定或_自動模組_設定來完成，這可讓您摘要完成和合規性、處理合併和衝突，以及以階段式方法推出設定。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

自動裝置管理的運作方式是使用所需的屬性來更新一組裝置 twins 或模組 twins，並根據對應項報告屬性來報告摘要。  它引進了名為設定的新類別和 JSON 檔 *，其中包含*三個部分：

* **目標條件**會定義要更新之裝置 twins 或模組 twins 的範圍。 目標條件會以查詢的方式指定於裝置對應項標籤和/或報告屬性上。

* **目標內容**會定義要在目標裝置 twins 或模組 twins 中新增或更新的所需屬性。 內容包含了一個路徑，連往所要變更屬性的區段。

* **計量**會定義各種設定狀態 (例如 **Success**、**Progress** 及 **Error**) 的摘要計數。 自訂計量會指定為對應項報告屬性上的查詢。  系統計量是測量對應項更新狀態的預設度量，例如目標的 twins 數目，以及已成功更新的 twins 數目。

第一次在建立設定之後，以及每隔五分鐘的間隔執行自動設定。 每次執行自動設定時，都會執行計量查詢。

## <a name="cli-prerequisites"></a>CLI 先決條件

* Azure 訂用帳戶中的 [IoT 中樞](../iot-hub/iot-hub-create-using-cli.md)。 

* 您環境中的 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 您的 Azure CLI 版本至少必須為2.0.70 或以上。 使用 `az –-version` 進行驗證。 這個版本支援 az 擴充命令並引進 Knack 命令架構。 

* [適用於 Azure CLI 的 IoT 擴充功能](https://github.com/Azure/azure-cli) \(英文\)。

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="implement-twins"></a>執行 twins

自動裝置設定需要使用裝置對應項，以同步處理雲端和裝置之間的狀態。  如需詳細資訊，請參閱[了解和使用 Azure IoT 中樞的裝置對應項](iot-hub-devguide-device-twins.md)。

自動模組設定需要使用模組 twins 來同步處理雲端與模組之間的狀態。 如需詳細資訊，請參閱[瞭解及使用 IoT 中樞中的模組 twins](iot-hub-devguide-module-twins.md)。

## <a name="use-tags-to-target-twins"></a>使用標籤來以 twins 為目標

建立設定之前，您必須指定想要影響的裝置或模組。 Azure IoT 中樞可識別裝置，並在裝置對應項中使用標記，並使用模組對應項中的標籤來識別模組。 每個裝置或模組都可以有多個標籤，而您可以用任何對解決方案有意義的方式來定義它們。 例如，如果您管理不同位置的裝置，請將下列標記新增至裝置對應項：

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="define-the-target-content-and-metrics"></a>定義目標內容和計量

目標內容和計量查詢會指定為 JSON 檔，以描述要設定的裝置對應項或模組對應項所需屬性，以及要測量的屬性。  若要使用 Azure CLI 建立自動設定，請在本機將目標內容和計量儲存為 .txt 檔案。 當您執行命令以將設定套用至裝置時，您會在稍後的章節中使用檔案路徑。

以下是自動裝置設定的基本目標內容範例：

```json
{
  "content": {
    "deviceContent": {
      "properties.desired.chillerWaterSettings": {
        "temperature": 38,
        "pressure": 78
      }
    }
}
```

自動模組設定的行為非常類似，但您的目標是 `moduleContent`，而不是 `deviceContent`。

```json
{
  "content": {
    "moduleContent": {
      "properties.desired.chillerWaterSettings": {
        "temperature": 38,
        "pressure": 78
      }
    }
}
```

以下是計量查詢的範例：

```json
{
  "queries": {
    "Compliant": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'",
    "Error": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='error'",
    "Pending": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='pending'"
  }
}
```

模組的計量查詢也類似于裝置的查詢，但您選取 `devices.modules`的 `moduleId`。 例如， 

```json
{
  "queries": {
    "Compliant": "select deviceId, moduleId from devices.module where configurations.[[chillermodulesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'"
  }
}
```

## <a name="create-a-configuration"></a>建立設定

您可以建立由目標內容和計量組成的組態，來設定目標裝置。 

使用以下命令建立組態：

```cli
   az iot hub configuration create --config-id [configuration id] \
     --labels [labels] --content [file path] --hub-name [hub name] \
     --target-condition [target query] --priority [int] \
     --metrics [metric queries]
```

* --**config-id** - 將會在 IoT 中樞建立的組態名稱。 為設定指定唯一的名稱，最長為 128 個小寫字母。 避免空格和下列無效字元：`& ^ [ ] { } \ | " < > /`。

* --**labels** - 新增標籤以協助追蹤您的組態。 標籤是成對的「名稱, 值」組合，可描述您的部署。 例如，`HostPlatform, Linux` 或 `Version, 3.0.1`。

* --**content** - 要設為對應項所需屬性的目標內容內嵌 JSON 或檔案路徑。 

* --**hub-name** - 要在其中建立組態的 IoT 中樞名稱。 中樞必須在目前訂用帳戶中。 使用 `az account set -s [subscription name]` 命令切換到所需的訂用帳戶

* --**目標-條件**-輸入目標條件，以決定將以這種設定為目標的裝置或模組。 針對自動裝置設定，條件是以裝置對應項標籤或裝置對應項所需屬性為基礎，且應符合運算式格式。 例如，`tags.environment='test'` 或 `properties.desired.devicemodel='4000x'`。 針對自動模組設定，條件是以模組對應項標記或模組對應項所需屬性為基礎。 例如，`from devices.modules where tags.environment='test'` 或 `from devices.modules where properties.reported.chillerProperties.model='4000x'`。

* --**priority** - 正整數。 如果有兩個或多個設定的目標為相同的裝置或模組，則會套用具有最高優先順序數值的設定。

* --**metrics** - 計量查詢的檔案路徑。 計量會提供裝置或模組在套用設定內容之後可能回報的各種狀態的摘要計數。 例如，您可以建立擱置設定變更的計量、錯誤的計量，以及成功設定變更的計量。 

## <a name="monitor-a-configuration"></a>監視設定

使用下列命令來顯示組態的內容︰

```cli
az iot hub configuration show --config-id [configuration id] \
  --hub-name [hub name]
```

* --**config-id** - 存在於 IoT 中樞的組態名稱。

* --**hub-name** - 組態存在於其中的 IoT 中樞名稱。 中樞必須在目前訂用帳戶中。 使用 `az account set -s [subscription name]` 命令切換到所需的訂用帳戶

在命令視窗中檢查組態。 [**計量**] 屬性會列出每個中樞所評估之每個度量的計數：

* **targetedCount** -系統計量，指定 IoT 中樞中符合目標條件的裝置 twins 或模組 twins 數目。

* **appliedCount** -系統度量指定已套用目標內容的裝置或模組數目。

* **您的自訂度量**-您定義的任何計量都是使用者計量。

您可以使用下列命令，顯示每個計量的裝置識別碼、模組識別碼或物件清單：

```cli
az iot hub configuration show-metric --config-id [configuration id] \
   --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
```

* --**config-id** - 存在於 IoT 中樞的部署名稱。

* --計量**識別碼**-您想要查看裝置識別碼或模組識別碼清單的度量名稱，例如 `appliedCount`。

* --**hub-name** - 部署存在於其中的 IoT 中樞名稱。 中樞必須在目前訂用帳戶中。 使用 `az account set -s [subscription name]` 命令切換到所需的訂用帳戶。

* --**metric-type** - 計量類型可以是 `system` 或 `user`。  系統計量是 `targetedCount` 和 `appliedCount`。 其他所有計量是使用者計量。

## <a name="modify-a-configuration"></a>修改設定

當您修改設定時，所做的變更會立即複寫到所有目標裝置。 

如果您更新目標條件，就會發生下列更新：

* 如果對應項不符合舊的目標條件，但符合新的目標條件，而此設定是該對應項的最高優先順序，則會套用此設定。 

* 如果目前正在執行此設定的對應項不再符合目標條件，則會移除設定中的設定，並由下一個最高優先順序的設定來修改對應項。 

* 如果目前執行此設定的對應項不再符合目標條件，且不符合任何其他設定的目標條件，則會移除設定中的設定，而且不會在對應項上進行其他變更。 

使用以下命令更新組態：

```cli
az iot hub configuration update --config-id [configuration id] \
   --hub-name [hub name] --set [property1.property2='value']
```

* --**config-id** - 存在於 IoT 中樞的組態名稱。

* --**hub-name** - 組態存在於其中的 IoT 中樞名稱。 中樞必須在目前訂用帳戶中。 使用 `az account set -s [subscription name]` 命令切換到所需的訂用帳戶。

* --**set** - 更新組態中的屬性。 您可以更新下列屬性：

    * targetCondition - 例如 `targetCondition=tags.location.state='Oregon'`

    * 標籤 

    * priority

## <a name="delete-a-configuration"></a>刪除設定

當您刪除設定時，任何裝置 twins 或模組 twins 都會採用其下一個最高優先順序的設定。 如果 twins 不符合任何其他設定的目標條件，則不會套用其他設定。 

使用以下命令刪除組態：

```cli
az iot hub configuration delete --config-id [configuration id] \
   --hub-name [hub name] 
```
* --**config-id** - 存在於 IoT 中樞的組態名稱。

* --**hub-name** - 組態存在於其中的 IoT 中樞名稱。 中樞必須在目前訂用帳戶中。 使用 `az account set -s [subscription name]` 命令切換到所需的訂用帳戶。

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何大規模設定和監視 IoT 裝置。 遵循下列連結以深入了解如何管理 Azure IoT 中樞：

* [管理大量的 IoT 中樞裝置身分識別](iot-hub-bulk-identity-mgmt.md)
* [IoT 中樞計量](iot-hub-metrics.md)
* [作業監視](iot-hub-operations-monitoring.md)

若要進一步探索 IoT 中樞的功能，請參閱︰

* [IoT 中樞開發人員指南](iot-hub-devguide.md)
* [使用 Azure IoT Edge 將 AI 部署到 Edge 裝置](../iot-edge/tutorial-simulate-device-linux.md)

若要探索使用 IoT 中樞裝置佈建服務進行 Just-In-Time 自動佈建，請參閱： 

* [Azure IoT 中樞裝置佈建服務](/azure/iot-dps)
