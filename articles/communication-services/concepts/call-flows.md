---
title: Azure 通訊服務中的通話流程
titleSuffix: An Azure Communication Services concept document
description: 了解 Azure 通訊服務中的通話流程。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 9fe5cb13ee352b2c49ab6ae57cabd6116cdfa720
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91667668"
---
# <a name="call-flows"></a>通話流程

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

下一節將概略說明 Azure 通訊服務中的通話流程。 訊號和媒體流程取決於您的使用者所進行的通話類型。 通話類型的範例包括一對一 VoIP、一對一 PSTN，以及包含 VoIP 與 PSTN 連線參與者組合的群組通話。 請參閱[通話類型](./voice-video-calling/about-call-types.md)。

## <a name="about-signaling-and-media-protocols"></a>關於訊號和媒體通訊協定

當您建立點對點或群組通話時，幕後會使用兩個通訊協定 - HTTP (REST) 用來處理訊號，SRTP 則用於媒體。 

用戶端程式庫之間或用戶端程式庫與通訊服務訊號控制之間的訊號，會使用 HTTP REST (TLS) 來處理。 對於即時媒體流量 (RTP)，建議採用使用者資料包通訊協定 (UDP)。 如果您的防火牆無法使用 UDP，則用戶端程式庫會將傳輸控制通訊協定 (TCP) 用於媒體。 

我們來回顧一下各種案例中適用的訊號和媒體通訊協定。 

## <a name="call-flow-cases"></a>通話流程案例

### <a name="case-1-voip-where-a-direct-connection-between-two-devices-is-possible"></a>案例 1：可在兩個裝置之間直接連線的 VoIP

在一對一 VoIP 或視訊通話中，流量會優先採用最直接的路徑。 「直接路徑」指的是，如果兩個用戶端程式庫可以直接互相連線，則會建立直接連線。 當兩個用戶端程式庫位於相同的子網路中 (例如，在子網路 192.168.1.0/24 中)，或兩個裝置分別位於可彼此查看的不同子網路時 (位於子網路 10.10.0.0/16 和 192.168.1.0/24 中的用戶端程式庫可互相聯繫)，通常就可直接連線。

:::image type="content" source="./media/call-flows/about-voice-case-1.png" alt-text="此圖顯示使用者與通訊服務之間的直接 VOIP 通話。":::

### <a name="case-2-voip-where-a-direct-connection-between-devices-is-not-possible-but-where-connection-between-nat-devices-is-possible"></a>案例 2：無法在裝置之間直接連線、但 NAT 裝置之間可以連線的 VoIP

如果兩個裝置位於無法彼此連線的子網路中 (例如，Alice 在咖啡廳工作，而 Bob 在家裡工作)，但 NAT 裝置之間可以連線，則用戶端的用戶端程式庫將透過 NAT 裝置建立連線。 

對於 Alice，這會是咖啡廳的 NAT，對 Bob 而言則是家裡的 NAT。 Alice 的裝置會傳送其 NAT 的外部位址，而 Bob 的裝置也會執行相同的動作。 用戶端程式庫會從 Azure 通訊服務免費提供的 STUN (Session Traversal Utilities for NAT) 服務獲知外部位址。 處理 Alice 與 Bob 雙方交握的邏輯會內嵌在 Azure 通訊服務提供的用戶端程式庫內。 (您不需要進行額外的設定)

:::image type="content" source="./media/call-flows/about-voice-case-2.png" alt-text="此圖顯示使用者與通訊服務之間的直接 VOIP 通話。":::

### <a name="case-3-voip-where-neither-a-direct-nor-nat-connection-is-possible"></a>案例 3：直接連線和 NAT 連線皆不可行的 VoIP

如果有一或兩個用戶端裝置位於對稱式 NAT 後方，則需要以個別的雲端服務在兩個用戶端程式庫之間轉送媒體。 這項服務稱為 TURN (Traversal Using Relays around NAT)，同樣由通訊服務所提供。 通訊服務的通話用戶端程式庫會根據偵測到的網路狀況自動使用 TURN 服務。 使用 Microsoft 的 TURN 服務須另外計費。

:::image type="content" source="./media/call-flows/about-voice-case-3.png" alt-text="此圖顯示使用者與通訊服務之間的直接 VOIP 通話。":::
 
### <a name="case-4-group-calls-with-pstn"></a>案例 4：PSTN 的群組通話

PSTN 通話的訊號和媒體均使用 Azure 通訊服務的電話語音資源。 這項資源會與其他電信業者互相連線。

PSTN 媒體流量會通過名為「媒體處理器」的元件。

:::image type="content" source="./media/call-flows/about-voice-pstn.png" alt-text="此圖顯示使用者與通訊服務之間的直接 VOIP 通話。":::

> [!NOTE]
> 對於熟悉媒體處理的人而言，我們的媒體處理器也會是背靠背使用者代理程式 (Back to Back User Agent)，如 [RFC 3261 SIP：工作階段初始通訊協定](https://tools.ietf.org/html/rfc3261)所定義，這表示在處理 Microsoft 與電信業者網路之間的通話時，可以轉譯解碼器。 Azure 通訊服務的訊號控制器是 Microsoft 根據相同 RFC 進行的 SIP Proxy 實作。

進行群組通話時，媒體和訊號一律會透過 Azure 通訊服務後端傳輸。 所有參與者的音訊和/或視訊都會混合在媒體處理器元件中。 群組通話的所有成員都會將其音訊和/或視訊串流傳送至媒體處理器，再由處理器傳回混合的媒體串流。

群組通話的預設即時通訊協定 (RTP) 為使用者資料包通訊協定 (UDP)。

> [!NOTE]
> 媒體處理器可作為 Multipoint 控制單位 (MCU) 或選擇性轉送單位 (SFU)

:::image type="content" source="./media/call-flows/about-voice-group-calls.png" alt-text="此圖顯示使用者與通訊服務之間的直接 VOIP 通話。":::

如果用戶端程式庫因防火牆限制而無法將 UDP 用於媒體，則會嘗試使用傳輸控制通訊協定 (TCP)。 請注意，媒體處理器元件需要 UDP，因此在發生這種情況時，將會在群組通話中新增通訊服務 TURN 服務，以將 TCP 轉譯為 UDP。 在此情況下將會產生 TURN 費用，除非手動停用 TURN 功能。

:::image type="content" source="./media/call-flows/about-voice-group-calls-2.png" alt-text="此圖顯示使用者與通訊服務之間的直接 VOIP 通話。":::

## <a name="next-steps"></a>下一步

> [!div class="nextstepaction"]
> [開始使用通話](../quickstarts/voice-video-calling/getting-started-with-calling.md)

您可能會對下列文件感興趣：

- 深入了解[通話類型](../concepts/voice-video-calling/about-call-types.md)
- 了解[用戶端-伺服器架構](./client-and-server-architecture.md)
