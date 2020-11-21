---
title: 發佈和訂閱 Azure IoT Edge |Microsoft Docs
description: 使用 IoT Edge MQTT 訊息代理程式來發佈和訂閱訊息
services: iot-edge
keywords: ''
author: kgremban
ms.author: kgremban
ms.reviewer: ebertra
ms.date: 11/09/2020
ms.topic: conceptual
ms.service: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 1ace40098e1d53c6199accea755ffb6969781663
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015658"
---
# <a name="publish-and-subscribe-with-azure-iot-edge"></a>發佈和訂閱 Azure IoT Edge

您可以使用 Azure IoT Edge MQTT 訊息代理程式來發佈和訂閱訊息。 本文說明如何連線到此訊息代理程式、如何透過使用者定義的主題發佈和訂閱訊息，以及如何使用 IoT 中樞訊息原件。 IoT Edge 的 MQTT 訊息代理程式 IoT Edge 中樞內建。 如需詳細資訊，請參閱 [IoT Edge 中樞的代理功能](iot-edge-runtime.md)。

> [!NOTE]
> IoT Edge MQTT broker 目前處於公開預覽狀態。

## <a name="pre-requisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶
- [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest&preserve-view=true) `azure-iot` 已安裝 CLI 擴充功能。 如需詳細資訊，請參閱 [適用于 azure Azure CLI 的 Azure IoT 擴充功能安裝步驟](https://docs.microsoft.com/cli/azure/azure-cli-reference-for-iot)。
- SKU 的 **IoT 中樞** ，也就是 F1、S1、S2 或 S3。
- 具有 **1.2 版或更新版本的 IoT Edge 裝置**。 由於 IoT Edge MQTT broker 目前處於公開預覽狀態，因此請在 edgeHub 容器上將下列環境變數設為 true，以啟用 MQTT 訊息代理程式：

   | Name | 值 |
   | - | - |
   | `experimentalFeatures__enabled` | `true` |
   | `experimentalFeatures__mqttBrokerEnabled` | `true` |

- Mosquitto 安裝在 IoT Edge 裝置上的 **用戶端**。 本文使用包含 [MOSQUITTO_PUB](https://mosquitto.org/man/mosquitto_pub-1.html) 和 [MOSQUITTO_SUB](https://mosquitto.org/man/mosquitto_sub-1.html)的熱門 Mosquitto 用戶端。 您可以改為使用其他 MQTT 用戶端。 若要在 Ubuntu 裝置上安裝 Mosquitto 用戶端，請執行下列命令：

    ```cmd
    sudo apt-get update && sudo apt-get install mosquitto-clients
    ```

    請勿安裝 Mosquitto 伺服器，因為它可能會導致封鎖 MQTT 埠 (8883 和 1883) 並與 IoT Edge 中樞發生衝突。

## <a name="connect-to-iot-edge-hub"></a>連接到 IoT Edge 中樞

連線到 IoT Edge hub 時，會遵循 [使用一般 MQTT 用戶端連線到 IoT 中樞一文](../iot-hub/iot-hub-mqtt-support.md) 或 [IoT Edge hub 文章的概念描述](iot-edge-runtime.md)中所述的相同步驟。 這些步驟包括：

1. （選擇性） MQTT 用戶端會使用傳輸層安全性 (TLS 來建立與 IoT Edge 中樞的 *安全* 連線) 
2. MQTT 用戶端會向 IoT Edge 中樞 *驗證* 自己
3. IoT Edge *中樞會授權 MQTT* 用戶端每個授權原則

### <a name="secure-connection-tls"></a> (TLS) 的安全連線

傳輸層安全性 (TLS) 用來建立用戶端和 IoT Edge 中樞之間的加密通訊。

若要停用 TLS，請使用埠 1883 (MQTT) 並將 edgeHub 容器系結至埠1883。

若要啟用 TLS，如果用戶端連線到埠 8883 (MQTTS) 至 MQTT 訊息代理程式，則會起始 TLS 通道。 訊息代理程式會傳送用戶端需要驗證的憑證鏈。 為了驗證憑證鏈，MQTT 訊息代理程式的根憑證必須安裝為用戶端上的受信任憑證。 如果根憑證不受信任，MQTT 訊息代理程式將會拒絕用戶端程式庫與憑證驗證錯誤。 在用戶端上安裝此代理程式根憑證的步驟，與 [透明閘道](how-to-create-transparent-gateway.md) 案例中的相同，如「 [準備下游裝置](how-to-connect-downstream-device.md#prepare-a-downstream-device) 」檔所述。

### <a name="authentication"></a>驗證

若要讓 MQTT 用戶端自行驗證，必須先將 CONNECT 封包傳送給 MQTT 訊息代理程式，以在其名稱中起始連線。 此封包提供三項驗證資訊： a `client identifier` 、a `username` 和 `password` ：

-   此 `client identifier` 欄位是 IoT 中樞內的裝置名稱或模組名稱。 其使用下列語法：

    - 針對裝置： `<device_name>`

    - 針對模組： `<device_name>/<module_name>`

   若要連線到 MQTT 訊息代理程式，必須在 IoT 中樞註冊裝置或模組。

   請注意，訊息代理程式不允許使用相同的認證來連接兩個用戶端。 如果第二個用戶端使用相同的認證進行連接，訊息代理程式會中斷已連線的用戶端連線。

- 此 `username` 欄位衍生自裝置或模組名稱，以及裝置所屬的 IoTHub 名稱使用下列語法：

    - 針對裝置： `<iot_hub_name>.azure-devices.net/<device_name>/?api-version=2018-06-30`

    - 針對模組： `<iot_hub_name>.azure-devices.net/<device_name>/<module_name>/?api-version=2018-06-30`

- `password`CONNECT 封包的欄位取決於驗證模式：

    - 在 [對稱金鑰驗證](how-to-authenticate-downstream-device.md#symmetric-key-authentication)的情況下，此 `password` 欄位是 SAS 權杖。
    - 如果是 [x.509 自我簽署驗證](how-to-authenticate-downstream-device.md#x509-self-signed-authentication)，則 `password` 欄位不存在。 在此驗證模式中，需要 TLS 通道。 用戶端必須連接到埠8883以建立 TLS 連線。 在 TLS 信號交換期間，MQTT 訊息代理程式會要求用戶端憑證。 此憑證是用來驗證用戶端的身分識別，因此 `password` 稍後在傳送 CONNECT 封包時，不需要此欄位。 同時傳送用戶端憑證和密碼欄位將會導致錯誤，而且將會關閉連接。 MQTT 程式庫和 TLS 用戶端程式庫在起始連線時，通常會有一種方式來傳送用戶端憑證。 您可以在 [使用 X509 憑證進行用戶端驗證](how-to-authenticate-downstream-device.md#x509-self-signed-authentication)一節中看到逐步範例。

IoT Edge 部署的模組會使用 [對稱金鑰驗證](how-to-authenticate-downstream-device.md#symmetric-key-authentication) ，而且可以呼叫本機 [IOT EDGE 工作負載 API](https://github.com/Azure/iotedge/blob/40f10950dc65dd955e20f51f35d69dd4882e1618/edgelet/workload/README.md) ，以程式設計方式取得 SAS 權杖（即使離線時也一樣）。

### <a name="authorization"></a>授權

一旦 MQTT 用戶端通過 IoT Edge hub 的驗證，就必須獲得授權才能連線。 一旦連線後，就必須獲得授權，才能發佈或訂閱特定主題。 這些授權是由 IoT Edge 中樞根據其授權原則來授與。 授權原則是一組以 JSON 結構表示的語句，會透過對應項傳送至 IoT Edge 中樞。 編輯 IoT Edge hub 對應項以設定其授權原則。

> [!NOTE]
> 針對公開預覽版，MQTT 訊息代理程式的授權原則編輯只能透過 Visual Studio、Visual Studio Code 或 Azure CLI 提供。 Azure 入口網站目前不支援編輯 IoT Edge 中樞對應項及其授權原則。

每個授權原則語句都是由或的組合所組成 `identities` `allow` `deny` `operations` `resources` ：

- `identities` 描述原則的主體。 它必須對應至 `client identifier` 用戶端在其 CONNECT 封包中傳送的。
- `allow` 或 `deny` 效果定義是要允許還是拒絕作業。
- `operations` 定義要授權的動作。 `mqtt:connect``mqtt:publish`和 `mqtt:subscribe` 現在是三個支援的動作。
- `resources` 定義原則的物件。 它可以是主題或以 [MQTT 萬用字元](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107)定義的主題模式。

以下是明確不允許「rogue_client」用戶端連線的授權原則範例，可讓任何 Azure IoT 用戶端進行連線，並允許「sensor_1」發佈至主題 `events/alerts` 。

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "routes":{
            "Route1":"FROM /messages/* INTO $upstream"
         },
         "storeAndForwardConfiguration":{
            "timeToLiveSecs":7200
         },
         "mqttBroker":{
            "authorizations":[
               {
                  "identities":[
                     "rogue_client"
                  ],
                  "deny":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities":[
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities":[
                     "sensor_1"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:publish"
                        ],
                        "resources":[
                           "events/alerts"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

撰寫您的授權原則時，有幾件事要牢記在心：
- 它需要對應項 `$edgeHub` 架構版本1。2
- 依預設，所有作業都會遭到拒絕。
- 授權語句的評估順序會與它們出現在 JSON 定義中的順序不同。 它一開始先查看 `identities` ，然後選取符合要求的第一個允許或拒絕語句。 如果允許和拒絕語句之間發生衝突，deny 語句會獲勝。
- 有幾個變數 (例如，可在授權原則中使用替代) ：
    - `{{iot:identity}}` 表示目前連接之用戶端的身分識別。 例如，在有模組的情況下為 `myDevice` 裝置 `myEdgeDevice/SampleModule` 。
    - `{{iot:device_id}}` 代表目前連接之裝置的身分識別。 例如，在有模組的情況下為 `myDevice` 裝置 `myEdgeDevice` 。
    - `{{iot:module_id}}` 表示目前連接之模組的身分識別。 例如，如果是在某個模組的情況下，則為「裝置」 `SampleModule` 。
    - `{{iot:this_device_id}}` 代表執行授權原則之 IoT Edge 裝置的身分識別。 例如 `myIoTEdgeDevice` 。

IoT 中樞的授權與使用者自訂主題的處理方式稍有不同。 以下是要記住的重點：
- Azure IoT 裝置或模組需要明確的授權規則，才能連線至 IoT Edge hub MQTT 訊息代理程式。 以下提供預設的 connect 授權原則。
- 依預設，Azure IoT 裝置或模組可以存取自己的 IoT 中樞主題，而不需要任何明確的授權規則。 不過，在該情況下，授權源自于父/子關聯性，而且必須設定這些關聯性。 IoT Edge 模組會自動設定為其 IoT Edge 裝置的子系，但必須將裝置明確設定為其 IoT Edge 閘道的子系。
- Azure IoT 裝置或模組可以存取其他裝置或模組的相關主題，這些主題包括定義適當的明確授權規則。

以下是預設的授權原則，可用來啟用所有 Azure IoT 裝置或模組以 **連接** 到 broker：

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "mqttBroker":{
            "authorizations":[
               {
                  "identities": [
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

現在您已瞭解如何連線到 IoT Edge MQTT 訊息代理程式，讓我們來看看如何使用它來先在使用者定義的主題上發佈和訂閱訊息，然後再從 IoT 中樞主題和最後到另一個 MQTT 訊息代理程式。

## <a name="publish-and-subscribe-on-user-defined-topics"></a>發佈和訂閱使用者自訂主題

在本文中，您將會使用一個名為 **sub_client** 的用戶端，該用戶端會訂閱主題和另一個名為 **pub_client** 的用戶端，以發佈至主題。 我們將使用 [對稱金鑰驗證](how-to-authenticate-downstream-device.md#symmetric-key-authentication) ，但您可以使用 [x.509 自我簽署驗證](how-to-authenticate-downstream-device.md#x509-self-signed-authentication) 或 [x.509 CA 簽署的驗證](./how-to-authenticate-downstream-device.md#x509-ca-signed-authentication)來完成相同的操作。

### <a name="create-publisher-and-subscriber-clients"></a>建立發行者和訂閱者用戶端

在 IoT 中樞內建立兩個 IoT 裝置，並取得其密碼。 使用終端機中的 Azure CLI 來：

1. 在 IoT 中樞內建立兩個 IoT 裝置，並將其父代至 IoT Edge 裝置：

    ```azurecli-interactive
    az iot hub device-identity create --device-id  sub_client --hub-name <iot_hub_name> --pd <edge_device_id>
    az iot hub device-identity create --device-id  pub_client --hub-name <iot_hub_name> --pd <edge_device_id>
    ```

2. 藉由產生 SAS 權杖來取得其密碼：

    - 針對裝置：
    
       ```azurecli-interactive
       az iot hub generate-sas-token -n <iot_hub_name> -d <device_name> --key-type primary --du 3600
       ```
    
       其中3600是 SAS 權杖的持續時間（以秒為單位） (例如 3600 = 1 小時) 。
    
    - 針對模組：
    
       ```azurecli-interactive
       az iot hub generate-sas-token -n <iot_hub_name> -d <device_name> -m <module_name> --key-type primary --du 3600
       ```
    
       其中3600是 SAS 權杖的持續時間（以秒為單位） (例如 3600 = 1 小時) 。

3. 複製 SAS 權杖，這是對應至輸出中 "SAS" 機碼的值。 以下是上述 Azure CLI 命令的輸出範例：

    ```
    {
       "sas": "SharedAccessSignature sr=example.azure-devices.net%2Fdevices%2Fdevice_1%2Fmodules%2Fmodule_a&sig=H5iMq8ZPJBkH3aBWCs0khoTPdFytHXk8VAxrthqIQS0%3D&se=1596249190"
    }
    ```

### <a name="authorize-publisher-and-subscriber-clients"></a>授權發行者和訂閱者用戶端

若要授權發行者和訂閱者，請透過 Azure CLI、Visual Studio 或 Visual Studio 程式碼編輯 IoT Edge hub 對應項，以包含下列授權原則：

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "mqttBroker":{
            "authorizations":[
               {
                  "identities": [
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities": [
                     "sub_client"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:subscribe"
                        ],
                        "resources":[
                           "test_topic"
                        ],
                     }
                  ],
               },
               {
                  "identities": [
                     "pub_client"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:publish"
                        ],
                        "resources":[
                           "test_topic"
                        ],
                     }
                  ],
               }
            ]
         }
      }
   }
}
```

### <a name="symmetric-keys-authentication-without-tls"></a>沒有 TLS 的對稱金鑰驗證

#### <a name="subscribe"></a>訂閱

將 **sub_client** MQTT 用戶端連線至 MQTT 訊息代理程式，並 `test_topic` 在 IoT Edge 裝置上執行下列命令來訂閱：

```bash
mosquitto_sub \
    -t "test_topic" \
    -i "sub_client" \
    -u "<iot_hub_name>.azure-devices.net/sub_client/?api-version=2018-06-30" \
    -P "<sas_token>" \
    -h "<edge_device_address>" \
    -V mqttv311 \
    -p 1883
```

`<edge_device_address>`  =  `localhost` 在此範例中，由於用戶端是在與 IoT Edge 相同的裝置上執行。

請注意，在第一個範例中，會使用埠 1883 (MQTT) （例如沒有 TLS）。 使用埠 8883 (MQTTS) 的另一個範例（例如啟用 TLS）會顯示在下一節中。

**Sub_client** 的 MQTT 用戶端現在已啟動，且正在等候傳入訊息 `test_topic` 。

#### <a name="publish"></a>發佈

在您的 IoT Edge 裝置上，從另一個終端機執行下列命令，以將 **pub_client** MQTT 用戶端連線至 MQTT 訊息代理程式，並將訊息發佈至與上述相同的訊息 `test_topic` ：

```bash
mosquitto_pub \
    -t "test_topic" \
    -i "pub_client" \
    -u "<iot_hub_name>.azure-devices.net/pub_client/?api-version=2018-06-30" \
    -P "<sas_token>" \
    -h "<edge_device_address>" \
    -V mqttv311 \
    -p 1883 \
    -m "hello"
```

`<edge_device_address>`  =  `localhost` 在此範例中，由於用戶端是在與 IoT Edge 相同的裝置上執行。

執行命令時， **sub_client** MQTT 用戶端會收到 "hello" 訊息。

### <a name="symmetric-keys-authentication-with-tls"></a>使用 TLS 進行對稱金鑰驗證

若要啟用 TLS，必須將埠從 1883 (MQTT) 變更為 8883 (MQTTS) ，而且用戶端必須具有 MQTT 訊息代理程式的根憑證，才能驗證 MQTT 訊息代理程式所傳送的憑證鏈。 您可以依照 [ (TLS) 的安全 ](#secure-connection-tls)連線一節中提供的步驟來完成這項操作。

由於用戶端是在與上述範例中的 MQTT 訊息代理程式相同的裝置上執行，因此只要將埠號碼從 1883 (MQTT) 變更為 8883 (MQTTS) ，就會套用相同的步驟來啟用 TLS。

## <a name="publish-and-subscribe-on-iot-hub-topics"></a>發佈和訂閱 IoT 中樞主題

[Azure IoT 裝置 sdk](https://github.com/Azure/azure-iot-sdks)已讓用戶端執行 IoT 中樞作業，但不允許在使用者定義的主題上發佈/訂閱。 只要遵守 IoT 中樞基本通訊協定，就可以使用「發佈」和「訂閱」語義的任何 MQTT 用戶端來執行「IoT 中樞」作業。 我們會逐步解說特例，以瞭解這些通訊協定的運作方式。

### <a name="send-telemetry-data-to-iot-hub"></a>將遙測資料傳送至 IoT 中樞

將遙測資料傳送至 IoT 中樞類似于在使用者定義的主題上發佈，但使用特定的 IoT 中樞主題：

- 針對裝置，會在主題上傳送遙測： `devices/<device_name>/messages/events`
- 針對模組，會在主題上傳送遙測： `devices/<device_name>/<module_name>/messages/events`

此外，建立路由，例如 `FROM /messages/* INTO $upstream` 將遙測從 IOT EDGE MQTT 訊息代理程式傳送到 IoT 中樞。 若要深入瞭解路由，請參閱宣告 [路由](module-composition.md#declare-routes)。

### <a name="get-twin"></a>取得對應項

取得裝置/模組對應項不是一般的 MQTT 模式。 用戶端必須發出 IoT 中樞即將提供的對應項要求。

若要接收 twins，用戶端必須訂閱 IoT 中樞特定主題 `$iothub/twin/res/#` 。 此主題名稱會繼承自 IoT 中樞，而且所有用戶端都必須訂閱相同的主題。 這並不表示裝置或模組會接收彼此的對應項。 IoT 中樞和 IoT Edge 中樞知道應該在何處傳遞對應項，即使所有裝置都會接聽相同的主題名稱。 

一旦建立訂用帳戶之後，用戶端就必須將訊息發佈到 IoT 中樞特定主題 `$iothub/twin/GET/?rid=<request_id>/#` （其中是任意識別碼），以要求對應項  `<request_id>` 。 IoT 中樞接著會將其回應傳送給用戶端訂閱的主題所要求的資料 `$iothub/twin/res/200/?rid=<request_id>` 。 這是用戶端可以將其要求與回應配對的方式。

### <a name="receive-twin-patches"></a>接收對應項修補程式

若要接收對應項修補程式，用戶端必須訂閱特殊的 IoTHub 主題 `$iothub/twin/PATCH/properties/desired/#` 。 訂用帳戶建立後，用戶端會收到此主題上 IoT 中樞所傳送的對應項修補程式。 

### <a name="receive-direct-methods"></a>接收直接方法

接收直接方法與接收完整 twins 很類似，因為用戶端必須先確認它已收到呼叫。 首先，用戶端訂閱 IoT 中樞特殊主題 `$iothub/methods/POST/#` 。 然後，在此主題收到直接方法之後，用戶端就必須 `rid` 從收到直接方法的子主題中解壓縮要求識別碼，最後在 IoT 中樞特殊主題上發佈確認訊息 `$iothub/methods/res/200/<request_id>` 。

### <a name="send-direct-methods"></a>傳送直接方法

傳送直接方法是 HTTP 呼叫，因此不會經過 MQTT 訊息代理程式。 若要將直接方法傳送至 IoT 中樞，請參閱 [瞭解及叫用直接方法](../iot-hub/iot-hub-devguide-direct-methods.md)。 若要在本機將直接方法傳送至另一個模組，請參閱此 [Azure IoT c # SDK 直接方法調用範例](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/ModuleClient.cs#L597)。

## <a name="publish-and-subscribe-between-mqtt-brokers"></a>在 MQTT 代理程式之間發佈和訂閱

若要連接兩個 MQTT 訊息代理程式，IoT Edge 中樞包含 MQTT 橋接器。 MQTT 橋接器通常用來將執行的 MQTT 訊息代理程式連線到另一個 MQTT 訊息代理程式。 只有本機流量的子集通常會推送至另一個訊息代理程式。

> [!NOTE]
> IoT Edge hub 橋接器目前只能在嵌套 IoT Edge 裝置之間使用。 它無法用來將資料傳送至 IoT 中樞，因為 IoT 中樞不是功能完整的 MQTT 訊息代理程式。 若要深入瞭解 IoT 中樞 MQTT 訊息代理程式功能的支援，請參閱 [使用 MQTT 通訊協定與 iot 中樞進行通訊](../iot-hub/iot-hub-mqtt-support.md)。 若要深入瞭解如何將 IoT Edge 裝置，請參閱 [將下游 IoT Edge 裝置連線到 Azure IoT Edge 閘道](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices) 

在嵌套的設定中，IoT Edge hub MQTT 橋接器可作為父 MQTT 訊息代理程式的用戶端，因此必須在父 EdgeHub 上設定授權規則，以允許子 EdgeHub 發佈和訂閱為橋接器設定的特定使用者定義主題。

IoT Edge 的 MQTT 橋接器是透過透過其對應項傳送至 IoT Edge 中樞的 JSON 結構進行設定。 編輯 IoT Edge hub 對應項以設定其 MQTT 橋接器。

> [!NOTE]
> 針對公開預覽版，MQTT 橋接器的設定只能透過 Visual Studio、Visual Studio Code 或 Azure CLI 提供。 Azure 入口網站目前不支援編輯 IoT Edge 中樞對應項及其 MQTT 橋接器設定。

MQTT 橋接器可以設定為將 IoT Edge hub MQTT 訊息代理程式連接到多個外部訊息代理程式。 針對每個外部訊息代理程式，需要下列設定：

- `endpoint` 這是要連接的遠端 MQTT 訊息代理程式的位址。 目前只支援父 IoT Edge 裝置，而且是由變數定義 `$upstream` 。
- `settings` 定義要為端點橋接的主題。 每個端點可以有多個設定，並使用下列值來進行設定：
    - `direction`： `in` 訂閱遠端訊息代理程式的主題，或 `out` 發佈至遠端訊息代理程式的主題
    - `topic`：要比對的核心主題模式。 您可以使用[MQTT 萬用字元](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107)來定義此模式。 您可以將不同的前置詞套用至本機訊息代理程式和遠端訊息代理程式上的此主題模式。
    - `outPrefix`：套用至遠端訊息代理程式之 `topic` 模式的前置詞。
    - `inPrefix`：套用至本機訊息代理程式之 `topic` 模式的前置詞。

以下是 IoT Edge MQTT 橋接器設定的範例，此設定會將父 IoT Edge 裝置主題上所收到的所有訊息 `alerts/#` ，將至相同主題的子 IoT Edge 裝置，並將在子 IoT Edge 裝置主題上傳送的所有訊息 `/local/telemetry/#` 到主題的上層 IoT Edge 裝置 `/remote/messages/#` 。

```json
{
    "schemaVersion": "1.2",
    "mqttBroker": {
        "bridges": [{
            "endpoint": "$upstream",
            "settings": [{
                    "direction": "in",
                    "topic": "alerts/#"
                },
                {
                    "direction": "out",
                    "topic": "",
                    "inPrefix": "/local/telemetry",
                    "outPrefix": "/remote/messages"
                }
            ]
        }]
    }
}
```
IoT Edge hub MQTT bridge 上的其他注意事項：
- 使用 MQTT 訊息代理程式時，會自動使用 MQTT 通訊協定做為上游通訊協定，而 IoT Edge 用於嵌套設定，例如使用 `parent_hostname` 指定的。 若要深入瞭解上游通訊協定，請參閱 [雲端通訊](iot-edge-runtime.md#cloud-communication)。 若要深入瞭解嵌套設定，請參閱 [將下游 IoT Edge 裝置連線到 Azure IoT Edge 閘道](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices)。

## <a name="next-steps"></a>下一步

[瞭解 IoT Edge 中樞](iot-edge-runtime.md#iot-edge-hub)
