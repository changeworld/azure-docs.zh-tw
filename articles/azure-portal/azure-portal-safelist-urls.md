---
title: 在防火牆或代理伺服器上安全列出 Azure 門戶網址
description: 將這些 URL 新增到代理伺服器旁路,以便與 Azure 門戶及其服務進行通訊
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 04/10/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 399c64c88e78079432fcf7c09dafd199da83358b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255042"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>在防火牆或代理伺服器上安全列出 Azure 門戶網址

可以配置本地安全設備以繞過 Azure 門戶 URL 的安全限制。 此配置可以提高本地或廣域網路與 Azure 雲端之間的性能和連接性。

網路管理員通常部署代理伺服器、防火牆或其他設備。 這些設備有助於保護並控制使用者存取網路的方式。 旨在保護使用者的規則有時會阻止或減緩合法的與業務相關的互聯網流量。 此流量包括您和 Azure 之間的通訊。 要優化網路與 Azure 門戶及其服務之間的連接,我們建議您將 Azure 門戶 URL 添加到安全清單中。

## <a name="azure-portal-urls-for-proxy-bypass"></a>代理繞過的 Azure 門戶網址

Azure 門戶的安全列表的 URL 終結點特定於部署組織的 Azure 雲。 要允許網路流量繞過限制,請選擇雲。 然後,將 URL 清單添加到代理伺服器或防火牆。

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
> 這些終結點的流量使用標準 TCP 埠進行 HTTP (80) 和 HTTPS (443)。
>
>
