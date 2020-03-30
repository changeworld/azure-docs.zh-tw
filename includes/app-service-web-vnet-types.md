---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: da43e639779dd4946cb014be777e3a6db0366e24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671539"
---
* 支援全系列價格方案 (除隔離式方案以外) 的多租用戶系統
* 應用服務環境 （ASE），部署到您的 VNet 並支援隔離定價計畫應用

本文檔通過 VNet 集成功能，該功能用於多租戶應用。 如果您的應用程式位於 [App Service 環境][ASEintro]，則它已經在 VNet 中，並且不需要使用 VNet 整合功能來觸達相同 VNet 中的資源。 有關所有網路功能的詳細資訊，請閱讀[應用服務網路功能][Networkingfeatures]

VNet 集成使應用可以訪問虛擬網路中的資源，但不會從 VNet 授予對應用的入站專用存取權限。 專用網站訪問是指僅從私人網路絡（如 Azure 虛擬網路內部）訪問應用。 VNet 集成僅用於從應用向 VNet 進行出站呼叫。 當在同一區域中的 VNet 和與其他地區的 VNet 一起使用時，VNet 集成功能的行為會有所不同。 VNet 集成功能有兩種變體。

* 區域 VNet 集成 - 連接到同一區域中的資源管理器 VNet 時，必須在要集成的 VNet 中具有專用子網。 
* 閘道需要 VNet 集成 - 連接到其他區域中的 VNet 或同一區域中的經典 VNet 時，您需要在目標 VNet 中預配虛擬網路閘道。

VNet 集成功能：

* 需要標準、高級、高級V2或彈性高級定價計畫 
* 支援 TCP 和 UDP
* 使用應用服務應用和功能應用

VNet 整合不支援的事項包括：

* 掛接磁碟機
* AD 整合 
* NetBios

閘道所需的 VNet 集成僅提供對目標 VNet 或連接到目標 VNet 的具有對等或 VPN 的網路中的資源的訪問。 閘道所需的 VNet 集成無法訪問 ExpressRoute 連接中可用的資源，或無法與服務終結點配合使用。 

無論使用何種版本，VNet 集成都能使應用訪問虛擬網路中的資源，但不會從虛擬網路授予對應用的入站專用存取權限。 私人網站存取是指讓您的應用程式只能透過私人網路 (例如從 Azure 虛擬網路中) 存取。 VNet 集成僅用於從應用向 VNet 進行出站呼叫。 

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features