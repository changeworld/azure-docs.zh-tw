---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: da43e639779dd4946cb014be777e3a6db0366e24
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671539"
---
* 支援全系列價格方案 (除隔離式方案以外) 的多租用戶系統
* App Service 環境（ASE），它會部署到您的 VNet 並支援隔離的定價方案應用程式

本檔會逐步解說 VNet 整合功能，以供在多租使用者應用程式中使用。 如果您的應用程式在[App Service 環境][ASEintro]中，則它已在 vnet 中，不需要使用 vnet 整合功能來觸達相同 vnet 中的資源。 如需所有網路功能的詳細資訊，請參閱[App Service 網路功能][Networkingfeatures]

VNet 整合可讓您的應用程式存取虛擬網路中的資源，但不會從 VNet 授與應用程式的輸入私用存取權。 私用網站存取是指讓應用程式只能從私人網路（例如從 Azure 虛擬網路內）存取。 VNet 整合僅適用于從您的應用程式對 VNet 進行輸出呼叫。 當搭配相同區域中的 Vnet 和其他區域中的 Vnet 使用時，VNet 整合功能的行為會有所不同。 VNet 整合功能有兩種變化。

* 區域 VNet 整合-連線至相同區域中的 Resource Manager Vnet 時，您必須在要整合的 VNet 中具有專用的子網。 
* 閘道所需的 VNet 整合-連線到位於相同區域的其他區域或傳統 VNet 的 Vnet 時，您需要在目標 VNet 中布建虛擬網路閘道。

VNet 整合功能：

* 需要標準、Premium、PremiumV2 或彈性 Premium 定價方案 
* 支援 TCP 和 UDP
* 使用 App Service 應用程式和函數應用程式

VNet 整合不支援的事項包括：

* 掛接磁碟機
* AD 整合 
* NetBios

閘道所需的 VNet 整合只提供存取目標 VNet 中的資源，或使用對等互連或 Vpn 連線到目標 VNet 的網路。 閘道所需的 VNet 整合無法存取 ExpressRoute 連線可用的資源，或與服務端點搭配運作。 

不論使用的版本為何，VNet 整合都會讓您的應用程式存取虛擬網路中的資源，但不會將輸入私用存取權授與虛擬網路中的應用程式。 私人網站存取是指讓您的應用程式只能透過私人網路 (例如從 Azure 虛擬網路中) 存取。 VNet 整合僅適用于從您的應用程式對 VNet 進行輸出呼叫。 

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features