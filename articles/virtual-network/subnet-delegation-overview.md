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
ms.openlocfilehash: 1fbb683754aed5b2a2e6e9c022713b7e87ad9ba9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91329202"
---
# <a name="what-is-subnet-delegation"></a>什麼是子網委派？

子網委派可讓您針對所選的 Azure PaaS 服務，指定需要插入虛擬網路的特定子網。 子網委派可讓客戶完全掌控管理 Azure 服務與虛擬網路的整合。

當您將子網委派至 Azure 服務時，您可以讓該服務建立該子網的一些基本網路設定規則，以協助 Azure 服務以穩定的方式操作其實例。 因此，Azure 服務可能會建立一些預先或部署後的條件，例如：
- 在共用的與專用子網中部署服務。
- 將一組網路意圖原則新增至服務，這是服務正常運作所需的部署之後。

##  <a name="advantages-of-subnet-delegation"></a>子網委派的優點

將子網委派給特定服務可提供下列優點：

- 有助於指定一或多個 Azure 服務的子網，並依據需求管理子網中的實例。 例如，虛擬網路擁有者可以針對委派的子網定義下列各項，以便更妥善地管理資源和存取，如下所示：
    - 網路篩選流量原則與網路安全性群組。
    - 具有使用者定義路由的路由原則。
    - 服務與服務端點設定整合。
- 藉由以網路意圖原則的形式定義部署的前置條件，協助插入的服務與虛擬網路進行更好的整合。 這可確保可能影響插入服務運作的任何動作都可以在 PUT 時遭到封鎖。


## <a name="who-can-delegate"></a>誰可以委派？
子網委派是虛擬網路擁有者必須執行的練習，以指定特定 Azure 服務的其中一個子網。 接著，Azure 服務會將實例部署到此子網，以供客戶工作負載取用。

## <a name="impact-of-subnet-delegation-on-your-subnet"></a>子網委派對子網的影響
每個 Azure 服務都會定義自己的部署模型，讓他們可以在委派的子網中定義其執行或不支援哪些屬性，以供插入之用，如下所示：
- 與其他 Azure 服務或 VM/虛擬機器擴展集位於相同子網中的共用子網，或僅支援其中只有此服務實例的專用子網。
- 支援與委派子網的 NSG 關聯。
- 支援與委派的子網相關聯的 NSG 也可與任何其他子網相關聯。
- 允許路由表與委派的子閘道聯。
- 允許與委派的子網相關聯的路由表與其他任何子網相關聯。
- 規定委派子網中的 IP 位址數目下限。
- 指定委派子網中的 IP 位址空間，從私人 IP 位址空間 (10.0.0.0/8、192.168.0.0/16、172.16.0.0/12) 。
- 指示自訂 DNS 設定有 Azure DNS 專案。
- 需要先移除委派，才能刪除子網或虛擬網路。

插入的服務也可以新增自己的原則，如下所示：
- **安全性原則**：指定服務運作所需的安全性規則集合。
- **路由原則**：指定服務運作所需的路由集合。

## <a name="what-subnet-delegation-does-not-do"></a>子網委派不會執行的動作

要插入委派子網的 Azure 服務仍有一組基本的屬性可供非委派的子網使用，例如：
-  Azure 服務可以將實例插入客戶子網，但不會影響現有的工作負載。
-  這些服務所套用的原則或路由是有彈性的，且可由客戶覆寫。

## <a name="next-steps"></a>後續步驟

- [委派子網](manage-subnet-delegation.md)
