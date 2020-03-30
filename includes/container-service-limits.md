---
title: 包含檔案
description: 包含檔案
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/22/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 6b4678b381e769993b01bbedd1cb4c0aeefc0cc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335059"
---
| 資源 | 限制 |
| --- | :--- |
| 每個訂閱的最大群集數 | 100 |
| 具有虛擬機器可用性集和基本負載等化器 SKU 的每個群集的最大節點  | 100 |
| 具有虛擬機器縮放集和[標準負載等化器 SKU][standard-load-balancer]的每個群集的最大節點 | 1000 （每個節點池 100[個節點][node-pool]） |
| 每個節點的最大窗格：與 Kubenet[的基本網路][basic-networking] | 110 |
| 每個節點的最大窗格：使用 Azure 容器[網路介面的高級網路][advanced-networking] | Azure CLI 部署：30<sup>1</sup><br />Azure 資源管理器範本： 30<sup>1</sup><br />入口網站部署：30 |

<sup>1</sup>使用 Azure CLI 或資源管理器範本部署 Azure 庫伯奈斯服務 （AKS） 群集時，此值每個節點最多可配置 250 個 pod。 在部署 AKS 群集後，或者如果您使用 Azure 門戶部署群集，則無法配置每個節點的最大窗格。<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-standard-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
