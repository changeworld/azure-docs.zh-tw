---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 10/01/2020
ms.author: ccompy
ms.openlocfilehash: 0b93111357cf0d6e57eeb5495d50bd18a15dca77
ms.sourcegitcommit: 31d242b611a2887e0af1fc501a7d808c933a6bf6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/29/2020
ms.locfileid: "97812798"
---
* 多組織使用者共用系統，可支援隔離以外的完整定價方案範圍。
* App Service 環境，其會部署至您的 VNet，並支援隔離的定價方案應用程式。

在多租使用者應用程式中使用 VNet 整合功能。 如果您的應用程式位於 [App Service 環境][ASEintro]，則它已經在 VNet 中，並且不需要使用 VNet 整合功能來觸達相同 VNet 中的資源。 如需所有網路功能的詳細資訊，請參閱 [App Service 網路功能][Networkingfeatures]。

VNet 整合可讓您的應用程式存取 VNet 中的資源，但不會將來自 VNet 的輸入私人存取權授與您的應用程式。 私人網站存取是指讓應用程式只能從私人網路存取，例如從 Azure 虛擬網路內。 VNet 整合僅用來從您的應用程式對 VNet 進行輸出呼叫。 當 VNet 整合功能與相同區域中的 VNet 和其他區域中的 VNet 搭配使用時，其行為會有所不同。 VNet 整合功能有兩種變化：

* **區域 VNet 整合**：當您連線到相同區域中 Azure Resource Manager 的虛擬網路時，您必須在您要整合的 VNet 中有一個專用子網。
* **閘道-必要的 Vnet 整合**：當您連線到其他區域中的 VNet 或相同區域中的傳統虛擬網路時，您需要在目標 VNet 中布建 Azure 虛擬網路閘道。

VNet 整合功能：

* 需要 Standard、Premium、>premiumv2、PremiumV3 或彈性 Premium 定價方案。
* 支援 TCP 和 UDP。
* 使用 Azure App Service 應用程式和函數應用程式。

VNet 整合不支援某些專案，例如：

* 掛接磁碟機。
* Active Directory 整合。
* NetBIOS.

閘道-必要的 VNet 整合可讓您只存取目標 VNet 中的資源，或使用對等互連或 Vpn 連線至目標 VNet 的網路。 閘道-必要的 VNet 整合無法存取可透過 Azure ExpressRoute 連線使用的資源，或使用服務端點。

無論使用的版本為何，VNet 整合可讓您的應用程式存取 VNet 中的資源，但不會將來自 VNet 的輸入私人存取權授與您的應用程式。 私人網站存取是指讓您的應用程式只能從私人網路（例如從 Azure VNet 內）存取。 VNet 整合僅適用于從您的應用程式對 VNet 進行的輸出呼叫。

<!--Links-->
[ASEintro]: ../articles/app-service/environment/intro.md
[Networkingfeatures]: ../articles/app-service/networking-features.md
