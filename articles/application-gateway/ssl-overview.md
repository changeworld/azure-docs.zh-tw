---
title: 在 Azure 應用程式閘道上啟用端對端 TLS
description: 本文將簡介應用程式閘道端對端 TLS 支援。
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 5/13/2020
ms.author: victorh
ms.openlocfilehash: adaf3dea5855a4af75977cb820ae12675c7f2ced
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648128"
---
# <a name="overview-of-tls-termination-and-end-to-end-tls-with-application-gateway"></a>應用程式閘道的 TLS 終止和端對端 TLS 概觀

傳輸層安全性 (TLS) (先前稱為安全通訊端層 (SSL)) 是在網頁伺服器與瀏覽器之間建立加密連結的標準安全性技術。 此連結可確保在網頁伺服器和瀏覽器之間傳遞的所有資料都保有私密性和加密狀態。 應用程式閘道支援閘道上的 TLS 終止以及端對端 TLS 加密。

## <a name="tls-termination"></a>TLS 終止

應用程式閘道支援在閘道上終止 TLS，之後流量通常會以未加密狀態流至後端伺服器。 在應用程式閘道上進行 TLS 終止有幾個優點：

- **提升效能** – 進行 TLS 解密時最大的效能影響，在於初始交握。 為了改善效能，進行解密的伺服器會快取 TLS 工作階段識別碼，並管理 TLS 工作階段票證。 如果在應用程式閘道上執行此動作，來自相同用戶端的所有要求都可以使用快取值。 如果在後端伺服器上執行此動作，則每次用戶端的要求傳至不同的伺服器時，用戶端都必須重新驗證。 使用 TLS 票證有助於減輕此問題，但並非所有用戶端都支援此功能，且可能難以設定和管理。
- **改善後端伺服器使用率** – SSL/TLS 處理會耗用大量 CPU，且隨著金鑰大小的增加，耗用量會更大。 從後端伺服器中移除此工作，可使其專注於最有效率的工作，即傳遞內容。
- **智慧型路由** – 藉由解密流量，應用程式閘道得以存取要求內容 (例如標頭、URI 等)，並可使用這項資料來路由要求。
- **憑證管理** – 憑證在購買後只需安裝在應用程式閘道上，而不是所有後端伺服器。 這可以節省時間和金錢。

若要設定 TLS 終止，必須將 TLS/SSL 憑證新增至接聽程式，讓應用程式閘道能夠根據 TLS/SSL 通訊協定規格來衍生對稱金鑰。 接下來，對稱金鑰可以用來加密和解密傳送至閘道的流量。 TLS/SSL 憑證必須採用「個人資訊交換」(PFX) 格式。 此檔案格式可允許將私密金鑰匯出，而應用程式閘道需要這個匯出的金鑰來執行流量的加密和解密。

> [!IMPORTANT] 
> 請注意，接聽程式上的憑證需要上傳整個憑證鏈。 


> [!NOTE] 
>
> 應用程式閘道不會提供任何建立新憑證或將憑證要求傳送至憑證授權單位的功能。

若要讓 TLS 連線正常運作，您必須確定 TLS/SSL 憑證符合下列條件：

- 目前的日期和時間在憑證的「有效期限自」和「有效期限至」日期範圍內。
- 憑證的「一般名稱」(CN) 符合要求中的主機標頭。 例如，如果用戶端提出 `https://www.contoso.com/` 要求，則 CN 必須是 `www.contoso.com`。

### <a name="certificates-supported-for-tls-termination"></a>支援 TLS 終止的憑證

應用程式閘道支援下列類型的憑證：

- CA (憑證授權單位) 憑證：CA 憑證是憑證授權單位 (CA) 所核發的數位憑證
- EV (擴充驗證) 憑證：EV 憑證是符合業界標準憑證指導方針的憑證。 此憑證會使瀏覽器定位器列變成綠色，並同時發佈公司名稱。
- 萬用字元憑證：此憑證支援以 *.site.com 為基礎的子網域 (數量不限)，而您的子網域會取代其中的 *。 但並不支援 site.com，因此，如果使用者在存取您的網站時未輸入前置的 "www"，萬用字元憑證就不會將其涵蓋在內。
- 自我簽署憑證：用戶端瀏覽器不信任這些憑證，且會警告使用者，指出虛擬服務的憑證不是信任鏈結的一部分。 自我簽署憑證適用於由系統管理員控制用戶端，且可安全略過瀏覽器安全性警示的測試或環境。 生產工作負載不得使用自我簽署憑證。

