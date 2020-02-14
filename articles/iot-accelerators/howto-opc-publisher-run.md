---
title: 執行 OPC 發行者 - Azure | Microsoft Docs
description: 此文章說明如何執行 OPC 發行者並對其進行偵錯。 它也會描述效能和記憶體上的考量。
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 4f5d57bab51d537b64ce4b800737219663c3d7a7
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198781"
---
# <a name="run-opc-publisher"></a>執行 OPC 發行者

此文章會描述如何執行 OPC 發行者並對其進行偵錯。 它也會描述效能和記憶體上的考量。

## <a name="command-line-options"></a>命令列選項

應用程式使用量是使用 `--help` 命令列選項來顯示，如下所示：

```sh/cmd
Current directory is: /appdata
Log file is: <hostname>-publisher.log
Log level is: info

OPC Publisher V2.3.0
Informational version: V2.3.0+Branch.develop_hans_methodlog.Sha.0985e54f01a0b0d7f143b1248936022ea5d749f9

Usage: opcpublisher.exe <applicationname> [<IoT Hubconnectionstring>] [<options>]

OPC Edge Publisher to subscribe to configured OPC UA servers and send telemetry to Azure IoT Hub.
To exit the application, just press CTRL-C while it is running.

applicationname: the OPC UA application name to use, required
                  The application name is also used to register the publisher under this name in the
                  IoT Hub device registry.

IoT Hubconnectionstring: the IoT Hub owner connectionstring, optional

There are a couple of environment variables which can be used to control the application:
_HUB_CS: sets the IoT Hub owner connectionstring
_GW_LOGP: sets the filename of the log file to use
_TPC_SP: sets the path to store certificates of trusted stations
_GW_PNFP: sets the filename of the publishing configuration file

Command line arguments overrule environment variable settings.

Options:
      --pf, --publishfile=VALUE
                              the filename to configure the nodes to publish.
                                Default: '/appdata/publishednodes.json'
      --tc, --telemetryconfigfile=VALUE
                              the filename to configure the ingested telemetry
                                Default: ''
  -s, --site=VALUE           the site OPC Publisher is working in. if specified
                                this domain is appended (delimited by a ':' to
                                the 'ApplicationURI' property when telemetry is
                                sent to IoT Hub.
                                The value must follow the syntactical rules of a
                                DNS hostname.
                                Default: not set
      --ic, --iotcentral     publisher will send OPC UA data in IoTCentral
                                compatible format (DisplayName of a node is used
                                as key, this key is the Field name in IoTCentral)
                                . you need to ensure that all DisplayName's are
                                unique. (Auto enables fetch display name)
                                Default: False
      --sw, --sessionconnectwait=VALUE
                              specify the wait time in seconds publisher is
                                trying to connect to disconnected endpoints and
                                starts monitoring unmonitored items
                                Min: 10
                                Default: 10
      --mq, --monitoreditemqueuecapacity=VALUE
                              specify how many notifications of monitored items
                                can be stored in the internal queue, if the data
                                can not be sent quick enough to IoT Hub
                                Min: 1024
                                Default: 8192
      --di, --diagnosticsinterval=VALUE
                              shows publisher diagnostic info at the specified
                                interval in seconds (need log level info).
                                -1 disables remote diagnostic log and diagnostic
                                output
                                0 disables diagnostic output
                                Default: 0
      --ns, --noshutdown=VALUE
                              same as runforever.
                                Default: False
      --rf, --runforever     publisher can not be stopped by pressing a key on
                                the console, but will run forever.
                                Default: False
      --lf, --logfile=VALUE  the filename of the logfile to use.
                                Default: './<hostname>-publisher.log'
      --lt, --logflushtimespan=VALUE
                              the timespan in seconds when the logfile should be
                                flushed.
                                Default: 00:00:30 sec
      --ll, --loglevel=VALUE the loglevel to use (allowed: fatal, error, warn,
                                info, debug, verbose).
                                Default: info
        --ih, --IoT Hubprotocol=VALUE
                              the protocol to use for communication with IoT Hub (
                                allowed values: Amqp, Http1, Amqp_WebSocket_Only,
                                  Amqp_Tcp_Only, Mqtt, Mqtt_WebSocket_Only, Mqtt_
                                Tcp_Only) or IoT EdgeHub (allowed values: Mqtt_
                                Tcp_Only, Amqp_Tcp_Only).
                                Default for IoT Hub: Mqtt_WebSocket_Only
                                Default for IoT EdgeHub: Amqp_Tcp_Only
      --ms, --IoT Hubmessagesize=VALUE
                              the max size of a message which can be send to
                                IoT Hub. when telemetry of this size is available
                                it will be sent.
                                0 will enforce immediate send when telemetry is
                                available
                                Min: 0
                                Max: 262144
                                Default: 262144
      --si, --IoT Hubsendinterval=VALUE
                              the interval in seconds when telemetry should be
                                send to IoT Hub. If 0, then only the
                                IoT Hubmessagesize parameter controls when
                                telemetry is sent.
                                Default: '10'
      --dc, --deviceconnectionstring=VALUE
                              if publisher is not able to register itself with
                                IoT Hub, you can create a device with name <
                                applicationname> manually and pass in the
                                connectionstring of this device.
                                Default: none
  -c, --connectionstring=VALUE
                              the IoT Hub owner connectionstring.
                                Default: none
      --hb, --heartbeatinterval=VALUE
                              the publisher is using this as default value in
                                seconds for the heartbeat interval setting of
                                nodes without
                                a heartbeat interval setting.
                                Default: 0
      --sf, --skipfirstevent=VALUE
                              the publisher is using this as default value for
                                the skip first event setting of nodes without
                                a skip first event setting.
                                Default: False
      --pn, --portnum=VALUE  the server port of the publisher OPC server
                                endpoint.
                                Default: 62222
      --pa, --path=VALUE     the enpoint URL path part of the publisher OPC
                                server endpoint.
                                Default: '/UA/Publisher'
      --lr, --ldsreginterval=VALUE
                              the LDS(-ME) registration interval in ms. If 0,
                                then the registration is disabled.
                                Default: 0
      --ol, --opcmaxstringlen=VALUE
                              the max length of a string opc can transmit/
                                receive.
                                Default: 131072
      --ot, --operationtimeout=VALUE
                              the operation timeout of the publisher OPC UA
                                client in ms.
                                Default: 120000
      --oi, --opcsamplinginterval=VALUE
                              the publisher is using this as default value in
                                milliseconds to request the servers to sample
                                the nodes with this interval
                                this value might be revised by the OPC UA
                                servers to a supported sampling interval.
                                please check the OPC UA specification for
                                details how this is handled by the OPC UA stack.
                                a negative value will set the sampling interval
                                to the publishing interval of the subscription
                                this node is on.
                                0 will configure the OPC UA server to sample in
                                the highest possible resolution and should be
                                taken with care.
                                Default: 1000
      --op, --opcpublishinginterval=VALUE
                              the publisher is using this as default value in
                                milliseconds for the publishing interval setting
                                of the subscriptions established to the OPC UA
                                servers.
                                please check the OPC UA specification for
                                details how this is handled by the OPC UA stack.
                                a value less than or equal zero will let the
                                server revise the publishing interval.
                                Default: 0
      --ct, --createsessiontimeout=VALUE
                              specify the timeout in seconds used when creating
                                a session to an endpoint. On unsuccessful
                                connection attemps a backoff up to 5 times the
                                specified timeout value is used.
                                Min: 1
                                Default: 10
      --ki, --keepaliveinterval=VALUE
                              specify the interval in seconds the publisher is
                                sending keep alive messages to the OPC servers
                                on the endpoints it is connected to.
                                Min: 2
                                Default: 2
      --kt, --keepalivethreshold=VALUE
                              specify the number of keep alive packets a server
                                can miss, before the session is disconneced
                                Min: 1
                                Default: 5
      --aa, --autoaccept     the publisher trusts all servers it is
                                establishing a connection to.
                                Default: False
      --tm, --trustmyself=VALUE
                              same as trustowncert.
                                Default: False
      --to, --trustowncert   the publisher certificate is put into the trusted
                                certificate store automatically.
                                Default: False
      --fd, --fetchdisplayname=VALUE
                              same as fetchname.
                                Default: False
      --fn, --fetchname      enable to read the display name of a published
                                node from the server. this will increase the
                                runtime.
                                Default: False
      --ss, --suppressedopcstatuscodes=VALUE
                              specifies the OPC UA status codes for which no
                                events should be generated.
                                Default: BadNoCommunication,
                                BadWaitingForInitialData
      --at, --appcertstoretype=VALUE
                              the own application cert store type.
                                (allowed values: Directory, X509Store)
                                Default: 'Directory'
      --ap, --appcertstorepath=VALUE
                              the path where the own application cert should be
                                stored
                                Default (depends on store type):
                                X509Store: 'CurrentUser\UA_MachineDefault'
                                Directory: 'pki/own'
      --tp, --trustedcertstorepath=VALUE
                              the path of the trusted cert store
                                Default: 'pki/trusted'
      --rp, --rejectedcertstorepath=VALUE
                              the path of the rejected cert store
                                Default 'pki/rejected'
      --ip, --issuercertstorepath=VALUE
                              the path of the trusted issuer cert store
                                Default 'pki/issuer'
      --csr                  show data to create a certificate signing request
                                Default 'False'
      --ab, --applicationcertbase64=VALUE
                              update/set this applications certificate with the
                                certificate passed in as bas64 string
      --af, --applicationcertfile=VALUE
                              update/set this applications certificate with the
                                certificate file specified
      --pb, --privatekeybase64=VALUE
                              initial provisioning of the application
                                certificate (with a PEM or PFX fomat) requires a
                                private key passed in as base64 string
      --pk, --privatekeyfile=VALUE
                              initial provisioning of the application
                                certificate (with a PEM or PFX fomat) requires a
                                private key passed in as file
      --cp, --certpassword=VALUE
                              the optional password for the PEM or PFX or the
                                installed application certificate
      --tb, --addtrustedcertbase64=VALUE
                              adds the certificate to the applications trusted
                                cert store passed in as base64 string (multiple
                                strings supported)
      --tf, --addtrustedcertfile=VALUE
                              adds the certificate file(s) to the applications
                                trusted cert store passed in as base64 string (
                                multiple filenames supported)
      --ib, --addissuercertbase64=VALUE
                              adds the specified issuer certificate to the
                                applications trusted issuer cert store passed in
                                as base64 string (multiple strings supported)
      --if, --addissuercertfile=VALUE
                              adds the specified issuer certificate file(s) to
                                the applications trusted issuer cert store (
                                multiple filenames supported)
      --rb, --updatecrlbase64=VALUE
                              update the CRL passed in as base64 string to the
                                corresponding cert store (trusted or trusted
                                issuer)
      --uc, --updatecrlfile=VALUE
                              update the CRL passed in as file to the
                                corresponding cert store (trusted or trusted
                                issuer)
      --rc, --removecert=VALUE
                              remove cert(s) with the given thumbprint(s) (
                                multiple thumbprints supported)
      --dt, --devicecertstoretype=VALUE
                              the IoT Hub device cert store type.
                                (allowed values: Directory, X509Store)
                                Default: X509Store
      --dp, --devicecertstorepath=VALUE
                              the path of the iot device cert store
                                Default Default (depends on store type):
                                X509Store: 'My'
                                Directory: 'CertificateStores/IoT Hub'
  -i, --install              register OPC Publisher with IoT Hub and then exits.
                                Default:  False
  -h, --help                 show this message and exit
      --st, --opcstacktracemask=VALUE
                              ignored, only supported for backward comaptibility.
      --sd, --shopfloordomain=VALUE
                              same as site option, only there for backward
                                compatibility
                                The value must follow the syntactical rules of a
                                DNS hostname.
                                Default: not set
      --vc, --verboseconsole=VALUE
                              ignored, only supported for backward comaptibility.
      --as, --autotrustservercerts=VALUE
                              same as autoaccept, only supported for backward
                                cmpatibility.
                                Default: False
      --tt, --trustedcertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the trusted cert store will always reside in a
                                directory.
      --rt, --rejectedcertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the rejected cert store will always reside in a
                                directory.
      --it, --issuercertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the trusted issuer cert store will always
                                reside in a directory.
```

