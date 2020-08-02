---
title: Azure CLI 範例-網路
description: 深入瞭解網路的 Azure CLI 範例，包括 Azure 資源之間連線的範例，以及負載平衡和流量方向的範例。
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: timlt
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/25/2017
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: c038f0d238646f43b93ba2a2c6a1120ab5feccee
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87497285"
---
# <a name="azure-cli-samples-for-networking"></a>網路功能的 Azure CLI 範例

下表包含使用 Azure CLI 所建置之 Bash 指令碼的連結。

| 指令碼 | 描述 |
|-|-|
|**Azure 資源之間的連線**||
| [建立多層式應用程式的虛擬網路](./scripts/virtual-network-cli-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | 建立具有前端和後端子網路的虛擬網路。 傳送到前端子網路的流量會限制為 HTTP 和 SSH，而傳送到後端子網路的流量則限制為 MySQL 且連接埠為 3306。 |
| [對等互連兩個虛擬網路](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | 在相同區域中建立和連線兩個虛擬網路。 |
| [透過網路虛擬設備路由傳送流量](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | 建立具有前端和後端子網路的虛擬網路，以及可在兩個子網路間路由傳送流量的 VM。 |
| [篩選輸入和輸出 VM 網路流量](./scripts/virtual-network-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | 建立具有前端和後端子網路的虛擬網路。 傳輸至前端子網路的輸入網路流量限制為 HTTP、HTTPS 和 SSH。 不允許從後端子網路傳輸至網際網路的輸出流量。 |
|**負載平衡和流量方向**||
| [在 VM 上平衡多個網站的負載](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md?toc=%2fazure%2fnetworking%2ftoc.json) | 建立具有多個 IP 設定的兩個 VM。這兩個 VM 會加入 Azure 可用性設定組，並可透過 Azure Load Balancer 來存取。 |
| [跨多個區域導向流量以達到高應用程式可用性](./scripts/traffic-manager-cli-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  建立兩個 App Service 方案、兩個 Web 應用程式、一個流量管理員設定檔和兩個流量管理員端點。 |
| | |
