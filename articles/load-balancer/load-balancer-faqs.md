---
title: 常見問題-Azure Load Balancer
description: Azure Load Balancer 常見問題的解答。
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: article
ms.date: 04/22/2020
ms.author: errobin
ms.openlocfilehash: e9f46b11d9c0b5251ee4d52f64d657926f6f9c5e
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222984"
---
# <a name="load-balancer-frequently-asked-questions"></a>Load Balancer 常見問題

## <a name="what-types-of-load-balancer-exist"></a>有哪些類型的 Load Balancer 存在？
內部負載平衡器，可平衡 VNET 內的流量和外部負載平衡器，以平衡進出網際網路連線端點的流量。 如需詳細資訊，請參閱 [Load Balancer 類型](components.md#frontend-ip-configurations)。 

針對這兩種類型，Azure 提供基本 SKU 和標準 SKU，具有不同的功能、效能、安全性和健康情況追蹤功能。 這些差異會在我們的 [SKU 比較](skus.md) 文章中說明。

 ## <a name="how-can-i-upgrade-from-a-basic-to-a-standard-load-balancer"></a>如何從基本升級至 Standard Load Balancer？
如需有關升級 Load Balancer SKU 的自動化腳本和指引，請參閱 [從基本升級至標準版](upgrade-basic-standard.md) 文章。

 ## <a name="what-are-the-different-load-balancing-options-in-azure"></a>Azure 中的不同負載平衡選項有哪些？
請參閱 [負載平衡器技術指南](/azure/architecture/guide/technology-choices/load-balancing-overview)  ，以取得可用的負載平衡服務和每個服務的建議用途。

## <a name="where-can-i-find-load-balancer-arm-templates"></a>我可以在哪裡找到 Load Balancer ARM 範本？
請參閱常見部署的 ARM 範本 [Azure Load Balancer 快速入門範本清單](/azure/templates/microsoft.network/loadbalancers#quickstart-templates) 。

## <a name="how-are-inbound-nat-rules-different-from-load-balancing-rules"></a>輸入 NAT 規則與負載平衡規則有何不同？
NAT 規則可用來指定要路由傳送流量的後端資源。 例如，設定特定負載平衡器埠，以將 RDP 流量傳送至特定的 VM。 負載平衡規則可用來指定要將流量路由傳送至其中的後端資源集區，以平衡每個實例之間的負載。 例如，負載平衡器規則可將負載平衡器埠80上的 TCP 封包路由傳送到 web 伺服器的集區。

## <a name="what-is-ip-1686312916"></a>什麼是 IP 168.63.129.16？
標示為 Azure 基礎結構的主機虛擬 IP 位址，Load Balancer Azure 健康情況探查的來源。 設定後端實例時，它們必須允許來自此 IP 位址的流量成功回應健康情況探查。 此規則不會與您的 Load Balancer 前端存取進行互動。 如果您不是使用 Azure Load Balancer，可以覆寫此規則。 您可以在 [這裡](../virtual-network/service-tags-overview.md#available-service-tags)深入瞭解服務標記。

## <a name="can-i-use-global-vnet-peering-with-basic-load-balancer"></a>我可以搭配基本 Load Balancer 使用全域 VNet 對等互連嗎？
不會。 基本 Load Balancer 不支援全域 VNET 對等互連。 您可以改用 Standard Load Balancer。 如需順暢的升級，請參閱 [從基本升級至標準版](upgrade-basic-standard.md) 文章。

## <a name="how-can-i-discover-the-public-ip-that-an-azure-vm-uses"></a>如何探索 Azure VM 使用的公用 IP？

有許多方式可判斷輸出連線的公用來源 IP 位址。 OpenDNS 提供可顯示您 VM 公用 IP 位址的服務。
藉由使用 nslookup 命令，您便可以將名稱 myip.opendns.com 的 DNS 查詢傳送給 OpenDNS 解析程式。 服務會傳回用來傳送查詢的來源 IP 位址。 當您從 VM 執行下列查詢時，回應會是用於該 VM 的公用 IP：

 ```nslookup myip.opendns.com resolver1.opendns.com```
 
## <a name="can-i-add-a-vm-from-the-same-availability-set-to-different-backend-pools-of-a-load-balancer"></a>我可以從相同的可用性設定組，將 VM 新增至 Load Balancer 的不同後端集區嗎？
不會，並不會發生這樣的事。

## <a name="how-do-connections-to-azure-storage-in-the-same-region-work"></a>相同區域中 Azure 儲存體的連接如何運作？
您不需要連線到與 VM 同區域的儲存體，即可透過上述案例獲得輸出連線。 如果不想此連線，請使用如前文所述的網路安全性群組 (NSG)。 如需其他區域的儲存體連線，則需要有輸出連線能力。 請注意，從同區域的 VM 連線到儲存體時，儲存體診斷記錄中來源 IP 位址會是內部提供者的位址，而不是 VM 的公用 IP 位址。 如果希望要將儲存體帳戶的存取，限制在同區域一或多個虛擬網路子網路中的 VM，請在設定儲存體帳戶防火牆時，使用[虛擬網路服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)，且不要使用公用 IP 位址。 設定好服務端點之後，您就會在儲存體診斷記錄中看到虛擬網路私人 IP 位址，而不是內部提供者的位址。

## <a name="what-are-best-practises-with-respect-to-outbound-connectivity"></a>關於輸出連線能力的最佳做法為何？
Standard Load Balancer 和標準公用 IP 引進輸出連線能力和不同行為。 這些與基本 SKU 不同。 如果您想要在使用標準 SKU 時輸出連線，您必須使用標準公用 IP 位址或標準公用 Load Balancer 明確定義該連線。 這包括在使用內部 Standard Load Balancer 時建立輸出連線。 建議您一律使用標準公用 Load Balancer 的輸出規則。 這表示當使用內部 Standard Load Balancer 時，如果想要使用輸出連線能力，您需要採取步驟來為後端集區中的 VM 建立輸出連線能力。 在輸出連線的內容中，單一獨立 VM （可用性設定組中的所有 VM），VMSS 中的所有實例會以群組的方式運作。 這表示，如果可用性設定組中的單一 VM 與標準 SKU 相關聯，則此時可用性設定組內的所有 VM 執行個體行為會遵循相同的規則，就如同與標準 SKU 相關聯一般，雖然個別執行個體並非直接與它相關聯。 當獨立 VM 有多張網路介面卡連接到負載平衡器時，也會出現這樣的行為。 如果單獨新增一張 NIC，就會出現相同的行為。 請仔細檢閱這整份文件了解整體概念，檢閱 [Standard Load Balancer](./load-balancer-overview.md) 了解 SKU 之間的差異，並檢閱[輸出規則](load-balancer-outbound-connections.md#outboundrules)。
使用輸出規則可讓您細部控制輸出連線的所有層面。
 
## <a name="next-steps"></a>後續步驟
如果上面未列出您的問題，請傳送有關此頁面的意見反應給您的問題。 這會為產品小組建立 GitHub 問題，以確保所有的重要客戶問題都會獲得解答。
