---
title: 常見問題-Azure Load Balancer
description: 有關 Azure Load Balancer 常見問題的解答。
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: article
ms.date: 04/22/2020
ms.author: errobin
ms.openlocfilehash: 3be8ce241817b3b2fa03976eebe3147c1dc9c877
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2020
ms.locfileid: "83005145"
---
# <a name="frequently-asked-questions"></a>常見問題集

## <a name="what-types-of-load-balancer-exist"></a>有哪些類型的 Load Balancer？
內部負載平衡器，可平衡 VNET 和外部負載平衡器之間的流量，以平衡進出網際網路連線端點的流量。 如需詳細資訊，請參閱[Load Balancer 類型](components.md#frontend-ip-configurations)。 

對於這兩種類型，Azure 提供基本 SKU 和標準 SKU，其具有不同的功能、效能、安全性和健康情況追蹤功能。 我們的[SKU 比較](skus.md)一文會說明這些差異。

 ## <a name="how-can-i-upgrade-from-a-basic-to-a-standard-load-balancer"></a>如何從基本升級至 Standard Load Balancer？
如需自動化腳本和升級 Load Balancer SKU 的指引，請參閱[從基本升級至標準](upgrade-basic-standard.md)文章。

 ## <a name="what-are-the-different-load-balancing-options-in-azure"></a>Azure 中有哪些不同的負載平衡選項？
請參閱[負載平衡器技術指南](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview)以取得可用的負載平衡服務，以及每個服務的建議用法。

## <a name="where-can-i-find-load-balancer-arm-templates"></a>哪裡可以找到 Load Balancer ARM 範本？
請參閱適用于一般部署的 ARM 範本的[Azure Load Balancer 快速入門範本清單](https://docs.microsoft.com/azure/templates/microsoft.network/loadbalancers#quickstart-templates)。

## <a name="how-are-inbound-nat-rules-different-from-load-balancing-rules"></a>輸入 NAT 規則與負載平衡規則有何不同？
NAT 規則是用來指定要路由傳送流量的後端資源。 例如，設定特定的負載平衡器埠，以將 RDP 流量傳送到特定的 VM。 負載平衡規則是用來指定要路由傳送流量的後端資源集區，以平衡每個實例的負載。 例如，負載平衡器規則可以將負載平衡器通訊埠80上的 TCP 封包路由傳送到 web 伺服器集區。

## <a name="next-steps"></a>後續步驟
如果您的問題未列于上方，請使用您的問題傳送關於此頁面的意見反應。 這會為產品小組建立 GitHub 問題，以確保所有的重要客戶問題都能獲得解答。
