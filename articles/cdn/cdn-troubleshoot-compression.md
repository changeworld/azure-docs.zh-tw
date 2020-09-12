---
title: 針對 Azure CDN 中的檔案壓縮進行疑難排解 | Microsoft Docs
description: 瞭解如何針對 Azure 內容傳遞網路中的檔案壓縮問題進行疑難排解。 本文涵蓋數個可能的原因。
services: cdn
documentationcenter: ''
author: sohamnc
manager: danielgi
editor: ''
ms.assetid: a6624e65-1a77-4486-b473-8d720ce28f8b
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: f49af1488a0c044639a72fc2ea52ba0a47727a24
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2020
ms.locfileid: "89433665"
---
# <a name="troubleshooting-cdn-file-compression"></a>CDN 檔案壓縮疑難排解
這篇文章可協助您針對 [CDN 檔案壓縮](cdn-improve-performance.md)的問題進行疑難排解。

如果在本文章中有任何需要協助的地方，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)上的 Azure 專家。 或者，您也可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後按一下 [取得支援]****。

## <a name="symptom"></a>徵狀
已為您的端點啟用壓縮，但會傳回未壓縮的檔案。

> [!TIP]
> 若要檢查傳回的檔案是否會壓縮，您需要使用 [Fiddler](https://www.telerik.com/fiddler) 之類的工具或您瀏覽器的[開發人員工具](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)。  檢查隨快取的 CDN 內容傳回的 HTTP 回應標頭。  如果名為 `Content-Encoding` 的標頭有 **gzip**、**bzip2** 或 **deflate** 值，內容會進行壓縮。
> 
> ![Content-Encoding 標頭](./media/cdn-troubleshoot-compression/cdn-content-header.png)
> 
> 

## <a name="cause"></a>原因
有幾個可能的原因，包括︰

* 要求的內容不適合進行壓縮。
* 要求的檔案類型未啟用壓縮。
* HTTP 要求未包含要求有效壓縮類型的標頭。
* 來源正在傳送區塊內容。

## <a name="troubleshooting-steps"></a>疑難排解步驟
> [!TIP]
> 隨著新端點的部署，CDN 組態變更會需要一些時間才能傳播至整個網路。  變更通常會在 90 分鐘內套用。  如果這是您第一次設定 CDN 端點壓縮，您應該考慮先等候 1-2 小時，確定壓縮設定已傳播至 POP。 
> 
> 

### <a name="verify-the-request"></a>驗證要求
首先，我們應該對要求進行快速的例行性檢查。  您可以使用瀏覽器的 [開發人員工具](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) 來檢視所做的要求。

* 驗證要求正傳送至您的端點 URL `<endpointname>.azureedge.net`，而不是您的來源。
* 驗證要求包含 **Accept-Encoding** 標頭，且該標頭的值包含 **gzip**、**deflate** 或 **bzip2**。

> [!NOTE]
> **來自 Akamai 的 Azure CDN** 設定檔只支援 **gzip** 編碼。
> 
> 

![CDN 要求標頭](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### <a name="verify-compression-settings-standard-cdn-profiles"></a>驗證壓縮設定 (標準 CDN 設定檔)
> [!NOTE]
> 如果您的 CDN 設定檔是**來自 Microsoft 的標準 Azure CDN**、**來自 Verizon 的標準 Azure CDN**或**來自 Akamai 的標準 Azure CDN** 設定檔，才能套用此步驟。 
> 
> 

在 [Azure 入口網站](https://portal.azure.com)中瀏覽至您的端點，然後按一下 [設定]**** 按鈕。

* 驗證已啟用壓縮。
* 驗證要壓縮之內容的 MIME 類型已包含在壓縮格式清單中。

![CDN 壓縮設定](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### <a name="verify-compression-settings-premium-cdn-profiles"></a>驗證壓縮設定 (進階 CDN 設定檔)
> [!NOTE]
> 如果您的 CDN 設定檔是 **來自 Verizon 的進階 Azure CDN** 設定檔，才能套用此步驟。
> 
> 

在 [Azure 入口網站](https://portal.azure.com)中瀏覽至您的端點，然後按一下 [管理]**** 按鈕。  即會開啟補充入口網站。  將滑鼠移至 [HTTP 大型]**** 索引標籤上，然後將滑鼠移至 [快取設定]**** 飛出視窗上。  按一下 [壓縮]****。 

* 驗證已啟用壓縮。
* 驗證 [檔案類型] **** 清單包含以逗號分隔 (無空格) 的 MIME 類型清單。
* 驗證要壓縮之內容的 MIME 類型已包含在壓縮格式清單中。

![CDN 進階壓縮設定](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)

### <a name="verify-the-content-is-cached-verizon-cdn-profiles"></a>驗證已快取內容 (Verizon CDN 設定檔)
> [!NOTE]
> 如果您的 CDN 設定檔是**來自 Verizon 的標準 Azure CDN** 或**來自 Verizon 的進階 Azure CDN** 設定檔，才能套用此步驟。
> 
> 

使用您瀏覽器的開發人員工具，檢查回應標頭以確保檔案會快取在要求它的區域中。

* 檢查 **Server** 回應標頭。  標頭應該具有格式 **平台 (POP/伺服器識別碼)**，如下列範例所示。
* 檢查 **X-Cache** 回應標頭。  標頭應為 **HIT**。  

![CDN 回應標頭](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### <a name="verify-the-file-meets-the-size-requirements-verizon-cdn-profiles"></a>驗證檔案符合大小需求 (Verizon CDN 設定檔)
> [!NOTE]
> 如果您的 CDN 設定檔是**來自 Verizon 的標準 Azure CDN** 或**來自 Verizon 的進階 Azure CDN** 設定檔，才能套用此步驟。
> 
> 

若要進行壓縮，檔案必須符合下列的大小需求︰

* 大於128位元組 (內容長度： 128) 
* 小於 3 MB

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>在原始伺服器中檢查要求的 **Via** 標頭
**Via** HTTP 標頭會向 Web 伺服器指出正在由 Proxy 伺服器傳遞要求。  Microsoft IIS Web 伺服器預設不會在要求包含 **Via** 標頭時壓縮回應。  若要覆寫這個行為，請執行下列作業︰

* **IIS 6**： [在 IIS Metabase 屬性中設定 HcNoCompressionForProxies="FALSE"](/previous-versions/iis/6.0-sdk/ms525390(v=vs.90))
* **IIS 7 和更新版本**：[在伺服器組態中將 **noCompressionForHttp10** 和 **noCompressionForProxies** 設定為 False](https://www.iis.net/configreference/system.webserver/httpcompression)

