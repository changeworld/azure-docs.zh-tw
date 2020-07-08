---
title: 針對網路連線問題進行疑難排解-Azure 事件方格 |Microsoft Docs
description: 本文提供有關使用 Azure 事件方格針對網路連線問題進行疑難排解的資訊。
services: event-grid
documentationcenter: na
author: batrived
ms.service: event-grid
ms.devlang: na
ms.topic: article
ms.date: 06/21/2020
ms.author: batrived
ms.openlocfilehash: 28a570c5fdd3de6f1a3d23b3589c46f7457cabfe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85397930"
---
# <a name="troubleshoot-connectivity-issues---azure-event-grid"></a>針對連線問題進行疑難排解-Azure 事件方格

用戶端應用程式無法連接到事件方格主題/網域的原因有很多種。 您所遇到的連線問題可能是永久性或暫時性的。 如果問題持續發生（永久），您可能會想要檢查組織的防火牆設定、IP 防火牆設定、服務標記、私人端點等等。 針對暫時性問題，執行命令來檢查捨棄的封包，並取得網路追蹤可能有助於疑難排解問題。

本文提供針對 Azure 事件方格的連線問題進行疑難排解的秘訣。

## <a name="troubleshoot-permanent-connectivity-issues"></a>針對永久連線問題進行疑難排解

如果應用程式根本無法連線到事件方格，請遵循本節中的步驟來針對問題進行疑難排解。

### <a name="check-if-there-is-a-service-outage"></a>檢查是否有服務中斷

