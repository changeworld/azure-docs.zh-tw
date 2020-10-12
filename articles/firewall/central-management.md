---
title: Azure 防火牆中央管理
description: 瞭解 Azure 防火牆管理員中央管理
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: victorh
ms.openlocfilehash: 23a7682d8a64de57db4ff9ae785ada90d4a06944
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87084665"
---
# <a name="azure-firewall-central-management"></a>Azure 防火牆中央管理

如果您管理多個防火牆，您知道持續變更的防火牆規則會讓它們難以保持同步。中央 IT 團隊需要一種方式來定義基本防火牆原則，並在多個營業單位中強制執行這些原則。 同時，DevOps 團隊想要建立自己的本機衍生防火牆原則，以提高靈活性。

Azure 防火牆管理員有助於解決這些問題。


## <a name="azure-firewall-manager"></a>Azure 防火牆管理員

Azure 防火牆管理員是網路安全性管理服務，可為雲端式安全性周邊提供集中的安全性原則及路由管理。 企業 IT 團隊可以輕鬆地針對跨多個 Azure 防火牆實例的流量篩選，集中定義網路和應用層級規則。 您可以跨越中樞和輪輻架構中的不同 Azure 區域和訂用帳戶，以進行流量治理和保護。 它也利用跨組織實行的衍生本機防火牆安全性原則，提供 DevOps 更好的靈活性。

### <a name="firewall-policy"></a>防火牆原則

防火牆原則是一種 Azure 資源，其中包含 NAT、網路和應用程式規則集合，以及威脅情報設定。 它是全域資源，可在 *安全虛擬中樞* 和 *中樞虛擬網路*中的多個 Azure 防火牆實例上使用。 您可從頭開始建立新原則，或繼承自現有的原則。 繼承可讓 DevOps 在組織規定的基本原則之上建立本機防火牆原則。 原則可跨地區和訂用帳戶運作。
 
您可以使用 Azure 防火牆管理員建立防火牆原則和關聯。 不過，您也可以使用 REST API、範本、Azure PowerShell 和 CLI 來建立和管理原則。 建立原則之後，您可以將它與虛擬 WAN 中樞內的防火牆建立關聯，使其成為 *受保護的虛擬中樞* 及/或虛擬網路中的防火牆，使其成為 *中樞虛擬網路*。

### <a name="pricing"></a>定價

原則會根據防火牆關聯計費。 具有零個或一個防火牆關聯的原則是免費的。 具有多個防火牆關聯的原則會以固定費率計費。 如需詳細資訊，請參閱 [Azure 防火牆管理員定價](https://azure.microsoft.com/pricing/details/firewall-manager/)。

## <a name="azure-firewall-management-partners"></a>Azure 防火牆管理合作夥伴

下列領先的協力廠商解決方案支援使用標準 Azure REST Api 的 Azure 防火牆中央管理。 其中每個解決方案都有自己獨特的特性和功能：

- [AlgoSec CloudFlow](https://www.algosec.com/azure/) 
- [Barracuda Cloud Security 監護人](https://www.barracuda.com/products/cloudsecurityguardian/for_azure)
- [Tufin Orca](https://www.tufin.com/products/tufin-orca)


## <a name="next-steps"></a>接下來的步驟

如需 Azure 防火牆管理員的詳細資訊，請參閱 [Azure 防火牆管理員是什麼？](../firewall-manager/overview.md)