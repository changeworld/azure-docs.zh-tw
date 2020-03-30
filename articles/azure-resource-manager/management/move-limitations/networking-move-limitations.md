---
title: 將 Azure 網路資源移動到新訂閱或資源組
description: 使用 Azure 資源管理器將虛擬網路和其他網路資源移動到新的資源組或訂閱。
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: 0cd6887d3489f2ffede0f5e3d63533a33a6ccc04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485230"
---
# <a name="move-guidance-for-networking-resources"></a>移動網路資源的指南

本文介紹如何針對特定方案移動虛擬網路和其他網路資源。

## <a name="dependent-resources"></a>相依資源

當您移動虛擬網路時，也必須移動其相依資源。 針對 VPN 閘道，您必須移動 IP 位址、虛擬網路閘道和所有相關聯的連線資源。 區域網路閘道可位於不同的資源群組。

要將具有網路介面卡的虛擬機器移動到新訂閱，必須移動所有從屬資源。 移動網路介面卡的虛擬網路、虛擬網路的所有其他網路介面卡和 VPN 閘道。

有關詳細資訊，請參閱[跨訂閱移動的方案](../move-resource-group-and-subscription.md#scenario-for-move-across-subscriptions)。

## <a name="peered-virtual-network"></a>對等虛擬網路

若要移動對等虛擬網路，您必須先停用虛擬網路對等互連。 停用之後，您可以移動虛擬網路。 移動之後，重新啟用虛擬網路對等互連。

## <a name="subnet-links"></a>子網連結

如果虛擬網路包含有資源導覽連結的子網路，則無法將虛擬網路移動到其他訂用帳戶。 例如，如果 Azure Cache for Redis 資源部署到子網路，該子網路具有資源導覽連結。

## <a name="next-steps"></a>後續步驟

如需用來移動資源的命令，請參閱[將資源移動到新的資源群組或訂用帳戶](../move-resource-group-and-subscription.md)。
