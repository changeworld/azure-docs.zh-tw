---
title: Azure Front Door 快取 |Microsoft Docs
description: 本文可協助您瞭解已啟用快取之路由規則 Front Door 的行為。
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2020
ms.author: duau
ms.openlocfilehash: 1a8064c3ff89c0bc8b0ceb5249492b912c219ce8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91535826"
---
# <a name="caching-with-azure-front-door"></a>使用 Azure Front Door 快取
下列檔指定具有已啟用快取之路由規則 Front Door 的行為。 Front Door 是現代的內容傳遞網路 (使用動態網站加速和負載平衡的 CDN) ，它也支援像任何其他 CDN 一樣的快取行為。

## <a name="delivery-of-large-files"></a>傳遞大型檔案
Azure Front Door 傳遞大型檔案，且檔案大小沒有上限。 Front Door 會使用稱為物件區塊化的技術。 要求大型檔案時，Front Door 會從後端擷取較小的檔案片段。 收到完整或位元組範圍的檔案要求之後，Front Door 環境會以 8 MB 的區塊向後端要求檔案。

</br>當區塊抵達 Front Door 環境之後，它就會快取並立即提供給使用者。 然後 Front Door 會以平行方式預先提取下一個區塊。 此預先擷取可確保內容會領先使用者一個區塊，以降低延遲。 如果要求) 或用戶端關閉連線，則此程式會繼續進行，直到 (下載整個檔案為止。

</br>如需位元組範圍要求的詳細資訊，請參閱 [RFC 7233](https://web.archive.org/web/20171009165003/http://www.rfc-base.org/rfc-7233.html)。
Front Door 會在收到任何區塊時進行快取，因此不需要在 Front Door 快取上快取整個檔案。 系統會從快取中提供檔案或位元組範圍的後續要求。 如果區塊未全部快取，則會使用預先提取來向後端要求區塊。 此優化依賴後端的功能來支援位元組範圍的要求。 如果後端不支援位元組範圍要求，此優化就不會生效。

## <a name="file-compression"></a>檔案壓縮
Front Door 可以動態壓縮邊緣上的內容，讓您的用戶端更快且更快速的回應時間。 所有檔案都符合壓縮資格。 不過，檔案必須是 MIME 類型才能符合壓縮資格。 目前，Front Door 不允許變更此清單。 目前的清單為：</br>
- "application/eot"
- "application/font"
- "application/font-sfnt"
- "application/javascript"
- "application/json"
- "application/opentype"
- "application/otf"
- "application/pkcs7-mime"
- "application/truetype"
- "application/ttf"，
- "application/vnd.ms-fontobject"
- "application/xhtml+xml"
- "application/xml"
- "application/xml+rss"
- "application/x-font-opentype"
- "application/x-font-truetype"
- "application/x-font-ttf"
- "application/x-httpd-cgi"
- "application/x-mpegurl"
- "application/x-opentype"
- "application/x-otf"
- "application/x-perl"
- "application/x-ttf"
- "application/x-javascript"
- "font/eot"
- "font/ttf"
- "font/otf"
- "font/opentype"
- "image/svg+xml"
- "text/css"
- "text/csv"
- "text/html"
- "text/javascript"
- "text/js"、"text/plain"
- "text/richtext"
- "text/tab-separated-values"
- "text/xml"
- "text/x-script"
- "text/x-component"
- "text/x-java-source"

此外，檔案大小也必須介於 1 KB 到 8 MB (含) 之間。

這些設定檔支援下列壓縮編碼：
- [Gzip (GNU zip) ](https://en.wikipedia.org/wiki/Gzip)
- [Brotli](https://en.wikipedia.org/wiki/Brotli)

如果要求支援 gzip 和 Brotli 壓縮，Brotli 壓縮的優先順序最高。</br>
當資產的要求指定壓縮，而要求導致快取遺漏時，Front Door 會直接在 POP 伺服器上壓縮資產。 之後會從快取提供壓縮的檔案。 產生的項目會使用 transfer-encoding: chunked 來傳回。

## <a name="query-string-behavior"></a>查詢字串行為
使用 Front Door，可以控制 Web 要求內含查詢字串時的檔案快取方式。 在包含查詢字串的 Web 要求中，查詢字串是要求中問號 (?) 之後的部分。 查詢字串可以包含一或多個索引鍵/值組，其中的欄位名稱與其值是以等號 (=) 分隔。 每個索引鍵/值組是以 & 符號分隔。 例如： `http://www.contoso.com/content.mov?field1=value1&field2=value2` 。 如果要求的查詢字串中有一個以上的索引鍵/值組，則其順序並不重要。
- **忽略查詢字串**：在此模式中，Front Door 會將要求者的查詢字串傳遞至第一個要求的後端，並快取資產。 從 Front Door 環境提供之資產的所有後續要求都會忽略查詢字串，直到快取的資產到期為止。

- **快取所有不重複的 URL**：在此模式中，每個要求都有一個唯一的 URL (包含查詢字串)，會被視為具有專屬快取的唯一資產。 例如，來自要求的後端回應 `www.example.ashx?q=test1` 會在 Front Door 環境中快取，並以相同的查詢字串傳回給後續的快取。 系統快取針對 `www.example.ashx?q=test2` 的要求，會將其視為具有專屬存留時間設定的個別資產。

## <a name="cache-purge"></a>快取清除

Front Door 快取資產，直到資產的存留時間 (TTL) 到期為止。 每當用戶端要求過期 TTL 的資產時，Front Door 環境就會抓取資產的新更新複本以服務要求，然後儲存重新整理的快取。

若要確定使用者一律會取得最新的資產複本，最佳作法是為每個更新設定資產版本，然後將它們發佈為新的 URL。 Front Door 將立即為下一個用戶端要求擷取新的資產。 有時您可能想要清除所有邊緣節點的快取內容，並強制它們全部擷取新的更新的資產。 原因可能是您的 web 應用程式更新，或快速更新包含不正確資訊的資產。

從邊緣節點選取您要清除的資產。 若要清除所有資產，請選取 [ **全部清除**]。 否則，在 [ **路徑**] 中，輸入您想要清除的每個資產的路徑。

要清除的路徑清單中支援這些格式：

- **單一路徑清除**：藉由指定資產的完整路徑來清除個別資產 (沒有通訊協定和網域) 的副檔名，例如/pictures/strasbourg.png;
- **萬用字元清除**︰星號 (\*) 可作為萬用字元。 清除路徑中含有/的所有資料夾、子資料夾和檔案， \* 或指定資料夾後面接著/，以清除特定資料夾下的所有子資料夾和檔案 \* ，例如/pictures/ \* 。
- **根網域清除**︰清除路徑中有 "/" 之端點的根目錄。

> [!NOTE]
> **清除萬用字元網域**：指定要清除的快取路徑（如本節所述），不適用於與 Front Door 相關聯的任何萬用字元網域。 目前，我們不支援直接清除萬用字元網域。 您可以藉由指定特定子域和清除路徑來清除特定子域的路徑。 例如，如果我的 Front Door 有 `*.contoso.com` ，可以輸入來清除子域的資產 `foo.contoso.com` `foo.contoso.com/path/*` 。 目前，在清除內容路徑中指定主機名稱 imited 至萬用字元網域的子域（如果適用）。
>

Front Door 上的快取清除是不區分大小寫的。 此外，它們不受查詢字串的限制，這表示清除 URL 將會清除它的所有查詢字串變化。 

## <a name="cache-expiration"></a>快取到期
下列標頭順序可用來決定專案將儲存在快取中的時間長度：</br>
1. Cache-Control: s-maxage=\<seconds>
2. 快取-控制：最大壽命 =\<seconds>
3. 到期： \<http-date>

Cache-Control 回應標頭，表示不會快取回應，例如快取控制：私用、快取控制：無快取和快取控制：不接受存放區。  如果沒有 Cache-Control 存在，則預設行為是 Front Door 會快取 X 個時間長度的資源，其中 X 會隨機挑選介於1到3天之間。

## <a name="request-headers"></a>要求標頭

使用快取時，不會將下列要求標頭轉送至後端。
- Content-Length
- Transfer-Encoding

## <a name="cache-duration"></a>快取持續時間

您可以在 Front Door 設計工具和規則引擎中設定快取持續時間。 在 Front Door 表設計工具中設定的快取持續時間是最小的快取持續時間。 如果來源的快取控制標頭的 TTL 大於覆寫值，則此覆寫將無法運作。 

透過規則引擎設定的快取持續時間是真正的快取覆寫，這表示無論原始回應標頭為何，它都會使用覆寫值。

## <a name="next-steps"></a>後續步驟

- 了解如何[建立 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的運作方式](front-door-routing-architecture.md)。
