---
title: Azure DNS 私人區域的自動註冊功能是什麼
description: Azure DNS 私人區域自動註冊功能概述
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 9d1854b459e799d5cbb401de9ac717dd7d0fde1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "71961252"
---
# <a name="what-is-the-autoregistration-feature-of-azure-dns-private-zones"></a>Azure DNS 私人區域的自動註冊功能是什麼

Azure DNS 私人區域自動註冊功能使部署在虛擬網路中的虛擬機器的 DNS 記錄管理帶來的痛苦。 當您[將虛擬網路](./private-dns-virtual-network-links.md)與專用 DNS 區域連結並對所有虛擬機器啟用自動註冊時，在專用 DNS 區域中自動創建部署在虛擬網路中的虛擬機器的 DNS 記錄。 除了前瞻查找記錄 （A 記錄），還會自動為虛擬機器創建反向對應記錄（PTR 記錄）。
如果將更多虛擬機器添加到虛擬網路，則這些虛擬機器的 DNS 記錄也會在連結的專用 DNS 區域中自動創建。

刪除虛擬機器時，虛擬機器的 DNS 記錄將自動從專用 DNS 區域中刪除。

您可以在創建虛擬網路連結時選擇"啟用自動註冊"選項來啟用自動註冊。

![啟用自動註冊](./media/privatedns-concepts/enable-autoregistration.png)

## <a name="restrictions"></a>限制

* 自動註冊僅適用于虛擬機器。 對於所有其他資源（如內部負載等化器等），您可以在連結到虛擬網路的專用 DNS 區域中手動創建 DNS 記錄。
* DNS 記錄僅針對主虛擬機器 NIC 自動創建。 如果您的虛擬機器有多個 NIC，則可以手動為其他網路介面創建 DNS 記錄。
* 不支援自動註冊 IPv6（AAAA 記錄）。

## <a name="next-steps"></a>後續步驟

* 了解如何使用 [Azure PowerShell](./private-dns-getstarted-powershell.md) 或 [Azure CLI](./private-dns-getstarted-cli.md)，在 Azure DNS 中建立私人區域。

* 請參閱一些常見[私人區域案例](./private-dns-scenarios.md)，這些案例可在 Azure DNS 中透過私人區域實現。

* 如需 Azure DNS 中私人區域的常見問題和解答，包括特定作業類型預期會有的特定行為，請參閱[私人 DNS 常見問題集](./dns-faq-private.md)。
