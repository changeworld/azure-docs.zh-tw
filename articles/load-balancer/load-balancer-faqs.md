---
title: 常見問題-Azure Load Balancer
description: 有關 Azure Load Balancer 常見問題的解答。
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: article
ms.date: 04/22/2020
ms.author: errobin
ms.openlocfilehash: 205a4bd119a7324c4e6524a0e29d432aa57bf315
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85848219"
---
# <a name="load-balancer-frequently-asked-questions"></a>Load Balancer 常見問題

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

## <a name="what-is-ip-1686312916"></a>什麼是 IP 168.63.129.16？
已標記為 Azure 基礎結構的主機虛擬 IP 位址，Load Balancer Azure 健康狀態探查的來源。 設定後端實例時，它們必須允許來自此 IP 位址的流量成功回應健康情況探查。 此規則不會與您 Load Balancer 前端的存取進行互動。 如果您不是使用 Azure Load Balancer，您可以覆寫此規則。 您可以在[這裡](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags)深入瞭解服務標記。

## <a name="can-i-use-global-vnet-peering-with-basic-load-balancer"></a>我可以搭配基本 Load Balancer 使用全域 VNET 對等互連嗎？
否。 基本 Load Balancer 不支援全域 VNET 對等互連。 您可以改用 Standard Load Balancer。 如需順暢的升級，請參閱[從基本升級至標準](upgrade-basic-standard.md)文章。

## <a name="how-can-i-discover-the-public-ip-that-an-azure-vm-uses"></a>如何探索 Azure VM 所使用的公用 IP？

有許多方式可判斷輸出連線的公用來源 IP 位址。 OpenDNS 提供可顯示您 VM 公用 IP 位址的服務。
藉由使用 nslookup 命令，您便可以將名稱 myip.opendns.com 的 DNS 查詢傳送給 OpenDNS 解析程式。 服務會傳回用來傳送查詢的來源 IP 位址。 當您從 VM 執行下列查詢時，回應會是用於該 VM 的公用 IP：

 ```nslookup myip.opendns.com resolver1.opendns.com```

## <a name="how-do-connections-to-azure-storage-in-the-same-region-work"></a>相同區域中的 Azure 儲存體連接如何？
您不需要連線到與 VM 同區域的儲存體，即可透過上述案例獲得輸出連線。 如果不想此連線，請使用如前文所述的網路安全性群組 (NSG)。 如需其他區域的儲存體連線，則需要有輸出連線能力。 請注意，從同區域的 VM 連線到儲存體時，儲存體診斷記錄中來源 IP 位址會是內部提供者的位址，而不是 VM 的公用 IP 位址。 如果希望要將儲存體帳戶的存取，限制在同區域一或多個虛擬網路子網路中的 VM，請在設定儲存體帳戶防火牆時，使用[虛擬網路服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)，且不要使用公用 IP 位址。 設定好服務端點之後，您就會在儲存體診斷記錄中看到虛擬網路私人 IP 位址，而不是內部提供者的位址。

## <a name="next-steps"></a>後續步驟
如果您的問題未列于上方，請使用您的問題傳送關於此頁面的意見反應。 這會為產品小組建立 GitHub 問題，以確保所有的重要客戶問題都能獲得解答。
