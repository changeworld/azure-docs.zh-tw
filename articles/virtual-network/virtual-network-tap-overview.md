---
title: Azure 虛擬網路 TAP 概觀 | Microsoft Docs
description: 了解虛擬網路 TAP。 虛擬網路 TAP 可為您提供虛擬機器網路流量的深層複本，以供串流至封包收集器。
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2019
ms.author: kaanan
ms.openlocfilehash: c468f289c9dbfe949890c5462dd7c9be3e548d5a
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938755"
---
# <a name="virtual-network-tap"></a>虛擬網路 TAP
> [!IMPORTANT]
> 虛擬網路點擊預覽目前已在所有 Azure 區域中保留。 您可以使用您的訂用帳戶識別碼寄送電子郵件給我們 <azurevnettap@microsoft.com> ，我們將會通知您未來有關預覽版的更新。 在過渡期間，您可以使用代理程式或 NVA 解決方案，透過[Azure Marketplace 供應](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/networking?page=1&subcategories=appliances%3Ball&search=Network%20Traffic&filters=partners)專案中提供的封[包訊息代理程式合作夥伴解決方案](#virtual-network-tap-partner-solutions)，提供點按/網路可見度功能。

Azure 虛擬網路 TAP (終端機存取點) 可讓您持續將虛擬機器網路流量串流到網路封包收集器或分析工具。 收集器或分析工具是由 [網路虛擬裝置](https://azure.microsoft.com/solutions/network-appliances/) 合作夥伴提供。 如需經驗證能與虛擬網路 TAP 相容的合作夥伴解決方案清單，請參閱[合作夥伴解決方案](#virtual-network-tap-partner-solutions)。
下圖顯示虛擬網路 TAP 的運作方式。 您可以在部署於虛擬網路的虛擬機器上，對其所連結的[網路介面](virtual-network-network-interface.md)新增 TAP 設定。 目的地是和所監視網路介面相同的虛擬網路或[對等互連虛擬](virtual-network-peering-overview.md)網路中所含的虛擬網路 IP 位址。 虛擬網路 TAP 的收集器解決方案可以部署在 Azure 內部負載平衡器後方，以獲得高可用性。
![虛擬網路 TAP 的運作方式](./media/virtual-network-tap/architecture.png)

## <a name="prerequisites"></a>必要條件

建立虛擬網路點之前，您必須已收到您在預覽版中註冊的確認電子郵件，並有一或多個使用 [Azure Resource Manager](../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 部署模型建立的虛擬機器，以及可在相同 Azure 區域中匯總點出流量的合作夥伴解決方案。 如果虛擬網路中沒有合作夥伴解決方案，請參閱[合作夥伴解決方案](#virtual-network-tap-partner-solutions)來加以部署。 您可以使用相同的虛擬網路 TAP 資源，以從相同或不同訂用帳戶中的多個網路介面彙總流量。 如果所監視的網路介面位在不同訂用帳戶中，則這兩個訂用帳戶必須與相同的 Azure Active Directory 租用戶相關聯。 此外，所監視的網路介面和用於彙總 TAP 流量的目的地端點可以位於相同區域中的對等互連虛擬網路。 如果您要使用這種部署模型，請確保[虛擬網路對等互連](virtual-network-peering-overview.md)已啟用，再設定虛擬網路 TAP。

## <a name="permissions"></a>權限

用來對網路介面套用 TAP 設定的帳戶，必須指派為[網路參與者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色，或屬於已指派下表中必要動作的[自訂角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)：

| 動作 | 名稱 |
|---|---|
| Microsoft.Network/virtualNetworkTaps/* | 必須有此動作，才能建立、更新、讀取和刪除虛擬網路 TAP 資源 |
| Microsoft.Network/networkInterfaces/read | 必須有此動作，才能讀取要在其上設定 TAP 的網路介面資源 |
| Microsoft.Network/tapConfigurations/* | 必須有此動作，才能建立、更新、讀取和刪除網路介面上的 TAP 設定 |


## <a name="virtual-network-tap-partner-solutions"></a>虛擬網路 TAP 合作夥伴解決方案

### <a name="network-packet-brokers"></a>網路封包訊息代理程式

- [Gigamon GigaSECURE](https://blog.gigamon.com/2018/09/13/why-microsofts-new-vtap-service-works-even-better-with-gigasecure-for-azure)
- [Ixia CloudLens](https://www.ixiacom.com/cloudlens/cloudlens-azure)
- [Nubeva Prisms](https://www.nubeva.com/azurevtap)
- [Big Switch Big Monitoring Fabric](https://www.bigswitch.com/products/big-monitoring-fabric/public-cloud/microsoft-azure)

### <a name="security-analytics-networkapplication-performance-management"></a>安全性分析、網路/應用程式效能管理

- [喚醒安全性](https://awakesecurity.com/technology-partners/microsoft-azure/)
- [Cisco Stealthwatch 雲端](https://blogs.cisco.com/security/cisco-stealthwatch-cloud-and-microsoft-azure-reliable-cloud-infrastructure-meets-comprehensive-cloud-security)
- [Darktrace](https://www.darktrace.com/en/azure/)
- [ExtraHop Reveal(x)](https://www.extrahop.com/partners/tech-partners/microsoft/)
- [Fidelis Cybersecurity](https://www.fidelissecurity.com/technology-partners/microsoft-azure )
- [Flowmon](https://www.flowmon.com/blog/azure-vtap)
- [NetFort LANGuardian](https://www.netfort.com/languardian/solutions/visibility-in-azure-network-tap/)
- [Netscout vSTREAM]( https://www.netscout.com/marketplace-azure)
- [Riverbed SteelCentral AppResponse]( https://www.riverbed.com/products/steelcentral/steelcentral-appresponse-11.html)
- [RSA NetWitness® Platform](https://www.rsa.com/content/dam/en/solution-brief/rsa-netwitness-platform-overview-for-federal-agencies.pdf)
- [Vectra Cognito](https://vectra.ai/microsoftazure)



## <a name="next-steps"></a>後續步驟

- 了解如何[建立虛擬網路 TAP](tutorial-tap-virtual-network-cli.md)。
