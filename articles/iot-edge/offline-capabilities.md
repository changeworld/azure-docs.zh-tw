---
title: 離線操作裝置 - Azure IoT Edge | Microsoft Docs
description: 了解 IoT Edge 裝置及模組針對延長時間期間無網際網路連線執行的方式，以及 IoT Edge 讓一般 IoT 裝置也能離線執行的方式。
author: kgremban
ms.author: kgremban
ms.date: 11/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 588926a90d9a40c00bca4914dc1d5ed08301ff75
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780788"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>瞭解 IoT Edge 裝置、模組及子裝置的擴充離線功能

Azure IoT Edge 支援 IoT Edge 裝置上的延伸離線作業，並在非 IoT Edge 的子裝置上啟用離線作業。 只要 IoT Edge 裝置有一個機會連線到 IoT 中樞，該裝置和任何子裝置都可以繼續使用間歇性或沒有網際網路連線。

## <a name="how-it-works"></a>運作方式

當 IoT Edge 裝置進入離線模式時，IoT Edge 中樞便會擔任三個角色。 首先，它會儲存任何可能前往上游的訊息，並會持續儲存到裝置重新連線為止。 其次，它會代替 IoT 中樞驗證模組及子裝置，使其能繼續執行。 第三，它可在子裝置之間進行平常需要透過 IoT 中樞進行的通訊。

下列範例示範 IoT Edge 案例於離線模式執行的方式：

1. **設定裝置**

   IoT Edge 裝置會自動啟用離線功能。 若要將該功能延伸至其他 IoT 裝置，您需要宣告 IoT 中樞內裝置間的父子關聯。 然後，您可以將子裝置設定為信任其指派的父裝置，並透過父系將裝置到雲端通訊路由傳送為閘道。

2. **與 IoT 中樞同步**

   在安裝完 IoT Edge 執行階段之後，IoT Edge 裝置至少需要處於線上狀態一次，以和 IoT 中樞進行同步處理。 在此同步中，IoT Edge 裝置會取得指派給它的任何子裝置詳細資料。 IoT Edge 裝置也會安全地更新其本機快取，允取離線作業，並會擷取遙測訊息本機存放區的設定。

3. **離線**

   從 IoT 中樞中斷連線時，IoT Edge 裝置、其部署的模組，以及任何子 IoT 裝置都能無限期的繼續執行。 模組及子裝置可在離線狀態時，透過向 IoT Edge 中樞進行驗證來啟動或重新啟動。 至 IoT 中樞的遙測繫結上游會儲存在本機。 模組或子 IoT 裝置之間的通訊則會透過直接方法或訊息維持。

4. **重新連線並與 IoT 中樞同步處理**

   一旦與 IoT 中樞的連線還原，IoT Edge 裝置便會再次進行同步處理。 本機儲存的訊息會立即傳遞至 IoT 中樞，但相依于連線速度、IoT 中樞延遲和相關因素。 它們會以儲存的相同順序來傳遞。

   模組和裝置所需屬性及回報屬性間的任何差異都會進行協調。 IoT Edge 裝置會更新任何對其受指派子 IoT 裝置集合進行的變更。

## <a name="restrictions-and-limits"></a>限制