檢查 azure[服務狀態網站](https://azure.microsoft.com/status/)上的 Azure 事件方格服務中斷。

### <a name="check-if-the-ports-required-to-communicate-with-event-grid-arent-blocked-by-organizations-firewall"></a>檢查與事件方格通訊所需的埠是否不受組織的防火牆封鎖

確認用來與 Azure 事件方格通訊的埠不會在貴組織的防火牆上遭到封鎖。 請參閱下表，以取得您需要開啟以與 Azure 事件方格通訊的輸出埠。

| 通訊協定 | 連接埠 |
| -------- | ----- |
| HTTPS    | 443   |

以下是用來檢查443埠是否已封鎖的範例命令。

```powershell
.\psping.exe -n 25 -i 1 -q {sampletopicname}.{region}-{suffix}.eventgrid.azure.net:443 -nobanner
```

在 Linux 上：

```shell
telnet {sampletopicname}.{region}-{suffix}.eventgrid.azure.net 443
```

### <a name="verify-that-ip-addresses-are-allowed-in-your-corporate-firewall"></a>確認公司防火牆中允許的 IP 位址

當您使用 Azure 時，有時必須允許公司防火牆或 proxy 中的特定 IP 位址範圍或 Url 存取您正在使用或嘗試使用的所有 Azure 服務。 確認事件方格所使用的 IP 位址允許流量。 針對 Azure 事件方格所使用的 IP 位址：請參閱[AZURE IP 範圍和服務標籤-公用雲端](https://www.microsoft.com/download/details.aspx?id=56519)和服務標籤[-AzureEventGrid](network-security.md#service-tags)。

> [!NOTE]
> 可以將新的 IP 位址新增至 AzureEventGrid 服務標籤，但它並不平常。 因此，在服務標籤上進行每週檢查是很好的。

### <a name="verify-that-azureeventgrid-service-tag-is-allowed-in-your-network-security-groups"></a>確認您的網路安全性群組中允許 AzureEventGrid 服務標記

如果您的應用程式是在子網內執行，而且有相關聯的網路安全性群組，請確認是否允許網際網路輸出或 AzureEventGrid 服務標籤。 請參閱[服務標記](../virtual-network/service-tags-overview.md)

### <a name="check-the-ip-firewall-settings-for-your-topicdomain"></a>檢查主題/網域的 IP 防火牆設定

檢查 EventGrid 主題/網域 IP 防火牆不會封鎖執行應用程式之電腦的公用 IP 位址。

根據預設，只要要求隨附有效的驗證和授權，就可以從網際網路存取事件方格主題/網域。 透過 IP 防火牆，您可以將其進一步限制為僅允許一組 IPv4 位址，或是使用 [CIDR (無類別網域間路由)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 標記法來設定 IPv4 位址範圍。

IP 防火牆規則會在事件方格主題/網域層級套用。 因此，規則會套用至來自用戶端的所有連接 (使用任何受支援的通訊協定)。 從不符合事件方格主題/網域上允許之 IP 規則的 IP 位址進行的任何連線嘗試都會被拒絕為禁止。 回應則不涉及 IP 規則。

如需詳細資訊，請參閱[設定 Azure 事件方格主題/網域的 IP 防火牆規則](configure-firewall.md)。

#### <a name="find-the-ip-addresses-blocked-by-ip-firewall"></a>尋找 IP 防火牆封鎖的 IP 位址

啟用事件方格主題/網域的診斷記錄[啟用診斷記錄](enable-diagnostic-logs-topic.md#enable-diagnostic-logs-for-a-custom-topic)。 您會看到拒絕連線的 IP 位址。

```json
{
  "time": "2019-11-01T00:17:13.4389048Z",
  "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME",
  "category": "PublishFailures",
  "operationName": "Post",
  "message": "inputEventsCount=null, requestUri=https://SAMPLE-TOPIC-NAME.region-suffix.eventgrid.azure.net/api/events, publisherInfo=PublisherInfo(category=User, inputSchema=EventGridEvent, armResourceId=/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME), httpStatusCode=Forbidden, errorType=ClientIPRejected, errorMessage=Publishing to SAMPLE-TOPIC-NAME.{region}-{suffix}.EVENTGRID.AZURE.NET by client {clientIp} is rejected due to IpAddress filtering rules."
}
```

### <a name="check-if-the-eventgrid-topicdomain-can-be-accessed-using-only-a-private-endpoint"></a>檢查是否只能使用私人端點來存取 EventGrid 主題/網域

如果將事件方格主題/網域設定為只能透過私人端點存取，請確認用戶端應用程式正在透過私人端點存取主題/網域。 若要確認這一點，請檢查用戶端應用程式是否在子網內執行，以及該子網中是否有事件方格主題/網域的私用端點。

[Azure 私用連結服務](../private-link/private-link-overview.md)可讓您透過虛擬網路中的**私人端點**來存取 Azure 事件方格。 私人端點是一種網路介面，其可以私人且安全的方式連線至 Azure Private Link 所支援服務。 私人端點會使用您 VNet 中的私人 IP 位址，有效地將服務帶入您的 VNet 中。 服務的所有流量都可以透過私人端點路由傳送，因此不需要閘道、NAT 裝置、ExpressRoute 或 VPN 連線或公用 IP 位址。 虛擬網路和服務間的流量會在通過 Microsoft 骨幹網路時隨之減少，降低資料在網際網路中公開的風險。 您可連線到 Azure 資源的執行個體，以取得最高層級的存取控制細微性。

如需詳細資訊，請參閱[設定私人端點](configure-private-endpoints.md)。

## <a name="troubleshoot-transient-connectivity-issues"></a>針對暫時性連接問題進行疑難排解

如果您遇到間歇性的連線問題，請流覽下列各節以取得疑難排解秘訣。

### <a name="run-the-command-to-check-dropped-packets"></a>執行命令以檢查丟棄的封包

當發生間歇連線問題時，請執行下列命令來檢查是否有任何已丟棄的封包。 此命令會嘗試使用服務每隔1秒建立25個不同的 TCP 連線。 然後，您可以檢查其中有多少個成功/失敗，同時查看 TCP 連接延遲。 您可以 `psping` 從[這裡](/sysinternals/downloads/psping)下載此工具。

```shell
.\psping.exe -n 25 -i 1 -q {sampletopicname}.{region}-{suffix}.eventgrid.azure.net:443 -nobanner
```

如果您使用其他工具（例如tcpping.exe），可以使用對等 `tcpping` [tcpping.exe](https://www.elifulkerson.com/projects/tcping.php)的命令。

如果先前的步驟沒有使用[Wireshark](https://www.wireshark.org/)之類的工具來協助並加以分析，請取得網路追蹤。 如有需要，請聯絡[Microsoft 支援服務](https://support.microsoft.com/)。

### <a name="service-upgradesrestarts"></a>服務升級/重新開機

暫時性連線問題可能是因為後端服務升級和重新開機所造成。 發生時，您可能會看到下列徵兆：

- 傳入訊息/要求中可能會有一個下降。
- 記錄檔可能包含錯誤訊息。
- 應用程式可能會在幾秒內中斷與服務的連線。
- 要求可能會短暫地進行節流。

攔截這些暫時性錯誤、將其關閉，然後重試呼叫，將可確保您的程式碼可在這些暫時性問題中復原。

## <a name="next-steps"></a>後續步驟

如果您需要更多協助，請將您的問題張貼在 [Stack Overflow 論壇](https://stackoverflow.com/questions/tagged/azure-eventgrid)，或開啟[支援票證](https://azure.microsoft.com/support/options/)。
