---
title: Azure 快速路由交叉管理 API 開發和集成
description: 本文為 ExpressRoute 合作夥伴提供有關 ExpressRoute 交叉連接資源類型的詳細概述。
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: mialdrid
ms.openlocfilehash: b4a83013d2cbeb2871a3963ec0c95144c02f4d66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77187018"
---
# <a name="expressroute-crossconnnections-api-development-and-integration"></a>快速路由交叉康寧 API 開發和集成

ExpressRoute 合作夥伴資源管理器 API 允許 ExpressRoute 合作夥伴管理客戶 ExpressRoute 電路的第 2 層和第 3 層配置。 ExpressRoute 合作夥伴資源管理器 API 引入了一種新的資源類型 **，即 ExpressRouteCrossConnections**。 合作夥伴使用此資源管理客戶 ExpressRoute 電路。

## <a name="workflow"></a>工作流程

ExpressRouteCrossConnections 資源是快速路由電路的影子資源。 當 Azure 客戶創建 ExpressRoute 電路並選擇特定的 ExpressRoute 合作夥伴時，Microsoft 會在合作夥伴的 Azure ExpressRoute 管理訂閱中創建 ExpressRouteCrossConnections 資源。 這樣，Microsoft 將定義一個資源組來創建中的 ExpressRouteCross連接資源。 資源組的命名標準為 [*交叉連接 -* 對等位置];其中對等位置 = 快速路由位置。 例如，如果客戶在丹佛創建 ExpressRoute 電路，則交叉連接將在合作夥伴的 Azure 訂閱中創建，在以下資源組中：**交叉康寧-丹佛**。

ExpressRoute 合作夥伴通過針對 ExpressRouteCrossConnections 資源發出 REST 操作來管理第 2 層和第 3 層配置。

## <a name="benefits"></a>優點

遷移到快速路由交叉連接資源的好處：

* ExpressRoute 合作夥伴的任何後續增強功能都將在 ExpressRoute 交叉連接資源中提供。

* 合作夥伴可以將[基於角色的存取控制](https://docs.microsoft.com/azure/role-based-access-control/overview)應用於快速 RouteCross 連接資源。 這些控制項可以定義使用者帳戶可以修改 ExpressRouteCrossConnect 資源以及添加/更新/刪除對等配置的許可權。

* ExpressRouteCross連接資源公開了可用於排除快速路由連接的 API。 這包括 ARP 表、BGP 路由表摘要和 BGP 路由表詳細資訊。 經典部署 API 不支援此功能。

* 合作夥伴還可以使用*RouteFilter*資源查找 Microsoft 對等互連上的廣告社區。

## <a name="api-development-and-integration-steps"></a>API 開發和集成步驟

要針對合作夥伴 API 進行開發，ExpressRoute 合作夥伴利用測試客戶和測試合作夥伴設置。 測試客戶設置將用於在映射到虛擬裝置和埠的測試對等位置創建 ExpressRoute 電路。 測試夥伴設置用於管理在測試對等位置創建的 ExpressRoute 電路。

### <a name="1-enlist-subscriptions"></a>1. 登記訂閱

要請求測試合作夥伴和測試客戶設置，請向 ExpressRoute 工程連絡人登記兩個即用即付 Azure 訂閱：
* **ExpressRoute_API_Dev_Provider_Sub：** 此訂閱將用於管理在虛擬裝置和埠上的測試對等位置創建的 ExpressRoute 電路。

* **ExpressRoute_API_Dev_Customer_Sub：** 此訂閱將用於在映射到虛擬裝置和埠的測試對等位置創建 ExpressRoute 電路。

測試對等位置：預設情況下，虛擬裝置和埠不會向生產客戶公開。 為了創建映射到測試設定的 ExpressRoute 電路，需要啟用訂閱功能標誌。

### <a name="2-register-the-dev_provider-subscription-to-access-the-expressroutecrossconnections-api"></a>2. 註冊Dev_Provider訂閱以訪問快速路由交叉連接 API

為了訪問 ExpressRouteCrossConnections API，合作夥伴訂閱需要註冊到**Microsoft.Network 資來源提供者**。 按照[Azure 資來源提供者和類型](/azure/azure-resource-manager/management/resource-providers-and-types#azure-portal)文章中的步驟完成註冊過程。

### <a name="3-set-up-authentication-for-azure-resource-manager-rest-api-calls"></a>3. 為 Azure 資源管理器 REST API 呼叫設置身份驗證

大多數 Azure 服務要求用戶端代碼在調用服務 API 之前使用有效的憑據與資源管理器進行身份驗證。 Azure AD 協調了各個參與者之間的身份驗證，並為用戶端提供訪問權杖作為身份驗證證明。

身份驗證過程涉及兩個主要步驟：

1. [註冊用戶端](https://docs.microsoft.com/rest/api/azure/#register-your-client-application-with-azure-ad)。
2. [創建訪問請求](https://docs.microsoft.com/rest/api/azure/#create-the-request)。

### <a name="4-provide-network-contributor-permission-to-the-client-application"></a>4. 向用戶端應用程式提供網路參與者許可權

成功配置身份驗證後，您需要根據Dev_Provider_Sub授予對用戶端應用程式的網路參與者存取權限。 要授予許可權，請登錄到[Azure 門戶](https://ms.portal.azure.com/#home)並完成以下步驟：

1. 導航到訂閱並選擇Dev_Provider_Sub
2. 導航到存取控制 （IAM）
3. 添加角色指派
4. 選擇網路參與者角色
5. 分配對 Azure AD 使用者、組或服務主體的訪問
6. 選擇用戶端應用程式
7. 儲存變更

### <a name="5-develop"></a>5. 開發

針對[快速路由交叉連接 API](https://docs.microsoft.com/rest/api/expressroute/expressroutecrossconnections)進行開發。

## <a name="rest-api"></a>REST API

有關 REST API 文檔[，請參閱快速路由交叉連接 REST API。](https://docs.microsoft.com/rest/api/expressroute/expressroutecrossconnections)

## <a name="next-steps"></a>後續步驟

有關所有快速路由 REST API 的詳細資訊，請參閱[快速路由 REST API](https://docs.microsoft.com/rest/api/expressroute/)。