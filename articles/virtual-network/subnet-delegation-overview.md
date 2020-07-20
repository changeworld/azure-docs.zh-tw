---
title: 什麼是 Azure 虛擬網路中的子網委派？
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74281333"
---
# <a name="what-is-subnet-delegation"></a>什麼是子網委派？

子網委派可讓您為您選擇的 Azure PaaS 服務指定特定的子網，以供需要插入您的虛擬網路中。 子網委派可讓客戶在管理 Azure 服務與虛擬網路整合時，提供完全控制權。

當您將子網委派至 Azure 服務時，您會允許該服務建立該子網的一些基本網路設定規則，以協助 Azure 服務以穩定的方式操作其實例。 因此，Azure 服務可能會建立一些預先或部署後的條件，例如：
- 在共用與專用的子網中部署服務。
- 在服務中加入一組網路意圖原則，這是服務正常運作所需的部署。

##  <a name="advantages-of-subnet-delegation"></a>子網委派的優點

將子網委派給特定的服務，可提供下列優點：

- 協助您指定一或多個 Azure 服務的子網，並根據需求來管理子網中的實例。 例如，虛擬網路擁有者可以為委派的子網定義下列內容，以更好的方式管理資源和存取權，如下所示：
    - 使用網路安全性群組的網路篩選流量原則。
    - 具有使用者定義路由的路由原則。
    - 服務與服務端點設定整合。
- 藉由以網路意圖原則的形式定義其部署的預先條件，協助插入服務更緊密地與虛擬網路整合。 這可確保任何可能影響插入服務運作的動作都可以在 PUT 時遭到封鎖。


## <a name="who-can-delegate"></a>誰可以委派？
子網委派是虛擬網路擁有者必須執行才能為特定 Azure 服務指定其中一個子網的練習。 Azure 服務接著會將實例部署到此子網，以供客戶工作負載取用。

## <a name="impact-of-subnet-delegation-on-your-subnet"></a>子網對子網委派的影響
每個 Azure 服務都會定義自己的部署模型，在其中，他們可以在委派的子網中定義其所執行或不支援的屬性以供插入之用，如下所示：
- 共用子網與其他 Azure 服務或相同子網中的 VM/虛擬機器擴展集，或只支援具有此服務之實例的專用子網。
- 支援與委派子網的 NSG 關聯。
- 支援與委派子網相關聯的 NSG 也可以與其他任何子網相關聯。
- 允許與委派子網的路由表關聯。
- 允許與委派子網相關聯的路由表與其他任何子網相關聯。
- 規定委派子網中 IP 位址的最小數目。
- 規定委派子網中的 IP 位址空間是來自私人 IP 位址空間（10.0.0.0/8、192.168.0.0/16、172.16.0.0/12）。
- 指示自訂 DNS 設定具有 Azure DNS 專案。

插入的服務也可以新增自己的原則，如下所示：
- **安全性原則**：所需的安全性規則集合，指定的服務才能正常執行。
- **路由原則**：所需的路由集合，指定的服務才能正常執行。

## <a name="what-subnet-delegation-does-not-do"></a>子網委派不會執行的動作

要插入委派子網中的 Azure 服務，仍然具有一組可用於非委派子網的基本屬性，例如：
-  Azure 服務可以將實例插入客戶子網，但不會影響現有的工作負載。
-  這些服務所套用的原則或路由具有彈性，且可由客戶覆寫。

## <a name="next-steps"></a>後續步驟

- [委派子網](manage-subnet-delegation.md)
