---
title: SAP HANA on Azure (大型執行個體) 的其他網路需求 | Microsoft Docs
description: SAP HANA on Azure (大型執行個體) 的其他網路需求。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 66bbd8b462ac35756be0fae6eba940ba0aba6c4b
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "77614573"
---
# <a name="additional-network-requirements-for-large-instances"></a>大型執行個體的其他網路需求

在部署 SAP HANA on Azure 的大型執行個體時，您可能會有額外的網路需求。

## <a name="add-more-ip-addresses-or-subnets"></a>新增更多 IP 位址或子網路

新增更多 IP 位址或子網路時，您可以使用 Azure 入口網站、PowerShell 或 Azure CLI。

請將新的 IP 位址範圍新增到虛擬網路位址空間中作為新的範圍，而不要產生新的彙總範圍。 請這項變更提交給 Microsoft。 這可讓您在用戶端中從這個新的 IP 位址範圍連線到 HANA 大型執行個體單位。 您可以建立 Azure 支援要求，以新增虛擬網路位址空間。 收到確認之後，請執行後續步驟。

若要從 Azure 入口網站建立額外的子網路，請參閱[使用 Azure 入口網站建立虛擬網路](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)。 若要從 PowerShell 建立子網路，請參閱[使用 PowerShell 建立虛擬網路](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)。

## <a name="add-virtual-networks"></a>新增虛擬網路

在最初連線到一或多個 Azure 虛擬網路之後，您可能會想要連線到可存取 SAP HANA on Azure (大型執行個體) 的額外虛擬網路。 首先，請提交 Azure 支援要求。 在該要求中，請納入可識別特定 Azure 部署的特定資訊。 此外也請納入 Azure 虛擬網路位址空間的一或多個 IP 位址空間範圍。 接著，「SAP HANA on Microsoft 服務管理」會為您提供連線到額外虛擬網路和 Azure ExpressRoute 所需的必要資訊。 針對每個虛擬網路，您將需要唯一的授權金鑰，才能連線到 ExpressRoute 線路，再連線到 HANA 大型執行個體。

## <a name="increase-expressroute-circuit-bandwidth"></a>增加 ExpressRoute 線路頻寬

請洽詢「SAP HANA on Microsoft 服務管理」。 如果他們建議您增加 SAP HANA on Azure (大型執行個體) ExpressRoute 線路的頻寬，請建立 Azure 支援要求。 （您可以要求增加單一線路頻寬，最多 10 Gbps）。您接著會在作業完成之後收到通知;您不需要執行其他任何動作，即可在 Azure 中啟用這個較高的速度。

## <a name="add-an-additional-expressroute-circuit"></a>新增額外的 ExpressRoute 線路

請洽詢「SAP HANA on Microsoft 服務管理」。 如果他們建議您新增額外的 ExpressRoute 線路，請建立 Azure 支援要求 (包括取得授權資訊以連線到新線路的要求)。 提出要求之前，您必須定義用於虛擬網路的位址空間。 「SAP HANA on Microsoft 服務管理」隨後就會提供授權。

建立新線路且「SAP HANA on Microsoft 服務管理」設定完成之後，您將會收到通知，當中含有您繼續作業所需的資訊。 如果 Azure 虛擬網路已經連線到相同 Azure 區域的另一個 SAP HANA on Azure (大型執行個體) ExpressRoute 線路，您就無法將其連線到這個額外的線路。

## <a name="delete-a-subnet"></a>刪除子網路

若要移除虛擬網路子網路，您可以使用 Azure 入口網站、PowerShell 或 Azure CLI。 如果您的 Azure 虛擬網路 IP 位址範圍或位址空間是一個彙總的範圍，Microsoft 就無須為您提供後續的協助。 （請注意，虛擬網路仍會傳播包含已刪除之子網的 BGP 路由位址空間）。您可能已將 Azure 虛擬網路位址範圍或位址空間定義為多個 IP 位址範圍，其中一個已指派給您已刪除的子網。 請務必先從您的虛擬網路位址空間中刪除該範圍。 接著，請通知「SAP HANA on Microsoft 服務管理」，以將其從可與 SAP HANA on Azure (大型執行個體) 通訊的範圍中移除。

如需詳細資訊，請參閱[刪除子網路](../../../virtual-network/virtual-network-manage-subnet.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-subnet)。

## <a name="delete-a-virtual-network"></a>刪除虛擬網路

如需相關資訊，請參閱[刪除虛擬網路](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-virtual-network)。

「SAP HANA on Microsoft 服務管理」會移除 SAP HANA on Azure (大型執行個體) ExpressRoute 線路的現有授權。 此外也會移除用來與 HANA 大型執行個體通訊的 Azure 虛擬網路 IP 位址範圍或位址空間。

移除虛擬網路之後，請建立 Azure 支援要求以提供要移除的一或多個 IP 位址空間範圍。

為了確保可移除所有項目，請刪除 ExpressRoute 連線、虛擬網路閘道、虛擬網路閘道公用 IP 和虛擬網路。

## <a name="delete-an-expressroute-circuit"></a>刪除 ExpressRoute 線路

若要移除額外的 SAP HANA on Azure (大型執行個體) ExpressRoute 線路，請透過「SAP HANA on Microsoft 服務管理」建立 Azure 支援要求。 接著要求刪除該線路。 您可以視需要在 Azure 訂用帳戶內刪除或保留虛擬網路。 不過，您必須將 HANA 大型執行個體 ExpressRoute 線路與連結的虛擬網路閘道之間的連線刪除。

## <a name="next-steps"></a>後續步驟

- [如何在 Azure 上安裝和設定 SAP HANA (大型執行個體)](hana-installation.md)