一般而言，僅會在初次執行應用程式時指定 IoT 中樞擁有者連接字串。 連接字串會被加密，並儲存在平台的憑證存放區。 在後續執行時，應用程式會從該憑證存放區讀取連接字串。 若您在每次執行時皆指定連接字串，系統便會將 IoT 中樞裝置登錄中針對應用程式所建立的裝置移除並重新建立它。

## <a name="run-natively-on-windows"></a>在 Windows 上以原生方式執行

使用 Visual Studio 開啟 **opcpublisher.sln** 專案，建置解決方案，然後發佈它。 您可以透過下列方式在已發佈的**目標目錄**中啟動應用程式：

```cmd
dotnet opcpublisher.dll <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-self-built-container"></a>使用自行建置的容器

以下列方式建置您自己的容器並啟動它：

```sh/cmd
docker run <your-container-name> <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-container-from-microsoft-container-registry"></a>使用來自 Microsoft 容器登錄的容器

Microsoft 容器登錄中有預先建置的容器可供使用。 請以下列方式來啟動它：

```sh/cmd
docker run mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

請檢查 [Docker Hub](https://hub.docker.com/_/microsoft-iotedge-opc-publisher) \(英文\) 以查看支援的作業系統和處理器架構。 如果 Docker 支援您的 OS 和 CPU 架構，便會自動選取正確的容器。

## <a name="run-as-an-azure-iot-edge-module"></a>以 Azure IoT Edge 模組的形式執行

OPC 發行者可以直接作為 [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge) \(部分機器翻譯\) 模組使用。 當您使用 OPC 發行者作為 IoT Edge 模組時，唯一支援的傳輸通訊協定是 **Amqp_Tcp_Only** 和 **Mqtt_Tcp_Only**。

若要將 OPC 發行者作為模組加入您的 IoT Edge 部署，請移至您在 Azure 入口網站中的 IoT 中樞設定，並完成下列步驟：

1. 移至 [IoT Edge] 並建立或選取您的 IoT Edge 裝置。
1. 選取 [設定模組]。
1. 選取 [部署模組] 底下的 [新增]，然後選取 [IoT Edge 模組]。
1. 在 [名稱] 欄位中，輸入 **publisher**。
1. 在 [映像 URI] 欄位中，輸入 `mcr.microsoft.com/iotedge/opc-publisher:<tag>`
1. 您可以在 [Docker Hub](https://hub.docker.com/_/microsoft-iotedge-opc-publisher) \(英文\) 上找到可用的標籤
1. 將下列 JSON 貼到 [容器建立選項] 欄位中：

    ```json
    {
        "Hostname": "publisher",
        "Cmd": [
            "--aa"
        ]
    }
    ```

    此設定會設定 IoT Edge 以使用 OPC 發行者映像來啟動名為 **publisher** 的容器。 容器系統的主機名稱被設定為 **publisher**。 OPC 發行者是透過下列命令列引數來呼叫：`--aa`。 透過此選項，OPC 發行者會信任其所連線至的 OPC UA 伺服器憑證。 您可以使用任何 OPC 發行者命令列選項。 唯一的限制是 IoT Edge 所支援的 [容器建立選項] 大小。

1. 其他設定保留不變，然後選取 [儲存]。
1. 如果您想要在本機搭配另一個 IoT Edge 模組來處理 OPC 發行者的輸出，請返回 [設定模組] 頁面。 然後請移至 [指定路由] 索引標籤，並加入看起來類似下列 JSON 的新路由：

    ```json
    {
      "routes": {
        "processingModuleToIoT Hub": "FROM /messages/modules/processingModule/outputs/* INTO $upstream",
        "opcPublisherToProcessingModule": "FROM /messages/modules/publisher INTO BrokeredEndpoint(\"/modules/processingModule/inputs/input1\")"
      }
    }
    ```

1. 返回 [設定模組] 頁面，選取 [下一步]，直到您抵達設定的最後一頁。
1. 選取 [提交] 以將設定傳送至 IoT Edge。
1. 當您在邊緣裝置上啟動 IoT Edge，且 Docker 容器 **publisher** 正在執行時，您可以使用 `docker logs -f publisher` 或檢查記錄檔來查看 OPC 發行者的記錄輸出。 在上一個範例中，記錄檔是位於 `d:\iiotegde\publisher-publisher.log` 上。 您也可以使用 [iot-edge-opc-publisher-diagnostics 工具](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics) \(英文\)。

### <a name="make-the-configuration-files-accessible-on-the-host"></a>使設定檔可在主機上存取

若要使 IoT Edge 模組設定檔可在主機檔案系統中存取，請使用下列 [容器建立選項]。 下列範例是使用適用於 Windows 的 Linux 容器來進行部署：

```json
{
    "Hostname": "publisher",
    "Cmd": [
        "--pf=./pn.json",
        "--aa"
    ],
    "HostConfig": {
        "Binds": [
            "d:/iiotedge:/appdata"
        ]
    }
}
```

透過這些選項，OPC 發行者會從 `./pn.json` 檔案讀取其應該發行的節點，且容器的工作目錄在啟動時會被設定為 `/appdata`。 透過這些設定，OPC 發行者會從容器讀取 `/appdata/pn.json` 檔案以取得其設定。 在沒有 `--pf` 選項的情況下，OPC 發行者會嘗試讀取預設設定檔 `./publishednodes.json`。

使用預設名稱 `publisher-publisher.log` 的記錄檔會被寫入 `/appdata`，且系統也會在此目錄中建立 `CertificateStores` 目錄。

若要使這些檔案皆可供在主機檔案系統中使用，容器設定將會需要繫結裝載磁碟區。 `d://iiotedge:/appdata` 繫結會將 `/appdata` 目錄 (其為在容器啟動時的目前工作目錄) 對應至主機目錄 `d://iiotedge`。 在沒有此選項的情況下，在容器下一次啟動時將不會保留任何檔案資料。

如果您是執行 Windows 容器，則 `Binds` 參數的語法將會不同。 在容器啟動時，工作目錄會是 `c:\appdata`。 若要將設定檔置於主機上的 `d:\iiotedge` 目錄，請在 `HostConfig` 區段中指定下列對應：

```json
"HostConfig": {
    "Binds": [
        "d:/iiotedge:c:/appdata"
    ]
}
```

如果您是在 Linux 上執行 Linux 容器，則 `Binds` 參數的語法又將再次不同。 在容器啟動時，工作目錄會是 `/appdata`。 若要將設定檔置於主機上的 `/iiotedge` 目錄，請在 `HostConfig` 區段中指定下列對應：

```json
"HostConfig": {
    "Binds": [
        "/iiotedge:/appdata"
    ]
}
```

## <a name="considerations-when-using-a-container"></a>使用容器時的考量

下列區段會列出使用容器時應考量的一些項目：

### <a name="access-to-the-opc-publisher-opc-ua-server"></a>OPC 發行者 OPC UA 伺服器的存取權

根據預設，OPC 發行者 OPC UA 伺服器會接聽 62222 連接埠。 若要在容器中公開此輸入連接埠，請使用下列命令：

```sh/cmd
docker run -p 62222:62222 mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="enable-intercontainer-name-resolution"></a>啟用容器間名稱解析

若要從容器內針對其他容器啟用名稱解析，請建立使用者定義 Docker 橋接網路，並使用 `--network` 選項將容器連線至此網路。 此外，請使用 `--name` 選項指派容器名稱，如下所示：

```sh/cmd
docker network create -d bridge iot_edge
docker run --network iot_edge --name publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

