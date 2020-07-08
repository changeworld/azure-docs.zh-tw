---
title: 將 Azure 入口網站 URL 列入防火牆或 Proxy 伺服器的安全清單
description: 將這些 Url 新增至 proxy 伺服器略過，以與 Azure 入口網站及其服務進行通訊
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 04/10/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 67ccf6e2f113c0ec72cfcbccedf88017e452ec4b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85361654"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>將 Azure 入口網站 URL 列入防火牆或 Proxy 伺服器的安全清單

您可以設定內部部署安全性裝置，略過 Azure 入口網站 Url 的安全性限制。 此設定可改善您的本機或廣域網路與 Azure 雲端之間的效能和連線能力。

網路系統管理員通常會部署 proxy 伺服器、防火牆或其他裝置。 這些裝置有助於保護安全，並控制使用者存取網際網路的方式。 為了保護使用者而設計的規則，有時會封鎖或減緩合法的商務相關網際網路流量。 此流量包括您與 Azure 之間的通訊。 若要優化您的網路與 Azure 入口網站及其服務之間的連線，建議您將 Azure 入口網站的 Url 新增至您的安全。

## <a name="azure-portal-urls-for-proxy-bypass"></a>Azure 入口網站 proxy 略過的 Url

Azure 入口網站的安全的 URL 端點是您組織部署所在的 Azure 雲端特有的。 若要允許這些端點的網路流量略過限制，請選取您的雲端。 然後，將 Url 清單新增至您的 proxy 伺服器或防火牆。

#### <a name="public-cloud"></a>[公用雲端](#tab/public-cloud)

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

#### <a name="us-government-cloud"></a>[美國政府雲端](#tab/us-government-cloud)

```
*.applicationinsights.us
*.azure.us
*.loganalytics.us
*.microsoft.us
*.microsoftonline.us
*.msauth.net
*.usgovcloudapi.net
*.usgovtrafficmanager.net
*.windowsazure.us
```

#### <a name="china-government-cloud"></a>[中國政府雲端](#tab/china-government-cloud)

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
