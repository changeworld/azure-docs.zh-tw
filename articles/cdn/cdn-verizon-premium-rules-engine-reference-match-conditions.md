---
title: 來自 Verizon Premium 的 Azure CDN 規則引擎比對條件
description: 來自 Verizon Premium 的 Azure 內容傳遞網路規則引擎比對條件的參考文件。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: efd6e6a93cd4ca79e6c4b6de69f8514e2d71b252
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84323309"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>來自 Verizon Premium 的 Azure CDN 規則引擎比對條件

此文章會針對來自 Verizon Premium 的 Azure 內容傳遞網路 (CDN) [規則引擎](cdn-verizon-premium-rules-engine.md)列出可用比對條件的詳細描述。

規則的第二個部分是比對條件。 比對條件會識別特定類型的要求，系統將針對這類要求執行一組功能。

例如，您可以使用比對條件：

- 篩選特定位置的內容要求。
- 篩選從特定 IP 位址或國家/地區產生的要求。
- 依標頭資訊篩選要求。

## <a name="match-conditions"></a><a name="top"></a>符合條件

* [永遠](#always)
* [裝置](#device)
* [位置](#location)
* [來源](#origin)
* [要求](#request)
* [URL](#url)

### <a name="always"></a><a name="always"></a>永遠

「一律」比對[條件](https://docs.vdms.com/cdn/Content/HRE/M/Always.htm)是設計用來將一組預設功能套用至所有要求。

### <a name="device"></a><a name="device"></a>裝置

這些比對條件是設計來根據用戶端的使用者代理程式來識別要求。

| 名稱       | 目的                                                           |
|------------|-------------------------------------------------------------------|
| 品牌名稱 | 識別裝置的品牌名稱是否符合的要求： <br> **-** 特定值（[品牌名稱常](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Literal.htm)值） <br> **-** 正則運算式（[品牌名稱 Regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Regex.htm)） <br> **-** 特定模式（[品牌名稱萬用字元](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Wildcard.htm)） |
| 裝置作業系統 | 識別裝置的 OS 是否符合時的要求： <br> **-** 特定值（[裝置 OS 常](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Literal.htm)值） <br> **-** 正則運算式（[裝置 OS Regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Regex.htm)） <br> **-** 特定模式（[裝置 OS 萬用字元](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Wildcard.htm)） |
| 裝置作業系統版本 | 識別裝置的 OS 版本是否符合時的要求： <br> **-** 特定值（[裝置作業系統版本文字](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Literal.htm)） <br> **-** 正則運算式（[裝置作業系統版本 Regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Regex.htm)） <br> **-** 特定模式（[裝置作業系統版本萬用字元](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Wildcard.htm)） |
| [有雙重方向嗎？](https://docs.vdms.com/cdn/Content/HRE/M/D-Dual-Orientation.htm) | 識別裝置是否支援雙重方向的要求。 |
| HTML 慣用 DTD | 識別裝置的 HTML 慣用 DTD 是否符合時的要求： <br> **-** 特定值（[HTML 慣用 DTD 常](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Literal.htm)值） <br> **-** 正則運算式（[HTML 慣用 DTD Regex](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Regex.htm)） <br> **-** 特定模式（[HTML 慣用 DTD 萬用字元](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Wildcard.htm)） |
| [映射內嵌？](https://docs.vdms.com/cdn/Content/HRE/M/D-Image-Inlining.htm) | 根據裝置是否支援 Base64 編碼映射來識別要求。 |
| [是否為 Android？](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Android.htm) | 識別裝置是否使用 Android OS 的要求。 |
| [是應用程式嗎？](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-App.htm) | 根據原生應用程式是否要求內容來識別要求。 |
| [是否為完整桌面？](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Full-Desktop.htm) | 根據裝置是否提供完整的桌面體驗來識別要求。 |
| [是否為 iOS？](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-iOS.htm) | 識別裝置是否使用 iOS 的要求。 |
| [是機器人嗎？](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Robot.htm) | 識別裝置是否視為自動化的 HTTP 用戶端（例如，機器人編目程式）的要求。 |
| [是智慧型電視嗎？](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Smart-TV.htm) | 識別裝置是否為智慧型電視的要求。 |
| [Smartphone 嗎？](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Smartphone.htm) | 根據裝置是否為 smartphone 來識別要求。
| [是否為平板電腦？](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Tablet.htm) | 根據裝置是否為平板電腦來識別要求。 |
| [是觸控式螢幕嗎？](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Touchscreen.htm) | 識別裝置的主要指標裝置是否為觸控式螢幕的要求。 |
| [Windows Phone 嗎？](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Windows-Phone.htm) | 識別裝置是否為 Windows Mobile 6.5/Windows Phone 7 或更高版本的要求。 |
| [是否為無線裝置？](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Wireless-Device.htm) | 識別裝置是否為無線的要求。 
| 行銷名稱 | 根據裝置的行銷名稱是否符合來識別要求： <br> **-** 特定值（[行銷名稱常](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Literal.htm)值） <br> **-** 正則運算式（[行銷名稱 Regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Regex.htm)） <br> **-** 特定模式（[行銷名稱萬用字元](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Wildcard.htm)） |
| 行動瀏覽器 | 識別裝置的瀏覽器是否符合時的要求： <br> **-** 特定值（行動[瀏覽器文字](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Literal.htm)） <br> **-** 正則運算式（行動[瀏覽器 Regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Regex.htm)） <br> **-** 特定模式（行動[瀏覽器萬用字元](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Wildcard.htm)） |
| 行動瀏覽器版本 | 識別裝置的瀏覽器版本是否符合時的要求： <br> **-** 特定值（行動[瀏覽器版本文字](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Literal.htm)） <br> **-** 正則運算式（[Mobile 瀏覽器版本 Regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Regex.htm)） <br> **-** 特定模式（行動[瀏覽器版本萬用字元](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Wildcard.htm)） |
| 模型名稱 | 根據裝置的型號名稱是否符合來識別要求： <br> **-** 特定值（[模型名稱常](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Literal.htm)值） <br> **-** 正則運算式（[模型名稱 Regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Regex.htm)） <br> **-** 特定模式（[模型名稱萬用字元](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Wildcard.htm)） |
| [漸進式下載？](https://docs.vdms.com/cdn/Content/HRE/M/D-Progressive-Download.htm) | 根據裝置是否支援漸進式下載來識別要求。 |
| 發行日期 | 識別裝置的發行日期是否符合時的要求： <br> **-** 特定值（[發行日期常](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Literal.htm)值） <br> **-** 正則運算式（[發行日期 Regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Regex.htm)） <br> **-** 特定模式（[發行日期萬用字元](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Wildcard.htm)） |
| [解析高度](https://docs.vdms.com/cdn/Content/HRE/M/D-Resolution-Height.htm) | 識別裝置高度的要求。 |
| [解析寬度](https://docs.vdms.com/cdn/Content/HRE/M/D-Resolution-Width.htm) | 依據裝置的寬度識別要求。 |

**[回到頁首](#top)**

### <a name="location"></a><a name="location"></a>位置

這些比對條件是設計來根據要求者的位置識別要求。

| 名稱       | 目的                                                           |
|------------|-------------------------------------------------------------------|
| [AS 號碼](https://docs.vdms.com/cdn/Content/HRE/M/AS-Number.htm) | 識別源自特定網路的要求。 |
| 城市名稱 | 識別要求是否源自其名稱符合的城市： <br> **-** 特定值（[城市名稱常](https://docs.vdms.com/cdn/Content/HRE/M/City-Name-Literal.htm)值） <br> **-** 正則運算式（[城市名稱 Regex](https://docs.vdms.com/cdn/Content/HRE/M/City-Name-Regex.htm)） |
| [Continent](https://docs.vdms.com/cdn/Content/HRE/M/Continent.htm) | 識別源自指定大陸的要求。 |
| [國家/地區](https://docs.vdms.com/cdn/Content/HRE/M/Country.htm) | 識別源自特定國家/地區的要求。 |
| [DMA 代碼](https://docs.vdms.com/cdn/Content/HRE/M/DMA-Code.htm) | 識別源自指定 metros （指定的市場區域）的要求。 |
| [經度](https://docs.vdms.com/cdn/Content/HRE/M/Latitude.htm) | 識別源自指定緯度的要求。 |
| [經度](https://docs.vdms.com/cdn/Content/HRE/M/Longitude.htm) | 識別源自指定經度的要求。 |
| [Metro 代碼](https://docs.vdms.com/cdn/Content/HRE/M/Metro-Code.htm) | 識別源自指定 metros （指定的市場區域）的要求。 |
| [郵遞區號](https://docs.vdms.com/cdn/Content/HRE/M/Postal-Code.htm) | 識別源自指定郵遞區號的要求。 |
| [地區碼](https://docs.vdms.com/cdn/Content/HRE/M/Region-Code.htm) | 識別源自指定區域的要求。 |

> [!NOTE]
> **我應該使用 Metro 代碼或 DMA 代碼嗎？** <br>
這兩個相符條件都會提供相同的功能。 不過，我們建議使用 Metro 程式碼比對條件來識別 DMA 要求。

**[回到頁首](#top)**

### <a name="origin"></a><a name="origin"></a>來源

這些比對條件是設計來識別指向 CDN 儲存體或客戶原始伺服器的要求。

| 名稱       | 目的                                                           |
|------------|-------------------------------------------------------------------|
| [CDN 原點](https://docs.vdms.com/cdn/Content/HRE/M/CDN-Origin.htm) | 識別對儲存於 CDN 儲存體之內容的要求。 |
| [客戶原點](https://docs.vdms.com/cdn/Content/HRE/M/Customer-Origin.htm) | 識別對儲存於特定客戶原始伺服器上之內容的要求。 |

**[回到頁首](#top)**

### <a name="request"></a><a name="request"></a>要求

這些比對條件是設計來根據要求的位置識別它們。

| 名稱              | 目的                                                                |
|-------------------|------------------------------------------------------------------------|
| [用戶端 IP 位址](https://docs.vdms.com/cdn/Content/HRE/M/Client-IP-Address.htm) | 識別源自特定 IP 位址的要求。 |
| Cookie 參數  | 藉由是否包含符合的 cookie 來識別要求： <br> **-** 特定值（[Cookie 參數常](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Literal.htm)值） <br> **-** 正則運算式（[Cookie 參數 Regex](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Regex.htm) <br> **-** 特定模式（[Cookie 參數萬用字元](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Wildcard.htm)） |
| [邊緣 CNAME](https://docs.vdms.com/cdn/Content/HRE/M/Edge-CNAME.htm) | 識別指向特定邊緣 CNAME 的要求。 |
| 轉介網域 | 藉由符合的主機名稱來識別要求： <br> **-** 特定值（[參考網域常](https://docs.vdms.com/cdn/Content/HRE/M/Referring-Domain-Literal.htm)值） <br> **-** 特定模式（[參考網域萬用字元](https://docs.vdms.com/cdn/Content/HRE/M/Referring-Domain-Wildcard.htm)） |
| 要求標頭 | 藉由是否包含符合的標頭來識別要求： <br> **-** 特定值（[要求標頭常](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Literal.htm)值） <br> **-** 正則運算式（[要求標頭 Regex](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Regex.htm)） <br> **-** 特定模式（[要求標頭萬用字元](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Wildcard.htm)） |
| [Request 方法](https://docs.vdms.com/cdn/Content/HRE/M/Request-Method.htm) | 依其 HTTP 方法來識別要求。 |
| [要求配置](https://docs.vdms.com/cdn/Content/HRE/M/Request-Scheme.htm) | 依其 HTTP 通訊協定來識別要求。 |

**[回到頁首](#top)**

### <a name="url"></a><a name="url"></a>URL

| 名稱              | 目的                                                                |
|-------------------|------------------------------------------------------------------------|
| URL 路徑 | 根據要求的相對路徑（包括檔案名）是否符合，來識別其是否相符： <br> **-** 特定值（[URL 路徑常](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Literal.htm)值） <br> **-** 正則運算式（[URL 路徑 Regex](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Regex.htm)） <br> **-** 特定模式（[URL 路徑萬用字元](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Wildcard.htm)） |
| URL 路徑目錄 | 根據要求的相對路徑是否符合而識別它們： <br> **-** 特定值（[URL 路徑目錄常](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Directory-Literal.htm)值） <br> **-** 特定模式（[URL 路徑目錄萬用字元](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Directory-Wildcard.htm)） |
| URL 路徑的副檔名 | 識別其副檔名是否符合的要求： <br> **-** 特定值（[URL 路徑延伸常](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Extension-Literal.htm)值） <br> **-** 特定模式（[URL 路徑延伸萬用字元](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Extension-Wildcard.htm)） |
| URL 路徑的檔案名稱 | 識別其檔案名是否符合的要求： <br> **-** 特定值（[URL 路徑檔案名常](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Filename-Literal.htm)值） <br> **-** 特定模式（[URL 路徑檔案名萬用字元](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Filename-Wildcard.htm)） |
| URL 查詢 | 識別其查詢字串是否符合的要求： <br> **-** 特定值（[URL 查詢常](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Literal.htm)值） <br> **-** 正則運算式（[URL 查詢 Regex](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Regex.htm)） <br> **-** 特定模式（[URL 查詢萬用字元](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Wildcard.htm)） |
| URL 查詢參數 | 藉由是否將查詢字串參數設定為符合的值，來識別要求： <br> **-** 特定值（[URL 查詢參數常](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Parameter-Literal.htm)值） <br> **-** 特定模式（[URL 查詢參數萬用字元](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Parameter-Wildcard.htm)） |

**[回到頁首](#top)**

如需最新比對條件，請參閱 [Verizon 規則引擎文件](https://docs.vdms.com/cdn/index.html#Quick_References/HRE_QR.htm#Conditio) \(英文\)。

## <a name="next-steps"></a>後續步驟

- [Azure 內容傳遞網路概觀](cdn-overview.md)
- [規則引擎參考](cdn-verizon-premium-rules-engine-reference.md)
- [規則引擎條件運算式](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [規則引擎功能](cdn-verizon-premium-rules-engine-reference-features.md)
- [使用規則引擎覆寫預設的 HTTP 行為](cdn-verizon-premium-rules-engine.md)
