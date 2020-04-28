---
title: Azure IoT 中樞裝置串流 |Microsoft Docs
description: 概述 Azure IoT 中樞裝置串流，這可針對各種雲端到裝置的通訊案例，加速安全的雙向 TCP 通道。
author: robinsh
services: iot-hub
ms.service: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: robinsh
ms.openlocfilehash: ff738e56226f7cbb720a754573a9d8607e0e3247
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "73890461"
---
# <a name="iot-hub-device-streams-preview"></a>IoT 中樞裝置串流 (預覽)

Azure IoT 中樞「裝置串流」** 能協助建立適用於各種不同雲端到裝置通訊案例的安全雙向 TCP 通道。 裝置串流是由 IoT 中樞「串流端點」** 進行調解，其會作為您的裝置和服務端點之間的 Proxy。 當裝置位於網路防火牆後方或私人網路內時，此設定 (如下圖所示) 特別有用。 因此，IoT 中樞裝置串流能提供方便與防火牆搭配使用的方式來協助解決客戶連線到 IoT 裝置的需求，而不需要大幅度地開啟傳入或傳出網路防火牆連接埠。

![「IoT 中樞裝置串流總覽」](./media/iot-hub-device-streams-overview/iot-hub-device-streams-overview.png )

使用 IoT 中樞裝置串流可讓裝置保持安全，並只需要開啟經由連接埠 443 針對 IoT 中樞串流端點的輸出 TCP 連線。 建立資料流之後，服務端和裝置端應用程式將能個別以程式設計方式存取 WebSocket 用戶端物件，以互相傳送及接收原始位元組。 此通道所提供的可靠性和排序保證皆等同於 TCP。

## <a name="benefits"></a>優點

IoT 中樞裝置串流提供下列優點：

* **防火牆易記的安全連線能力：** 您可以從服務端點連線到 IoT 裝置，而不需在裝置或網路周邊開啟輸入防火牆埠（只需要透過埠443對 IoT 中樞的輸出連線）。

* **驗證：** 通道的裝置和服務端都需要使用其對應的認證，向 IoT 中樞進行驗證。

* **加密：** 根據預設，IoT 中樞裝置串流會使用具備 TLS 功能的連接。 這可確保不論應用程式是否使用加密，流量都一律會加密。

* **簡單的連線能力：** 在許多情況下，使用裝置串流就不需要複雜的虛擬私人網路設定，即可啟用 IoT 裝置的連線能力。

* **與 tcp/ip 堆疊的相容性：** IoT 中樞裝置串流可以容納 TCP/IP 應用程式流量。 這表示廣泛的專利與標準型的通訊協定都可以運用這個功能。

* **在私人網路設置中便於使用：** 服務可以藉由參考裝置識別碼（而不是裝置的 IP 位址）來與裝置通訊。 這在裝置位於私人網路內且具有私人 IP 位址，或其 IP 位址為動態指派且無法由服務端得知的情況下非常有用。

## <a name="device-stream-workflows"></a>裝置串流工作流程

當服務透過提供裝置識別碼來要求連線到該裝置時，系統便會起始裝置串流。 此工作流程特別適用於用戶端/伺服器通訊模型 (包括 SSH 和 RDP)，其中使用者會使用 SSH 或 RDP 用戶端程式，從遠端連線到在裝置上執行的 SSH 或 RDP 伺服器。

裝置串流的建立程序涉及裝置、服務、IoT 中樞的主要端點和串流端點之間的交涉。 IoT 中樞的主要端點負責協調裝置串流的建立，而串流端點則會負責處理在服務和裝置之間傳送的流量。

### <a name="device-stream-creation-flow"></a>裝置串流建立流程

使用 SDK 以程式設計方式建立裝置串流會涉及下列步驟，這些步驟也詳述於下圖之中：

![「裝置串流交握進程」](./media/iot-hub-device-streams-overview/iot-hub-device-streams-handshake.png)

1. 裝置應用程式會事先登錄回呼，以在新的裝置串流對裝置起始時收到通知。 此步驟通常在裝置開機並連線到 IoT 中樞時發生。

2. 需要時，服務端程式會藉由提供裝置識別碼 (「不是」__ IP 位址) 來起始裝置串流。

3. IoT 中樞會叫用在步驟 1 中登錄的回呼來通知裝置端程式。 裝置可能會接受或拒絕串流初始化要求。 對於您的應用程式案例來說，此邏輯可能是特定的。 如果裝置拒絕串流要求，IoT 中樞會據以通知服務；若接受，便會遵循以下步驟。

4. 裝置會建立經由連接埠 443 針對串流端點的安全傳出 TCP 連線，並將連線升級為 WebSocket。 IoT 中樞會將串流端點的 URL 及驗證用的認證，包含在於步驟 3 所傳送的要求中提供給裝置。

5. 服務會得知裝置接受資料流的結果，然後繼續建立自己對串流端點的 WebSocket。 同樣地，它會從 IoT 中樞收到串流端點 URL 和驗證資訊。

在上述交握程序中：

