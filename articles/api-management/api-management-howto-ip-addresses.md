---
title: Azure API 管理服務的 IP 位址 |微軟文檔
description: 瞭解如何檢索 Azure API 管理服務的 IP 位址以及更改時間。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/26/2019
ms.author: apimpm
ms.openlocfilehash: 45501fee9ae6ff47643a1ed197a07c4ba598e981
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80047736"
---
# <a name="ip-addresses-of-azure-api-management"></a>Azure API 管理的 IP 位址

在本文中，我們將介紹如何檢索 Azure API 管理服務的 IP 位址。 如果服務位於虛擬網路中，則 IP 位址可以是公共位址，也可以是私有的。

您可以使用 IP 位址創建防火牆規則、篩選後端服務的傳入流量或限制出站流量。

## <a name="ip-addresses-of-api-management-service"></a>API 管理服務的 IP 位址

開發人員、基本、標準或高級層中的每個 API 管理服務實例都有公共 IP 位址，這些 IP 位址僅特定于該服務實例（它們不與其他資源分享）。 

可以從 Azure 門戶中的資源的概述儀表板檢索 IP 位址。

![API 管理 IP 位址](media/api-management-howto-ip-addresses/public-ip.png)

您還可以使用以下 API 呼叫以程式設計方式獲取它們：

```
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>
```

公共 IP 位址將是回應的一部分：

```json
{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.77.143.53"
    ],
    ...
  }
  ...
}
```

在[多區域部署中](api-management-howto-deploy-multi-region.md)，每個區域部署都有一個公共 IP 位址。

## <a name="ip-addresses-of-api-management-service-in-vnet"></a>VNet 中 API 管理服務的 IP 位址

如果您的 API 管理服務位於虛擬網路中，它將有兩種類型的 IP 位址 - 公共位址和專用位址。

公共 IP 位址用於埠`3443`上的內部通信 - 用於管理配置（例如，通過 Azure 資源管理器）。 在外部 VNet 配置中，它們還用於運行時 API 流量。 當請求從 API 管理發送到面向公共（面向 Internet 的）後端時，公共 IP 位址將作為請求的來源可見。

專用虛擬 IP （VIP） 位址**僅在**內部[VNet 模式下](api-management-using-with-internal-vnet.md)可用，用於從網路內部連接到 API 管理終結點 - 閘道、開發人員門戶和管理平面，以便直接存取 API。 您可以使用它們在網路中設置 DNS 記錄。

您將在 Azure 門戶和 API 呼叫的回應中看到這兩種類型的位址：

![VNet IP 位址中的 API 管理](media/api-management-howto-ip-addresses/vnet-ip.png)


```json
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>

{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.85.20.170"
    ],
    "privateIPAddresses": [
      "192.168.1.5"
    ],
    ...
  },
  ...
}
```

API 管理使用公共 IP 位址進行 VNet 外部的連接，使用私人 IP 位址進行 VNet 中的連接。

## <a name="ip-addresses-of-consumption-tier-api-management-service"></a>消費層 API 管理服務的 IP 位址

如果您的 API 管理服務是消耗層服務，則它沒有私人 IP 位址。 消費層服務在共用基礎架構上運行，並且沒有確定性 IP 位址。 

出於流量限制目的，可以使用 Azure 資料中心的 IP 位址範圍。 有關確切步驟，請參閱[Azure 函數文檔一文](../azure-functions/ip-addresses.md#data-center-outbound-ip-addresses)。

## <a name="changes-to-the-ip-addresses"></a>對 IP 位址的更改

在 API 管理的開發人員、基本、標準和高級層中，公共 IP 位址 （VIP） 在服務的存留期內是靜態的，但以下例外情況除外：

* 服務遭到刪除，然後又重新建立。
* 服務訂用帳戶遭到[暫止](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states)或[警告](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) (例如，因為未付款)，然後又恢復。
* Azure 虛擬網路將添加到服務中或從服務中刪除。
* API 管理服務在外部和內部 VNet 部署模式之間切換。

在[多區域部署中](api-management-howto-deploy-multi-region.md)，如果區域已騰空並恢復，區域 IP 位址將更改。
