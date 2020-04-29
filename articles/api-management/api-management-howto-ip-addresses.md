---
title: Azure API 管理服務的 IP 位址 |Microsoft Docs
description: 瞭解如何在 Azure API 管理服務變更時，取得其 IP 位址。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80047736"
---
# <a name="ip-addresses-of-azure-api-management"></a>Azure API 管理的 IP 位址

在本文中，我們會說明如何取得 Azure API 管理服務的 IP 位址。 如果服務位於虛擬網路中，則 IP 位址可以是公用或私用。

您可以使用 IP 位址來建立防火牆規則、篩選後端服務的連入流量，或限制輸出流量。

## <a name="ip-addresses-of-api-management-service"></a>API 管理服務的 IP 位址

開發人員、基本、標準或進階層中的每個 API 管理服務實例都有公用 IP 位址，僅適用于該服務實例（它們不會與其他資源分享）。 

您可以在 Azure 入口網站中，從資源的 [總覽] 儀表板取得 IP 位址。

![API 管理 IP 位址](media/api-management-howto-ip-addresses/public-ip.png)

您也可以使用下列 API 呼叫以程式設計方式提取它們：

```
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>
```

公用 IP 位址將會是回應的一部分：

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

在[多重區域部署](api-management-howto-deploy-multi-region.md)中，每個區域部署都有一個公用 IP 位址。

## <a name="ip-addresses-of-api-management-service-in-vnet"></a>VNet 中 API 管理服務的 IP 位址

如果您的 API 管理服務位於虛擬網路中，則會有兩種類型的 IP 位址-「公用」和「私用」。

公用 IP 位址會用於埠`3443`上的內部通訊，以管理設定（例如，透過 Azure Resource Manager）。 在外部 VNet 設定中，它們也會用於執行時間 API 流量。 從 API 管理將要求傳送至公開（網際網路面向）後端時，公用 IP 位址會顯示為要求的來源。

私人虛擬 IP （VIP）位址（**僅**適用于[內部 VNet 模式](api-management-using-with-internal-vnet.md)）是用來從網路內部連線至 api 管理端點-閘道、開發人員入口網站，以及直接存取 api 的管理平面。 您可以使用它們來設定網路內的 DNS 記錄。

您會在 Azure 入口網站和 API 呼叫的回應中看到這兩種類型的位址：

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

API 管理會使用公用 IP 位址來進行 VNet 外部的連線，以及 VNet 內連線的私人 IP 位址。

## <a name="ip-addresses-of-consumption-tier-api-management-service"></a>耗用量層 API 管理服務的 IP 位址

如果您的 API 管理服務是耗用量層服務，則不會有專用的 IP 位址。 取用層服務會在共用基礎結構上執行，且不會有具決定性的 IP 位址。 

基於流量限制的目的，您可以使用 Azure 資料中心的 IP 位址範圍。 如需精確的步驟，請參閱[Azure Functions 檔文章](../azure-functions/ip-addresses.md#data-center-outbound-ip-addresses)。

## <a name="changes-to-the-ip-addresses"></a>IP 位址的變更

在 API 管理的開發人員、基本、標準和高階層中，公用 IP 位址（VIP）在服務的存留期間都是靜態的，但有下列例外狀況：

* 服務遭到刪除，然後又重新建立。
* 服務訂用帳戶遭到[暫止](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states)或[警告](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) (例如，因為未付款)，然後又恢復。
* Azure 虛擬網路會新增至服務或從中移除。
* API 管理服務會在外部和內部 VNet 部署模式之間切換。

在[多重區域部署](api-management-howto-deploy-multi-region.md)中，區域 IP 位址會在區域已清空後重新復原時變更。