* 交握程序 (步驟 2 至 5) 必須在 60 秒內完成，否則交握會因逾時而失敗，且系統會據以通知服務。

* 在上述串流建立流程完成之後，串流端點會作為 Proxy，並透過服務與裝置個別的 WebSocket 傳輸兩者之間流量。

* 裝置和服務都需要經由連接埠 443 針對 IoT 中樞主要端點及串流端點的輸出連線能力。 在「IoT 中樞」入口網站的 [概觀]** 索引標籤上，即可取得這些端點的 URL。

* 已建立之串流的可靠性和排序皆等同於 TCP。

* 針對 IoT 中樞和串流端點的所有連線都會使用 TLS 且經過加密。

### <a name="termination-flow"></a>終止流程

當針對閘道的任一 TCP 連線 (由服務或裝置) 中斷時，已建立的串流就會終止。 這可以會透過關閉裝置或服務程式上的 WebSocket 來主動達成，或是因網路連線逾時或程序失敗而意外發生。 在裝置或服務針對串流端點的連線終止時，另一端的 TCP 連線也會被 (強制) 終止。如果需要，服務和裝置必須負責重新建立串流。

## <a name="connectivity-requirements"></a>連線能力需求

裝置串流的裝置和服務端都必須要能夠針對 IoT 中樞和其串流端點建立啟用 TLS 的連線。 這需要經由連接埠 443 針對這些端點的傳出連線能力。 與這些端點相關聯的主機名稱可在 IoT 中樞的 [概觀]** 索引標籤上找到，如下圖所示：