容器現在已可以由相同網路上的其他容器使用 `publisher` 名稱來觸達。

### <a name="access-other-systems-from-within-the-container"></a>從容器內存取其他系統

其他容器可以使用在上一節中所描述的參數來觸達。 如果裝載 Docker 的作業系統已啟用 DNS，則可以存取 DNS 已知的所有系統。

在使用 NetBIOS 名稱解析的網路中，請使用 `--add-host` 選項來啟動容器以啟用針對其他系統的存取。 實際上，此選項會將下列項目加入容器的主機檔案：

```cmd/sh
docker run --add-host mydevbox:192.168.178.23  mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="assign-a-hostname"></a>指派主機名稱

OPC 發行者會使用其運作所在電腦的主機名稱，來產生憑證和端點。 如果沒有使用 `-h` 選項來設定主機名稱，Docker 便會選擇隨機的主機名稱。 下列範例會示範如何將容器的內部主機名稱設為 `publisher`：

```sh/cmd
docker run -h publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-bind-mounts-shared-filesystem"></a>使用繫結掛接 (共用檔案系統)

與其使用容器檔案系統，您可以選擇要用來儲存設定資訊和記錄檔的主機檔案系統。 若要設定此選項，請在繫結掛接模式下使用 `-v` 的 `docker run` 選項。

