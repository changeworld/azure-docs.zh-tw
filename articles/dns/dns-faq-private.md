---
title: Azure 私人 DNS 常見問題集
description: 在本文中，您將瞭解 Azure 私人 DNS 的常見問題
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 10/05/2019
ms.author: rohink
ms.openlocfilehash: d6faf5bd42c90ea1510d454a2ab7939f121b3d0d
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87925000"
---
# <a name="azure-private-dns-faq"></a>Azure 私人 DNS 常見問題集

以下是關於 Azure 私人 DNS 的常見問題。

## <a name="does-azure-dns-support-private-domains"></a>Azure DNS 是否支援私人網域？

使用 Azure 私人 DNS 區域功能可支援私人網域。 私人 DNS 區域只能從指定的虛擬網路中解析。 如需詳細資訊，請參閱[概觀](private-dns-overview.md)。

如需 Azure 中其他內部 DNS 選項的資訊，請參閱[vm 和角色實例的名稱解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)。

## <a name="will-azure-private-dns-zones-work-across-azure-regions"></a>Azure 私人 DNS 區域會跨 Azure 區域工作嗎？

是。 私人區域支援在跨 Azure 區域的虛擬網路之間進行 DNS 解析。 就算是沒有明確對虛擬網路進行對等，私人區域也能運作。 所有虛擬網路都必須連結到私人 DNS 區域。

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>針對私人區域，虛擬網路是否需要能夠連線至網際網路？

否。 私人區域能與虛擬網路搭配運作。 您可以使用它們來管理虛擬網路中的虛擬機器或其他資源的網域。 名稱解析並不需要任何網際網路連線能力。

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>相同的私人區域是否可以用於數個虛擬網路以進行解析？

是。 您可以將私人 DNS 區域與數千個虛擬網路連結。 如需詳細資訊，請參閱[Azure DNS 限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits)

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-linked-to-a-private-zone"></a>屬於不同訂用帳戶的虛擬網路是否可以連結至私人區域？

是。 您必須在虛擬網路及私人 DNS 區域上皆擁有寫入作業權限。 寫入權限可以授與數個 Azure 角色。 例如，傳統網路參與者 Azure 角色具有虛擬網路的寫入權限，而私人 DNS 區域參與者角色具有私人 DNS 區域的寫入權限。 如需 Azure 角色的詳細資訊，請參閱 azure[角色型存取控制 (AZURE RBAC) ](../role-based-access-control/overview.md)。

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>當您刪除虛擬機器時，會自動刪除私人區域中自動註冊的虛擬機器 DNS 記錄嗎？

是。 如果您在已啟用自動註冊的連結虛擬網路中刪除虛擬機器，則會自動刪除已註冊的記錄。

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>是否可以手動刪除來自連結虛擬網路的私人區域中自動註冊的虛擬機器記錄？

是。 您可以將自動註冊的 DNS 記錄覆寫為於區域中手動建立的 DNS 記錄。 下列問題和解答會說明這個議題。

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>當我嘗試手動建立新的 DNS 記錄到與連結的虛擬網路中的自動註冊現有虛擬機器具有相同主機名稱的私人區域時，會發生什麼事？

您嘗試在私人區域中手動建立新的 DNS 記錄，其主機名稱必須與連結的虛擬網路中現有、自動註冊的虛擬機器相同。 當您這樣做時，新的 DNS 記錄會覆寫自動註冊的虛擬機器記錄。 如果您再次嘗試從區域刪除此手動建立的 DNS 記錄，刪除作業會成功。 只要虛擬機器仍然存在且有附加私人 IP，就會再次自動註冊。 DNS 記錄會在區域中自動重新建立。

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>當我們將連結的虛擬網路與私人區域取消連結時，會發生什麼事？ 來自虛擬網路的自動註冊虛擬機器記錄是否也會從區域中移除？

是。 若要將連結的虛擬網路與私人區域取消連結，請更新 DNS 區域以移除相關聯的虛擬網路連結。 此程序也會從區域中移除自動註冊的虛擬機器記錄。

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>當我們刪除連結至私人區域的連結虛擬網路時，會發生什麼事？ 我們是否必須手動更新私人區域，才能將虛擬網路從區域取消連結虛擬網路？

否。 當您刪除連結的虛擬網路，而不先將它從私人區域取消連結時，您的刪除作業會成功，且 DNS 區域的連結會自動清除。

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>使用預設 FQDN 的 DNS 解析 (internal.cloudapp.net) 仍然可以正常執行，即使私人區域 (例如，private.contoso.com) 已連結至虛擬網路呢？

是。 私人區域不會取代預設的 Azure 提供的 internal.cloudapp.net 區域。 無論是仰賴由 Azure 所提供的 internal.cloudapp.net 或您自己的私人區域，都請使用您想要解析之目標區域的 FQDN。

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>已連結虛擬網路內之虛擬機器上的 DNS 尾碼，是否會變更成私人區域的尾碼？

否。 您已連結虛擬網路中虛擬機器上的 DNS 尾碼會保持為由 Azure 所提供的預設尾碼 ("*.internal.cloudapp.net")。 您可以手動將虛擬機器上的 DNS 尾碼變更為私人區域的尾碼。
如需如何變更此後綴的指引，請參閱[使用動態 DNS 在您自己的 DNS 伺服器中註冊主機名稱](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-ddns#windows-clients)

## <a name="what-are-the-usage-limits-for-azure-dns-private-zones"></a>Azure DNS 私人區域的使用限制為何？

如需 Azure DNS 私人區域的使用限制詳細資料，請參閱[Azure DNS 限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits)。

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>為什麼我現有的私人 DNS 區域不會顯示在新的入口網站體驗中？

如果您現有的私人 DNS 區域是使用預覽 API 建立的，您必須將這些區域遷移至新的資源模型。 使用預覽 API 建立的私人 DNS 區域不會顯示在新的入口網站體驗中。 如需如何遷移至新資源模型的指示，請參閱下文。

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>如何? 將現有的私人 DNS 區域遷移至新的模型？

我們強烈建議您儘快遷移至新的資源模型。 將支援舊版資源模型，不過，將不會在此模型之上開發進一步的功能。 在未來，我們打算以新的資源模型來取代它。 如需如何將您現有的私人 DNS 區域遷移至新資源模型的指引，請參閱[Azure DNS 私人區域的遷移指南](private-dns-migration-guide.md)。

## <a name="next-steps"></a>後續步驟

- [深入瞭解 Azure 私人 DNS](private-dns-overview.md)
