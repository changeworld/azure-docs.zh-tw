---
title: 什麼是 Azure DNS 私人區域
description: 專用 DNS 區域概述
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: a951bc07c4a8ed42b1c116332d13674656bbaafd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646790"
---
# <a name="what-is-a-private-azure-dns-zone"></a>什麼是專用 Azure DNS 區域

Azure Private DNS 提供一個可靠、安全的 DNS 服務，讓您不必新增自訂 DNS 解決方案，就能管理及解析虛擬網路中的網域名稱。 藉由使用私人 DNS 區域，您就可以使用自己的自訂網域名稱，而不是現今可用的 Azure 提供名稱。 

專用 DNS 區域中包含的記錄無法從 Internet 解析。 針對專用 DNS 區域的 DNS 解析僅適用于連結到它的虛擬網路。

您可以通過創建[虛擬網路連結](./private-dns-virtual-network-links.md)將專用 DNS 區域連結到一個或多個虛擬網路。
您還可以啟用[自動註冊](./private-dns-autoregistration.md)功能，以自動管理部署在虛擬網路中的虛擬機器的 DNS 記錄的生命週期。

## <a name="limits"></a>限制

要瞭解可以在訂閱中創建多少個專用 DNS 區域，以及專用 DNS 區域中支援的記錄集數，請參閱[Azure DNS 限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits)

## <a name="restrictions"></a>限制

* 不支援單一標記的專用 DNS 區域。 您的專用 DNS 區域必須具有兩個或多個標籤。 例如contoso.com有兩個標籤由點分隔。 專用 DNS 區域最多可以有 34 個標籤。
* 不能在專用 DNS 區域中創建區域委派 （NS 記錄）。 如果要使用子域，可以直接將域創建為專用 DNS 區域，並將其連結到虛擬網路，而無需從父地區設定命名伺服器委派。

## <a name="next-steps"></a>後續步驟

* 了解如何使用 [Azure PowerShell](./private-dns-getstarted-powershell.md) 或 [Azure CLI](./private-dns-getstarted-cli.md)，在 Azure DNS 中建立私人區域。

* 請參閱一些常見[私人區域案例](./private-dns-scenarios.md)，這些案例可在 Azure DNS 中透過私人區域實現。

* 如需 Azure DNS 中私人區域的常見問題和解答，包括特定作業類型預期會有的特定行為，請參閱[私人 DNS 常見問題集](./dns-faq-private.md)。