## <a name="opc-ua-x509-certificates"></a>OPC UA X.509 憑證

OPC UA 會使用 X.509 憑證來在 OPC UA 用戶端和伺服器建立連線時驗證它們，以及對它們之間的通訊進行加密。 OPC 發行者會使用由 OPC UA 堆疊所維護的憑證存放區來管理所有憑證。 在啟動時，OPC 發行者會檢查是否有適用於其本身的憑證。 如果憑證存放區上沒有任何憑證，且沒有透過命令列傳遞憑證，OPC 發行者便會建立自我簽署的憑證。 如需詳細資訊，請參閱 **中的**InitApplicationSecurityAsync`OpcApplicationConfigurationSecurity.cs` 方法。

自我簽署憑證無法提供任何安全性，因為它們不是由受信任的 CA 所簽署。

OPC 發行者提供可用來執行下列動作的命令列選項：

- 擷取由 OPC 發行者所使用之目前應用程式憑證的 CSR 資訊。
- 搭配 CA 簽署的憑證來佈建 OPC 發行者。
- 搭配新的金鑰組和相符的 CA 簽署憑證來佈建 OPC 發行者。
- 將憑證加入受信任的對等或受信任的簽發者憑證存放區。
- 加入 CRL。
- 將憑證從受信任的對等或受信任的簽發者憑證存放區移除。

