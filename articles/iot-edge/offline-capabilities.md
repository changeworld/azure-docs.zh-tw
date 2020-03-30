---
title: 離線操作裝置 - Azure IoT Edge | Microsoft Docs
description: 了解 IoT Edge 裝置及模組針對延長時間期間無網際網路連線執行的方式，以及 IoT Edge 讓一般 IoT 裝置也能離線執行的方式。
author: kgremban
ms.author: kgremban
ms.date: 11/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 55512491121aee28404ab5f85b4223c67a2f0e1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80236067"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>瞭解 IoT 邊緣設備、模組和子設備的擴展離線功能

Azure IoT Edge 支援在 IoT Edge 設備上擴展離線操作，並在非 IoT Edge 子設備上啟用離線操作。 只要 IoT Edge 設備有一個連接到 IoT 中心的機會，該設備和任何子設備都可以在間歇性連接或無互聯網連接下繼續工作。

## <a name="how-it-works"></a>運作方式

當 IoT Edge 裝置進入離線模式時，IoT Edge 中樞便會擔任三個角色。 首先，它會儲存任何可能前往上游的訊息，並會持續儲存到裝置重新連線為止。 其次，它會代替 IoT 中樞驗證模組及子裝置，使其能繼續執行。 第三，它可在子裝置之間進行平常需要透過 IoT 中樞進行的通訊。

下列範例示範 IoT Edge 案例於離線模式執行的方式：

1. **設定裝置**

   IoT Edge 裝置會自動啟用離線功能。 若要將該功能延伸至其他 IoT 裝置，您需要宣告 IoT 中樞內裝置間的父子關聯。 然後，將子設備配置為信任其分配的父設備，並將設備到雲的通信通過父設備路由為閘道。

2. **與 IoT 中心同步**

   在安裝完 IoT Edge 執行階段之後，IoT Edge 裝置至少需要處於線上狀態一次，以和 IoT 中樞進行同步處理。 在此同步中，IoT Edge 裝置會取得指派給它的任何子裝置詳細資料。 IoT Edge 裝置也會安全地更新其本機快取，允取離線作業，並會擷取遙測訊息本機存放區的設定。

3. **離線**

   從 IoT 中樞中斷連線時，IoT Edge 裝置、其部署的模組，以及任何子 IoT 裝置都能無限期的繼續執行。 模組及子裝置可在離線狀態時，透過向 IoT Edge 中樞進行驗證來啟動或重新啟動。 至 IoT 中樞的遙測繫結上游會儲存在本機。 模組或子 IoT 裝置之間的通訊則會透過直接方法或訊息維持。

4. **重新連接 IoT 中心並重新同步**

   一旦與 IoT 中樞的連線還原，IoT Edge 裝置便會再次進行同步處理。 本機存放區的消息會直接傳遞到 IoT 中心，但取決於連線速度、IoT 中心延遲和相關因素。 它們按存儲順序相同。

   模組和裝置所需屬性及回報屬性間的任何差異都會進行協調。 IoT Edge 裝置會更新任何對其受指派子 IoT 裝置集合進行的變更。

## <a name="restrictions-and-limits"></a>限制

