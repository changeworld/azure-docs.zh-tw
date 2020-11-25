---
title: 來自 Verizon Premium 的 Azure CDN 規則引擎功能
description: 來自 Verizon Premium 的 Azure CDN 規則引擎功能的參考文件。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 06/02/2020
ms.author: allensu
ms.openlocfilehash: 0ea4f167b992ccfbc4156ac06c8f636d2ef4a355
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020410"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-features"></a>來自 Verizon Premium 的 Azure CDN 規則引擎功能

本本會針對 Azure 內容傳遞網路 (CDN) [規則引擎](cdn-verizon-premium-rules-engine.md)列出可用功能的詳細說明。

規則的第三個部分是功能。 功能會定義動作類型，以套用到透過一組比對條件來識別的要求類型。

## <a name="azure-cdn-from-verizon-premium-rules-engine-features-reference"></a><a name="top"></a>來自 Verizon Premium 的 Azure CDN 規則引擎功能參考

可用的功能類型為：

* [存取](#access)
* [Caching](#caching)
* [註解](#comment)
* [標頭](#headers)
* [記錄](#logs)
* [最佳化](#optimize)
* [來源](#origin)
* [特殊](#specialty)
* [URL](#url)
* [Web 應用程式防火牆](#waf)

### <a name="access"></a><a name="access"></a>Access

這些功能是設計來控制內容的存取權。

| 名稱       | 目的                                                           |
|------------|-------------------------------------------------------------------|
| [拒絕存取 (403)](https://docs.vdms.com/cdn/Content/HRE/F/Deny-Access-403.htm) | 判斷所有要求是否已遭拒絕且含有 [403 禁止] 回應。 |
| [權杖驗證](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth.htm) | 判斷是否要將權杖型驗證套用到要求。 |
| [權杖驗證拒絕代碼](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Denial-Code.htm) | 判斷在要求因權杖型驗證而遭到拒絕時將傳回給使用者的回應類型。 |
| [權杖驗證會忽略 URL 的大小寫](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Ignore-URL-Case.htm) | 判斷透過權杖型驗證所做的 URL 比較是否會區分大小寫。 |
| [權杖驗證參數](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Parameter.htm) | 判斷是否應將權杖型驗證查詢字串參數重新命名。 |

**[回到頂端](#top)**

### <a name="caching"></a><a name="caching"></a>Caching

這些功能是設計來自訂快取內容的時機和方法。

| 名稱       | 目的                                                           |
|------------|-------------------------------------------------------------------|
| [頻寬參數](https://docs.vdms.com/cdn/Content/HRE/F/Bandwidth-Parameters.htm) | 判斷是否將會使用頻寬節流設定參數 (例如 ec_rate 和 ec_prebuf)。 |
| [頻寬節流設定](https://docs.vdms.com/cdn/Content/HRE/F/Bandwidth-Throttling.htm) | 針對我們的 Edge Server 所提供的回應進行頻寬節流設定。 |
| [略過快取](https://docs.vdms.com/cdn/Content/HRE/F/Bypass-Cache.htm) | 判斷要求是否可以利用我們的快取技術。 |
| [Cache-Control 標頭處理](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Control-Header-Treatment.htm) |  當 [外部最大壽命] 功能為作用中時，透過 Edge Server 來控制 Cache-Control 標頭的產生。 |
| [快取索引鍵查詢字串](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Key-Query-String.htm) | 判斷快取索引 **鍵** _ 是否會包含或排除與要求相關聯的查詢字串參數。 <br> __ 針對快取目的唯一識別資產的相對路徑。  我們的 edge server 會在檢查快取的內容時使用這個相對路徑。  根據預設，快取索引鍵不會包含查詢字串 parameters。_ |
| [快取索引鍵重寫](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Key-Rewrite.htm) | 重寫與要求相關聯的快取索引鍵。 |
| [完成快取填滿](https://docs.vdms.com/cdn/Content/HRE/F/Complete-Cache-Fill.htm) | 判斷在要求於 Edge Server 上產生部分快取遺失時會發生什麼事。 |
| [壓縮檔案類型](https://docs.vdms.com/cdn/Content/HRE/F/Compress-File-Types.htm) | 定義將在伺服器上壓縮的檔案格式。 | 
| [預設的內部最大壽命](https://docs.vdms.com/cdn/Content/HRE/F/Default-Internal-Max-Age.htm) | 判斷 Edge Server 到原始伺服器快取重新驗證之間的預設最大壽命間隔。 |
| [Expires 標頭處理](https://docs.vdms.com/cdn/Content/HRE/F/Expires-Header-Treatment.htm) | 當 [外部最大壽命] 功能為作用中時，透過 Edge Server 來控制 Expires 標頭的產生。 |
| [外部最大壽命](https://docs.vdms.com/cdn/Content/HRE/F/External-Max-Age.htm) | 判斷瀏覽器到 Edge Server 快取重新驗證之間的最大壽命間隔。 |
| [強制執行內部最大壽命](https://docs.vdms.com/cdn/Content/HRE/F/Force-Internal-Max-Age.htm) | 判斷 Edge Server 到原始伺服器快取重新驗證之間的最大壽命間隔。 |
| [H.264 支援 (HTTP 漸進式下載)](https://docs.vdms.com/cdn/Content/HRE/F/H.264-Support-HPD.htm) | 判斷可能用於串流處理內容的 H.264 檔案格式類型。 |
| [H.264 支援影片搜尋參數](https://docs.vdms.com/cdn/Content/HRE/F/H.264-Support-VSP-HPD.htm) | 覆寫指派給在使用 HTTP 漸進式下載時，透過 h.264 媒體控制搜尋的參數名稱。 |
| [接受 No-Cache 要求](https://docs.vdms.com/cdn/Content/HRE/F/Honor-No-Cache-Request.htm) | 判斷是否要將 HTTP 用戶端的 no-cache 要求轉送到原始伺服器。 |
| [忽略原始的 No-Cache](https://docs.vdms.com/cdn/Content/HRE/F/Ignore-Origin-No-Cache.htm) | 判斷我們的 CDN 是否將忽略原始伺服器所提供的特定指示詞。 |
| [忽略無法滿足的範圍](https://docs.vdms.com/cdn/Content/HRE/F/Ignore-Unsatisfiable-Ranges.htm) | 判斷在要求產生「416 無法滿足的要求範圍」狀態代碼時將傳回用戶端的要求。 |
| [內部最大過時](https://docs.vdms.com/cdn/Content/HRE/F/Internal-Max-Stale.htm) | 控制當 Edge Server 無法使用原始伺服器重新驗證快取的資產時，從 Edge Server 所提供的快取資產可能會經歷多長的標準到期時間。 |
| [部分快取共用](https://docs.vdms.com/cdn/Content/HRE/F/Partial-Cache-Sharing.htm) | 判斷要求是否可以產生部分快取的內容。 |
| [預先驗證快取的內容](https://docs.vdms.com/cdn/Content/HRE/F/Prevalidate-Cached-Content.htm) | 在快取內容的 TTL 到期之前，判斷其是否適用進行早期重新驗證。 |
| [重新整理零位元組的快取檔案](https://docs.vdms.com/cdn/Content/HRE/F/Refresh-Zero-Byte-Cache-Files.htm) | 判斷如何透過我們的 Edge Server 來處理 HTTP 用戶端對於 0 位元組快取資產的要求。 |
| [設定可快取的狀態碼](https://docs.vdms.com/cdn/Content/HRE/F/Set-Cacheable-Status-Codes.htm) | 定義一組可產生快取內容的狀態碼。 |
| [發生錯誤時傳遞過時的內容](https://docs.vdms.com/cdn/Content/HRE/F/Stale-Content-Delivery-on-Error.htm) | 判斷在快取重新驗證期間發生錯誤時，或者在接收到來自客戶原始伺服器的要求內容時，是否要傳遞到期的快取內容。 | 
| [在重新驗證時過期](https://docs.vdms.com/cdn/Content/HRE/F/Stale-While-Revalidate.htm) | 允許我們的 Edge Server 在進行重新驗證時提供過時的用戶端給要求者，藉以改善效能。 |

**[回到頂端](#top)**

### <a name="comment"></a><a name="comment"></a>註解

「註解」功能能夠在規則中新增附註。

**[回到頂端](#top)**

### <a name="headers"></a><a name="headers"></a>標題

這些功能是設計來新增、修改或刪除要求或回應的標頭。

| 名稱       | 目的                                                           |
|------------|-------------------------------------------------------------------|
| [Age 回應標頭](https://docs.vdms.com/cdn/Content/HRE/F/Age-Response-Header.htm) | 判斷 Age 回應標頭是否將包含於傳送到要求者的回應中。 |
| [偵錯快取回應標頭](https://docs.vdms.com/cdn/Content/HRE/F/Debug-Cache-Response-Headers.htm) | 判斷回應是否可包含 [X-EC-Debug 回應標頭](https://docs.vdms.com/cdn/Content/Knowledge_Base/X_EC_Debug.htm) ，以提供所要求資產的快取原則資訊。 |
| [修改用戶端要求標頭](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Request-Header.htm) | 覆寫、附加或刪除要求的標頭。 |
| [修改用戶端回應標頭](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Response-Header.htm) | 覆寫、附加或刪除回應的標頭。 |
| [設定用戶端 IP 自訂標頭](https://docs.vdms.com/cdn/Content/HRE/F/Set-Client-IP-Custom-Header.htm) | 允許將要新增到要求的要求用戶端 IP 位址做為自訂要求標頭。 |

**[回到頂端](#top)**

### <a name="logs"></a><a name="logs"></a>記錄

這些功能是設計來自訂儲存於原始記錄檔中的資料。

| 名稱       | 目的                                                           |
|------------|-------------------------------------------------------------------|
| [自訂記錄欄位 1](https://docs.vdms.com/cdn/Content/HRE/F/Custom-Log-Field-1.htm) | 判斷將指派給原始記錄檔中自訂記錄欄位的格式和內容。 |
| [記錄查詢字串](https://docs.vdms.com/cdn/Content/HRE/F/Log-Query-String.htm) | 判斷查詢字串以及 URL 是否將一起儲存於存取記錄中。 |

**[回到頂端](#top)**

### <a name="optimize"></a><a name="optimize"></a>最佳化

這些功能會判斷要求是否將經歷 Edge 最佳化工具所提供的最佳化。

| 名稱       | 目的                                                           |
|------------|-------------------------------------------------------------------|
| [Edge 最佳化工具](https://docs.vdms.com/cdn/Content/HRE/F/Edge-Optimizer.htm) | 判斷 Edge 最佳化工具是否可套用至要求。 |
| [Edge 最佳化工具 - 具現化設定](https://docs.vdms.com/cdn/Content/HRE/F/Edge-Optimizer-Instantiate-Configuration.htm) | 具現化或啟用與網站相關聯的 Edge 最佳化工具組態。 |

**[回到頂端](#top)**

### <a name="origin"></a><a name="origin"></a>來源

這些功能是設計來控制 CDN 與原始伺服器通訊的方式。

| 名稱       | 目的                                                           |
|------------|-------------------------------------------------------------------|
| [Keep-Alive 要求的最大值](https://docs.vdms.com/cdn/Content/HRE/F/Maximum-Keep-Alive-Requests.htm) | 判斷在關閉 Keep-Alive 連線之前，適用於該連線的最大要求數目。 |
| [Proxy 特定的標頭](https://docs.vdms.com/cdn/Content/HRE/F/Proxy-Special-Headers.htm) | 定義將從 edge server 轉送到源伺服器之 [CDN 特定要求標頭](https://docs.vdms.com/cdn/Content/Knowledge_Base/Request-Format.htm#RequestHeaders) 的集合。 |

**[回到頂端](#top)**

### <a name="specialty"></a><a name="specialty"></a>特殊

這些功能提供的進階功能僅可供進階使用者使用。

| 名稱       | 目的                                                           |
|------------|-------------------------------------------------------------------|
| [可快取的 HTTP 方法](https://docs.vdms.com/cdn/Content/HRE/F/Cacheable-HTTP-Methods.htm) | 判斷可在我們的網路上快取的其他 HTTP 方法組。 |
| [可快取的要求主體大小](https://docs.vdms.com/cdn/Content/HRE/F/Cacheable-Request-Body-Size.htm) | 定義用以判斷是否可快取 POST 回應的臨界值。 |
| [QUIC](https://docs.vdms.com/cdn/Content/HRE/F/QUIC.htm) | 判斷用戶端是否會收到我們的 CDN 服務支援 QUIC 的通知。 |
| [串流優化](https://docs.vdms.com/cdn/Content/HRE/F/Streaming-Optimization.htm) | 調整您的快取設定，以優化即時資料流的效能，並減少源伺服器的負載。 |
| [User 變數](https://docs.vdms.com/cdn/Content/HRE/F/User-Variable.htm) | 將值指派給傳遞給定制流量處理解決方案的使用者自訂變數。 |

**[回到頂端](#top)**

### <a name="url"></a><a name="url"></a>URL

這些功能可讓要求重新導向至不同的 URL 或重寫為不同的 URL。

| 名稱       | 目的                                                           |
|------------|-------------------------------------------------------------------|
| [遵循重新導向](https://docs.vdms.com/cdn/Content/HRE/F/Follow-Redirects.htm) | 判斷要求是否可以重新導向至定義於客戶原始伺服器所傳回之位置標頭中的主機名稱。 |
| [URL 重新導向](https://docs.vdms.com/cdn/Content/HRE/F/URL-Redirect.htm) | 透過位置標頭將要求重新導向。 |
| [URL 重寫](https://docs.vdms.com/cdn/Content/HRE/F/URL-Rewrite.htm) | 重寫要求 URL。 |

**[回到頂端](#top)**

### <a name="web-application-firewall"></a><a name="waf"></a>Web 應用程式防火牆

[Web 應用程式防火牆](https://docs.vdms.com/cdn/Content/HRE/F/Web_Application_Firewall.htm)功能會判斷要求是否會由 Web 應用程式防火牆進行篩選。

**[回到頂端](#top)**

如需最新功能，請參閱 [Verizon 規則引擎文件](https://docs.vdms.com/cdn/index.html#Quick_References/HRE_QR.htm#Actions) \(英文\)。

## <a name="next-steps"></a>後續步驟

- [規則引擎參考](cdn-verizon-premium-rules-engine-reference.md)
- [規則引擎條件運算式](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [規則引擎比對條件](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [使用規則引擎覆寫 HTTP 行為](cdn-verizon-premium-rules-engine.md)
- [Azure CDN 概觀](cdn-overview.md)
