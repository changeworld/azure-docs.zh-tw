---
title: 比較 Azure 內容傳遞網路 (CDN) 產品功能
description: 了解每項 Azure 內容傳遞網路 (CDN) 產品所支援的功能。
services: cdn
documentationcenter: ''
author: asudbring
ms.service: azure-cdn
ms.topic: overview
ms.date: 11/15/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 8dbf3b5de4b908eb831158b73b2b17472bc4895e
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020958"
---
# <a name="what-are-the-comparisons-between-azure-cdn-product-features"></a>Azure CDN 產品功能之間的比較有哪些？

Azure 內容傳遞網路 (CDN) 包含四項產品： 

* **來自 Microsoft 的 Azure CDN 標準**
* **來自 Akamai 的 Azure CDN 標準**
* **來自 Verizon 的 Azure CDN 標準**
* **來自 Verizon 的 Azure CDN 進階**。 

下表將比較各項產品的可用功能。

| **效能功能和最佳化** | **標準 Microsoft** | **標準 Akamai** | **標準 Verizon** | **進階 Verizon** |
| --- | --- | --- | --- | --- |
| [動態網站加速](./cdn-dynamic-site-acceleration.md)  | 透過 [Azure Front Door Service](../frontdoor/front-door-overview.md) 提供 | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[動態網站加速 - 調整映像壓縮](./cdn-dynamic-site-acceleration.md#adaptive-image-compression-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[動態網站加速 - 物件預先擷取](./cdn-dynamic-site-acceleration.md#object-prefetch-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| [一般 Web 傳遞最佳化](./cdn-optimization-overview.md#general-web-delivery)  | **&#x2713;** | **&#x2713;** 如果您的平均檔案大小小於 10 MB，請選取此最佳化類型。  | **&#x2713;** |  **&#x2713;** |
| [影片串流最佳化](./cdn-media-streaming-optimization.md)  | 透過一般 Web 傳遞 | **&#x2713;**  | 透過一般 Web 傳遞 |  透過一般 Web 傳遞 |
| [大型檔案最佳化](./cdn-large-file-optimization.md)  | 透過一般 Web 傳遞 | **&#x2713;** 如果您的平均檔案大小大於 10 MB，請選取此最佳化類型。   | 透過一般 Web 傳遞 |  透過一般 Web 傳遞 |
| 變更最佳化類型 | |**&#x2713;** | | |
| 原始連接埠 |所有 TCP 連接埠 |[允許的來源連接埠](/previous-versions/azure/mt757337(v%3Dazure.100)#allowed-origin-ports) |所有 TCP 連接埠 |所有 TCP 連接埠 |
| [全域伺服器負載平衡 (GSLB)](../traffic-manager/traffic-manager-load-balancing-azure.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [快速清除](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;** 來自 Akamai 的 Azure CDN 目前不支援「全部清除」和「萬用字元清除」。 |**&#x2713;** |**&#x2713;** |
| [資產預先載入](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| 快取/標頭設定 (使用[快取規則](cdn-caching-rules.md))  |**&#x2713;** 使用 [標準規則引擎](cdn-standard-rules-engine.md)  |**&#x2713;** |**&#x2713;** | |
| 可自訂的規則式內容傳遞引擎 |**&#x2713;** 使用 [標準規則引擎](cdn-standard-rules-engine.md)  | | |**&#x2713;** 使用 [規則引擎](./cdn-verizon-premium-rules-engine.md) |
| 快取/標頭設定  |**&#x2713;** 使用 [標準規則引擎](cdn-standard-rules-engine.md) | | |**&#x2713;** 使用 [進階規則引擎](./cdn-verizon-premium-rules-engine.md) |
| URL 重新導向/重寫 |**&#x2713;** 使用 [標準規則引擎](cdn-standard-rules-engine.md)  | | |**&#x2713;** 使用 [進階規則引擎](./cdn-verizon-premium-rules-engine.md) |
| 行動裝置規則  |**&#x2713;** 使用 [標準規則引擎](cdn-standard-rules-engine.md) | | |**&#x2713;** 使用 [進階規則引擎](./cdn-verizon-premium-rules-engine.md) |
| [查詢字串快取](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| IPv4/IPv6 雙重堆疊 | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTP/2 支援](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **安全性** | **標準 Microsoft** | **標準 Akamai** | **標準 Verizon** | **進階 Verizon** | 
| CDN 端點的 HTTPS 支援 | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [自訂網域 HTTPS](cdn-custom-ssl.md)  | **&#x2713;** | **&#x2713;** ，直接 CNAME 必須啟用 |**&#x2713;** |**&#x2713;** |
| [自訂網域名稱支援](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [地理位置篩選](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [權杖驗證](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [DDOS 保護](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [使用您自己的憑證](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) |**&#x2713;** |  | **&#x2713;** | **&#x2713;** |
| 支援的 TLS 版本 | TLS 1.2、TLS 1.0/1.1 - [可設定](/rest/api/cdn/customdomains/enablecustomhttps#usermanagedhttpsparameters) | TLS 1.2 | TLS 1.2 | TLS 1.2 |
||||
| **分析和報告** | **標準 Microsoft** | **標準 Akamai** | **標準 Verizon** | **進階 Verizon** | 
| [Azure 診斷記錄](cdn-azure-diagnostic-logs.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [來自 Verizon 的核心報告](cdn-analyze-usage-patterns.md)  |  | |**&#x2713;** |**&#x2713;** |
| [來自 Verizon 的自訂報告](cdn-verizon-custom-reports.md)  |  | |**&#x2713;** |**&#x2713;** |
| [進階 HTTP 報告](cdn-advanced-http-reports.md)  |  | | |**&#x2713;** |
| [即時統計資料](cdn-real-time-stats.md)  |  | | |**&#x2713;** |
| [邊緣節點效能](cdn-edge-performance.md)  |  | | |**&#x2713;** |
| [即時警示](cdn-real-time-alerts.md)  |  | | |**&#x2713;** |
||||
| **容易使用** | **標準 Microsoft** | **標準 Akamai** | **標準 Verizon** | **進階 Verizon** | 
| 輕鬆地與 Azure 服務 (例如[儲存體](cdn-create-a-storage-account-with-cdn.md)、[Web Apps](cdn-add-to-web-app.md) 和[媒體服務](../media-services/previous/media-services-portal-manage-streaming-endpoints.md)) 整合  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| 透過 [REST API](/rest/api/cdn/)、[.NET](cdn-app-dev-net.md)、[Node.js](cdn-app-dev-node.md) 或 [PowerShell](cdn-manage-powershell.md) 管理  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [壓縮 MIME 類型](./cdn-improve-performance.md)  |僅限預設值 |可設定 |可設定  |可設定  |
| 壓縮編碼  |gzip、brotli |gzip |gzip、deflate、bzip2、brotli  |gzip、deflate、bzip2、brotli  |

## <a name="migration"></a>移轉

如需將 [來自 Verizon 的標準 Azure CDN]  設定檔移轉至 [來自 Verizon 的進階 Azure CDN]  的相關資訊，請參閱[將 Azure CDN 設定檔從標準 Verizon 移轉至進階 Verizon](cdn-migrate.md)。 

> [!NOTE]
> 您可以從標準 Verizon 升級到進階 Verizon，但目前在其他產品之間並沒有轉換機制。

## <a name="next-steps"></a>後續步驟

* 深入了解 [Azure CDN](cdn-overview.md)。