如需詳細資訊，請參閱[設定應用程式閘道的 TLS 終止](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)。

### <a name="size-of-the-certificate"></a>憑證的大小
請查看[應用程式閘道限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#application-gateway-limits)一節，以了解支援的 TLS/SSL 憑證大小上限。

## <a name="end-to-end-tls-encryption"></a>端對端 TLS 加密

您可能不想要對後端伺服器進行未加密的通訊。 您可能有安全性需求、合規性需求，或應用程式可能只接受安全連線。 Azure 應用程式閘道具有端對端 TLS 加密，可支援這些需求。

當您使用應用程式閘道的第 7 層負載平衡功能時，端對端 TLS 可讓您加密敏感性資料，並安全地將其傳輸至後端。 這些功能包括 Cookie 型工作階段親和性、URL 型路由、支援以網站為基礎的路由，以及重寫或插入 X-Forwarded-* 標頭的能力。

當設定為端對端 TLS 通訊模式時，應用程式閘道會在閘道上終止 TLS 工作階段，並解密使用者流量。 然後，它會套用所設定的規則來選取要將流量路由傳送到的適當後端集區執行個體。 應用程式閘道接著再起始連往後端伺服器的新 TLS 連線，並先使用後端伺服器的公開金鑰憑證重新加密資料，再將要求傳輸至後端。 任何來自 Web 伺服器的回應都會經歷相同的程序而回到使用者端。 若要啟用端對端 TLS，請將 [BackendHTTPSetting](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) 中的通訊協定設為 HTTPS，接著再套用到後端集區。

對於應用程式閘道和 WAF v1 SKU，TLS 原則會同時套用至前端和後端流量。 在前端，應用程式閘道會作為伺服器，並強制執行原則。 在後端，應用程式閘道會作為用戶端，並在 TLS 交握期間依照喜好設定傳送通訊協定/密碼資訊。

對於應用程式閘道和 WAF v2 SKU，TLS 原則只會套用至前端流量，並在後端伺服器提供所有加密，如此，在交握期間就有權選取特定的加密和 TLS 版本。

應用程式閘道只會與符合下列條件的後端伺服器進行通訊：已將伺服器用於應用程式閘道的憑證列入白名單中，或已由知名的 CA 授權單位簽署其憑證，且憑證的 CN 符合 HTTP 後端設定中的主機名稱。 其中包括受信任的 Azure 服務，例如 Azure App Service/Web Apps 和 Azure API 管理。

如果後端集區中的成員未由知名的 CA 授權單位簽署其憑證，則後端集區中每個已啟用端對端 TLS 的執行個體都必須進行憑證設定，以支援安全通訊。 新增憑證可確保應用程式閘道只會與已知的後端執行個體通訊。 這會進而保護端對端通訊。

> [!NOTE] 
>
> 新增至**後端 HTTP 設定**以驗證後端伺服器的憑證，可以與在應用程式閘道上針對 TLS 終止新增至**接聽程式**的憑證相同，或為了增強安全性而不同。

![端對端 TLS 案例][1]

在此範例中，會使用端對端 TLS，將使用 TLS1.2 的要求路由傳送至 Pool1 中的後端伺服器。

## <a name="end-to-end-tls-and-whitelisting-of-certificates"></a>端對端 TLS 和憑證白名單

應用程式閘道只會與已知的後端執行個體通訊，後者已將其憑證列入應用程式閘道的白名單。 端對端 TLS 設定程序在使用的應用程式閘道版本方面有一些差異。 下一節會個別加以說明。

## <a name="end-to-end-tls-with-the-v1-sku"></a>v1 SKU 的端對端 TLS

若要在後端伺服器上啟用端對端 TLS，並讓應用程式閘道將要求路由至該處，健康情況探查必須成功，並傳回狀況良好的回應。

針對 HTTPS 健康情況探查，應用程式閘道 v1 SKU 會使用要上傳至 HTTP 設定且完全相符的驗證憑證 (後端伺服器憑證的公開金鑰，而不是根憑證)。

於是，只允許連接至已知和允許清單中的後端。 健康情況探查會將其餘後端視為狀況不良。 自我簽署憑證僅供測試之用，並不建議用於生產工作負載。 這類憑證必須如先前步驟所述，加入應用程式閘道的允許清單之中，才能使用。

> [!NOTE]
> 受信任的 Azure 服務 (例如 Azure App Service) 不需進行驗證和信任根憑證設定。 依預設會將其視為受信任。

## <a name="end-to-end-tls-with-the-v2-sku"></a>v2 SKU 的端對端 TLS

在「應用程式閘道 v2 SKU」中，「驗證憑證」已被淘汰且被「受信任的根憑證」取代。 它們的功能與「驗證憑證」類似，但有幾個主要差異：

- 憑證如果是由 CN 與 HTTP 後端設定中主機名稱相符的已知 CA 授權單位所簽署，則無須執行任何其他步驟，端對端 TLS 就能運作。 

   例如，如果後端憑證是由已知 CA 所簽發且 CN 為 contoso.com，而後端 HTTP 設定的主機欄位也設定為 contoso.com，便無須執行任何其他步驟。 您可以將後端 HTTP 設定通訊協定設定為 HTTPS，健康狀態探查和資料路徑就會都啟用 TLS。 如果您使用 Azure App Service 或其他 Azure Web 服務作為後端，則這些項目也會隱含地受到信任，而無須執行任何進一步的步驟，即可進行端對端 TLS。
   
> [!NOTE] 
>
> 若要讓 TLS/SSL 憑證受到信任，後端伺服器的該憑證必須由知名 CA 所核發。 如果憑證不是由受信任的 CA 所核發，應用程式閘道就會檢查核發 CA 的憑證是否由受信任的 CA 核發，直到找到信任的 CA 為止 (此時將會建立受信任的安全連線)，或找不到信任的 CA (此時，應用程式閘道會將後端標示為狀況不良)。 因此，建議後端伺服器憑證應同時包含根和中繼 CA。

- 如果憑證是自我簽署的，或由未知的媒介所簽署的，則若要在 v2 SKU 中啟用端對端 TLS，就必須定義受信任的根憑證。 「應用程式閘道」只會與符合下列條件的後端進行通訊：其伺服器憑證的根憑證符合與集區相關之後端 HTTP 設定中受信任根憑證清單內的其中一個憑證。

- 除了根憑證相符之外，應用程式閘道 v2 也會驗證後端 HTTP 設定中所指定的「主機」設定是否符合後端伺服器 TLS/SSL 憑證所出示的通用名稱 (CN)。 嘗試與後端建立 TLS 連線時，應用程式閘道 v2 會將「伺服器名稱指示」(SNI) 延伸模組設定為後端 HTTP 設定中所指定的「主機」。

- 如果選擇**從後端位址挑選主機名稱**，而不是從後端 HTTP 設定中的 [主機] 欄位挑選，則 SNI 標頭會一律設定為後端集區 FQDN，而後端伺服器 TLS/SSL 憑證上的 CN 必須符合其 FQDN。 此案例不支援具有 IP 的後端集區成員。

- 根憑證是一個來自後端伺服器憑證的 Base64 編碼根憑證。

## <a name="sni-differences-in-the-v1-and-v2-sku"></a>v1 與 v2 SKU 中的 SNI 差異

如前所述，應用程式閘道會在應用程式閘道接聽程式上終止用戶端的 TLS 流量 (我們將其稱為前端連線)、解密流量、套用必要的規則以決定要求要轉送到的後端伺服器，並與後端伺服器建立新的 TLS 工作階段 (我們將其稱為後端連線)。

下表概述 v1 與 v2 SKU 在前端和後端連線方面的 SNI 差異。

### <a name="frontend-tls-connection-client-to-application-gateway"></a>前端 TLS 連線 (用戶端對應用程式閘道)

---
狀況 | v1 | v2 |
| --- | --- | --- |
| 如果用戶端指定了 SNI 標頭，且所有多網站接聽程式都已啟用「需要 SNI」旗標 | 傳回適當的憑證，且若網站不存在 (根據 server_name 來判斷)，則會重設連線。 | 傳回適當的憑證 (如果有的話)，否則傳回設定的第一個 HTTPS 接聽程式的憑證 (依順序)|
| 如果用戶端未指定 SNI 標頭，且所有多網站標頭都已啟用「需要 SNI」 | 重設連線 | 傳回設定的第一個 HTTPS 接聽程式的憑證 (依順序)
| 如果用戶端未指定 SNI 標頭，且有基本接聽程式設定了憑證 | 將基本接聽程式中設定的憑證傳回至用戶端 (預設或後援憑證) | 傳回設定的第一個 HTTPS 接聽程式的憑證 (依順序) |

### <a name="backend-tls-connection-application-gateway-to-the-backend-server"></a>後端 TLS 連線 (應用程式閘道對後端伺服器)

#### <a name="for-probe-traffic"></a>探查流量

---
狀況 | v1 | v2 |
| --- | --- | --- |
| TLS 交握期間的 SNI (server_name) 標頭 (FQDN) | 設定為後端集區中的 FQDN。 依據 [RFC 6066](https://tools.ietf.org/html/rfc6066)，SNI 主機名稱中不允許常值 IPv4 和 IPv6 位址。 <br> **注意：** 後端集區中的 FQDN 應透過 DNS 解析為後端伺服器的 IP 位址 (公用或私人) | SNI 標頭 (server_name) 會設定為從連結至 HTTP 設定 (如已設定) 的自訂探查中的主機名稱，否則，依序會設為 HTTP 設定中指定的主機名稱，或後端集區中指定的 FQDN。 優先順序是自訂探查 > HTTP 設定 > 後端集區。 <br> **注意：** 如果 HTTP 設定和自訂探查中設定的主機名稱不同，則根據優先順序，SNI 將會設定為自訂探查中的主機名稱。
| 如果後端集區位址為 IP 位址 (v1)，或自訂探查主機名稱設定為 IP 位址 (v2) | 將不會設定 SNI (server_name)。 <br> **注意：** 在此情況下，後端伺服器應該能夠傳回預設/後援憑證，而此憑證應在 [驗證憑證] 下的 HTTP 設定中列入白名單。 如果後端伺服器中未設定任何預設/後援憑證，而且需要 SNI，則伺服器可能會重設連線，並將導致探查失敗 | 依照先前所述的優先順序，如果以 IP 位址作為主機名稱，則不會根據 [RFC 6066](https://tools.ietf.org/html/rfc6066) 設定 SNI。 <br> **注意：** 如果未設定任何自訂探查，且未在 HTTP 設定或後端集區上設定主機名稱，則在 v2 探查中也不會設定 SNI |

> [!NOTE] 
> 如果未設定自訂探查，則應用程式閘道會以下列格式傳送預設探查 - \<protocol\>://127.0.0.1:\<port\>/。 以預設 HTTPS 探查為例，會以 https://127.0.0.1:443/ 的格式傳送。 請注意，此處所述的 127.0.0.1 僅作為 HTTP 主機標頭使用，根據 RFC 6066，將不會作為 SNI 標頭使用。 如需健康情況探查錯誤的詳細資訊，請參閱[後端健康情況疑難排解指南](application-gateway-backend-health-troubleshooting.md)。

#### <a name="for-live-traffic"></a>即時流量

---
狀況 | v1 | v2 |
| --- | --- | --- |
| TLS 交握期間的 SNI (server_name) 標頭 (FQDN) | 設定為後端集區中的 FQDN。 依據 [RFC 6066](https://tools.ietf.org/html/rfc6066)，SNI 主機名稱中不允許常值 IPv4 和 IPv6 位址。 <br> **注意：** 後端集區中的 FQDN 應透過 DNS 解析為後端伺服器的 IP 位址 (公用或私人) | SNI 標頭 (server_name) 會設定為 HTTP 設定中的主機名稱，否則，若選擇了 *PickHostnameFromBackendAddress* 選項，或未指定主機名稱，則會將其設定為後端集區設定中的 FQDN
| 如果後端集區位址是 IP 位址，或未在 HTTP 設定中設定主機名稱 | 如果後端集區項目不是 FQDN，則不會根據 [RFC 6066](https://tools.ietf.org/html/rfc6066) 設定 SNI | SNI 將會設定為用戶端輸入 FQDN 中的主機名稱，而後端憑證的 CN 必須符合此主機名稱。

## <a name="next-steps"></a>後續步驟

了解端對端 TLS 之後，請移至[使用 PowerShell 以應用程式閘道設定端對端 TLS](application-gateway-end-to-end-ssl-powershell.md)，以使用端對端 TLS 來建立應用程式閘道。

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
