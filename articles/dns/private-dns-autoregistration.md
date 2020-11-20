---
title: Azure DNS 私人區域的自動註冊功能
description: Azure DNS 私人區域的自動註冊功能總覽
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 8e976199730ec74f9e76b9f4199e90e192916474
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966712"
---
# <a name="what-is-the-autoregistration-feature-of-azure-dns-private-zones"></a>Azure DNS 私人區域的自動註冊功能為何

Azure DNS 私人區域自動註冊功能，可讓您在虛擬網路中部署的虛擬機器進行 DNS 記錄管理的難題。 當您將 [虛擬網路連結](./private-dns-virtual-network-links.md) 至私人 dns 區域，並啟用所有虛擬機器的自動註冊時，會在私人 DNS 區域中自動建立部署在虛擬網路中的虛擬機器 DNS 記錄。 除了向前查看記錄 (記錄) ，也會自動為虛擬機器建立反向對應記錄 (PTR 記錄) 。
如果您將更多虛擬機器新增至虛擬網路，則這些虛擬機器的 DNS 記錄也會自動在連結的私人 DNS 區域中建立。

當您刪除虛擬機器時，系統會自動從私人 DNS 區域中刪除虛擬機器的 DNS 記錄。

您可以在建立虛擬網路連結時選取 [啟用自動註冊] 選項，以啟用自動註冊。

![啟用自動註冊](./media/privatedns-concepts/enable-autoregistration.png)

## <a name="restrictions"></a>限制

* 自動註冊僅適用于虛擬機器。 針對其他所有資源（例如內部負載平衡器等），您可以在連結至虛擬網路的私人 DNS 區域中手動建立 DNS 記錄。
* 只會針對主要虛擬機器 NIC 自動建立 DNS 記錄。 如果您的虛擬機器有一個以上的 NIC，您可以手動建立其他網路介面的 DNS 記錄。
* 只有在主要虛擬機器 NIC 使用 DHCP 時，才會自動建立 DNS 記錄。 如果已設定靜態 IP 位址 (例如，若要 [在 Azure) 中使用多個 IP 位址](../virtual-network/virtual-network-multiple-ip-addresses-portal.md#os-config) ，自動註冊不會建立該虛擬機器的記錄。
* 不支援自動註冊的 IPv6 (AAAA 記錄) 。

## <a name="next-steps"></a>後續步驟

* 了解如何使用 [Azure PowerShell](./private-dns-getstarted-powershell.md) 或 [Azure CLI](./private-dns-getstarted-cli.md)，在 Azure DNS 中建立私人區域。

* 請參閱一些常見[私人區域案例](./private-dns-scenarios.md)，這些案例可在 Azure DNS 中透過私人區域實現。

* 如需 Azure DNS 中私人區域的常見問題和解答，包括特定作業類型預期會有的特定行為，請參閱[私人 DNS 常見問題集](./dns-faq-private.md)。