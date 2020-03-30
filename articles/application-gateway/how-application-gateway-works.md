---
title: 應用程式閘道的工作原理
description: 本文提供有關應用程式閘道如何接受傳入請求並將其路由到後端的資訊。
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: absha
ms.openlocfilehash: 84a7bdfb9f8f7c741140cbe2086149dff90db211
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132975"
---
# <a name="how-an-application-gateway-works"></a>應用程式閘道的工作原理

本文介紹應用程式閘道如何接受傳入請求並將其路由到後端。

![應用程式閘道如何接受請求](./media/how-application-gateway-works/how-application-gateway-works.png)

## <a name="how-an-application-gateway-accepts-a-request"></a>應用程式閘道如何接受請求

1. 在用戶端向應用程式閘道發送請求之前，它使用網域名稱系統 （DNS） 伺服器解析應用程式閘道的功能變數名稱。 Azure 控制 DNS 條目，因為所有應用程式閘道都在azure.com域中。

2. Azure DNS 將 IP 位址返回到用戶端，用戶端是應用程式閘道的前端 IP 位址。

3. 應用程式閘道接受一個或多個攔截器上的傳入流量。 攔截器是檢查連接請求的邏輯實體。 它配置了前端 IP 位址、協定和埠號，用於從用戶端到應用程式閘道的連接。

4. 如果正在使用 Web 應用程式防火牆 （WAF），則應用程式閘道會根據 WAF 規則檢查請求標頭和正文（如果存在）。 此操作確定請求是有效請求還是安全威脅。 如果請求有效，則將其路由到後端。 如果請求無效，並且 WAF 處於"預防"模式，則會將其作為安全威脅阻止。 如果處於檢測模式，則評估並記錄請求，但仍轉發到後端伺服器。

Azure 應用程式閘道可用作內部應用程式負載等化器或面向 Internet 的應用程式負載等化器。 面向 Internet 的應用程式閘道使用公共 IP 位址。 面向 Internet 的應用程式閘道的 DNS 名稱可公開解析為其公共 IP 位址。 因此，面向 Internet 的應用程式閘道可以將用戶端請求路由到互聯網。

內部應用程式閘道僅使用私人 IP 位址。 如果使用自訂或專用 DNS[區域](https://docs.microsoft.com/azure/dns/private-dns-overview)，則功能變數名稱應在內部解析為應用程式閘道的私人 IP 位址。 因此，內部負載等化器只能路由具有應用程式閘道虛擬網路存取權限的用戶端的請求。

## <a name="how-an-application-gateway-routes-a-request"></a>應用程式閘道如何路由請求

如果請求有效且未被 WAF 阻止，則應用程式閘道將評估與攔截器關聯的請求路由規則。 此操作確定要將請求路由到哪個後端池。

根據請求路由規則，應用程式閘道確定是將攔截器上的所有請求路由到特定後端池，將請求路由到基於 URL 路徑的不同後端池，還是將請求重定向到另一個埠或外部網站。
>[!NOTE]
>規則按在 v1 SKU 門戶中列出的順序進行處理。 

When the application gateway selects the backend pool, it sends the request to one of the healthy backend servers in the pool (y.y.y.y). 伺服器的運行狀況由運行狀況探測器確定。 如果後端池包含多個伺服器，則應用程式閘道使用迴圈演算法在正常運行的伺服器之間路由請求。 此負載平衡伺服器上的請求。

在應用程式閘道確定後端伺服器後，它將基於 HTTP 設置打開與後端伺服器的新 TCP 會話。 HTTP 設置指定與後端伺服器建立新會話所需的協定、埠和其他與路由相關的設置。

HTTP 設置中使用的埠和協定確定應用程式閘道和後端伺服器之間的流量是加密（從而完成端到端 TLS）還是未加密。

當應用程式閘道將原始要求傳送到後端伺服器時，它將遵守 HTTP 設置中與重寫主機名稱、路徑和協定相關的任何自訂配置。 此操作維護基於 Cookie 的會話相關性、連接耗盡、後端的主機名稱選擇等。

 >[!NOTE]
>如果後端池：
> - **是公共終結點**，應用程式閘道使用其前端公共 IP 到達伺服器。 如果沒有前端公共 IP 位址，則為出站外部連接分配一個。
> - **包含內部可解析的 FQDN 或私人 IP 位址**，應用程式閘道使用其實例私人 IP 位址將請求路由到後端伺服器。
> - **包含外部終結點或外部可解析的 FQDN，** 應用程式閘道使用其前端公共 IP 位址將請求路由到後端伺服器。 DNS 解析基於專用 DNS 區域或自訂 DNS 伺服器（如果已配置）或使用預設的 Azure 提供的 DNS。 如果沒有前端公共 IP 位址，則為出站外部連接分配一個。

### <a name="modifications-to-the-request"></a>對請求的修改

應用程式閘道在將請求轉發到後端之前，向所有請求插入四個附加標頭。 這些標頭是 x 轉發的、x 轉發的原型、x 轉發埠和 x 原始主機。 x 轉發標頭的格式是 IP：port 的逗號分隔清單。

x 轉發原型的有效值是 HTTP 或 HTTPS。 X 轉發埠指定請求到達應用程式閘道的埠。 X 原始主機標頭包含請求到達的原始主機標頭。 此標頭在 Azure 網站集成中很有用，在將流量路由到後端之前，將修改傳入的主機標頭。 如果會話關聯作為選項啟用，則添加閘道管理的關聯 Cookie。

您可以使用[重寫 HTTP 標頭](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)配置應用程式閘道以修改標頭，或者使用路徑覆蓋設置修改 URI 路徑。 但是，除非配置為這樣做，否則所有傳入請求都近到後端。

## <a name="next-steps"></a>後續步驟

[瞭解應用程式閘道元件](application-gateway-components.md)
