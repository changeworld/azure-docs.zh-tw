---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 04/15/2020
ms.author: ccompy
ms.openlocfilehash: c31a5aaa9866a4ce97cd3cd59a8e363834f70587
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312808"
---
* 支援除隔離之外的所有定價計劃的多租戶系統。
* 應用服務環境,部署到您的 VNet 並支持隔離定價計劃應用。

VNet 整合功能用於多租戶應用。 如果您的應用程式位於 [App Service 環境][ASEintro]，則它已經在 VNet 中，並且不需要使用 VNet 整合功能來觸達相同 VNet 中的資源。 有關所有網路功能的詳細資訊,請參閱[應用服務網路功能][Networkingfeatures]。

VNet 整合應用可以存取 VNet 中的資源,但不會從 VNet 授予對應用的入站專用存取許可權。 專用網站訪問是指僅從專用網路(如 Azure 虛擬網路內部)訪問應用。 VNet 整合僅用於從應用向 VNet 進行出站呼叫。 當 VNet 整合功能與同一區域中的 VNet 和其他地區的 VNet 一起使用時,其行為方式會有所不同。 VNet 整合功能有兩種變體:

* **區域 VNet 整合**:連接到同一區域中的 Azure 資源管理器虛擬網路時,必須在要整合的 VNet 中具有專用子網路。
* **閘道所需的 VNet 整合**:當您連接到其他區域中的 VNet 或同一區域中的經典虛擬網路時,需要在目標 VNet 中預配 Azure 虛擬網路閘道。

VNet 整合式功能:

* 需要標準、高級、高級 V2 或彈性高級定價計劃。
* 支援 TCP 和 UDP。
* 使用 Azure 應用服務應用和功能應用。

VNet 整合不支援某些內容,例如:

* 掛接磁碟機。
* 活動目錄集成。
* 內比奧斯。

閘道所需的 VNet 整合僅提供對目標 VNet 或連接到目標 VNet 的具有對等或 VPN 的網路中的資源的訪問。 閘道所需的 VNet 整合無法存取 Azure ExpressRoute 連接中可用的資源,或無法與服務終結點配合使用。

無論使用何種版本,VNet 整合都能使應用存取 VNet 中的資源,但不會從 VNet 授予對應用的入站專用存取許可權。 專用網站訪問是指僅從專用網路(如 Azure VNet 中)訪問應用。 VNet 整合僅用於從應用向 VNet 進行出站呼叫。

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features