本文中描述的擴展離線功能在[IoT Edge 版本 1.0.7 或更高版本中](https://github.com/Azure/azure-iotedge/releases)可用。 先前版本具備一部分的離線功能。 不具備延伸離線功能的現有 IoT Edge 裝置無法透過變更執行階段版本來進行升級，而必須使用新的 IoT Edge 裝置識別重新設定，才能獲得這些功能。

只能將非 IoT 邊緣設備添加為子設備。

IoT Edge 設備及其分配的子設備可以在初始一次性同步後無限期離線運行。但是，消息的存儲取決於存留時間 （TTL） 設置和存儲消息的可用磁碟空間。

## <a name="set-up-parent-and-child-devices"></a>設置父設備和子設備

對於 IoT Edge 設備將其擴展的離線功能擴展到子 IoT 設備，您需要完成兩個步驟。 首先，在 Azure 門戶中聲明父子關係。 其次，在父設備與任何子設備之間創建信任關係，然後將設備到雲的通信配置為通過父設備作為閘道。

### <a name="assign-child-devices"></a>指派子裝置

子設備可以是註冊到同一 IoT 中心的任何非 IoT 邊緣設備。 父設備可以有多個子設備，但子設備只有一個父設備。 有三個選項可以將子設備設置為邊緣設備：通過 Azure 門戶、使用 Azure CLI 或使用 IoT 中心服務 SDK。

以下各節提供了如何為現有 IoT 設備在 IoT 中心中聲明父/子關係的示例。 如果要為子設備創建新的設備標識，請參閱[將下游設備驗證為 Azure IoT 中心](how-to-authenticate-downstream-device.md)以瞭解更多資訊。

#### <a name="option-1-iot-hub-portal"></a>選項 1：IoT 中心門戶

創建新設備時，可以聲明父子關係。 或者，對於現有設備，可以從父 IoT Edge 設備或子 IoT 設備的設備詳細資訊頁聲明關係。

   ![從 IoT Edge 裝置詳細資料頁面管理子裝置](./media/offline-capabilities/manage-child-devices.png)

#### <a name="option-2-use-the-az-command-line-tool"></a>選項 2：使用`az`命令列工具

使用具有[IoT 擴展](https://github.com/azure/azure-iot-cli-extension)（v0.7.0 或較新的）的[Azure 命令列介面](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)，可以使用[設備標識](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)子命令管理父子關係。 下面的示例使用查詢將中心中的所有非 IoT Edge 設備指定為 IoT Edge 設備的子設備。

```azurecli
# Set IoT Edge parent device
egde_device="edge-device1"

# Get All IoT Devices
device_list=$(az iot hub query \
        --hub-name replace-with-hub-name \
        --subscription replace-with-sub-name \
        --resource-group replace-with-rg-name \
        -q "SELECT * FROM devices WHERE capabilities.iotEdge = false" \
        --query 'join(`, `, [].deviceId)' -o tsv)

# Add all IoT devices to IoT Edge (as child)
az iot hub device-identity add-children \
  --device-id $egde_device \
  --child-list $device_list \
  --hub-name replace-with-hub-name \
  --resource-group replace-with-rg-name \
  --subscription replace-with-sub-name
```

您可以修改[查詢](../iot-hub/iot-hub-devguide-query-language.md)以選擇設備的不同子集。 如果指定一組大型設備，則該命令可能需要幾秒鐘的時間。

#### <a name="option-3-use-iot-hub-service-sdk"></a>選項 3：使用 IoT 中心服務 SDK

最後，您可以使用 C#、JAVA 或 Node.js IoT 中心服務 SDK 以程式設計方式管理父子關係。 下面是使用 C# SDK[分配子設備的示例](https://aka.ms/set-child-iot-device-c-sharp)。

### <a name="set-up-the-parent-device-as-a-gateway"></a>將父設備設置為閘道

您可以將父/子關係視為透明閘道，其中子設備在 IoT 中心中有自己的標識，但通過其父設備通過雲進行通信。 為了安全通信，子設備需要能夠驗證父設備是否來自受信任的源。 否則，協力廠商可能會設置惡意設備以類比父設備並攔截通信。

創建此信任關係的一種方法在以下文章中進行了詳細介紹：

* [設定 IoT Edge 裝置作為透明閘道](how-to-create-transparent-gateway.md)
* [將下游（子）設備連接到 Azure IoT 邊緣閘道](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>指定 DNS 伺服器

為了提高魯棒性，強烈建議您指定環境中使用的 DNS 伺服器位址。 要為 IoT Edge 設置 DNS 伺服器，請參閱[邊緣代理模組的解析度，不斷報告"空設定檔"，並且故障排除文章中沒有在設備上啟動模組](troubleshoot.md#edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device)。

## <a name="optional-offline-settings"></a>選擇性離線設定

如果設備離線，IoT Edge 父設備將存儲所有設備到雲的消息，直到重新建立連接。 IoT Edge 中心模組管理離線消息的存儲和轉發。 對於可能長時間離線的設備，通過配置兩個 IoT Edge 中心設置來優化性能。

首先，增加即時設置的時間，以便 IoT Edge 中心將消息保留足夠長的時間，以便設備重新連接。 然後，增加額外的磁碟空間來儲存訊息。

### <a name="time-to-live"></a>存留時間

存留時間設定是在過期前，訊息所能等待傳遞的時間長度。 預設為 7200 秒 (兩小時)。 最大值僅受整數變數的最大值的限制，該最大值約為 20 億。

此設定是 IoT Edge 中樞的所需設定，會儲存在模組對應項中。 您可以在 Azure 門戶中或直接在部署清單中配置它。

```json
"$edgeHub": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {},
        "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
        }
    }
}
```

### <a name="host-storage-for-system-modules"></a>系統模組的主機存儲

預設情況下，消息和模組狀態資訊存儲在 IoT Edge 中心的本地容器檔案系統中。 為提高可靠性，尤其是在離線操作時，您還可以在主機 IoT Edge 設備上專用存儲。 有關詳細資訊，請參閱[為模組提供對設備的本機存放區的存取權限](how-to-access-host-storage-from-module.md)

## <a name="next-steps"></a>後續步驟

詳細瞭解如何為父/子設備連接設置透明閘道：

* [設定 IoT Edge 裝置作為透明閘道](how-to-create-transparent-gateway.md)
* [向 Azure IoT 中樞驗證下游裝置](how-to-authenticate-downstream-device.md)
* [將下游裝置連線到 Azure IoT Edge 閘道](how-to-connect-downstream-device.md)
