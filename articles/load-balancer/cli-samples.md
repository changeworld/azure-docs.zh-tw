---
title: 適用於負載平衡器的 Azure CLI 範例
titleSuffix: Azure Load Balancer
description: Azure CLI 範例
services: load-balancer
documentationcenter: load-balancer
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18, devx-track-azurecli
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 06/14/2018
ms.author: allensu
ms.openlocfilehash: fcc2a579f2fe9048dd58cd8b52c8c704c894936b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87499342"
---
# <a name="azure-cli-samples-for-load-balancer"></a>適用於負載平衡器的 Azure CLI 範例

下表包含使用 Azure CLI 所建置之 Bash 指令碼的連結。

* [使用 VM 平衡流量負載以達到高可用性](./scripts/load-balancer-linux-cli-sample-nlb.md)

  依據高可用性和負載平衡組態建立數個虛擬機器。

* [跨越多個可用性區域為 VM 進行負載平衡](./scripts/load-balancer-linux-cli-sample-zone-redundant-frontend.md)

  在某區域的不同可用性區域中建立三個 VM，並建立一個具有區域備援前端 IP 位址的 Standard Load Balancer。 萬一整個資料中心失敗或遺失，這個負載平衡設定有助於保護您的應用程式和資料免於受害。

* [在特定可用性區域內為 VM 進行負載平衡](./scripts/load-balancer-linux-cli-sample-zonal-frontend.md)

  建立三個 VM、具有區域前端 IP 且有助於對齊資料路徑的 Standard Load Balancer，以及指定區域的單一區域中的資源。

* [在 VM 上平衡多個網站的負載](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md)

  建立具有多個 IP 設定的兩個 VM。這兩個 VM 會加入 Azure 可用性設定組，並可透過 Azure Load Balancer 來存取。
