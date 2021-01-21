---
title: Azure IoT 中樞 IP 篩選器 | Microsoft Docs
description: 如何使用 IP 篩選來允許從特定 IP 位址連線到您的 Azure IoT 中樞。
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/19/2020
ms.author: jlian
ms.openlocfilehash: c6544e8ac00744602476207a89567aea5afe5b1d
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632349"
---
# <a name="use-ip-filters"></a>使用 IP 篩選器

安全性是任何以 Azure IoT 中樞為基礎之 IoT 解決方案的重要一環。 有時候您需要在執行安全性設定的程序中明確地指定可連線的裝置 IP 位址。 「IP 篩選器」功能可讓您設定規則，以拒絕或接受來自特定 IPv4 位址的流量。

## <a name="when-to-use"></a>使用時機

使用 IP 篩選器只接收來自指定 IP 位址範圍的流量，並拒絕其他所有流量。 例如，您使用 IoT 中樞搭配 [Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) 來建立 iot 中樞與內部部署基礎結構之間的私人連線。

## <a name="default-setting"></a>預設設定

若要移至 [IP 篩選器設定] 頁面，請選取 [網路]、[公用存取]，然後選擇 [選取的 IP 範圍]：

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-default.png" alt-text="IoT 中樞預設 IP 篩選器設定":::

根據預設，IoT 中樞之入口網站中的 **IP 篩選器** 方格是空的。 此預設設定表示您的中樞會封鎖來自所有 IP 位址的連接。 此預設設定等同于封鎖 `0.0.0.0/0` IP 位址範圍的規則。

## <a name="add-or-edit-an-ip-filter-rule"></a>新增或編輯 IP 篩選器規則

若要新增 IP 篩選器規則，請選取 [+ 新增 IP 篩選器規則]。

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-add-rule.png" alt-text="新增 IP 篩選器規則到 IoT 中樞":::

選取 [新增 IP 篩選器規則] 之後，請填寫欄位。

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-after-selecting-add.png" alt-text="選取 [新增 IP 篩選器規則] 之後":::

* 提供 IP 篩選器規則的 **名稱**。 這個名稱必須是唯一、不區分大小寫的英數位元字串，長度最多為128個字元。 所能接受的字元只有 ASCII 7 位元英數字元以及 `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`。

* 提供單一 IPv4 位址或以 CIDR 標記法表示的 IP 位址區塊。 例如，在 CIDR 表示法中，192.168.100.0/22 表示從 192.168.100.0 到 192.168.103.255 的 1024 個 IPv4 位址。

填寫欄位之後，請選取 [儲存] 以儲存規則。 您會看到通知您正在進行更新的警示。

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png" alt-text="有關儲存 IP 篩選器規則的通知":::

當您達到 10 個 IP 篩選器規則的上限後，[新增] 選項便會停用。

若要編輯現有的規則，請選取您要變更的資料，進行變更，然後選取 [儲存] 以儲存您的編輯。

## <a name="delete-an-ip-filter-rule"></a>刪除 IP 篩選器規則

若要刪除 IP 篩選器規則，請選取該資料列上的垃圾桶圖示，然後選取 [儲存]。 此規則會被移除，並儲存變更。

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-delete-rule.png" alt-text="刪除 IoT 中樞 IP 篩選器規則":::

## <a name="apply-ip-filter-rules-to-the-built-in-event-hub-compatible-endpoint"></a>將 IP 篩選規則套用至內建事件中樞相容端點

若要將 IP 篩選規則套用至內建事件中樞相容端點，請核取 [ **將 ip 篩選套用至內建端點**] 旁的核取方塊，然後選取 [ **儲存**]。

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-built-in-endpoint.png" alt-text="顯示內建端點和儲存切換的影像":::

> [!NOTE]
> 此選項不適用於免費 (F1) IoT 中樞。 若要將 IP 篩選規則套用至內建端點，請使用付費的 IoT 中樞。

藉由啟用此選項，您的 IP 篩選器規則會複寫至內建端點，因此只有受信任的 IP 範圍可以存取它。

如果您停用此選項，則所有 IP 位址都可存取內建端點。 如果您想要從具有變更 IP 位址（例如 Azure 串流分析）的服務讀取端點，此行為可能會很有用。 

## <a name="how-filter-rules-are-applied"></a>篩選器規則的套用方式

IP 篩選器規則會套用在 IoT 中樞服務層級。 因此，IP 篩選器規則會套用至來自裝置和後端應用程式的所有連接 (使用任何受支援的通訊協定)。 此外，您也可以選擇 [內建事件中樞相容端點](iot-hub-devguide-messages-read-builtin.md) (不是透過 IoT 中樞連接字串) 系結至這些規則。 

從未明確允許的 IP 位址進行的任何連線嘗試都會收到未經授權的401狀態碼和描述。 回應訊息則不涉及 IP 規則。 拒絕 IP 位址可防止其他 Azure 服務 (例如 Azure 串流分析、Azure 虛擬機器，或 Azure 入口網站中的裝置總管) 與 IoT 中樞互動。

> [!NOTE]
> 如果您必須使用 Azure 串流分析 (ASA) 從啟用 IP 篩選器的 IoT 中樞讀取訊息，請 **停** 用 [ **將 ip 篩選套用至內建端點** ] 選項，然後使用 IoT 中樞的事件中樞相容名稱和端點，在 ASA 中手動新增 [事件中樞資料流程輸入](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs) 。

### <a name="ordering"></a>排序

IP 篩選規則可 *允許* 規則並套用，而不需要排序。 只允許您新增的 IP 位址連線到 IoT 中樞。 

例如，如果您想要接受範圍內的位址 `192.168.100.0/22` 並拒絕所有其他專案，您只需要在具有位址範圍的方格中新增一個規則 `192.168.100.0/22` 。

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>使用 Azure CLI 擷取和更新 IP 篩選條件

您可以透過 [Azure CLI](/cli/azure/) 擷取和更新 IoT 中樞的 IP 篩選條件。

若要擷取 IoT 中樞目前的 IP 篩選條件，請執行：

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

這會傳回 JSON 物件，其中您現有的 IP 篩選條件會列在 `properties.networkRuleSets` 機碼之下：

```json
{
...
    "properties": {
        "networkRuleSets": {
            "defaultAction": "Deny",
            "applyToBuiltInEventHubEndpoint": true,
            "ipRules": [{
                    "filterName": "TrustedFactories",
                    "action": "Allow",
                    "ipMask": "1.2.3.4/5"
                },
                {
                    "filterName": "TrustedDevices",
                    "action": "Allow",
                    "ipMask": "1.1.1.1/1"
                }
            ]
        }
    }
}
```

若要為 IoT 中樞新增 IP 篩選條件，請執行：

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.networkRuleSets.ipRules "{\"action\":\"Allow\",\"filterName\":\"TrustedIP\",\"ipMask\":\"192.168.0.1\"}"
```

若要移除 IoT 中樞中的現有 IP 篩選條件，請執行：

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.networkRuleSets.ipRules <ipFilterIndexToRemove>
```

在這裡， `<ipFilterIndexToRemove>` 必須對應到 IoT 中樞的 IP 篩選器順序 `properties.networkRuleSets.ipRules` 。

## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>使用 Azure PowerShell 擷取和更新 IP 篩選條件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

您可以透過 [Azure PowerShell](/powershell/azure/) 擷取和設定 IoT 中樞的 IP 篩選器。

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.networkRuleSets.ipRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='TrustedIP'; 'action'='Allow'; 'ipMask'='192.168.0.1'}

# Add your new IP filter rule
$iothubResource.Properties.networkRuleSets.ipRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.networkRuleSets.ipRules = @($iothubResource.Properties.networkRuleSets.ipRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

## <a name="update-ip-filter-rules-using-rest"></a>使用 REST 更新 IP 篩選規則


您也可以使用 Azure 資源提供者的 REST 端點，擷取和修改 IoT 中樞的 IP 篩選條件。 請參閱 [createorupdate 方法](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate)中的 `properties.networkRuleSets`。

## <a name="ip-filter-classic-retirement"></a>IP 篩選 (傳統) 淘汰

傳統 IP 篩選已淘汰。 若要深入瞭解，請參閱 [IoT 中樞傳統 IP 篩選器和如何升級](iot-hub-ip-filter-classic.md)。

## <a name="next-steps"></a>後續步驟

若要進一步探索 IoT 中樞的功能，請參閱︰

* [IoT 中樞計量](iot-hub-metrics.md)
* [IoT 中樞利用 Private Link 和受控識別支援虛擬網路](virtual-network-support.md)
* [管理 IoT 中樞的公用網路存取](iot-hub-public-network-access.md)
* [監視 IoT 中樞](monitor-iot-hub.md)
