---
title: 使用 Azure IoT 中心 (CLI) 進行大規模自動裝置管理 |微軟文件
description: 使用 Azure IoT 中心自動設定管理多個 IoT 裝置或模組
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: robinsh
ms.openlocfilehash: 60d0ef30a1c7d948a9e837a8bc37c76ace415545
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024960"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-cli"></a>使用 Azure CLI 自動 IoT 裝置與模組管理

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Azure IoT Hub 中的自動裝置管理可自動執行管理大型設備佇列的許多重複性和複雜的任務。 通過自動裝置管理,您可以根據設備的屬性定位一組設備,定義所需的配置,然後讓 IoT 中心在設備進入作用域時更新它們。 此更新使用_自動設備配置_或_自動模組配置_完成,它允許您總結完成和合規性,處理合併和衝突,並分階段推出配置。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

自動設備管理的工作原理是更新一組具有所需屬性的設備孿生或模組孿生,並報告基於孿生報告屬性的摘要。  它引入了一個新的類和 JSON 文檔,稱為*配置*,包含三個部分:

* **目標條件**定義要更新的設備孿生或模組孿生的範圍。 目標條件會以查詢的方式指定於裝置對應項標籤和/或報告屬性上。

* **目標內容**定義要在目標設備孿生或模組孿生中添加或更新所需的屬性。 內容包含了一個路徑，連往所要變更屬性的區段。

* **計量**會定義各種設定狀態 (例如 **Success**、**Progress** 及 **Error**) 的摘要計數。 自定義指標指定為對孿生報告屬性的查詢。  系統指標是衡量孿生更新狀態的默認指標,例如目標雙胞胎數和已成功更新的雙胞胎數。

自動配置在創建配置后不久首次運行,然後每隔五分鐘運行一次。 每次運行自動配置時都會運行指標查詢。

## <a name="cli-prerequisites"></a>CLI 先決條件

* Azure 訂閱中的[IoT 中心](../iot-hub/iot-hub-create-using-cli.md)。 

* 您環境中的 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 至少,Azure CLI 版本必須為 2.0.70 或以上。 使用 `az –-version` 進行驗證。 這個版本支援 az 擴充命令並引進 Knack 命令架構。 

