---
title: 針對連線問題進行疑難排解-Azure 事件中樞 |Microsoft Docs
description: 本文提供有關針對 Azure 事件中樞的連線問題進行疑難排解的資訊。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: b85c0895d1c8f165f494d29013adea014187dd23
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87039322"
---
# <a name="troubleshoot-connectivity-issues---azure-event-hubs"></a>針對連線問題進行疑難排解-Azure 事件中樞
用戶端應用程式無法連接到事件中樞的原因有很多種。 您所遇到的連線問題可能是永久性或暫時性的。 如果問題持續發生（永久），您可能會想要檢查連接字串、組織的防火牆設定、IP 防火牆設定、網路安全性設定（服務端點、私人端點等等）等等。 針對暫時性問題，升級至最新版本的 SDK、執行命令以檢查捨棄的封包，以及取得網路追蹤，可能有助於疑難排解問題。 

本文提供針對 Azure 事件中樞的連線問題進行疑難排解的秘訣。 

## <a name="troubleshoot-permanent-connectivity-issues"></a>針對永久連線問題進行疑難排解
如果應用程式根本無法連線到事件中樞，請遵循本節中的步驟來針對問題進行疑難排解。 

### <a name="check-if-there-is-a-service-outage"></a>檢查是否有服務中斷
檢查[Azure 服務狀態網站](https://azure.microsoft.com/status/)上的 Azure 事件中樞服務中斷情形。

### <a name="verify-the-connection-string"></a>驗證連接字串 
請確認您使用的連接字串正確。 請參閱[取得連接字串](event-hubs-get-connection-string.md)，以使用 AZURE 入口網站、CLI 或 PowerShell 來取得連接字串。 

針對 Kafka 用戶端，請確認已正確設定 producer.config 或 consumer.config 檔案。 如需詳細資訊，請參閱[在事件中樞中使用 Kafka 來傳送和接收訊息](event-hubs-quickstart-kafka-enabled-event-hubs.md#send-and-receive-messages-with-kafka-in-event-hubs)。

### <a name="check-if-the-ports-required-to-communicate-with-event-hubs-are-blocked-by-organizations-firewall"></a>檢查組織的防火牆是否封鎖與事件中樞通訊所需的埠
確認用來與 Azure 事件中樞通訊的埠不會在貴組織的防火牆上遭到封鎖。 請參閱下表，以取得您需要開啟以與 Azure 事件中樞通訊的輸出埠。 

| 通訊協定 | 連接埠 | 詳細資料 | 
| -------- | ----- | ------- | 
| AMQP | 5671 與 5672 | 請參閱 [AMQP 通訊協定指南](../service-bus-messaging/service-bus-amqp-protocol-guide.md) | 
| HTTP、HTTPS | 80、443 |  |
| Kafka | 9093 | 請參閱[從 Kafka 應用程式使用事件中樞](event-hubs-for-kafka-ecosystem-overview.md)

以下是用來檢查5671埠是否已封鎖的範例命令。

```powershell
tnc <yournamespacename>.servicebus.windows.net -port 5671
```

在 Linux 上：

```shell
telnet <yournamespacename>.servicebus.windows.net 5671
```

### <a name="verify-that-ip-addresses-are-allowed-in-your-corporate-firewall"></a>確認公司防火牆中允許的 IP 位址
當您使用 Azure 時，有時必須允許公司防火牆或 proxy 中的特定 IP 位址範圍或 Url 存取您正在使用或嘗試使用的所有 Azure 服務。 確認事件中樞所使用的 IP 位址允許流量。 針對 Azure 事件中樞所使用的 IP 位址：請參閱[AZURE IP 範圍和服務標籤-公用雲端](https://www.microsoft.com/download/details.aspx?id=56519)。

此外，請確認您命名空間的 IP 位址是允許的。 若要尋找正確的 IP 位址以允許您的連線，請遵循下列步驟：

1. 從命令提示字元執行下列命令： 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. 記下 `Non-authoritative answer` 中傳回的 IP 位址。 只有當您將命名空間還原到不同的叢集時，才會變更該 IP 位址。

如果您將區域備援用於命名空間，則需要執行一些額外的步驟： 

1. 首先，在命名空間上執行 nslookup。

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. 記下 [非授權回答] 區段中的名稱，其採用下列其中一種格式： 

    ```
    <name>-s1.cloudapp.net
    <name>-s2.cloudapp.net
    <name>-s3.cloudapp.net
    ```
3. 針對每個尾碼為 s1、s2 和 s3 的程式執行 nslookup，以取得三個可用性區域中執行的三個實例的 IP 位址。 

### <a name="verify-that-azureeventgrid-service-tag-is-allowed-in-your-network-security-groups"></a>確認您的網路安全性群組中允許 AzureEventGrid 服務標記
如果您的應用程式是在子網內執行，而且有相關聯的網路安全性群組，請確認是否允許網際網路輸出，或允許 AzureEventGrid 服務標籤。 請參閱[虛擬網路服務](../virtual-network/service-tags-overview.md)標籤，並搜尋 `EventHub` 。

### <a name="check-if-the-application-needs-to-be-running-in-a-specific-subnet-of-a-vnet"></a>檢查應用程式是否需要在 vnet 的特定子網中執行
確認您的應用程式正在具有命名空間存取權的虛擬網路子網中執行。 如果不是，請在可以存取命名空間的子網中執行應用程式，或將應用程式正在執行的電腦 IP 位址新增到[ip 防火牆](event-hubs-ip-filtering.md)。 

當您建立事件中樞命名空間的虛擬網路服務端點時，命名空間只會接受系結至服務端點之子網的流量。 這種行為有例外。 您可以在 IP 防火牆中新增特定的 IP 位址，以啟用事件中樞公用端點的存取權。 如需詳細資訊，請參閱[網路服務端點](event-hubs-service-endpoints.md)。

### <a name="check-the-ip-firewall-settings-for-your-namespace"></a>檢查命名空間的 IP 防火牆設定
檢查 IP 防火牆不會封鎖執行應用程式之電腦的公用 IP 位址。  

根據預設，只要要求具備有效的驗證和授權，便可以從網際網路存取事件中樞命名空間。 透過 IP 防火牆，您可以將其進一步限制為僅允許一組 IPv4 位址，或是使用 [CIDR (無類別網域間路由)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 標記法來設定 IPv4 位址範圍。

IP 防火牆規則會在事件中樞命名空間層級套用。 因此，規則會套用至來自用戶端的所有連接 (使用任何受支援的通訊協定)。 任何來自某個 IP 位址的連線嘗試，只要不符合「事件中樞」命名空間上的允許 IP 規則，系統就會將其視為未經授權而予以拒絕。 回應則不涉及 IP 規則。 IP 篩選器規則會依序套用，而且第一個符合 IP 位址的規則會決定接受或拒絕動作。

如需詳細資訊，請參閱[設定 Azure 事件中樞命名空間的 IP 防火牆規則](event-hubs-ip-filtering.md)。 若要檢查您是否有 IP 篩選、虛擬網路或憑證鏈問題，請參閱針對[網路相關問題進行疑難排解](#troubleshoot-network-related-issues)。

#### <a name="find-the-ip-addresses-blocked-by-ip-firewall"></a>尋找 IP 防火牆封鎖的 IP 位址
遵循[啟用診斷記錄](event-hubs-diagnostic-logs.md#enable-diagnostic-logs)中的指示，啟用[事件中樞虛擬網路線上活動](event-hubs-diagnostic-logs.md#event-hubs-virtual-network-connection-event-schema)的診斷記錄。 您會看到拒絕連線的 IP 位址。

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

### <a name="check-if-the-namespace-can-be-accessed-using-only-a-private-endpoint"></a>檢查是否只能使用私用端點來存取命名空間
如果事件中樞命名空間設定為只能透過私人端點存取，請確認用戶端應用程式正在透過私人端點存取命名空間。 

[Azure 私人連結服務](../private-link/private-link-overview.md)可讓您透過虛擬網路中的**私人端點**來存取 Azure 事件中樞。 私人端點是一種網路介面，其可以私人且安全的方式連線至 Azure Private Link 所支援服務。 私人端點會使用您虛擬網路中的私人 IP 位址，有效地將服務帶入您的虛擬網路。 服務的所有流量都可以透過私人端點路由傳送，因此不需要閘道、NAT 裝置、ExpressRoute 或 VPN 連線或公用 IP 位址。 虛擬網路和服務間的流量會在通過 Microsoft 骨幹網路時隨之減少，降低資料在網際網路中公開的風險。 您可連線到 Azure 資源的執行個體，以取得最高層級的存取控制細微性。

如需詳細資訊，請參閱[設定私人端點](private-link-service.md)。 請參閱**驗證私用端點連接的運作**一節，以確認已使用私用端點。 

### <a name="troubleshoot-network-related-issues"></a>針對網路相關問題進行疑難排解
若要針對事件中樞的網路相關問題進行疑難排解，請遵循下列步驟： 

流覽至或[wget](https://www.gnu.org/software/wget/) `https://<yournamespacename>.servicebus.windows.net/` 。 它有助於檢查您是否有 IP 篩選或虛擬網路或憑證鏈問題（最常見的情況是使用 JAVA SDK）。

**成功訊息**的範例：

```xml
<feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
```

**失敗錯誤訊息**的範例：

```json
<Error>
    <Code>400</Code>
    <Detail>
        Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
    </Detail>
</Error>
```

## <a name="troubleshoot-transient-connectivity-issues"></a>針對暫時性連接問題進行疑難排解
如果您遇到間歇性的連線問題，請流覽下列各節以取得疑難排解秘訣。 

### <a name="use-the-latest-version-of-the-client-sdk"></a>使用最新版的用戶端 SDK
在較新版本的 SDK 中，某些暫時性連線問題可能已修正，而不是您所使用的。 請確定您在應用程式中使用最新版本的用戶端 Sdk。 Sdk 會持續以新的/更新的功能和 bug 修正進行改良，因此請務必使用最新的套件進行測試。 請檢查版本資訊，以瞭解已修正的問題，以及新增/更新的功能。 

如需用戶端 Sdk 的詳細資訊，請參閱[Azure 事件中樞用戶端 sdk](sdks.md)文章。 

### <a name="run-the-command-to-check-dropped-packets"></a>執行命令以檢查丟棄的封包
當發生間歇連線問題時，請執行下列命令來檢查是否有任何已丟棄的封包。 此命令會嘗試使用服務每隔1秒建立25個不同的 TCP 連線。 然後，您可以檢查其中有多少個成功/失敗，同時查看 TCP 連接延遲。 您可以 `psping` 從[這裡](/sysinternals/downloads/psping)下載此工具。

```shell
.\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.windows.net:5671 -nobanner     
```
如果您使用其他工具（例如、等等），可以使用對等的命令 `tnc` `ping` 。 

如果先前的步驟沒有使用[Wireshark](https://www.wireshark.org/)之類的工具來協助並加以分析，請取得網路追蹤。 如有需要，請聯絡[Microsoft 支援服務](https://support.microsoft.com/)。 

### <a name="service-upgradesrestarts"></a>服務升級/重新開機
暫時性連線問題可能是因為後端服務升級和重新開機所造成。 發生時，您可能會看到下列徵兆： 

- 傳入訊息/要求中可能會有一個下降。
- 記錄檔可能包含錯誤訊息。
- 應用程式可能會在幾秒內中斷與服務的連線。
- 要求可能會短暫地進行節流。

如果應用程式代碼利用 SDK，則重試原則已經內建且作用中。 應用程式會重新連線，而不會對應用程式/工作流程造成重大影響。 攔截這些暫時性錯誤、將其關閉，然後重試呼叫，將可確保您的程式碼可在這些暫時性問題中復原。

## <a name="next-steps"></a>後續步驟
查看下列文章：

* [針對驗證及授權問題進行疑難排解](troubleshoot-authentication-authorization.md)
