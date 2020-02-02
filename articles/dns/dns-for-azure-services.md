---
title: 搭配其他 Azure 服務使用 Azure DNS
description: 在此學習路徑中，您將開始瞭解如何使用 Azure DNS 來解析其他 Azure 服務的名稱
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
tags: azure dns
ms.assetid: e9b5eb94-7984-4640-9930-564bb9e82b78
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 09/21/2016
ms.author: rohink
ms.openlocfilehash: fa2c1ced6405c967ca33562d6215b304b8507e5a
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "76937244"
---
# <a name="how-azure-dns-works-with-other-azure-services"></a>Azure DNS 如何與其他 Azure 服務搭配運作

Azure DNS 是一種託管的 DNS 管理與名稱解析服務。 您可以使用它來為您在 Azure 中部署的其他應用程式和服務建立公用 DNS 名稱。 為您的自訂網域中的 Azure 服務建立名稱。 您只要為您的服務新增正確類型的記錄。

* 針對動態配置的 IP 位址，您可以建立 DNS CNAME 記錄，對應至 Azure 為您的服務建立的 DNS 名稱。 DNS 標準禁止您在區域頂點使用 CNAME 記錄。 您可以改為使用別名記錄。 如需詳細資訊，請參閱[教學課程：設定別名記錄來參考 Azure 公用 IP 位址](tutorial-alias-pip.md)。
* 若使用靜態配置的 IP 位址，您可以用任何名稱建立 DNS A 記錄，包括在區域頂點的「裸網域」名稱。

下表概述可用於各種 Azure 服務的支援記錄類型。 如表格所示，Azure DNS 只支援網際網路面向網路資源的 DNS 記錄。 Azure DNS 無法用於內部私人位址的名稱解析。

| Azure 服務 | Linux | 說明 |
| --- | --- | --- |
| Azure 應用程式閘道 |[前端公用 IP](dns-custom-domain.md#public-ip-address) |您可以建立 DNS A 或 CNAME 記錄。 |
| Azure Load Balancer |[前端公用 IP](dns-custom-domain.md#public-ip-address) |您可以建立 DNS A 或 CNAME 記錄。 負載平衡器可以有動態指派的 IPv6 公用 IP 位址。 建立 IPv6 位址的 CNAME 記錄。 |
| Azure 流量管理員 |公開名稱 |您可以建立對應至指派給您流量管理員設定檔之 trafficmanager.net 名稱的別名記錄。 如需詳細資訊，請參閱[教學課程：使用流量管理員來設定別名記錄以支援頂點網域名稱](tutorial-alias-tm.md)。 |
| Azure 雲端服務 |[公用 IP](dns-custom-domain.md#public-ip-address) |若使用靜態配置的 IP 位址，您可以建立 DNS A 記錄。 若使用動態配置的 IP 位址，您必須建立對應至 *cloudapp.net* 名稱的 CNAME 記錄。|
| Azure App Service | [外部 IP](dns-custom-domain.md#app-service-web-apps) |若使用外部 IP 位址，您可以建立 DNS A 記錄。 若不是，則必須建立對應至 azurewebsites.net 名稱的 CNAME 記錄。 如需詳細資訊，請參閱[將自訂網域名稱對應至 Azure 應用程式](../app-service/app-service-web-tutorial-custom-domain.md)。 |
| Azure Resource Manager VM |[公用 IP](dns-custom-domain.md#public-ip-address) |Resource Manager VM 可以有公用 IP 位址。 具有公用 IP 位址的 VM 也可能放在負載平衡器後面。 您可以為公用位址建立 DNS A、CNAME 或別名記錄。 您可以使用此自訂名稱來略過負載平衡器上的 VIP。 |
| 傳統 VM |[公用 IP](dns-custom-domain.md#public-ip-address) |使用 PowerShell 或 CLI 建立的傳統 VM 可設定為使用動態或靜態 (保留) 的虛擬位址。 您可以分別建立 DNS CNAME 或 A 記錄。 |
