---
title: Azure 專用連結常見問題 （FAQ）
description: 瞭解 Azure 專用連結。
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 7870b68ca931123d50e88e846aa066ce53972dbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75349928"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Azure 專用連結常見問題 （FAQ）

## <a name="private-link"></a>私人連結

### <a name="what-is-azure-private-endpoint-and-azure-private-link-service"></a>什麼是 Azure 專用終結點和 Azure 專用連結服務？

- **[Azure 專用終結點](private-endpoint-overview.md)**：Azure 專用終結點是一個網路介面，可私下安全地連接到由 Azure 專用連結提供支援的服務。 可以使用專用終結點連接到支援專用連結的 Azure PaaS 服務或您自己的專用連結服務。
- **[Azure 專用連結服務](private-link-service-overview.md)**：Azure 專用連結服務是由服務提供者創建的服務。 目前，專用鏈路服務可以連接到標準負載等化器的前端 IP 配置。 

### <a name="how-is-traffic-being-sent-when-using-private-link"></a>使用專用鏈路時流量是如何發送的？
流量使用 Microsoft 主幹進行私下發送。 它不會穿過互聯網。  
 
### <a name="what-is-the-difference-between-a-service-endpoints-and-a-private-endpoints"></a>服務終結點和專用終結點之間的區別是什麼？
- 使用專用終結點時，將授予提供細微性分段的給定服務背後的特定資源的網路存取權限，流量也可以從本地到達服務資源，而無需使用公共終結點。
- 服務終結點仍然是可公開路由的 IP 位址。  專用終結點是配置專用終結點的虛擬網路位址空間中的專用 IP。

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>專用連結服務和專用終結點之間的關係是什麼？
專用終結點提供對多個專用連結資源類型的訪問，包括 Azure PaaS 服務和您自己的專用連結服務。 這是一對多的關係。 一個專用連結服務可以從多個專用終結點接收連接。 另一方面，一個專用終結點只能連接到一個專用連結服務。    

## <a name="private-endpoint"></a>私人端點 
 
### <a name="can-i-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>我可以在同一 VNet 中創建多個專用終結點嗎？ 他們能否連接到不同的服務？ 
是。 可以在同一 VNet 或子網中有多個專用終結點。 它們可以連接到不同的服務。  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>是否需要專用終結點的專用子網？ 
否。 不需要專用終結點的專用子網。 您可以從部署服務的 VNet 中的任何子網中選擇專用終結點 IP。  
 
### <a name="can-private-endpoint-connect-to-private-link-service-across-azure-active-directory-tenants"></a>私有終結點是否可以跨 Azure 活動目錄租戶連接到專用連結服務？ 
是。 專用終結點可以跨 AD 租戶連接到專用連結服務或 Azure PaaS。  
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>私有終結點是否可以跨 Azure 區域連接到 Azure PaaS 資源？
是。 專用終結點可以跨 Azure 區域連接到 Azure PaaS 資源。

## <a name="private-link-service"></a>Private Link 服務
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>創建專用連結服務的先決條件是什麼？ 
服務後端應位於虛擬網路和標準負載等化器後面。
 
### <a name="how-can-i-scale-my-private-link-service"></a>如何擴展我的專用連結服務？ 
您可以通過幾種不同的方式擴展專用連結服務： 
- 將後端 VM 添加到標準負載等化器後面的池中 
- 向專用連結服務添加 IP。 我們允許每個專用鏈路服務最多 8 個 IP。  
- 向標準負載等化器添加新的專用鏈路服務。 我們允許每個負載等化器最多提供 8 個專用鏈路服務。   

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>我可以將我的服務連接到多個專用終結點嗎？
是。 一個專用連結服務可以從多個專用終結點接收連接。 但是，一個專用終結點只能連接到一個專用連結服務。  
 
### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>如何控制專用連結服務的曝光？
您可以使用專用連結服務上的可見度配置來控制曝光。 可見度支援三種設置：

- **無**- 只有具有 RBAC 存取權限的訂閱才能找到服務。 
- **限制性**- 只有白名單且具有 RBAC 存取權限的訂閱才能找到服務。 
- **全部**- 每個人都可以找到服務。 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>我能否使用基本負載等化器創建專用鏈路服務？ 
否。 不支援通過基本負載等化器提供專用鏈路服務。
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>專用連結服務是否需要專用子網？ 
否。 專用連結服務不需要專用子網。 您可以在 VNet 中選擇部署服務的任何子網。   

### <a name="i-am-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>我是使用 Azure 專用連結的服務提供者。 我是否需要確保所有客戶都擁有唯一的 IP 空間，並且不會與我的 IP 空間重疊？ 
否。 Azure 專用連結為您提供此功能。 因此，您不需要與客戶的位址空間具有非重疊的位址空間。 

##  <a name="next-steps"></a>後續步驟

- 瞭解[Azure 專用連結](private-link-overview.md)
