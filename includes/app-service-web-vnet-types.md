---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: 41b7c7163ff56c848b1b66e606c06ba45d36e610
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419574"
---
* 支援除隔離之外的所有定價計劃的多租戶系統。
* 應用服務環境,部署到虛擬網路並支援隔離定價計劃應用。

VNet 整合功能用於多租戶應用。 如果應用位於[應用服務環境中][ASEintro],則它已位於虛擬網路中,並且不需要使用 VNet 集成功能來訪問同一虛擬網路中的資源。 有關所有網路功能的詳細資訊,請參閱[應用服務網路功能][Networkingfeatures]。

VNet 整合應用可以存取虛擬網路中的資源,但不會從虛擬網路授予對應用的入站專用存取許可權。 專用網站訪問是指僅從專用網路(如 Azure 虛擬網路內部)訪問應用。 VNet 整合僅用於從應用向虛擬網路進行出站呼叫。 當 VNet 整合功能與同一區域中的虛擬網路和其他地區的虛擬網路一起使用時,其行為方式會有所不同。 VNet 整合功能有兩種變體:

* **區域 VNet 整合**:連接到同一區域中的 Azure 資源管理器虛擬網路時,必須在要整合的虛擬網路中具有專用子網路。
* **閘道所需的 VNet 整合**:當您連接到其他區域中的虛擬網路或同一區域中的經典虛擬網路時,需要在目標虛擬網路中預配 Azure 虛擬網路閘道。

VNet 整合式功能:

* 需要標準、高級、高級 V2 或彈性高級定價計劃。
* 支援 TCP 和 UDP。
* 使用 Azure 應用服務應用和功能應用。

VNet 整合不支援某些內容,例如:

* 掛接磁碟機。
* 活動目錄集成。
* 內比奧斯。

閘道所需的 VNet 整合僅提供對目標虛擬網路或與目標虛擬網路連接到目標虛擬網路的與對等互連或 VPN 相連的資源的存取。 閘道所需的 VNet 整合無法存取 Azure ExpressRoute 連接中可用的資源,或無法與服務終結點配合使用。

無論使用何種版本,VNet 整合都能使應用存取虛擬網路中的資源,但不會從虛擬網路授予對應用的入站專用存取許可權。 專用網站訪問是指僅從專用網路(如 Azure 虛擬網路內部)使應用可訪問。 VNet 整合僅用於從應用向虛擬網路進行出站呼叫。

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features
