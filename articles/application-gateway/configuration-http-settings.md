---
title: Azure 應用程式閘道 HTTP 設定
description: 本文說明如何設定 Azure 應用程式閘道 HTTP 設定。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: c0c939a6a8323dfdfafddb46ccdb7d7ef3dd2f2c
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89652812"
---
# <a name="application-gateway-http-settings-configuration"></a>應用程式閘道 HTTP 設定

應用程式閘道會使用您在此處指定的設定，將流量路由傳送至後端伺服器。 建立 HTTP 設定之後，您必須將它與一或多個要求路由規則相關聯。

## <a name="cookie-based-affinity"></a>以 Cookie 為基礎的同質性

Azure 應用程式閘道會使用閘道管理的 cookie 來維護使用者會話。 當使用者將第一個要求傳送至應用程式閘道時，它會在回應中使用包含會話詳細資料的雜湊值來設定親和性 cookie，如此一來，包含相似性 cookie 的後續要求就會路由傳送至相同的後端伺服器，以維持一致。 

當您想要在相同的伺服器上保留使用者會話，以及將會話狀態儲存在伺服器本機上的使用者會話時，這項功能就很有用。 如果應用程式無法處理以 cookie 為基礎的親和性，您就無法使用這項功能。 若要使用它，請確定用戶端支援 cookie。

