---
title: Azure ExpressRoute CrossConnnections API 開發與整合
description: 本文提供有關 expressRouteCrossConnections 資源類型之 ExpressRoute 合作夥伴的詳細總覽。
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: mialdrid
ms.openlocfilehash: b4a83013d2cbeb2871a3963ec0c95144c02f4d66
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77187018"
---
# <a name="expressroute-crossconnnections-api-development-and-integration"></a>ExpressRoute CrossConnnections API 開發與整合

ExpressRoute 合作夥伴 Resource Manager API 可讓 ExpressRoute 合作夥伴管理客戶 ExpressRoute 線路的第2層和第3層設定。 ExpressRoute 合作夥伴 Resource Manager API 引進了新的資源類型**expressRouteCrossConnections**。 合作夥伴會使用此資源來管理客戶 ExpressRoute 線路。

## <a name="workflow"></a>工作流程

ExpressRouteCrossConnections 資源是 ExpressRoute 線路的陰影資源。 當 Azure 客戶建立 ExpressRoute 線路並選取特定的 ExpressRoute 合作夥伴時，Microsoft 會在合作夥伴的 Azure ExpressRoute 管理訂用帳戶中建立 expressRouteCrossConnections 資源。 在此情況下，Microsoft 會定義要在其中建立 expressRouteCrossConnections 資源的資源群組。 資源群組的命名標準為 **CrossConnection-* PeeringLocation * * *;其中 PeeringLocation = ExpressRoute 位置。 例如，如果客戶在丹佛建立 ExpressRoute 線路，則 CrossConnection 會建立在合作夥伴的 Azure 訂用帳戶中的下列資源群組中： **CrossConnnection-丹佛**。

ExpressRoute 合作夥伴會藉由對 expressRouteCrossConnections 資源發出 REST 作業，來管理第2層和第3層設定。

## <a name="benefits"></a>優點

移至 expressRouteCrossConnections 資源的優點：

* ExpressRoute 合作夥伴的任何未來增強功能將會在 ExpressRouteCrossConnection 資源上提供。

* 合作夥伴可以將以[角色為基礎的存取控制](https://docs.microsoft.com/azure/role-based-access-control/overview)套用至 expressRouteCrossConnection 資源。 這些控制項可以定義許可權，讓使用者帳戶可以修改 expressRouteCrossConnection 資源，以及新增/更新/刪除對等互連設定。

* ExpressRouteCrossConnection 資源會公開應用程式開發介面，這些 Api 有助於針對 ExpressRoute 連線進行疑難排解。 這包括 ARP 資料表、BGP 路由表摘要和 BGP 路由表詳細資料。 傳統部署 Api 不支援這項功能。

* 合作夥伴也可以使用*RouteFilter*資源，在 Microsoft 對等互連上查閱公告的社區。

## <a name="api-development-and-integration-steps"></a>API 開發和整合步驟

為了針對合作夥伴 API 進行開發，ExpressRoute 合作夥伴會運用測試客戶和測試合作夥伴設定。 測試客戶設定將用來在測試對等互連位置（對應至虛擬裝置和埠）中建立 ExpressRoute 線路。 測試夥伴設定是用來管理在測試對等互連位置中建立的 ExpressRoute 線路。

### <a name="1-enlist-subscriptions"></a>1. 登記訂閱

若要要求測試夥伴並測試客戶設定，請將兩個隨用隨付 Azure 訂用帳戶登記至您的 ExpressRoute 工程連絡人：
* **ExpressRoute_API_Dev_Provider_Sub：** 此訂用帳戶將用來管理在虛擬裝置和埠上的測試對等互連位置中建立的 ExpressRoute 線路。

* **ExpressRoute_API_Dev_Customer_Sub：** 此訂用帳戶將用來在測試對等互連位置（對應至虛擬裝置和埠）中建立 ExpressRoute 線路。

測試對等互連位置：根據預設，虛擬裝置和埠不會公開給生產客戶。 為了建立對應至測試設定的 ExpressRoute 線路，必須啟用訂用帳戶功能旗標。

### <a name="2-register-the-dev_provider-subscription-to-access-the-expressroutecrossconnections-api"></a>2. 註冊 Dev_Provider 訂用帳戶以存取 expressRouteCrossConnections API

若要存取 expressRouteCrossConnections API，合作夥伴訂用帳戶必須在**Microsoft 網路資源提供者**中註冊。 請遵循[Azure 資源提供者和類型](/azure/azure-resource-manager/management/resource-providers-and-types#azure-portal)一文中的步驟，完成註冊程式。

### <a name="3-set-up-authentication-for-azure-resource-manager-rest-api-calls"></a>3. 設定 Azure Resource Manager REST API 呼叫的驗證

在呼叫服務 Api 之前，大部分的 Azure 服務都需要用戶端程式代碼使用有效的認證向 Resource Manager 進行驗證。 驗證會在各種動作專案之間進行協調，方法是 Azure AD 並提供用戶端存取權杖做為驗證證明。

驗證程式牽涉到兩個主要步驟：

1. [註冊用戶端](https://docs.microsoft.com/rest/api/azure/#register-your-client-application-with-azure-ad)。
2. [建立存取要求](https://docs.microsoft.com/rest/api/azure/#create-the-request)。

### <a name="4-provide-network-contributor-permission-to-the-client-application"></a>4. 提供用戶端應用程式的網路參與者許可權

成功設定驗證之後，您必須在 Dev_Provider_Sub 之下，授與網路參與者對用戶端應用程式的存取權。 若要授與許可權，請登入[Azure 入口網站](https://ms.portal.azure.com/#home)，然後完成下列步驟：

1. 流覽至 [訂用帳戶]，然後選取 [Dev_Provider_Sub
2. 流覽至存取控制（IAM）
3. 新增角色指派
4. 選取網路參與者角色
5. 將存取權指派給 Azure AD 的使用者、群組或服務主體
6. 選取您的用戶端應用程式
7. 儲存變更

### <a name="5-develop"></a>5. 開發

針對[EXPRESSROUTECROSSCONNECTIONS API](https://docs.microsoft.com/rest/api/expressroute/expressroutecrossconnections)進行開發。

## <a name="rest-api"></a>REST API

如需 REST API 檔，請參閱[ExpressRoute CrossConnections REST API](https://docs.microsoft.com/rest/api/expressroute/expressroutecrossconnections) 。

## <a name="next-steps"></a>後續步驟

如需所有 ExpressRoute REST Api 的詳細資訊，請參閱[EXPRESSROUTE Rest api](https://docs.microsoft.com/rest/api/expressroute/)。