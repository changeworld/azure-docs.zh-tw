---
title: 什麼是 Azure DNS 私人區域
description: 私人 DNS 區域的總覽
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: a951bc07c4a8ed42b1c116332d13674656bbaafd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75646790"
---
# <a name="what-is-a-private-azure-dns-zone"></a>什麼是私人 Azure DNS 區域

Azure Private DNS 提供一個可靠、安全的 DNS 服務，讓您不必新增自訂 DNS 解決方案，就能管理及解析虛擬網路中的網域名稱。 藉由使用私人 DNS 區域，您就可以使用自己的自訂網域名稱，而不是現今可用的 Azure 提供名稱。 

私人 DNS 區域中包含的記錄無法從網際網路解析。 針對私人 DNS 區域的 DNS 解析只能從連結到它的虛擬網路運作。

您可以藉由建立[虛擬網路連結](./private-dns-virtual-network-links.md)，將私人 DNS 區域連結至一或多個虛擬網路。
您也可以啟用[自動註冊](./private-dns-autoregistration.md)功能，自動管理虛擬網路中部署之虛擬機器的 DNS 記錄生命週期。

## <a name="limits"></a>限制

若要瞭解您可以在訂用帳戶中建立多少私人 DNS 區域，以及私人 DNS 區域中支援多少個記錄集，請參閱[Azure DNS 限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits)

## <a name="restrictions"></a>限制

* 不支援單一標記的私人 DNS 區域。 您的私人 DNS 區域必須有兩個以上的標籤。 例如，contoso.com 有兩個以點分隔的標籤。 私人 DNS 區域最多可以有34標籤。
* 您無法在私人 DNS 區域中建立區域委派（NS 記錄）。 如果您想要使用子域，您可以直接建立網域作為私人 DNS 區域，並將它連結至虛擬網路，而不需要從父區域設定 nameserver 委派。

## <a name="next-steps"></a>後續步驟

* 了解如何使用 [Azure PowerShell](./private-dns-getstarted-powershell.md) 或 [Azure CLI](./private-dns-getstarted-cli.md)，在 Azure DNS 中建立私人區域。

* 請參閱一些常見[私人區域案例](./private-dns-scenarios.md)，這些案例可在 Azure DNS 中透過私人區域實現。

* 如需 Azure DNS 中私人區域的常見問題和解答，包括特定作業類型預期會有的特定行為，請參閱[私人 DNS 常見問題集](./dns-faq-private.md)。
