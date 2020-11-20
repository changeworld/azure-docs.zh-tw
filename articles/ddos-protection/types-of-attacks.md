---
title: Azure DDoS 保護標準的攻擊類型降低
description: 瞭解 Azure DDoS 保護標準保護的攻擊類型。
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 8b213755aeed1590df3d1f5e91f44796c13c7711
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94991769"
---
# <a name="types-of-ddos-attacks-overview"></a>DDoS 攻擊的類型總覽

DDoS 保護標準層可降低下列攻擊類型的風險：

- **體積型攻擊**：這些攻擊會充斥大量看似合法流量的網路層。 其中包括 UDP 洪水、放大氾濫，以及其他詐騙封包氾濫。 DDoS Protection Standard 可透過 Azure 的全球網路規模自動吸收和清除這些潛在的多重 gb 攻擊，來降低這些攻擊。
- **通訊協定攻擊**：這些攻擊透過利用第 3 層和第 4 層通訊協定堆疊中的弱點，讓目標無法供存取。 它們包含 SYN 洪水攻擊、反映攻擊和其他通訊協定攻擊。 DDoS 保護標準層可透過與用戶端互動來區別惡意與合法流量並封鎖惡意流量，以降低這些攻擊的風險。 
- **資源 (應用程式) 層攻擊**：這些攻擊會鎖定 Web 應用程式封包，以中斷主機之間的資料傳輸。 其中包括 HTTP 通訊協定違規、SQL 插入、跨網站腳本和其他第7層攻擊。 使用 Web 應用程式防火牆，例如 Azure [應用程式閘道 Web 應用程式防火牆](../web-application-firewall/ag/ag-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，以及 DDoS Protection Standard，以提供這些攻擊的防禦。 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall) 還提供了協力廠商 Web 應用程式防火牆供應項目。

## <a name="azure-ddos-protection-standard"></a>Azure DDoS Protection Standard

DDoS Protection Standard 可保護虛擬網路中的資源，包括與虛擬機器相關聯的公用 IP 位址、內部負載平衡器，以及應用程式閘道。 當與應用程式閘道 web 應用程式防火牆搭配使用，或在具有公用 IP 的虛擬網路中部署協力廠商 web 應用程式防火牆時，DDoS 保護標準可提供完整的第3層至第7層風險降低功能。

## <a name="next-steps"></a>下一步

- 瞭解如何 [建立 DDoS 保護計劃](manage-ddos-protection.md)。