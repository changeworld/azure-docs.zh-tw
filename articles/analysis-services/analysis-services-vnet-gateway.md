---
title: 設定 Azure Analysis Services 以存取 VNet 資料來源 | Microsoft Docs
description: 了解如何設定 Azure Analysis Services 伺服器，以使用閘道存取 Azure 虛擬網路 (VNet) 上的資料來源。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 7622b6b5855850a0150f43863fa117828e23d4bd
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83698863"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>使用 Azure 虛擬網路 (VNet) 上的資料來源閘道

本文描述 Azure Analysis Services **AlwaysUseGateway** 伺服器屬性，可在資料來源位於 [Azure 虛擬網路 (VNet)](../virtual-network/virtual-networks-overview.md) 的情況下使用。

## <a name="server-access-to-vnet-data-sources"></a>對 VNet 資料來源的伺服器存取

如果透過 VNET 存取資料來源，則在您自己的環境中，Azure Analysis Services 伺服器必須像在內部部署一樣地連線至這些資料來源。 您可以設定 **AlwaysUseGateway** 伺服器屬性，以指定伺服器透過[內部部署閘道](analysis-services-gateway.md)存取所有資料來源資料。 

Azure SQL Database 受控執行個體資料來源在具有私人 IP 位址的 Azure VNet 中執行。 如果實例上啟用公用端點，則不需要閘道。 如果未啟用公用端點，則需要內部部署的資料閘道，而且 AlwaysUseGateway 屬性必須設定為 true。

> [!NOTE]
> 只有在已安裝和設定[內部部署的資料閘道](analysis-services-gateway.md)時，這個屬性才有效。 閘道可以位於 VNet 上。

## <a name="configure-alwaysusegateway-property"></a>設定 AlwaysUseGateway 屬性

1. 在 [SSMS] > [伺服器] > [屬性] > [一般]，選取 [顯示進階 (所有) 屬性]。
2. 在 **ASPaaS\AlwaysUseGateway** 中，選取 [true]。

    ![一律使用閘道屬性](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>另請參閱
[連線至內部部署資料來源](analysis-services-gateway.md)   
[安裝及設定內部部署資料閘道](analysis-services-gateway-install.md)   
[Azure 虛擬網路 (VNET)](../virtual-network/virtual-networks-overview.md)   