這些選項都可以讓您使用檔案或 Base64 編碼字串來傳入參數。

所有憑證存放區的預設存放區類型都是檔案系統，其可透過使用命令列選項來變更。 由於容器無法在其檔案系統中提供永續性儲存體，您必須選擇不同的存放區類型。 使用 Docker `-v` 選項來在主機檔案系統中或 Docker 磁碟區上保存憑證存放區。 如果您使用 Docker 磁碟區，則可以使用 Base64 編碼字串傳入憑證。

執行階段環境會影響憑證的保存方式。 請避免在您每次以下列方式執行應用程式時建立新的憑證存放區：

- 在於 Windows 上以原生方式執行時；您無法使用 `Directory` 類型的應用程式憑證存放區，因為存取私密金鑰會失敗。 在此案例中，請使用 `--at X509Store` 選項。
- 以 Linux Docker 容器的形式執行時；您可以使用 Docker 執行選項 `-v <hostdirectory>:/appdata`，將憑證存放區對應至主機檔案系統。 此選項能使憑證以跨應用程式執行的方式保存。
- 以 Linux Docker 容器的形式執行，且您想要針對應用程式憑證使用 X509 存放區；請使用 Docker 執行選項 `-v x509certstores:/root/.dotnet/corefx/cryptography/x509stores` 及應用程式選項 `--at X509Store`

## <a name="performance-and-memory-considerations"></a>效能和記憶體考量

此節會討論管理記憶體及效能的選項：

