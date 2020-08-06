---
title: Azure Front 中 HTTP 標頭的通訊協定支援 |Microsoft Docs
description: 本文說明 Front 門支援的 HTTP 標頭通訊協定。
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: a1060cbf1b2204c3feba413b8c8ce0cba58941c6
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87799118"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door"></a>Azure Front 中 HTTP 標頭的通訊協定支援
本文概述 Front 門板支援的通訊協定和部分的呼叫路徑 (查看影像) 。 下列各節提供有關 Front 門板所支援之 HTTP 標頭的詳細資訊。

![Azure Front 門板 HTTP 標頭通訊協定][1]

>[!IMPORTANT]
>Front 門不會認證此處未記載的任何 HTTP 標頭。

## <a name="client-to-front-door"></a>用戶端至 Front Door
Front 門板會接受來自傳入要求的大部分標頭，而不需要修改。 某些保留的標頭會從傳入要求中移除（如果已傳送），包括具有 X FD-* 前置詞的標頭。

## <a name="front-door-to-backend"></a>Front Door 至後端

Front 門包含來自傳入要求的標頭，除非因為限制而移除。 Front 門也會新增下列標頭：

| 標頭  | 範例和描述 |
| ------------- | ------------- |
| Via |  Via： 1.1 Azure </br> Front 門板會將用戶端的 HTTP 版本加上*Azure*做為 Via 標頭的值。 此標頭會指出用戶端的 HTTP 版本，而該 Front 門是用戶端與後端之間要求的中繼收件者。  |
| X-Azure-ClientIP | X-Azure-ClientIP：127.0.0。1 </br> 表示與所處理之要求相關聯的用戶端 IP 位址。 例如，來自 proxy 的要求可能會加入 X 轉送的標頭，以指出原始呼叫者的 IP 位址。 |
| X-Azure-SocketIP |  X-Azure-SocketIP：127.0.0。1 </br> 表示與目前要求來源的 TCP 連線相關聯的通訊端 IP 位址。 要求的用戶端 IP 位址可能不等於其通訊端 IP 位址，因為使用者可以任意覆寫它。|
| X-Azure-Ref |  X-Azure-Ref： 0zxV + XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz </br> 唯一的參考字串，可識別 Front 門所提供的要求。 它是用來搜尋存取記錄，而對疑難排解很重要。|
| X-Azure-RequestChain |  X-Azure-RequestChain：躍點 = 1 </br> 前端用來偵測要求迴圈的標頭，而且使用者不應依賴它。 |
| X-Forwarded-For | X-轉送-適用于：127.0.0。1 </br> X 轉送的 (XFF) HTTP 標頭欄位通常會識別用戶端透過 HTTP proxy 或負載平衡器連接到 web 伺服器的原始 IP 位址。 如果有現有的 XFF 標頭，則 Front 門板會將用戶端通訊端 IP 附加至它，或使用用戶端通訊端 IP 新增 XFF 標頭。 |
| X-Forwarded-Host | X-轉送-主機： contoso.azurefd.net </br> [X 轉送的主機 HTTP 標頭] 欄位是用來識別主機 HTTP 要求標頭中用戶端所要求之原始主機的常見方法。 這是因為處理要求的後端伺服器的前端主機名稱可能不同。 |
| X-Forwarded-Proto | X-轉送-Proto： HTTP </br> X 轉送的-Proto HTTP 標頭欄位通常用來識別 HTTP 要求的原始通訊協定，因為以設定為基礎的 Front 門可能會使用 HTTPS 與後端通訊。 即使反向 proxy 的要求是 HTTP 也是如此。 |
| X-FD-HealthProbe | HealthProbe HTTP 標頭欄位是用來識別來自 Front 大門的健康情況探查。 如果此標頭設為1，則要求為健全狀況探查。 當您想要從具有 [X 轉送主機標頭] 欄位的特定 Front 門進行嚴格存取時，可以使用。 |
|X-Azure-FDID | X-FDID 標頭：437c82cd-360a-4a54-94c3-5ff707647783 </br> 此欄位包含的 frontdoorID 可用來識別連入要求來自哪個 Front 門。 此欄位是由 Front 門板服務所填入。 | 

## <a name="front-door-to-client"></a>Front Door 至用戶端

從後端傳送至 Front 的任何標頭也會傳遞至用戶端。 以下是從前門傳送至用戶端的標頭。

| 標頭  | 範例 |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref： 0zxV + XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> 這是唯一的參考字串，可識別由 Front Door 提供服務的要求。 這對疑難排解很重要，因為它是用來搜尋存取記錄。|

## <a name="next-steps"></a>後續步驟

- [建立前端服務](quickstart-create-front-door.md)
- [Front 門板的運作方式](front-door-routing-architecture.md)

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png