![「裝置串流端點」](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

或者，您也可以使用中樞的 properties 區段下的 Azure CLI 來抓取端點資訊，特別`property.hostname`是`property.deviceStreams`和金鑰。

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

輸出是中樞的裝置和服務為了建立裝置串流而可能需要連線之所有端點的 JSON 物件。

```json
{
  "streamingEndpoints": [
    "https://<YourIoTHubName>.<region-stamp>.streams.azure-devices.net"
  ]
}
```

> [!NOTE]
> 請確定您已安裝 Azure CLI 2.0.57 版或更新版本。 您可以從 [[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ] 頁面下載最新版本。
>

## <a name="allow-outbound-connectivity-to-the-device-streaming-endpoints"></a>允許裝置串流端點的輸出連線能力

如同本文開頭所述，您的裝置會在裝置串流初始化程式期間，建立 IoT 中樞串流端點的輸出連線。 您裝置或其網路上的防火牆必須允許透過連接埠 443 對串流閘道進行傳出連線 (請注意，通訊會透過以 TLS 加密的 WebSocket 連線進行)。

在 Azure IoT 中樞入口網站的 [總覽] 索引標籤底下，可以找到裝置串流端點![的主機名稱。](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

或者，您也可以使用 Azure CLI 找到此資訊：

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

> [!NOTE]
> 請確定您已安裝 Azure CLI 2.0.57 版或更新版本。 您可以從 [[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ] 頁面下載最新版本。
>

## <a name="troubleshoot-via-device-streams-activity-logs"></a>透過裝置串流活動記錄進行疑難排解

您可以設定 Azure 監視器記錄檔，以便在您的 IoT 中樞中收集裝置串流的活動記錄。 這對於針對案例進行疑難排解來說很有幫助。

請遵循下列步驟，為您 IoT 中樞的裝置串流活動設定 Azure 監視器記錄：

1. 瀏覽至您 IoT 中樞中的 [診斷設定]** 索引標籤，然後按一下 [開啟診斷]** 連結。

   ![[啟用診斷記錄]](./media/iot-hub-device-streams-overview/device-streams-diagnostics-settings-pane.png)

2. 提供診斷設定的名稱，然後選擇 [傳送至 Log Analytics]** 選項。 系統會引導您選擇現有的 Log Analytics 工作區資源，或建立一個新資源。 此外，請核取清單中的 [DeviceStreams]**。

    ![「啟用裝置串流記錄」](./media/iot-hub-device-streams-overview/device-streams-configure-diagnostics.png)

3. 您現在可以在 IoT 中樞入口網站中的 [記錄]** 索引標籤下存取您的裝置串流記錄。 裝置串流活動記錄將會出現在 `AzureDiagnostics` 資料表中，並具有 `Category=DeviceStreams`。

   如下所示，記錄中也會提供目標裝置的身分識別和作業的結果。

   ![「存取裝置串流記錄」](./media/iot-hub-device-streams-overview/device-streams-view-logs.png)

## <a name="regional-availability"></a>區域可用性

在公開預覽期間，[美國中部]、[美國中部] EUAP、[北歐] 和 [東南亞] 區域都有提供 IoT 中樞的裝置串流。 請務必在這其中一個區域建立您的中樞。

## <a name="sdk-availability"></a>SDK 可用性

每個串流的兩端 (裝置端和服務端) 都會使用 IoT 中樞 SDK 來建立通道。 在公開預覽期間，客戶可以選擇下列 SDK 語言：

* C 和 C# SDK 支援裝置端上的裝置串流。

* NodeJS 和 C# SDK 支援服務端上的裝置串流。

## <a name="iot-hub-device-stream-samples"></a>IoT 中樞裝置串流範例

[IoT 中樞] 頁面上提供兩個[快速入門範例](/azure/iot-hub)。 這些會示範應用程式如何使用裝置串流。

* *Echo*範例示範如何以程式設計方式使用裝置串流（藉由直接呼叫 SDK API）。

* 「本機 Proxy」** 範例示範透過裝置資料流建立現成的用戶端/伺服器應用程式流量通道 (例如 SSH、RDP 或 Web)。

下面會更詳細說明這些範例。

### <a name="echo-sample"></a>回應範例

回應範例示範如何以程式設計方式使用裝置資料流，在服務與裝置應用程式之間傳送及接收位元組。 請注意，您可以使用不同語言的服務和裝置程式。 例如，您可以使用 C 裝置程式搭配 c # 服務程式。

以下是 echo 範例：

* [C # 服務和服務程式](quickstart-device-streams-echo-csharp.md)

* [Node.js 服務程式](quickstart-device-streams-echo-nodejs.md)

* [C 裝置程式](quickstart-device-streams-echo-c.md)

### <a name="local-proxy-sample-for-ssh-or-rdp"></a>本機 proxy 範例（適用于 SSH 或 RDP）

本機 Proxy 範例會示範為現有應用程式流量 (涉及用戶端和伺服器程式之間的通訊) 啟用通道的方式。 此設定適用於 SSH 和 RDP 等用戶端/伺服器通訊協定，其中服務端會作為用戶端 (執行 SSH 或 RDP 用戶端程式)，而裝置端則會作為伺服器 (執行 SSH 精靈或 RDP 伺服器程式)。

本節描述如何使用裝置資料流，讓使用者能夠使用 SSH 透過裝置資料流連線到裝置 (藉由使用通訊協定的對應連接埠，RDP 或其他用戶端/伺服器應用程式的案例也都類似)。

該設定會利用下圖中的兩個「本機 Proxy」** 程式，也就是「裝置本機 Proxy」** 和「服務本機 Proxy」**。 本機 Proxy 程式會負責執行與「IoT 中樞」的[裝置資料流起始交握](#device-stream-creation-flow)，並使用一般用戶端/伺服器通訊端來與 SSH 用戶端和 SSH 精靈進行互動。

![「適用于 SSH/RDP 的裝置串流 proxy 設定」](./media/iot-hub-device-streams-overview/iot-hub-device-streams-ssh.png)

1. 使用者會執行服務本機 Proxy 以起始針對裝置的裝置串流。

2. 裝置本機 Proxy 會接受串流起始要求，然後系統會建立與「IoT 中樞」串流端點的通道 (如上所述)。

3. 裝置本機 Proxy 會連線接聽裝置上連接埠 22 的 SSH 精靈端點。

4. 服務本機 Proxy 會在等候來自使用者之新 SSH 連線的指定連接埠上進行接聽 (範例中使用連接埠 2222，但可以設定成任何其他可用的連接埠)。 使用者將 SSH 用戶端指向 localhost 上的服務本機 Proxy 連接埠。

### <a name="notes"></a>注意

* 上述步驟會完成 SSH 用戶端 (右側) 到 SSH 精靈 (左側) 之間的端對端通道。 這個端對端連線能力中有部分牽涉到將流量透過裝置資料流傳送給「IoT 中樞」。

* 圖中的箭頭指出兩個端點之間建立連線的方向。 具體來說，請留意到沒有針對裝置的傳入連線 (這通常會被防火牆封鎖)。

* 在服務本機 Proxy 上選擇使用連接埠 2222 是任意選擇。 Proxy 可以設定為使用任何其他可用的連接埠。

* 埠22的選擇與通訊協定相關，在此案例中是 SSH 特有的。 針對 RDP 的案例，則必須使用連接埠 3389。 這可以在所提供的範例程式中設定。

使用以下連結來取得如何以您偏好的語言執行本機 Proxy 程式的指示。 與[回應範例](#echo-sample)類似，您可以執行以不同語言撰寫的裝置本機 Proxy 程式和服務本機 Proxy 程式，因為它們完全互通。

* [C # 服務和服務程式](quickstart-device-streams-proxy-csharp.md)

* [Node.js 服務程式](quickstart-device-streams-proxy-nodejs.md)

* [C 裝置程式](quickstart-device-streams-proxy-c.md)

## <a name="next-steps"></a>後續步驟

若要深入瞭解裝置串流，請使用下列連結。

> [!div class="nextstepaction"]
> [IoT show 上的裝置串流（Channel 9）](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fchannel9.msdn.com%2FShows%2FInternet-of-Things-Show%2FAzure-IoT-Hub-Device-Streams&data=02%7C01%7Crezas%40microsoft.com%7Cc3486254a89a43edea7c08d67a88bcea%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636831125031268909&sdata=S6u9qiehBN4tmgII637uJeVubUll0IZ4p2ddtG5pDBc%3D&reserved=0)
