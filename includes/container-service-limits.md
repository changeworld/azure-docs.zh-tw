---
title: 包含檔案
description: 包含檔案
services: container-service
author: mlearned
ms.service: container-service
ms.topic: include
ms.date: 11/22/2019
ms.author: mlearned
ms.custom: include file
ms.openlocfilehash: a42bba1b6524825aa571e4c18319b61b97829792
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96584559"
---
| 資源 | 限制 |
| --- | :--- |
| 每個訂用帳戶的叢集上限 | 1000 |
| 具有虛擬機器可用性設定組和基本 Load Balancer SKU 的每個叢集節點數目上限  | 100 |
| 具有虛擬機器擴展集和 [Standard Load Balancer SKU][standard-load-balancer] 的每個叢集節點數目上限 | 1000 (每個[節點集區][node-pool] 100 個節點) |
| 每個節點的 Pod 數目上限：採用 Kubenet 的[基本網路][basic-networking] | 110 |
| 每個節點的 Pod 數目上限：使用 Azure Container 網路介面的[進階網路][advanced-networking] | Azure CLI 部署：30<sup>1</sup><br />Azure Resource Manager 範本：30<sup>1</sup><br />入口網站部署：30 |

<sup>1</sup> 當您使用 Azure CLI 或 Resource Manager 範本部署 Azure Kubernetes Service (AKS) 叢集時，此值最高可設定為每個節點 250 個 Pod。 在您已部署 AKS 叢集之後，或如果您使用 Azure 入口網站部署叢集，就無法設定每個節點的 Pod 上限。<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest