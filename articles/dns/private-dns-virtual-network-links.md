---
title: 什麼是 Azure DNS 私人區域的虛擬網路鏈路子資源
description: 虛擬網路鏈路子資源 Azure DNS 私人區域概述
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 9181ef93dfedbc28b297bef48a0bc37ba6d69798
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646756"
---
# <a name="what-is-a-virtual-network-link"></a>什麼是虛擬網路連結？

在 Azure 中創建專用 DNS 區域後，不會立即從任何虛擬網路訪問該區域。 您必須將其連結到虛擬網路，然後才能訪問專用 DNS 區域。
要將專用 DNS 區域與虛擬網路連結，必須在專用 DNS 區域下創建虛擬網路連結。 每個專用 DNS 區域都有一個虛擬網路連結子資源的集合。 這些資源中的每一個都表示與虛擬網路的連接。

您可以將虛擬網路連結到專用 DNS 區域，作為註冊虛擬網路或解析度虛擬網路。

## <a name="registration-virtual-network"></a>註冊虛擬網路

在專用 DNS 區域和虛擬網路之間[創建連結](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network)時，您可以選擇打開虛擬機器的 DNS 記錄[自動註冊](./private-dns-autoregistration.md)。 如果選擇此選項，虛擬網路將成為專用 DNS 區域的註冊虛擬網路。 將自動為網路中部署的虛擬機器創建 DNS 記錄。 DNS 記錄是為已在虛擬網路中部署的虛擬機器創建的。 從虛擬網路的角度來看，專用 DNS 區域將成為該虛擬網路的註冊區域。
一個專用 DNS 區域可以有多個註冊虛擬網路，但每個虛擬網路可以有一個與之關聯的註冊區域。

## <a name="resolution-virtual-network"></a>解析虛擬網路

當您在專用 DNS 區域下創建虛擬網路連結並選擇不啟用 DNS 記錄自動註冊時，虛擬網路將被視為僅解決虛擬網路。 部署在此類網路中的虛擬機器的 DNS 記錄不會自動在連結的專用 DNS 區域中創建。 但是，部署在此類網路中的虛擬機器可以從專用 DNS 區域成功查詢 DNS 記錄。 這些記錄可能由您手動創建，也可以從已作為註冊網路與專用 DNS 區域連結的其他虛擬網路填充。
一個專用 DNS 區域可以有多個解析度虛擬網路，而虛擬網路可以有多個解析區域與之關聯。

## <a name="limits"></a>限制

要瞭解有多少註冊和解決網路，可以連結到專用 DNS 區域，請參閱[Azure DNS 限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits)

## <a name="other-considerations"></a>其他考量

* 不支援使用經典部署模型部署的虛擬網路。

* 只能在專用 DNS 區域和虛擬網路之間創建一個鏈路。

* 專用 DNS 區域下的每個虛擬網路連結都必須在專用 DNS 區域的上下文中具有唯一名稱。 您可以在不同的專用 DNS 區域中具有同名連結。

* 創建虛擬網路連結後，請檢查虛擬網路連結資源的"連結狀態"欄位。 根據虛擬網路的大小，可能需要幾分鐘時間才能運行鏈路，並將鏈路狀態更改為 *"已完成*"。

* 刪除虛擬網路時，將會自動刪除與虛擬網路關聯的不同專用 DNS 區域中與其關聯的所有虛擬網路連結和自動註冊的 DNS 記錄。

## <a name="next-steps"></a>後續步驟

* 瞭解如何使用[Azure 門戶](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network)將虛擬網路連結到專用 DNS 區域

* 了解如何使用 [Azure PowerShell](./private-dns-getstarted-powershell.md) 或 [Azure CLI](./private-dns-getstarted-cli.md)，在 Azure DNS 中建立私人區域。

* 請參閱一些常見[私人區域案例](./private-dns-scenarios.md)，這些案例可在 Azure DNS 中透過私人區域實現。

* 如需 Azure DNS 中私人區域的常見問題和解答，包括特定作業類型預期會有的特定行為，請參閱[私人 DNS 常見問題集](./dns-faq-private.md)。