* [Azure CLI 的 IoT 延伸](https://github.com/Azure/azure-cli)。

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="implement-twins"></a>實施雙胞胎

自動裝置設定需要使用裝置對應項，以同步處理雲端和裝置之間的狀態。  有關詳細資訊,請參閱在[IoT 中心中瞭解和使用裝置孿生](iot-hub-devguide-device-twins.md)。

自動模組配置需要使用模組孿生來在雲和模組之間同步狀態。 有關詳細資訊,請參閱在[IoT 中心中瞭解和使用模組孿生](iot-hub-devguide-module-twins.md)。

## <a name="use-tags-to-target-twins"></a>使用標記定位雙胞胎

在創建配置之前,必須指定要影響的設備或模組。 Azure IoT 中心標識設備並使用設備孿生中的標記,並使用模組孿生中的標記標識模組。 每個設備或模組可以有多個標記,您可以以任何對解決方案有意義的方式定義它們。 例如,如果您管理不同位置的設備,則向設備孿生添加以下標記:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="define-the-target-content-and-metrics"></a>定義目標內容和計量

目標內容和指標查詢被指定為 JSON 文檔,用於描述要設置和報告要測量的屬性的設備孿生或模組孿生所需屬性。  要使用 Azure CLI 建立自動設定,請將目標內容和指標本地保存為 .txt 檔。 執行命令時,在後面部分中使用文件路徑將配置應用於設備。

下面是自動裝置設定的基本目標內容範例:

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

自動模組設定的行為非常相似,但您的目標是`moduleContent``deviceContent`而不是 。

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

模組的指標查詢也類似於裝置的查詢,但您可以從中選擇`moduleId``devices.modules`。 例如： 

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

```azurecli
   az iot hub configuration create --config-id [configuration id] \
     --labels [labels] --content [file path] --hub-name [hub name] \
     --target-condition [target query] --priority [int] \
     --metrics [metric queries]
```

* --**config-id** - 將會在 IoT 中樞建立的組態名稱。 為設定指定唯一的名稱，最長為 128 個小寫字母。 避免空格和下列無效字元：`& ^ [ ] { } \ | " < > /`。

* --**labels** - 新增標籤以協助追蹤您的組態。 標籤是成對的「名稱, 值」組合，可描述您的部署。 例如，`HostPlatform, Linux` 或 `Version, 3.0.1`。

* --**content** - 要設為對應項所需屬性的目標內容內嵌 JSON 或檔案路徑。 

* --**hub-name** - 要在其中建立組態的 IoT 中樞名稱。 中樞必須在目前訂用帳戶中。 使用 `az account set -s [subscription name]` 命令切換到所需的訂用帳戶

* --**目標條件**- 輸入目標條件,確定此設定將針對哪些設備或模組。對於自動設備配置,條件基於設備孿生標記或設備孿生所需屬性,並且應匹配表達式格式。例如，`tags.environment='test'` 或 `properties.desired.devicemodel='4000x'`。對於自動模組配置,條件基於模組孿生標記或模組孿生所需屬性。 例如，`from devices.modules where tags.environment='test'` 或 `from devices.modules where properties.reported.chillerProperties.model='4000x'`。

* --**priority** - 正整數。 如果兩個或多個配置針對同一設備或模組,則應用具有優先順序最高數值的配置。

* --**metrics** - 計量查詢的檔案路徑。 指標提供設備或模組在應用配置內容後可能報告的各種狀態的匯總計數。 例如，您可以建立擱置設定變更的計量、錯誤的計量，以及成功設定變更的計量。 

## <a name="monitor-a-configuration"></a>監視設定

使用下列命令來顯示組態的內容︰

```azurecli
az iot hub configuration show --config-id [configuration id] \
  --hub-name [hub name]
```

* --**config-id** - 存在於 IoT 中樞的組態名稱。

* --**hub-name** - 組態存在於其中的 IoT 中樞名稱。 中樞必須在目前訂用帳戶中。 使用 `az account set -s [subscription name]` 命令切換到所需的訂用帳戶

在命令視窗中檢查組態。**metrics** 屬性會列出每個中樞所評估每個計量的計數：

* **目標計數**- 一個系統指標,用於指定 IoT 中心中與目標條件匹配的設備孿生或模組孿生的數量。

* **應用計數**- 系統指標指定應用目標內容的設備或模組數。

* **自訂指標**- 您定義的任何指標都是用戶指標。

可以使用以下指令顯示每個指標的裝置 ID、模組 ID 或物件的清單:

```azurecli
az iot hub configuration show-metric --config-id [configuration id] \
   --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
```

* --**config-id** - 存在於 IoT 中樞的部署名稱。

* --**公制代碼**- 要檢視裝置 ID 或模組 ID 清單`appliedCount`的指標名稱,例如 。

* --**hub-name** - 部署存在於其中的 IoT 中樞名稱。 中樞必須在目前訂用帳戶中。 使用 `az account set -s [subscription name]` 命令切換到所需的訂用帳戶。

* --**metric-type** - 計量類型可以是 `system` 或 `user`。  系統計量是 `targetedCount` 和 `appliedCount`。 其他所有計量是使用者計量。

## <a name="modify-a-configuration"></a>修改設定

當您修改設定時，所做的變更會立即複寫到所有目標裝置。 

如果您更新目標條件，就會發生下列更新：

* 如果孿生不符合舊的目標條件,但滿足新的目標條件,並且此配置是該孿生的最高優先順序,則應用此配置。 

* 如果當前運行此配置的孿生不再滿足目標條件,則配置中的設置將被刪除,並且孿生將由下一個最高優先順序配置修改。 

* 如果當前運行此配置的孿生不再滿足目標條件,並且不符合任何其他配置的目標條件,則將刪除配置中的設置,並且不會對孿生進行其他更改。 

使用以下命令更新組態：

```azurecli
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

刪除配置時,任何設備孿生或模組孿生都將採用其下一個最高優先順序配置。 如果孿生不符合任何其他配置的目標條件,則不應用其他設置。 

使用以下命令刪除組態：

```azurecli
az iot hub configuration delete --config-id [configuration id] \
   --hub-name [hub name] 
```

* --**config-id** - 存在於 IoT 中樞的組態名稱。

* --**hub-name** - 組態存在於其中的 IoT 中樞名稱。 中樞必須在目前訂用帳戶中。 使用 `az account set -s [subscription name]` 命令切換到所需的訂用帳戶。

## <a name="next-steps"></a>後續步驟

在本文中,您學習了如何大規模配置和監視 IoT 設備。 遵循下列連結以深入了解如何管理 Azure IoT 中樞：

* [管理大量的 IoT 中樞裝置身分識別](iot-hub-bulk-identity-mgmt.md)
* [IoT 中樞度量](iot-hub-metrics.md)
* [作業監視](iot-hub-operations-monitoring.md)

若要進一步探索 IoT 中樞的功能，請參閱︰

* [IoT 中心開發人員指南](iot-hub-devguide.md)
* [使用 Azure IoT Edge 將 AI 部署到 Edge 裝置](../iot-edge/tutorial-simulate-device-linux.md)

若要探索使用 IoT 中樞裝置佈建服務進行 Just-In-Time 自動佈建，請參閱： 

* [Azure IoT 中樞裝置佈建服務](/azure/iot-dps)
