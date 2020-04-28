---
title: 為 VNet 資料來源設定 Azure Analysis Services |Microsoft Docs
description: 瞭解如何設定 Azure Analysis Services 伺服器，以使用 Azure 虛擬網路（VNet）上的資料來源閘道。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 384f4c2ee3021678c530aa5c98eaf4dbfd661e95
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "73572270"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>使用 Azure 虛擬網路 (VNet) 上的資料來源閘道

本文說明在[azure 虛擬網路（VNet）](../virtual-network/virtual-networks-overview.md)上的資料來源時，所要使用的 Azure Analysis Services **AlwaysUseGateway**伺服器屬性。

## <a name="server-access-to-vnet-data-sources"></a>對 VNet 資料來源的伺服器存取

如果透過 VNET 存取資料來源，則在您自己的環境中，Azure Analysis Services 伺服器必須像在內部部署一樣地連線至這些資料來源。 您可以設定 [ **AlwaysUseGateway**伺服器] 屬性，以指定要透過內部[部署閘道](analysis-services-gateway.md)存取所有資料來源的伺服器。 

Azure SQL Database 受控執行個體資料來源會在具有私人 IP 位址的 Azure VNet 中執行。 如果在實例上啟用公用端點，則不需要閘道。 如果未啟用公用端點，則需要內部部署資料閘道，而且 AlwaysUseGateway 屬性必須設定為 true。

> [!NOTE]
> 只有在已安裝並設定內部[部署資料閘道](analysis-services-gateway.md)時，此屬性才會生效。 閘道可以位於 VNet 上。

## <a name="configure-alwaysusegateway-property"></a>設定 AlwaysUseGateway 屬性

1. 在 SSMS > 伺服器 >**屬性** > ]**[一般**] 中，選取 [**顯示 Advanced （全部）] 屬性**。
2. 在 **ASPaaS\AlwaysUseGateway** 中，選取 [true]****。

    ![一律使用閘道屬性](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>另請參閱
[連接到內部部署資料來源](analysis-services-gateway.md)   
[安裝和設定內部部署資料閘道](analysis-services-gateway-install.md)   
[Azure 虛擬網路（VNET）](../virtual-network/virtual-networks-overview.md)   

