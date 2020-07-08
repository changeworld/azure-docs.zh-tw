---
title: Include 檔案
description: 包含檔案
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/22/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 6b4678b381e769993b01bbedd1cb4c0aeefc0cc1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80335059"
---
| 資源 | 限制 |
| --- | :--- |
| 每個訂用帳戶的群集上限 | 100 |
| 具有虛擬機器可用性設定組和基本 Load Balancer SKU 之每個叢集的節點數目上限  | 100 |
| 每個叢集的節點數目上限，虛擬機器擴展集和[STANDARD LOAD BALANCER SKU][standard-load-balancer] | 1000（每個[節點集][node-pool]區100個節點） |
| 每個節點的最大 pod 數：使用 Kubenet 的[基本網路][basic-networking]功能 | 110 |
| 每個節點的最大 pod 數：使用 Azure 容器網路介面的[Advanced 網路][advanced-networking] | Azure CLI 部署：30<sup>1</sup><br />Azure Resource Manager 範本： 30<sup>1</sup><br />入口網站部署：30 |

<sup>1</sup>當您使用 Azure CLI 或 Resource Manager 範本部署 Azure Kubernetes Service （AKS）叢集時，此值最多可設定為每個節點250個 pod。 您無法在已部署 AKS 叢集之後，或使用 Azure 入口網站部署叢集時，設定每個節點的最大 pod 數。<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-standard-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
