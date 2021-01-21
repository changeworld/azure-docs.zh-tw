---
title: Azure IoT 中樞傳統 IP 篩選 (已淘汰) |Microsoft Docs
description: 如何從傳統 IP 篩選器升級和優點
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: jlian
ms.openlocfilehash: 70cea7a388c07bee9caa2e25e4061a3d3bb2b460
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633900"
---
# <a name="iot-hub-classic-ip-filter-and-how-to-upgrade"></a>IoT 中樞傳統 IP 篩選器和如何升級 

IoT 中樞升級的 IP 篩選器會保護內建端點，而且預設為安全的。 雖然我們不會進行重大變更，但升級的 IP 篩選器的增強式安全性模型與傳統的 IP 篩選器不相容，因此我們宣佈淘汰。 若要深入瞭解新升級的 IP 篩選器，請參閱 [新功能](#whats-new) 和 [IoT 中樞 IP 篩選](iot-hub-ip-filtering.md)器。

為了避免服務中斷，您必須在遷移期限之前執行引導式升級，此時將會自動執行升級。 若要深入瞭解遷移時程表，請參閱 [Azure 更新](https://aka.ms/ipfilterv2azupdate)。

## <a name="how-to-upgrade"></a>如何升級

1.  造訪 Azure 入口網站
2.  瀏覽至您的 IoT 中樞。
3.  從左側功能表中選取 [ **網路** ]。
4.  您應該會看到橫幅，提示您將 IP 篩選器升級至新的模型。 選取 [是] 以繼續。
    :::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-upgrade-banner.png" alt-text="顯示橫幅提示以從傳統 IP 篩選器升級的影像":::
5.  由於新的 IP 篩選器預設會封鎖所有 IP，因此升級會移除您的個別拒絕規則，但在儲存之前讓您有機會進行審核。 請仔細檢查規則，以確保它們能為您工作。
6.  遵循提示以完成升級。

## <a name="whats-new"></a>最新消息

### <a name="secure-by-default"></a>預設保護

傳統 IP 篩選器會依預設以隱含方式允許所有 IP 位址連線到 IoT 中樞，而這與最常見的網路安全性案例並不一致。 一般來說，您只想要信任的 IP 位址能夠連線到 IoT 中樞，並拒絕其他所有專案。 若要使用傳統 IP 篩選達成此目標，這是一個多步驟的處理常式。 例如，如果您只想接受來自的流量 `192.168.100.0/22` ，您必須

1. 設定的單一 *允許* 規則 `192.168.100.0/22` 。
1. 針對 (「全部封鎖」規則設定不同的 *封鎖* 規則 `0.0.0.0/0`) 
1. 請確定已正確排序規則，並將允許規則排列在封鎖規則的上方。

在實務上，這個多步驟進程會造成混淆。 使用者未設定「封鎖全部」規則，或未正確地排序規則，導致非預期的風險。 

新的 IP 篩選器預設會封鎖所有 IP 位址。 只允許您明確新增的 IP 範圍連線到 IoT 中樞。 在上述範例中，不再需要步驟2和3。 這項新行為可依 [預設安全準則](https://wikipedia.org/wiki/Secure_by_default)來簡化設定和遵守。

### <a name="protect-the-built-in-event-hub-compatible-endpoint"></a>保護內建事件中樞相容端點

傳統 IP 篩選器無法套用至內建端點。 這項限制表示，具有封鎖所有規則的事件 (區塊 `0.0.0.0/0`) 設定，則仍可從任何 IP 位址存取內建端點。

新的 IP 篩選器提供將規則套用至內建端點的選項，可減少網路安全性威脅的風險。

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-built-in-endpoint.png" alt-text="顯示要套用至內建端點之切換的影像":::

> [!NOTE]
> 此選項不適用於免費 (F1) IoT 中樞。 若要將 IP 篩選規則套用至內建端點，請使用付費的 IoT 中樞。

### <a name="api-impact"></a>API 影響

從 (以及) 和更新版本的 IoT 中樞資源 API 都提供升級的 IP 篩選器 `2020-08-31` `2020-08-31-preview` 。 傳統 IP 篩選器仍可在所有 API 版本中使用，但將會在未來的 API 版本中移除（接近遷移期限）。 若要深入瞭解遷移時程表，請參閱 [Azure 更新](https://aka.ms/ipfilterv2azupdate)。

## <a name="tip-try-the-changes-before-they-apply"></a>秘訣：在套用之前先嘗試變更

由於新的 IP 篩選準則預設會封鎖所有 IP 位址，因此個別的封鎖規則不再相容。 因此，引導式升級程式會移除這些個別的區塊規則。 

若要嘗試使用傳統 IP 篩選器進行變更：

1. 造訪 IoT 中樞內的 [ **網路** 功能] 索引標籤
1. 請注意您現有的 IP 篩選器 (傳統) 設定，以防您想要復原
1. 在 [ **封鎖** 規則] 旁邊，選取垃圾桶圖示以移除它們
1. 在底部新增另一個規則 `0.0.0.0/0` ，並選擇 [**封鎖**]
1. 選取 [儲存]。

此設定會模擬新的 IP 篩選器在從傳統升級後的行為。 其中一個例外狀況是內建的 endpoint protection，無法嘗試使用傳統 IP 篩選器。 不過，這項功能是選擇性的，因此，如果您認為可能會中斷某些動作，就不需要使用它。

## <a name="tip-check-diagnostic-logs-for-all-ip-connections-to-your-iot-hub"></a>秘訣：檢查診斷記錄中是否有 IoT 中樞的所有 IP 連接

為了確保順利轉換，請在 [連線] 類別下檢查您的診斷記錄。 尋找 `maskedIpAddress` 屬性來查看範圍是否如您所預期。 請記住：新的 IP 篩選器會封鎖尚未明確新增的所有 IP 位址。

## <a name="iot-hub-classic-ip-filter-documentation-retired"></a>IoT 中樞傳統 IP 篩選檔 (淘汰) 

> [!IMPORTANT]
> 以下是傳統 IP 篩選器的原始檔案，即將淘汰。

安全性是任何以 Azure IoT 中樞為基礎之 IoT 解決方案的重要一環。 有時候您需要在執行安全性設定的程序中明確地指定可連線的裝置 IP 位址。 「IP 篩選器」功能可讓您設定規則，以拒絕或接受來自特定 IPv4 位址的流量。

### <a name="when-to-use"></a>使用時機

有兩個特定使用案例適合封鎖特定 IP 位址的 IoT 中樞端點︰

* 您的 IoT 中樞只應接收來自指定 IP 位址範圍的流量，並拒絕其他所有流量。 例如，您搭配 [Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) 使用 IoT 中樞來建立 IoT 中樞與內部部署基礎結構之間的私人連線。

* 您需要拒絕 IoT 中樞系統管理員認為可疑的 IP 位址所傳來的流量。

### <a name="how-filter-rules-are-applied"></a>篩選器規則的套用方式

IP 篩選器規則會套用在 IoT 中樞服務層級。 因此，IP 篩選器規則會套用至來自裝置和後端應用程式的所有連接 (使用任何受支援的通訊協定)。 不過，直接從[內建事件中樞相容端點](iot-hub-devguide-messages-read-builtin.md) (而不是透過 IoT 中樞連接字串) 讀取的用戶端，並不會繫結至 IP 篩選器規則。 

嘗試建立連線的 IP 位址若符合 IoT 中樞內的拒絕 IP 規則，將會收到未授權 401 狀態碼和描述。 回應訊息則不涉及 IP 規則。 拒絕 IP 位址可防止其他 Azure 服務 (例如 Azure 串流分析、Azure 虛擬機器，或 Azure 入口網站中的裝置總管) 與 IoT 中樞互動。

> [!NOTE]
> 如果您必須使用 Azure 串流分析 (ASA) 從啟用 IP 篩選的 IoT 中樞讀取訊息，請在 ASA 中使用 IoT 中樞之與事件中樞相容的名稱和端點來手動新增[事件中樞串流輸入](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs)。

### <a name="default-setting"></a>預設設定

根據預設，IoT 中樞之入口網站中的 **IP 篩選器** 方格是空的。 這個預設設定表示您的中樞會接受來自任何 IP 位址的連線。 這項預設設定等同於可接受 0.0.0.0/0 IP 位址範圍的規則。

若要移至 [IP 篩選器設定] 頁面，請選取 [網路]、[公用存取]，然後選擇 [選取的 IP 範圍]：

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-default.png" alt-text="IoT 中樞預設 IP 篩選器設定":::

### <a name="add-or-edit-an-ip-filter-rule"></a>新增或編輯 IP 篩選器規則

若要新增 IP 篩選器規則，請選取 [+ 新增 IP 篩選器規則]。

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-add-rule.png" alt-text="新增 IP 篩選器規則到 IoT 中樞":::

選取 [新增 IP 篩選器規則] 之後，請填寫欄位。

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-after-selecting-add.png" alt-text="選取 [新增 IP 篩選器規則] 之後":::

* 提供 IP 篩選器規則的 **名稱**。 名稱必須是唯一的、不區分大小寫的英數字元字串，長度上限為 128 個字元。 所能接受的字元只有 ASCII 7 位元英數字元以及 `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`。

* 提供單一 IPv4 位址或以 CIDR 標記法表示的 IP 位址區塊。 例如，在 CIDR 表示法中，192.168.100.0/22 表示從 192.168.100.0 到 192.168.103.255 的 1024 個 IPv4 位址。

* 選取 [允許] 或 [封鎖] 作為 IP 篩選器規則的 **動作**。

填寫欄位之後，請選取 [儲存] 以儲存規則。 您會看到通知您正在進行更新的警示。

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-save-new-rule.png" alt-text="有關儲存 IP 篩選器規則的通知":::

當您達到 10 個 IP 篩選器規則的上限後，[新增] 選項便會停用。

若要編輯現有的規則，請選取您要變更的資料，進行變更，然後選取 [儲存] 以儲存您的編輯。

### <a name="delete-an-ip-filter-rule"></a>刪除 IP 篩選器規則

若要刪除 IP 篩選器規則，請選取該資料列上的垃圾桶圖示，然後選取 [儲存]。 此規則會被移除，並儲存變更。

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-delete-rule.png" alt-text="刪除 IoT 中樞 IP 篩選器規則":::

### <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>使用 Azure CLI 擷取和更新 IP 篩選條件

您可以透過 [Azure CLI](https://docs.microsoft.com/cli/azure/) 擷取和更新 IoT 中樞的 IP 篩選條件。

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

### <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>使用 Azure PowerShell 擷取和更新 IP 篩選條件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

您可以透過 [Azure PowerShell](/powershell/azure/) 擷取和設定 IoT 中樞的 IP 篩選器。

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

### <a name="update-ip-filter-rules-using-rest"></a>使用 REST 更新 IP 篩選規則

您也可以使用 Azure 資源提供者的 REST 端點，擷取和修改 IoT 中樞的 IP 篩選條件。 請參閱 [createorupdate 方法](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate)中的 `properties.ipFilterRules`。

### <a name="ip-filter-rule-evaluation"></a>IP 篩選器規則評估

IP 篩選器規則會依序套用，第一個符合 IP 位址的規則會決定接受或拒絕動作。

例如，如果您想要接受 192.168.100.0/22 範圍中的位址，並拒絕其他所有位址，則方格中的第一個規則應接受位址範圍 192.168.100.0/22。 下一個規則應使用 0.0.0.0/0 範圍拒絕所有位址。

按一下資料列前端呈垂直方向的三個點並使用拖放功能，即可變更方格中的 IP 篩選器規則順序。

若要儲存新的 IP 篩選器規則順序，請按一下 [儲存]。

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-rule-order.png" alt-text="變更 IoT 中樞 IP 篩選規則的順序":::

## <a name="next-steps"></a>後續步驟

若要進一步探索 IoT 中樞的功能，請參閱︰

* [使用 IP 篩選器](iot-hub-ip-filtering.md)