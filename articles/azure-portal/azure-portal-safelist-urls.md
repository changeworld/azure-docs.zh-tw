---
title: 在防火牆或代理伺服器上安全列出 Azure 門戶 URL
description: 將這些 URL 添加到代理伺服器旁路，以便與 Azure 門戶及其服務進行通信
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: c5bba7296a05cfbb72698a991ece1ef298689bd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76900651"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>在防火牆或代理伺服器上安全列出 Azure 門戶 URL

可以配置本地安全設備以繞過 Azure 門戶 URL 的安全限制。 此配置可以提高本地或廣域網路絡與 Azure 雲之間的性能和連線性。

網路系統管理員通常部署代理伺服器、防火牆或其他設備。 這些設備有助於保護並控制使用者訪問互聯網的方式。 旨在保護使用者的規則有時會阻止或減緩合法的與業務相關的互聯網流量。 此流量包括您和 Azure 之間的通信。 要優化網路與 Azure 門戶及其服務之間的連接，我們建議您將 Azure 門戶 URL 添加到安全清單中。

## <a name="azure-portal-urls-for-proxy-bypass"></a>用於代理繞過的 Azure 門戶 URL

Azure 門戶的安全清單的 URL 終結點特定于部署組織的 Azure 雲。 要允許網路流量繞過限制，請選擇雲。 然後，將 URL 清單添加到代理伺服器或防火牆。

#### <a name="public-cloud"></a>[公共雲](#tab/public-cloud)

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

#### <a name="us-government-cloud"></a>[美國政府雲](#tab/us-government-cloud)

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

#### <a name="china-government-cloud"></a>[中國政府雲](#tab/china-government-cloud)

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
> 這些終結點的流量使用標準 TCP 埠進行 HTTP （80） 和 HTTPS （443）。
>
>
## <a name="next-steps"></a>後續步驟

需要安全列出 IP 位址？ 下載雲的 Microsoft Azure 資料中心 IP 範圍清單：

* [全球](https://www.microsoft.com/download/details.aspx?id=56519)
* [美國政府](https://www.microsoft.com/download/details.aspx?id=57063)
* [德國](https://www.microsoft.com/download/details.aspx?id=57064)
* [中國](https://www.microsoft.com/download/details.aspx?id=57062)

其他 Microsoft 服務使用其他 URL 和 IP 位址進行連接。 要優化 Microsoft 365 服務的網路連接，請參閱[為 Office 365 設置網路](/office365/enterprise/set-up-network-for-office-365)。
