---
title: Azure 虛擬網路中的子網委派是什麼？
description: 瞭解 Azure 虛擬網路中的子網委派
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2019
ms.author: kumud
ms.openlocfilehash: b33ff808b802b6848e2d5debaf515a73bf21a1bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74281333"
---
# <a name="what-is-subnet-delegation"></a>什麼是子網委派？

子網委派使您能夠為需要注入虛擬網路的 Azure PaaS 服務指定特定的子網。 子網委派為客戶提供了管理 Azure 服務集成到其虛擬網路的完全控制。

將子網委派給 Azure 服務時，允許該服務為該子網建立一些基本的網路設定規則，這有助於 Azure 服務以穩定的方式操作其實例。 因此，Azure 服務可能會建立一些部署前或部署後條件，例如：
- 在共用子網與專用子網中部署服務。
- 向服務添加一組網路意圖策略，部署後服務正常工作所需的網路意圖策略。

##  <a name="advantages-of-subnet-delegation"></a>子網委派的優勢

將子網委派給特定服務具有以下優點：

- 説明為一個或多個 Azure 服務指定子網，並根據需要管理子網中的實例。 例如，虛擬網路擁有者可以為委派子網定義以下內容，以便更好地管理資源和訪問，如下所示：
    - 具有網路安全性群組的網路篩選流量策略。
    - 具有使用者定義的路由的路由策略。
    - 服務與服務終結點組態集成。
- 通過以網路意圖策略的形式定義部署的預設條件，説明注入服務以更好地與虛擬網路集成。 這可確保在 PUT 上阻止任何可能影響注入服務功能的操作。


## <a name="who-can-delegate"></a>誰可以委派？
子網委派是虛擬網路擁有者為指定特定 Azure 服務的一個子網而需要執行的練習。 Azure 服務又將實例部署到此子網，供客戶工作負荷使用。

## <a name="impact-of-subnet-delegation-on-your-subnet"></a>子網委派對子網的影響
每個 Azure 服務定義自己的部署模型，其中它們可以定義它們為注入目的在委派子網中執行或不支援哪些屬性，如下所示：
- 共用子網與其他 Azure 服務或 VM/ 虛擬機器規模集在同一子網中，或者它僅支援專用子網，其中僅包含此服務的實例。
- 支援與委派子網的 NSG 關聯。
- 與委派子閘道聯的支援 NSG 也可以與任何其他子閘道聯。
- 允許路由表與委派的子閘道聯。
- 允許與委派子閘道聯的路由表與任何其他子網相關聯。
- 規定委派子網中的最小 IP 位址數。
- 指定委派子網中的 IP 位址空間來自私人 IP 位址空間 （10.0.0.0/8， 192.168.0.0/16， 172.16.0.0/12）。
- 規定自訂 DNS 配置具有 Azure DNS 條目。

注入的服務還可以添加各自的策略，如下所示：
- **安全性原則**：收集給定服務正常工作所需的安全規則。
- **路由策略**：收集給定服務正常工作所需的路由。

## <a name="what-subnet-delegation-does-not-do"></a>哪些子網委派不執行

注入委派子網的 Azure 服務仍然具有可用於非委派子網的基本屬性集，例如：
-  Azure 服務可以將實例注入到客戶子網中，但不能影響現有工作負荷。
-  這些服務應用的策略或路由是靈活的，客戶可以覆蓋策略或路由。

## <a name="next-steps"></a>後續步驟

- [委派子網](manage-subnet-delegation.md)