### <a name="command-line-parameters-to-control-performance-and-memory"></a>用來控制效能及記憶體的命令列參數

當您執行 OPC 發行者時，您需要留意您的效能需求，以及主機上可用的記憶體資源。

記憶體及效能是彼此相依的，且皆須依賴您針對發行所設定的節點數目設定。 請確保下列參數會符合您的需求：

- IoT 中樞傳送間隔：`--si`
- IoT 中樞訊息大小 (預設為 `1`)：`--ms`
- 監視的項目佇列容量：`--mq`

`--mq` 參數能控制內部佇列的容量上限，其會對所有 OPC 節點值變更通知進行緩衝。 如果 OPC 發行者將訊息傳送至 IoT 中樞的速度不夠快，此佇列便會對通知進行緩衝。 參數會設定可被緩衝的通知數目。 如果您看到此佇列中的項目數隨著測試回合持續增加，為了避免失去訊息，您應：

- 減少 IoT 中樞傳送間隔
- 增加 IoT 中樞訊息大小

`--si` 參數會強制 OPC 發行者依指定間隔將訊息傳送至 IoT 中樞。 OPC 發行者會在達到 `--ms` 參數所指定的訊息大小，或是 `--si` 參數所指定的間隔時，便立即傳送訊息。 若要停用訊息大小選項，請使用 `--ms 0`。 在此情況下，OPC 發行者會使用最大的 IoT 中樞訊息大小 (256 kB) 來對資料進行批次處理。

`--ms` 參數可讓您針對傳送至 IoT 中樞的訊息進行批次處理。 您使用的通訊協定會決定將訊息傳送至 IoT 中樞的額外負荷，與實際傳送承載的時間相比是否很高。 如果您的案例允許在 IoT 中樞使用資料時具有延遲，請設定 OPC 發行者以使用最大的訊息大小 (256 kB)。

在您於生產環境案例中使用 OPC 發行者之前，請在生產環境條件下測試效能和記憶體使用量。 您可以使用 `--di` 參數來指定 OPC 發行者寫入診斷資訊的間隔 (以秒為單位)。

### <a name="test-measurements"></a>測試量值

下列範例診斷所顯示的量值，針對 `--si` 和 `--ms` 參數具有不同的值，並會搭配 1 秒的 OPC 發行間隔發行 500 個節點。  測試在 Windows 10 上使用 OPC 發行者偵錯組建以原生模式執行 120 秒。 IoT 中樞通訊協定是預設的 MQTT 通訊協定。

#### <a name="default-configuration---si-10---ms-262144"></a>預設設定 (--si 10 --ms 262144)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:33:05 (started @ 26.10.2017 15:31:09)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54363
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54363
---------------------------------
messages sent to IoT Hub: 109
last successful msg sent @: 26.10.2017 15:33:04
bytes sent to IoT Hub: 12709429
avg msg size: 116600
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 10
--ms setting: 262144
--ih setting: Mqtt
==========================================================================
```

預設設定會每隔 10 秒鐘，或是每當可供 IoT 中樞使用的資料量達到 256 kB 時便將資料傳送至 IoT 中樞。 此設定會產生約 10 秒的中度延遲，但由於訊息大小較大，其失去資料的機率也是最低的。 診斷輸出顯示沒有遺失的 OPC 節點更新：`monitored item notifications enqueue failure: 0`。

#### <a name="constant-send-interval---si-1---ms-0"></a>常數傳送間隔 (--si 1 --ms 0)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:35:59 (started @ 26.10.2017 15:34:03)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54243
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54243
---------------------------------
messages sent to IoT Hub: 109
last successful msg sent @: 26.10.2017 15:35:59
bytes sent to IoT Hub: 12683836
avg msg size: 116365
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 1
--ms setting: 0
--ih setting: Mqtt
==========================================================================
```

當訊息大小被設為 0 時，OPC 發行者會於內部使用最大支援的 IoT 中樞大小 (256 kB) 來對資料進行批次處理。 診斷輸出顯示平均訊息大小為 115,019 個位元組。 在此設定中，OPC 發行者不會遺失任何 OPC 節點值更新，且具有比預設設定低的延遲。

