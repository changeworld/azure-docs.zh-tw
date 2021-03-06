---
title: Azure Front Door HTTP2 支援 |Microsoft Docs
description: 本文可協助您瞭解 Azure Front Door 中的 HTTP/2 支援
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 20d45f5966aca3df89e17e03aa6120a4ddf5c5b9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448702"
---
# <a name="http2-support-in-azure-front-door"></a>Azure Front Door 中的 HTTP/2 支援

目前，所有 Azure Front Door 設定的 HTTP/2 支援皆為使用中狀態。 客戶不需要採取任何動作。

HTTP/2 是 HTTP/1.1 的主要修訂，可縮短回應時間以提供更快的 web 效能。 HTTP/2 是藉由維護熟悉的 HTTP 方法、狀態碼和語義來完成，以改善使用者體驗。 雖然 HTTP/2 是設計用來搭配 HTTP 和 HTTPS，但許多用戶端網頁瀏覽器僅支援透過傳輸層安全性 (TLS) 的 HTTP/2。

> [!NOTE]
> HTTP/2 通訊協定支援僅適用于從用戶端到 Front Door 的要求。 在後端集區中，從 Front Door 到後端的通訊會透過 HTTP/1.1 進行。 

### <a name="http2-benefits"></a>HTTP/2 的優點

HTTP/2 的優點包括︰

*   **多工和並行**

    使用 HTTP 1.1 時，提出多個資源要求需要多個 TCP 連線，每個連線都有其相關聯的效能負荷。 HTTP/2 允許在單一 TCP 連線上要求多個資源。

*   **標頭壓縮**

    將提供的資源壓縮 HTTP 標頭，可大幅縮短網路上的時間。

*   **資料流相依性**

    資料流相依性可讓用戶端向伺服器表示哪個資源最優先。


## <a name="http2-browser-support"></a>HTTP/2 瀏覽器支援

所有主要瀏覽器都已在其目前的版本中實作 HTTP/2 支援。 不支援的瀏覽器會自動切換回 HTTP/1.1。

|瀏覽器|最低版本|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="next-steps"></a>後續步驟

若要深入了解 HTTP/2，請瀏覽下列資源：

- [HTTP/2 規格首頁](https://http2.github.io/)
- [官方 HTTP/2 常見問題集](https://http2.github.io/faq/)
- 了解如何[建立 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的運作方式](front-door-routing-architecture.md)。
