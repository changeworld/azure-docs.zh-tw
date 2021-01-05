---
title: " (Azure Private Link 常見問題的常見問題) "
description: 瞭解 Azure Private Link。
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: allensu
ms.openlocfilehash: c074c29b7a37f49d5a4c7a5fab00b9a3e41c6893
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901533"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a> (Azure Private Link 常見問題的常見問題) 

## <a name="private-link"></a>私人連結

### <a name="what-is-azure-private-endpoint-and-azure-private-link-service"></a>什麼是 Azure 私人端點和 Azure Private Link 服務？

- **[Azure 私人端點](private-endpoint-overview.md)**： Azure 私人端點是一種網路介面，可讓您以私人且安全的方式連接到 Azure Private Link 的服務。 您可以使用私人端點來連接到支援 Private Link 或您自己的 Private Link 服務的 Azure PaaS 服務。
- **[Azure Private Link 服務](private-link-service-overview.md)**： Azure Private Link 服務是服務提供者所建立的服務。 目前，Private Link 服務可以附加至 Standard Load Balancer 的前端 IP 設定。 

### <a name="how-is-traffic-being-sent-when-using-private-link"></a>使用 Private Link 時如何傳送流量？
流量會使用 Microsoft 骨幹私下傳送。 它不會跨越網際網路。 Azure Private Link 不會儲存客戶資料。
 
### <a name="what-is-the-difference-between-a-service-endpoints-and-a-private-endpoints"></a>服務端點和私人端點之間有何差異？
- 私人端點會將網路存取權授與指定服務後方的特定資源，提供細微的分割。 流量可以從內部部署連線到服務資源，而不需使用公用端點。
- 服務端點會保持可公開路由傳送的 IP 位址。  私人端點是私人端點設定所在的虛擬網路位址空間中的私人 IP。

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>Private Link 服務和私人端點之間的關係為何？
多個私人連結資源類型支援透過私人端點的存取。 資源包括 Azure PaaS 服務，以及您自己的 Private Link 服務。 這是一對多關聯性。 

Private Link 服務會接收來自多個私人端點的連接。 私人端點會連接到一個 Private Link 服務。    

## <a name="private-endpoint"></a>私人端點 
 
### <a name="can-i-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>是否可以在相同的 VNet 中建立多個私人端點？ 他們是否可以連接到不同的服務？ 
是。 您可以在相同的 VNet 或子網中有多個私人端點。 他們可以連接至不同的服務。  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>私人端點是否需要專用子網？ 
不會。 私人端點不需要專用子網。 您可以從部署服務的 VNet 中，選擇來自任何子網的私人端點 IP。  
 
### <a name="can-a-private-endpoint-connect-to-private-link-services-across-azure-active-directory-tenants"></a>私人端點是否可以連接到跨 Azure Active Directory 租使用者的 Private Link 服務？ 
是。 私人端點可以跨 Azure Active Directory 租使用者連接 Private Link 服務或 Azure PaaS。 跨租使用者連接的私人端點需要手動要求核准。 
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>私人端點是否可以跨 Azure 區域連接到 Azure PaaS 資源？
是。 私人端點可以跨 Azure 區域連接到 Azure PaaS 資源。

### <a name="can-i-modify-my-private-endpoint-network-interface-nic-"></a>我可以 (NIC) 來修改我的私人端點網路介面嗎？
建立私人端點時，會指派唯讀的 NIC。 這無法修改，而且會保留在私人端點的生命週期中。

## <a name="private-link-service"></a>Private Link 服務
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>建立 Private Link 服務的必要條件為何？ 
您的服務後端應該位於虛擬網路中，並在 Standard Load Balancer 後方。
 
### <a name="how-can-i-scale-my-private-link-service"></a>如何調整 Private Link 服務的規模？ 
您可以透過幾種不同的方式來調整 Private Link 服務： 
- 將後端 Vm 新增至您 Standard Load Balancer 後方的集區 
- 將 IP 新增至 Private Link 服務。 每 Private Link 服務最多允許8個 Ip。  
- 將新的 Private Link 服務新增至 Standard Load Balancer。 每個負載平衡器最多可以有八個 Private Link 服務。   

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>我可以將服務連線至多個私人端點嗎？
是。 一個 Private Link 服務可以接收來自多個私人端點的連接。 不過，一個私人端點只能連接到一個 Private Link 服務。  
 
### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>如何控制 Private Link 服務的公開？
您可以使用 Private Link 服務上的可見度設定來控制暴露的風險。 可見度支援三項設定：

- **無** -只有 Azure RBAC 存取的訂用帳戶可以找到服務。 
- 僅限 **限制** 的訂用帳戶與 Azure RBAC 存取權可以找到該服務。 
- **所有** 人都可以找到該服務。 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>我可以使用基本負載平衡器建立 Private Link 服務嗎？ 
不會。 不支援基本負載平衡器上的 Private Link 服務。
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>Private Link 服務需要專用的子網嗎？ 
不會。 Private Link 服務不需要專用子網。 您可以在 VNet 中選擇您的服務部署所在的任何子網。   

### <a name="im-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>我是使用 Azure Private Link 的服務提供者。 我是否需要確定所有的客戶都有唯一的 IP 空間，而不會與 IP 空間重迭？ 
不會。 Azure Private Link 為您提供這種功能。 您不一定要有與客戶位址空間不重迭的位址空間。 

##  <a name="next-steps"></a>後續步驟

- 瞭解 [Azure Private Link](private-link-overview.md)
