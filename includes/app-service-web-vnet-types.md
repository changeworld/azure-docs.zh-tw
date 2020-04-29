---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 04/15/2020
ms.author: ccompy
ms.openlocfilehash: c31a5aaa9866a4ce97cd3cd59a8e363834f70587
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81312808"
---
* 多租使用者系統，除了隔離外，支援完整範圍的定價方案。
* App Service 環境，它會部署到您的 VNet，並支援隔離的定價方案應用程式。

VNet 整合功能會用於多租使用者應用程式。 如果您的應用程式位於 [App Service 環境][ASEintro]，則它已經在 VNet 中，並且不需要使用 VNet 整合功能來觸達相同 VNet 中的資源。 如需所有網路功能的詳細資訊，請參閱[App Service 網路功能][Networkingfeatures]。

VNet 整合可讓您的應用程式存取 VNet 中的資源，但不會將輸入私用存取權授與 VNet 中的應用程式。 私用網站存取是指讓應用程式只能從私人網路（例如從 Azure 虛擬網路內）存取。 VNet 整合只能用來從您的應用程式對 VNet 進行輸出呼叫。 當 VNet 整合功能搭配相同區域中的 VNet 與其他區域中的 VNet 使用時，其行為會有所不同。 VNet 整合功能有兩種變化：

* **區域 VNet 整合**.. 當您連線至相同區域中 Azure Resource Manager 的虛擬網路時，您必須在要整合的 VNet 中具有專用的子網。
* **閘道所需的 Vnet 整合**：當您連線到位於相同區域中的其他區域或傳統虛擬網路的 vnet 時，您需要在目標 VNet 中布建 Azure 虛擬網路閘道。

VNet 整合功能：

* 需要標準、Premium、PremiumV2 或彈性 Premium 定價方案。
* 支援 TCP 和 UDP。
* 使用 Azure App Service 應用程式和函數應用程式。

VNet 整合不支援一些事項，例如：

* 掛接磁碟機。
* Active Directory 整合。
* NetBIOS.

閘道所需的 VNet 整合可讓您只存取目標 VNet 中的資源，或使用對等互連或 Vpn 連線到目標 VNet 的網路。 閘道所需的 VNet 整合無法存取跨 Azure ExpressRoute 連線可用的資源，或與服務端點搭配運作。

不論使用的版本為何，VNet 整合都會讓您的應用程式存取 VNet 中的資源，但不會將輸入私用存取權授與 VNet 中的應用程式。 私用網站存取是指讓您的應用程式只能從私人網路（例如從 Azure VNet 內）存取。 VNet 整合僅適用于從您的應用程式對 VNet 進行輸出呼叫。

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features