### <a name="send-each-opc-node-value-update---si-0---ms-0"></a>傳送每個 OPC 節點值更新 (--si 0 --ms 0)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:39:33 (started @ 26.10.2017 15:37:37)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 8184
monitored item notifications enqueued: 54232
monitored item notifications enqueue failure: 44624
monitored item notifications dequeued: 1424
---------------------------------
messages sent to IoT Hub: 1423
last successful msg sent @: 26.10.2017 15:39:33
bytes sent to IoT Hub: 333046
avg msg size: 234
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 96
--si setting: 0
--ms setting: 0
--ih setting: Mqtt
==========================================================================
```

此設定會針對每個 OPC 節點值變更向 IoT 中樞傳送訊息。 診斷顯示平均訊息大小是 234 個位元組，這非常小。 此設定的優點在於 OPC 發行者不會添加任何延遲。 遺失 OPC 節點值更新的數目 (`monitored item notifications enqueue failure: 44624`) 非常高，這使得這個設定不適合用於具有大量需要發行之遙測的案例。

### <a name="maximum-batching---si-0---ms-262144"></a>最大批次處理 (--si 0 --ms 262144)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:42:55 (started @ 26.10.2017 15:41:00)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54137
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54137
---------------------------------
messages sent to IoT Hub: 48
last successful msg sent @: 26.10.2017 15:42:55
bytes sent to IoT Hub: 12565544
avg msg size: 261782
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 0
--ms setting: 262144
--ih setting: Mqtt
==========================================================================
```

此設定會對最大數目的 OPC 節點值更新進行批次處理。 最大的 IoT 中樞訊息大小是 256 kB，其已被設定於此。 沒有要求的傳送間隔，這代表供 IoT 中樞使用的資料量會決定延遲多寡。 此設定失去任何 OPC 節點值的機率最低，適合用來發佈大量的節點。 當您使用此設定時，請確定您的案例沒有在未抵達 256 kB 的訊息大小時會產生高延遲的條件。

## <a name="debug-the-application"></a>偵錯應用程式

若要對應用程式進行偵錯，請使用 Visual Studio 開啟 **opcpublisher.sln** 解決方案檔案，並使用 Visual Studio 偵錯工具。

如果您需要在 OPC 發行者中存取 OPC UA 伺服器，請確定您的防火牆已允許存取伺服器所聆聽的連接埠。 預設通訊埠為：62222。

## <a name="control-the-application-remotely"></a>遠端控制應用程式

設定要發行的節點可以透過使用 IoT 中樞直接方法來達成。

OPC 發行者會實作幾個額外的 IoT 中樞直接方法呼叫來讀取：

- 一般資訊。
- OPC 工作階段、訂用帳戶及被監視項目的診斷資訊。
- IoT 中樞訊息及事件的診斷資訊。
- 啟動記錄檔。
- 記錄的最後 100 行。
- 關閉應用程式。

下列 GitHub 存放庫包含用來[設定要發行的節點](https://github.com/Azure-Samples/iot-edge-opc-publisher-nodeconfiguration) \(英文\) 和[讀取診斷資訊](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics) \(英文\) 的工具。 這兩個工具同時也以容器的形式在 Docker Hub 中提供。

## <a name="use-a-sample-opc-ua-server"></a>使用範例 OPC UA 伺服器

如果您沒有真實的 OPC UA 伺服器，則可以使用[範例 OPC UA PLC](https://github.com/Azure-Samples/iot-edge-opc-plc) \(英文\) 來開始進行。 此範例 PLC 同時也在 Docker Hub 上提供。

它會實作數個標籤，其會產生具有異常情況的隨機資料及標籤。 如果您需要模擬額外的標籤值，則可以延伸此範例。

## <a name="next-steps"></a>後續步驟

您已了解執行 OPC 發行者的方式，建議的後續步驟是了解 [OPC 對應項](overview-opc-twin.md)和 [OPC 保存庫](overview-opc-vault.md)。
