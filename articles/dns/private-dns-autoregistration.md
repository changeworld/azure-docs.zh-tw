---
title: 什麼是 Azure DNS 私人區域的自動註冊功能
description: Azure DNS 私人區域的自動註冊功能總覽
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 9d1854b459e799d5cbb401de9ac717dd7d0fde1d
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "71961252"
---
# <a name="what-is-the-autoregistration-feature-of-azure-dns-private-zones"></a>Azure DNS 私人區域的自動註冊功能為何

Azure DNS 私人區域自動註冊功能會讓部署在虛擬網路中的虛擬機器進行 DNS 記錄管理的困難。 當您[連結虛擬網路](./private-dns-virtual-network-links.md)與私人 dns 區域，並啟用所有虛擬機器的自動註冊時，部署在虛擬網路中的虛擬機器 DNS 記錄會自動建立在私人 dns 區域中。 除了向前查詢記錄（A 記錄）之外，也會自動為虛擬機器建立反向對應記錄（PTR 記錄）。
如果您將更多虛擬機器新增至虛擬網路，則也會在連結的私人 DNS 區域中自動建立這些虛擬機器的 DNS 記錄。

當您刪除虛擬機器時，會自動從私人 DNS 區域中刪除虛擬機器的 DNS 記錄。

您可以在建立虛擬網路連結時，選取 [啟用自動註冊] 選項來啟用自動註冊。

![啟用自動註冊](./media/privatedns-concepts/enable-autoregistration.png)

## <a name="restrictions"></a>限制

* 自動註冊僅適用于虛擬機器。 對於內部負載平衡器等所有其他資源，您可以在連結至虛擬網路的私人 DNS 區域中手動建立 DNS 記錄。
* 只有主要虛擬機器 NIC 會自動建立 DNS 記錄。 如果您的虛擬機器有一個以上的 NIC，您可以手動建立其他網路介面的 DNS 記錄。
* 不支援 IPv6 的自動註冊（AAAA 記錄）。

## <a name="next-steps"></a>後續步驟

* 了解如何使用 [Azure PowerShell](./private-dns-getstarted-powershell.md) 或 [Azure CLI](./private-dns-getstarted-cli.md)，在 Azure DNS 中建立私人區域。

* 請參閱一些常見[私人區域案例](./private-dns-scenarios.md)，這些案例可在 Azure DNS 中透過私人區域實現。

* 如需 Azure DNS 中私人區域的常見問題和解答，包括特定作業類型預期會有的特定行為，請參閱[私人 DNS 常見問題集](./dns-faq-private.md)。
