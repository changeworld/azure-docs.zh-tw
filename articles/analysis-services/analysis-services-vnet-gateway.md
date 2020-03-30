---
title: 為 VNet 資料來源配置 Azure 分析服務 |微軟文檔
description: 瞭解如何配置 Azure 分析服務伺服器以在 Azure 虛擬網路 （VNet） 上使用資料來源的閘道。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 384f4c2ee3021678c530aa5c98eaf4dbfd661e95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572270"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>使用 Azure 虛擬網路 (VNet) 上的資料來源閘道

本文介紹 Azure 分析服務**AlwaysUseGateway**伺服器屬性，用於資料來源位於[Azure 虛擬網路 （VNet）](../virtual-network/virtual-networks-overview.md)上時使用。

## <a name="server-access-to-vnet-data-sources"></a>對 VNet 資料來源的伺服器存取

如果透過 VNET 存取資料來源，則在您自己的環境中，Azure Analysis Services 伺服器必須像在內部部署一樣地連線至這些資料來源。 您可以配置**AlwaysUseGateway**伺服器屬性來指定伺服器通過[本地閘道](analysis-services-gateway.md)訪問所有資料來源。 

Azure SQL 資料庫託管實例資料來源在 Azure VNet 中運行，具有私人 IP 位址。 如果在實例上啟用了公共終結點，則不需要閘道。 如果未啟用公共終結點，則需要本地資料閘道，並且必須將 AlwaysUseGateway 屬性設置為 true。

> [!NOTE]
> 僅當安裝並配置[本地資料閘道](analysis-services-gateway.md)時，此屬性才有效。 閘道可以位於 VNet 上。

## <a name="configure-alwaysusegateway-property"></a>設定 AlwaysUseGateway 屬性

1. 在 SSMS >伺服器>**屬性** > **一般**，選擇**顯示高級（全部）屬性**。
2. 在 **ASPaaS\AlwaysUseGateway** 中，選取 [true]****。

    ![一律使用閘道屬性](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>另請參閱
[連接到本地資料來源](analysis-services-gateway.md)   
[安裝和配置本地資料閘道](analysis-services-gateway-install.md)   
[Azure 虛擬網路 （VNET）](../virtual-network/virtual-networks-overview.md)   

