---
title: 將防火牆或 proxy 伺服器上的 Azure 入口網站 Url
description: 將這些 Url 新增至 proxy 伺服器略過，以與 Azure 入口網站及其服務進行通訊
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: c5bba7296a05cfbb72698a991ece1ef298689bd1
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900651"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>將防火牆或 proxy 伺服器上的 Azure 入口網站 Url

您可以設定內部部署安全性裝置，略過 Azure 入口網站 Url 的安全性限制。 此設定可改善您的本機或廣域網路與 Azure 雲端之間的效能和連線能力。

網路系統管理員通常會部署 proxy 伺服器、防火牆或其他裝置。 這些裝置有助於保護安全，並控制使用者存取網際網路的方式。 為了保護使用者而設計的規則，有時會封鎖或減緩合法的商務相關網際網路流量。 此流量包括您與 Azure 之間的通訊。 若要優化您的網路與 Azure 入口網站及其服務之間的連線，建議您將 Azure 入口網站的 Url 新增至您的安全。

## <a name="azure-portal-urls-for-proxy-bypass"></a>Azure 入口網站 proxy 略過的 Url

Azure 入口網站的安全的 URL 端點是您組織部署所在的 Azure 雲端特有的。 若要允許這些端點的網路流量略過限制，請選取您的雲端。 然後，將 Url 清單新增至您的 proxy 伺服器或防火牆。

#### <a name="public-cloudtabpublic-cloud"></a>[公用雲端](#tab/public-cloud)

```
*.aadcdn.microsoftonline-p.com
*.aka.ms
*.applicationinsights.io
*.azure.com
*.azure.net
*.azureafd.net
*.azure-api.net
*.azuredatalakestore.net
*.azureedge.net
*.loganalytics.io
*.microsoft.com
*.microsoftonline.com
*.microsoftonline-p.com
*.msauth.net
*.msftauth.net
*.trafficmanager.net
*.visualstudio.com
*.windows.net
*.windows-int.net
```

#### <a name="us-government-cloudtabus-government-cloud"></a>[美國政府雲端](#tab/us-government-cloud)

```
*.azure.us
*.loganalytics.us
*.microsoft.us
*.microsoftonline.us
*.msauth.net
*.usgovcloudapi.net
*.usgovtrafficmanager.net
*.windowsazure.us
```

#### <a name="china-government-cloudtabchina-government-cloud"></a>[中國政府雲端](#tab/china-government-cloud)

```
*.azure.cn
*.microsoft.cn
*.microsoftonline.cn
*.chinacloudapi.cn
*.trafficmanager.cn
*.chinacloudsites.cn
*.windowsazure.cn
```
---

> [!NOTE]
> 這些端點的流量會使用適用于 HTTP （80）和 HTTPS （443）的標準 TCP 埠。
>
>
## <a name="next-steps"></a>後續步驟

需要將 IP 位址加入至安全的名稱嗎？ 下載您雲端的 Microsoft Azure datacenter IP 範圍清單：

* [國際](https://www.microsoft.com/download/details.aspx?id=56519)
* [美國政府](https://www.microsoft.com/download/details.aspx?id=57063)
* [德國](https://www.microsoft.com/download/details.aspx?id=57064)
* [中國](https://www.microsoft.com/download/details.aspx?id=57062)

其他 Microsoft 服務會使用額外的 Url 和 IP 位址來進行連線。 若要將 Microsoft 365 服務的網路連線優化，請參閱[設定您的網路以進行 Office 365](/office365/enterprise/set-up-network-for-office-365)。