[Chromium 瀏覽器](https://www.chromium.org/Home) [v80 更新](https://chromiumdash.appspot.com/schedule)會帶出一個規定，其中不含[SameSite](https://tools.ietf.org/id/draft-ietf-httpbis-rfc6265bis-03.html#rfc.section.5.3.7)屬性的 HTTP Cookie 必須視為 SameSite = 不嚴格的。 在 CORS (跨原始資源分享) 要求的情況下，如果 cookie 必須在協力廠商內容中傳送，則必須使用 *SameSite = None;安全* 屬性，只應透過 HTTPS 傳送。 否則，在僅限 HTTP 案例中，瀏覽器不會在協力廠商內容中傳送 cookie。 這項來自 Chrome 的更新目標是增強安全性，並避免 (CSRF) 攻擊的跨網站偽造要求。 

為了支援這項變更，自 17 2020 年2月起，應用程式閘道 (所有的 SKU 類型) 除了現有的*ApplicationGatewayAffinity* cookie 之外，還會插入另一個稱為*ApplicationGatewayAffinityCORS*的 cookie。 *ApplicationGatewayAffinityCORS* cookie 已新增兩個屬性 (*"SameSite = None;「安全*」) 讓即使是跨原始來源的要求，仍會維持「粘滯」會話。

請注意，預設的親和性 cookie 名稱是 *ApplicationGatewayAffinity* ，您可以加以變更。 如果您使用自訂的親和性 cookie 名稱，則會以 CORS 作為尾碼來新增額外的 cookie。 例如， *CustomCookieNameCORS*。

> [!NOTE]
> 如果設定屬性 *SameSite = None* ，則必須同時包含 *安全* 旗標，且必須透過 HTTPS 傳送。  如果需要透過 CORS 的會話親和性，您必須將工作負載遷移至 HTTPS。 請參閱這裡的應用程式閘道的 TLS 卸載和端對端 TLS 檔- [總覽](ssl-overview.md)、 [使用 Azure 入口網站設定 tls 終止的應用程式閘道](create-ssl-portal.md)、使用 [應用程式閘道搭配入口網站設定端對端 tls](end-to-end-ssl-portal.md)。

## <a name="connection-draining"></a>清空連線

連接清空可協助您在規劃的服務更新期間，正常地移除後端集區成員。 您可以藉由啟用 HTTP 設定的連線清空，將此設定套用至後端集區的所有成員。 它可確保後端集區的所有取消登錄實例都繼續維持現有的連線，並提供可設定的超時時間的進行中要求，且不會收到任何新的要求或連接。 唯一的例外是由於閘道管理的會話親和性，會將要求系結至取消註冊的實例，而且會繼續轉送至取消註冊的實例。 連接清空適用于從後端集區中明確移除的後端實例。

## <a name="protocol"></a>通訊協定

應用程式閘道支援 HTTP 和 HTTPS 來將要求路由傳送至後端伺服器。 如果您選擇 HTTP，則連至後端伺服器的流量都會加密。 如果無法接受未加密的通訊，請選擇 [HTTPS]。

這項設定與接聽程式中的 HTTPS 結合， [可支援端對端 TLS](ssl-overview.md)。 這可讓您安全地傳輸加密至後端的機密資料。 在已啟用端對端 TLS 的後端集區中，每個後端伺服器都必須使用憑證來設定，以允許安全通訊。

## <a name="port"></a>Port

此設定會指定後端伺服器接聽來自應用程式閘道之流量的埠。 您可以設定介於1到65535之間的埠。

## <a name="request-timeout"></a>要求逾時

此設定是應用程式閘道等待接收來自後端伺服器回應的秒數。

## <a name="override-back-end-path"></a>覆寫後端路徑

這項設定可讓您設定選擇性的自訂轉送路徑，以在將要求轉送至後端時使用。 傳入路徑中符合 [覆 **寫後端路徑** ] 欄位中之自訂路徑的任何部分，都會複製到轉送的路徑。 下表顯示這項功能的運作方式：

- 當 HTTP 設定附加至基本要求路由規則時：

  | 原始要求  | 覆寫後端路徑 | 要求轉寄至後端 |
  | ----------------- | --------------------- | ---------------------------- |
  | /home/            | 覆寫            | /override/home/              |
  | /home/secondhome/ | 覆寫            | /override/home/secondhome/   |

- 當 HTTP 設定附加至以路徑為基礎的要求路由規則時：

  | 原始要求           | 路徑規則       | 覆寫後端路徑 | 要求轉寄至後端 |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | /pathrule*      | 覆寫            | /override/home/              |
  | /pathrule/home/secondhome/ | /pathrule*      | 覆寫            | /override/home/secondhome/   |
  | /home/                     | /pathrule*      | 覆寫            | /override/home/              |
  | /home/secondhome/          | /pathrule*      | 覆寫            | /override/home/secondhome/   |
  | /pathrule/home/            | /pathrule/home* | 覆寫            | 覆寫                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | 覆寫            | /override/secondhome/        |
  | /pathrule/                 | /pathrule/      | 覆寫            | 覆寫                   |

## <a name="use-for-app-service"></a>使用於 app service

這是僅限 UI 的快捷方式，可選取 Azure App Service 後端的兩個必要設定。 它可讓您 **從後端位址挑選主機名稱**，如果您還沒有自訂探查，則會建立新的探查。  (需詳細資訊，請參閱本文的 [從後端位址設定挑選主機名稱](#pick-host-name-from-back-end-address)一節。 ) 新的探查會建立，並從後端成員的位址挑選探查標頭。

## <a name="use-custom-probe"></a>使用自訂探查

此設定會將 [自訂探查](application-gateway-probe-overview.md#custom-health-probe) 與 HTTP 設定產生關聯。 您只能將一個自訂探查與 HTTP 設定建立關聯。 如果您未明確建立自訂探查的關聯，則會使用 [預設探查](application-gateway-probe-overview.md#default-health-probe-settings) 來監視後端的健康情況。 建議您建立自訂探查，以進一步控制您後端的健全狀況監視。

> [!NOTE]
> 除非對應的 HTTP 設定與接聽程式明確相關聯，否則自訂探查不會監視後端集區的健康情況。

## <a name="pick-host-name-from-back-end-address"></a>從後端位址挑選主機名稱

這項功能會將要求中的 *主機* 標頭動態設定為後端集區的主機名稱。 它會使用 IP 位址或 FQDN。

當後端的功能變數名稱不同于應用程式閘道的 DNS 名稱時，後端會依賴特定主機標頭來解析為正確的端點，這項功能會有所説明。

範例案例為多租使用者服務作為後端。 App service 是一種多租使用者服務，會使用具有單一 IP 位址的共用空間。 因此，只能透過在自訂網域設定中設定的主機名稱來存取 app service。

根據預設，自訂功能變數名稱為 *example.azurewebsites.net*。 若要使用應用程式閘道透過未在 app service 中明確註冊的主機名稱，或透過應用程式閘道的 FQDN 來存取 app service，請將原始要求中的主機名稱覆寫至 app service 的主機名稱。 若要這樣做，請啟用 [ **從後端位址設定挑選主機名稱** ] 設定。

針對現有的自訂 DNS 名稱對應至 app service 的自訂網域，您不需要啟用此設定。

> [!NOTE]
> App Service 環境（這是專用部署）不需要此設定。

## <a name="host-name-override"></a>主機名稱覆寫

這項功能會以您指定的主機名稱，取代應用程式閘道上連入要求中的 *主機* 標頭。

例如，如果在 [**主機名稱**] 設定中指定*www.contoso.com* ，則將 `https://appgw.eastus.cloudapp.azure.com/path1` `https://www.contoso.com/path1` 要求轉送至後端伺服器時，原始的要求 * 會變更為 *。

## <a name="next-steps"></a>接下來的步驟

- [深入瞭解後端集區](configuration-overview.md#back-end-pool)