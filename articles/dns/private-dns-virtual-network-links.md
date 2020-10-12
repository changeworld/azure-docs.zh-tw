---
title: 什麼是 Azure DNS 私人區域的虛擬網路連結 subresource
description: 摘要說明虛擬網路連結子資源 a Azure DNS 私人區域
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 9181ef93dfedbc28b297bef48a0bc37ba6d69798
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "75646756"
---
# <a name="what-is-a-virtual-network-link"></a>什麼是虛擬網路連結？

一旦在 Azure 中建立私人 DNS 區域，就無法立即從任何虛擬網路存取。 您必須將它連結到虛擬網路，然後該網路中所裝載的 VM 才能存取私人 DNS 區域。
若要將私人 DNS 區域連結至虛擬網路，您必須在私人 DNS 區域下建立虛擬網路連結。 每個私人 DNS 區域都有一組虛擬網路連結子資源。 這些資源的每一個都代表一個虛擬網路的連接。

您可以將虛擬網路連結至私人 DNS 區域作為註冊虛擬網路，或連結為解析虛擬網路。

## <a name="registration-virtual-network"></a>註冊虛擬網路

當您建立私人 DNS 區域和虛擬網路之間 [的連結](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network) 時，您可以選擇開啟虛擬機器的 DNS 記錄 [自動註冊](./private-dns-autoregistration.md) 。 如果您選擇此選項，則虛擬網路會成為私人 DNS 區域的註冊虛擬網路。 系統會自動為您部署在網路中的虛擬機器建立 DNS 記錄。 系統會為您已在虛擬網路中部署的虛擬機器建立 DNS 記錄。 從虛擬網路的觀點來看，私人 DNS 區域會成為該虛擬網路的註冊區域。
一個私人 DNS 區域可以有多個註冊虛擬網路，但每個虛擬網路只能有一個相關聯的註冊區域。

## <a name="resolution-virtual-network"></a>解析虛擬網路

當您在私人 DNS 區域下建立虛擬網路連結，並選擇不啟用 DNS 記錄自動註冊時，會將虛擬網路視為僅解析虛擬網路。 在這類網路中部署之虛擬機器的 DNS 記錄，將不會在連結的私人 DNS 區域中自動建立。 不過，部署在這類網路中的虛擬機器可以成功查詢私人 DNS 區域中的 DNS 記錄。 您可以手動建立這些記錄，也可以從已連結為具有私人 DNS 區域之註冊網路的其他虛擬網路來加以填入。
一個私人 DNS 區域可以有多個解析虛擬網路，而一個虛擬網路可以有多個與其相關聯的解析區域。

## <a name="limits"></a>限制

若要瞭解多少註冊和解析網路，您可以連結至私人 DNS 區域，請參閱 [Azure DNS 限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits)

## <a name="other-considerations"></a>其他考量

* 不支援使用傳統部署模型部署的虛擬網路。

* 您只能在私人 DNS 區域和虛擬網路之間建立一個連結。

* 私人 DNS 區域下的每個虛擬網路連結，在私人 DNS 區域的內容中都必須有唯一的名稱。 您可以在不同的私人 DNS 區域中有相同名稱的連結。

* 建立虛擬網路連結之後，請檢查虛擬網路連結資源的 [連結狀態] 欄位。 視虛擬網路的大小而定，可能需要幾分鐘的時間，連結才會運作，而連結狀態會變更為 [ *已完成*]。

* 當您刪除虛擬網路時，會自動刪除在不同私人 DNS 區域中與其相關聯的所有虛擬網路連結和自動註冊的 DNS 記錄。

## <a name="next-steps"></a>後續步驟

* 瞭解如何使用[Azure 入口網站](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network)將虛擬網路連結至私人 DNS 區域

* 了解如何使用 [Azure PowerShell](./private-dns-getstarted-powershell.md) 或 [Azure CLI](./private-dns-getstarted-cli.md)，在 Azure DNS 中建立私人區域。

* 請參閱一些常見[私人區域案例](./private-dns-scenarios.md)，這些案例可在 Azure DNS 中透過私人區域實現。

* 如需 Azure DNS 中私人區域的常見問題和解答，包括特定作業類型預期會有的特定行為，請參閱[私人 DNS 常見問題集](./dns-faq-private.md)。
