---
title: Azure Front Door 中 HTTP 標頭的通訊協定支援 |Microsoft Docs
description: 本文說明 Front Door 支援的 HTTP 標頭通訊協定。
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: duau
ms.openlocfilehash: 6c820754f7566261cd49f0b2ee24ec82dc1c8a9c
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92318345"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door"></a>Azure Front Door 中 HTTP 標頭的通訊協定支援
本文將概述 Front Door 支援的通訊協定與部分的呼叫路徑 (請參閱影像) 。 下列各節提供 Front Door 所支援之 HTTP 標頭的詳細資訊。

:::image type="content" source="./media/front-door-http-headers-protocol/front-door-protocol-summary.png" alt-text="Azure Front Door HTTP 標頭通訊協定":::

>[!IMPORTANT]
>Front Door 不會認證此處未記載的任何 HTTP 標頭。

## <a name="client-to-front-door"></a>用戶端至 Front Door
Front Door 接受連入要求的大部分標頭，而不加以修改。 某些保留的標頭會從傳入要求中移除（如果已傳送），包括具有 X-FD-* 首碼的標頭。

| 標頭  | 範例和描述 |
| ------------- | ------------- |
| X-Azure-InternalError：  | 此標頭將包含在處理要求時，Front Door 所遇到的錯誤碼。 此錯誤指出 Front Door 服務/基礎結構的內部問題。 報告要支援的問題。  |

## <a name="front-door-to-backend"></a>Front Door 至後端

Front Door 包含連入要求的標頭，除非這些標頭是因為限制而移除。 Front Door 也會新增下列標頭：

| 標頭  | 範例和描述 |
| ------------- | ------------- |
| Via |  *Via： 1.1 Azure* </br> Front Door 新增用戶端的 HTTP 版本，後面接著 *Azure* 作為 Via 標頭的值。 此標頭會指出用戶端的 HTTP 版本，且 Front Door 是用戶端與後端之間要求的中繼收件者。  |
| X-Azure-ClientIP | *X-Azure-ClientIP：127.0.0。1* </br> 表示與所處理之要求相關聯的用戶端 IP 位址。 例如，來自 proxy 的要求可能會新增 X 轉送的標頭，以指出原始呼叫端的 IP 位址。 |
| X-Azure-SocketIP |  *X-Azure-SocketIP：127.0.0。1* </br> 表示與目前要求來源之 TCP 連接相關聯的通訊端 IP 位址。 要求的用戶端 IP 位址可能不等於其通訊端 IP 位址，因為使用者可以任意覆寫它。|
| X-Azure-Ref | *X-Azure-Ref： 0zxV + XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> 識別 Front Door 所服務之要求的唯一參考字串。 它是用來搜尋存取記錄，以及用來進行疑難排解的關鍵。|
| X-Azure-RequestChain |  *X-Azure-RequestChain：躍點 = 1* </br> Front Door 用來偵測要求迴圈的標頭，且使用者不應該依賴它。 |
| X-Azure-FDID | *X-Azure-FDID：55ce4ed1-4b06-4bf1-b40e-4638452104da* <br/> 識別要求來自特定 Front Door 資源的參考字串。 您可以在 Azure 入口網站中看到值，或使用管理 API 來抓取。 您可以使用此標頭搭配 IP Acl 來鎖定您的端點，以只接受來自特定 Front Door 資源的要求。 如需[詳細資訊](front-door-faq.md#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door)，請參閱常見問題 |
| X-Forwarded-For | *X 轉送-代表：127.0.0。1* </br> X 轉送的 (XFF) HTTP 標頭欄位通常會識別用戶端透過 HTTP proxy 或負載平衡器連接到 web 伺服器的原始 IP 位址。 如果有現有的 XFF 標頭，則 Front Door 會將用戶端通訊端 IP 附加至該標頭，或使用用戶端通訊端 IP 新增 XFF 標頭。 |
| X-Forwarded-Host | *X 轉送-主機： contoso.azurefd.net* </br> [X 轉寄-主機 HTTP 標頭] 欄位是常見的方法，用來識別主機 HTTP 要求標頭中用戶端所要求的原始主機。 這是因為處理要求的後端伺服器可能會有不同 Front Door 的主機名稱。 |
| X-Forwarded-Proto | *X 轉送-Proto： HTTP* </br> [X 轉送-Proto HTTP 標頭] 欄位通常用來識別 HTTP 要求的原始通訊協定 根據設定的 Front Door 可能會使用 HTTPS 與後端通訊。 即使反向 proxy 的要求為 HTTP，也是如此。 |
| X-FD-HealthProbe | HealthProbe HTTP 標頭欄位是用來識別來自 Front Door 的健康情況探查。 如果此標頭設定為1，則要求是健康情況探查。 當您想要利用 X 轉送的主機標頭欄位，從特定的 Front Door 嚴格存取時，可以使用。 |
|X-Azure-FDID | *X-Azure-FDID 標頭：437c82cd-360a-4a54-94c3-5ff707647783* </br> 此欄位包含的 frontdoorID 可用來識別連入要求的來源 Front Door。 Front Door 服務會填入此欄位。 | 
|X-Azure-ExternalError | *X-Azure-ExternalError：0x830c1011，不熟悉憑證授權單位單位。* </br> 此標頭會顯示 Front Door 伺服器在建立與後端伺服器之間的連線以處理要求時，所遇到的錯誤碼。 此標頭將有助於找出 Front Door 與後端應用程式之間的連線問題。 此標頭將包含詳細的錯誤訊息，可協助您找出後端的連線問題 (例如 DNS 解析、不正確憑證等等。 ) 。 | 

## <a name="backend-to-front-door"></a>要 Front Door 的後端

| 標頭  | 範例和描述 |
| ------------- | ------------- |
| X-Azure-OriginStatusCode |  *X-Azure-OriginStatusCode：503* </br> 此標頭包含後端所傳回的 HTTP 狀態碼。 您可以使用此標頭來識別在您的後端中執行的應用程式所傳回的 HTTP 狀態碼，而不會通過後端記錄。 此狀態碼可能不同于 Front Door 傳送給用戶端的回應中的 HTTP 狀態碼。 此標頭可讓您判斷後端是否行為錯誤，或是否有 Front Door 服務的問題。 |

## <a name="front-door-to-client"></a>Front Door 至用戶端

任何從後端傳送至 Front Door 的標頭也會傳遞給用戶端。 以下是從 Front Door 傳送至用戶端的標頭。

| 標頭  | 範例和描述 |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref： 0zxV + XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> 這是唯一的參考字串，用來識別 Front Door 所服務的要求，對於進行疑難排解是很重要的，因為它是用來搜尋存取記錄。|
| X-快取 | *X-快取： TCP_HIT* </br> 此標頭描述要求的快取狀態，可讓您識別是否正在 Front Door 的快取中提供回應內容。 |

## <a name="next-steps"></a>後續步驟

- [建立 Front Door](quickstart-create-front-door.md)
- [Front Door 的運作方式](front-door-routing-architecture.md)
