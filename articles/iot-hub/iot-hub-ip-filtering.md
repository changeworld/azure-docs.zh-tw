---
title: Azure IoT 中樞 IP 連線篩選器 | Microsoft Docs
description: 如何使用 IP 篩選來封鎖從特定 IP 位址至 Azure IoT 中樞的連接。 您可以封鎖來自個別 IP 位址或 IP 位址範圍的連接。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/22/2017
ms.author: robinsh
ms.openlocfilehash: b1550254e969e96fbc83c4c344189d414a8fa8d3
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2020
ms.locfileid: "82995514"
---
# <a name="use-ip-filters"></a>使用 IP 篩選器

安全性是任何以 Azure IoT 中樞為基礎之 IoT 解決方案的重要一環。 有時候您需要在執行安全性設定的程序中明確地指定可連線的裝置 IP 位址。 「IP 篩選器」** 功能可讓您設定規則，以拒絕或接受來自特定 IPv4 位址的流量。

## <a name="when-to-use"></a>使用時機

有兩個特定使用案例適合封鎖特定 IP 位址的 IoT 中樞端點︰

* 您的 IoT 中樞只應接收來自指定 IP 位址範圍的流量，並拒絕其他所有流量。 例如，您搭配 [Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) 使用 IoT 中樞來建立 IoT 中樞與內部部署基礎結構之間的私人連線。

* 您需要拒絕 IoT 中樞系統管理員認為可疑的 IP 位址所傳來的流量。

## <a name="how-filter-rules-are-applied"></a>篩選器規則的套用方式

IP 篩選器規則會套用在 IoT 中樞服務層級。 因此，IP 篩選器規則會套用至來自裝置和後端應用程式的所有連接 (使用任何受支援的通訊協定)。

嘗試建立連線的 IP 位址若符合 IoT 中樞內的拒絕 IP 規則，將會收到未授權 401 狀態碼和描述。 回應訊息則不涉及 IP 規則。

## <a name="default-setting"></a>預設設定

根據預設，IoT 中樞之入口網站中的 **IP 篩選器**方格是空的。 這個預設設定表示您的中樞會接受來自任何 IP 位址的連線。 這項預設設定等同於可接受 0.0.0.0/0 IP 位址範圍的規則。

![IoT 中樞預設 IP 篩選器設定](./media/iot-hub-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>新增或編輯 IP 篩選器規則

若要新增 IP 篩選規則，請選取 [ **+ 新增 Ip 篩選規則**]。

![新增 IP 篩選器規則到 IoT 中樞](./media/iot-hub-ip-filtering/ip-filter-add-rule.png)

選取 [**新增 IP 篩選規則**] 之後，請填寫欄位。

![選取 [新增 IP 篩選規則] 之後](./media/iot-hub-ip-filtering/ip-filter-after-selecting-add.png)

* 提供 IP 篩選規則的**名稱**。 這必須是唯一、不區分大小寫的英數位元字串，最長可達128個字元。 所能接受的字元只有 ASCII 7 位元英數字元以及 `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`。

* 提供單一 IPv4 位址或以 CIDR 標記法表示的 IP 位址區塊。 例如，在 CIDR 表示法中，192.168.100.0/22 表示從 192.168.100.0 到 192.168.103.255 的 1024 個 IPv4 位址。

* 選取 [**允許**] 或 [**封鎖**] 做為 IP 篩選規則的**動作**。

填寫欄位之後，請選取 [**儲存**] 以儲存規則。 您會看到警示，通知您正在進行更新。

![有關儲存 IP 篩選器規則的通知](./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png)

當您達到 10 個 IP 篩選器規則的上限後，[新增]**** 選項便會停用。

若要編輯現有的規則，請選取您要變更的資料，進行變更，然後選取 [**儲存**] 以儲存編輯。

> [!NOTE]
> 拒絕 IP 位址可防止其他 Azure 服務 (例如 Azure 串流分析、Azure 虛擬機器，或入口網站中的裝置總管) 與 IoT 中樞互動。

> [!WARNING]
> 如果您使用 Azure 串流分析（ASA）從啟用 IP 篩選的 IoT 中樞讀取訊息，請使用 IoT 中樞的事件中樞相容名稱和端點，以手動方式在 ASA 中新增[事件中樞串流輸入](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-inputs#stream-data-from-event-hubs)。

## <a name="delete-an-ip-filter-rule"></a>刪除 IP 篩選器規則

若要刪除 IP 篩選規則，請選取該資料列上的垃圾桶圖示，然後選取 [**儲存**]。 此規則會被移除，並儲存變更。

![刪除 IoT 中樞 IP 篩選器規則](./media/iot-hub-ip-filtering/ip-filter-delete-rule.png)

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>使用 Azure CLI 擷取和更新 IP 篩選條件

您可以透過 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 擷取和更新 IoT 中樞的 IP 篩選條件。

若要擷取 IoT 中樞目前的 IP 篩選條件，請執行：

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

這會傳回 JSON 物件，其中您現有的 IP 篩選條件會列在 `properties.ipFilterRules` 機碼之下：

```json
{
...
    "properties": {
        "ipFilterRules": [
        {
            "action": "Reject",
            "filterName": "MaliciousIP",
            "ipMask": "6.6.6.6/6"
        },
        {
            "action": "Allow",
            "filterName": "GoodIP",
            "ipMask": "131.107.160.200"
        },
        ...
        ],
    },
...
}
```

若要為 IoT 中樞新增 IP 篩選條件，請執行：

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules "{\"action\":\"Reject\",\"filterName\":\"MaliciousIP\",\"ipMask\":\"6.6.6.6/6\"}"
```

若要移除 IoT 中樞中的現有 IP 篩選條件，請執行：

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules <ipFilterIndexToRemove>
```

請注意，`<ipFilterIndexToRemove>` 必須對應到 IoT 中樞的 `properties.ipFilterRules` 中的 IP 篩選條件排序方式。

## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>使用 Azure PowerShell 擷取和更新 IP 篩選條件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

您 IoT 中樞的 IP 篩選器可以透過[Azure PowerShell](/powershell/azure/overview)抓取和設定。

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.ipFilterRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='MaliciousIP'; 'action'='Reject'; 'ipMask'='6.6.6.6/6'}

# Add your new IP filter rule
$iothubResource.Properties.ipFilterRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.ipFilterRules = @($iothubResource.Properties.ipFilterRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

## <a name="update-ip-filter-rules-using-rest"></a>使用 REST 更新 IP 篩選規則

您也可以使用 Azure 資源提供者的 REST 端點，擷取和修改 IoT 中樞的 IP 篩選條件。 請參閱 [createorupdate 方法](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate)中的 `properties.ipFilterRules`。

## <a name="ip-filter-rule-evaluation"></a>IP 篩選器規則評估

IP 篩選器規則會依序套用，第一個符合 IP 位址的規則會決定接受或拒絕動作。

例如，如果您想要接受 192.168.100.0/22 範圍中的位址，並拒絕其他所有位址，則方格中的第一個規則應接受位址範圍 192.168.100.0/22。 下一個規則應使用 0.0.0.0/0 範圍拒絕所有位址。

按一下資料列前端呈垂直方向的三個點並使用拖放功能，即可變更方格中的 IP 篩選器規則順序。

若要儲存新的 IP 篩選器規則順序，請按一下 [儲存]****。

![變更 IoT 中樞 IP 篩選器規則的順序](./media/iot-hub-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>後續步驟

若要進一步探索 IoT 中樞的功能，請參閱︰

* [作業監視](iot-hub-operations-monitoring.md)
* [IoT 中樞度量](iot-hub-metrics.md)