這篇文章中所述的延伸離線功能適用于[IoT Edge 版本1.0.7 或更高版本](https://github.com/Azure/azure-iotedge/releases)。 先前版本具備一部分的離線功能。 不具備延伸離線功能的現有 IoT Edge 裝置無法透過變更執行階段版本來進行升級，而必須使用新的 IoT Edge 裝置識別重新設定，才能獲得這些功能。

只有非 IoT Edge 的裝置可以新增為子裝置。

IoT Edge 裝置及其指派的子裝置在初次同步處理一次之後，可以無限期地離線運作。不過，訊息的儲存取決於存留時間（TTL）設定和可用的磁碟空間來儲存訊息。

## <a name="set-up-parent-and-child-devices"></a>設定父系和子裝置

若要讓 IoT Edge 裝置將其延伸的離線功能延伸至子 IoT 裝置，您需要完成兩個步驟。 首先，宣告 Azure 入口網站中的父子式關聯性。 其次，在父裝置與任何子裝置之間建立信任關係，然後設定裝置到雲端通訊，以透過父系做為閘道。

### <a name="assign-child-devices"></a>指派子裝置

子裝置可以是任何註冊到相同 IoT 中樞的非 IoT Edge 裝置。 父裝置可以有多個子裝置，但子裝置只有一個父系。 有三個選項可將子裝置設定為邊緣裝置：透過 Azure 入口網站、使用 Azure CLI，或使用 IoT 中樞服務 SDK。

下列各節提供範例，說明如何在現有 IoT 裝置的 IoT 中樞中宣告父系/子關聯性。 如果您要為您的子裝置建立新的裝置身分識別，請參閱[驗證下游裝置以 Azure IoT 中樞](how-to-authenticate-downstream-device.md)，以取得詳細資訊。

#### <a name="option-1-iot-hub-portal"></a>選項1： IoT 中樞入口網站

建立新裝置時，您可以宣告父子式關聯性。 或者，針對現有的裝置，您可以從父 IoT Edge 裝置或子 IoT 裝置的 [裝置詳細資料] 頁面宣告關聯性。

   ![從 IoT Edge 裝置詳細資料頁面管理子裝置](./media/offline-capabilities/manage-child-devices.png)

#### <a name="option-2-use-the-az-command-line-tool"></a>選項2：使用`az`命令列工具

使用[Azure 命令列介面](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)搭配[IoT 擴充](https://github.com/azure/azure-iot-cli-extension)功能（v 0.7.0 或更新版本），您可以使用[裝置身分識別](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)子命令來管理父子關聯性。 下列範例會使用查詢，將中樞內的所有非 IoT Edge 裝置指派為 IoT Edge 裝置的子裝置。

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

您可以修改[查詢](../iot-hub/iot-hub-devguide-query-language.md)，以選取不同的裝置子集。 如果您指定一組大型裝置，此命令可能需要幾秒鐘的時間。

#### <a name="option-3-use-iot-hub-service-sdk"></a>選項3：使用 IoT 中樞服務 SDK

最後，您可以使用 c #、JAVA 或 node.js IoT 中樞服務 SDK，以程式設計方式管理父子關聯性。 以下是使用 c # SDK[指派子裝置的範例](https://aka.ms/set-child-iot-device-c-sharp)。

### <a name="set-up-the-parent-device-as-a-gateway"></a>將父裝置設定為閘道

您可以將父代/子系關聯性視為透明閘道，其中子裝置在 IoT 中樞中有自己的身分識別，但透過雲端的父系進行通訊。 若要進行安全通訊，子裝置必須能夠驗證父裝置是否來自信任的來源。 否則，協力廠商可以設定惡意裝置來模擬家長並攔截通訊。

下列文章將詳細說明建立此信任關係的其中一種方式：

* [設定 IoT Edge 裝置作為透明閘道](how-to-create-transparent-gateway.md)
* [將下游（子系）裝置連線到 Azure IoT Edge 閘道](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>指定 DNS 伺服器

若要改善穩定性，強烈建議您指定您的環境中所使用的 DNS 伺服器位址。 若要設定 DNS 伺服器的 IoT Edge，請參閱[Edge 代理程式模組的解決方式持續報告「空白的設定檔案」，而在疑難排解文章中沒有任何模組在裝置上啟動](troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device)。

## <a name="optional-offline-settings"></a>選擇性離線設定

如果您的裝置離線，IoT Edge 的父裝置會儲存所有的裝置到雲端訊息，直到連線重新建立為止。 IoT Edge 中樞模組會管理離線訊息的儲存和轉送。 對於可能會離線一段時間的裝置，請設定兩個 IoT Edge 中樞設定以優化效能。

首先，增加 [存留時間] 設定，讓 IoT Edge hub 將訊息保留多久，讓您的裝置重新連線。 然後，增加額外的磁碟空間來儲存訊息。

### <a name="time-to-live"></a>存留時間

存留時間設定是在過期前，訊息所能等待傳遞的時間長度。 預設為 7200 秒 (兩小時)。 最大值只受限於整數變數的最大值，這大約是2000000000。

此設定是 IoT Edge 中樞的所需設定，會儲存在模組對應項中。 您可以在 Azure 入口網站中，或直接在部署資訊清單中設定它。

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

### <a name="host-storage-for-system-modules"></a>系統模組的主機存放裝置

根據預設，訊息和模組狀態資訊會儲存在 IoT Edge 中樞的本機容器檔案系統中。 為了改善可靠性，特別是在離線操作時，您也可以將存放裝置專用於主機 IoT Edge 裝置上。 如需詳細資訊，請參閱將[模組存取權提供給裝置的本機儲存體](how-to-access-host-storage-from-module.md)

## <a name="next-steps"></a>後續步驟

深入瞭解如何為您的父系/子裝置連線設定透明閘道：

* [設定 IoT Edge 裝置作為透明閘道](how-to-create-transparent-gateway.md)
* [在 Azure IoT 中樞中驗證下游裝置](how-to-authenticate-downstream-device.md)
* [將下游裝置連線到 Azure IoT Edge 閘道](how-to-connect-downstream-device.md)
