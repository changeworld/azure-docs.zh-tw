---
title: 驗證方法
titleSuffix: Azure Maps
description: 在本文中，您將瞭解 Azure Active Directory 和共用金鑰驗證。 這兩者都用於 Microsoft Azure Maps 服務。 瞭解如何取得 Azure 地圖服務訂用帳戶金鑰。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: e270a9a1a773442a4d1d28d9bbfe37639cac7c65
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133379"
---
# <a name="authentication-with-azure-maps"></a>向 Azure 地圖服務驗證

Azure 地圖服務支援兩種驗證要求的方式：共用金鑰驗證和[Azure Active Directory （Azure AD）](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)驗證。 本文說明這些驗證方法，以協助引導您執行 Azure 地圖服務服務。

> [!NOTE]
> 為了改善與 Azure 地圖服務的安全通訊，我們現在支援傳輸層安全性（TLS）1.2，而且我們即將淘汰對 TLS 1.0 和1.1 的支援。 若要避免任何服務中斷，請在**2020 年4月2日前，將您的伺服器和應用程式更新為使用 TLS 1.2**。  如果您目前使用 TLS 1.x，請評估您的 TLS 1.2 就緒程度，並使用[解決 TLS 1.0 問題](https://docs.microsoft.com/security/solving-tls1-problem)中所述的測試來開發遷移計畫。

## <a name="shared-key-authentication"></a>共用金鑰驗證

 建立 Azure 地圖服務帳戶之後，會產生主要和次要金鑰。 當您使用共用金鑰驗證呼叫 Azure 地圖服務時，建議您使用主要金鑰做為訂用帳戶金鑰。 共用金鑰驗證會將 Azure 地圖服務帳戶所產生的金鑰傳遞給 Azure 地圖服務服務。 針對每個 Azure 地圖服務服務的要求，將*訂*用帳戶金鑰新增為 URL 的參數。 次要金鑰可用於輪流金鑰變更之類的案例中。  

如需在 Azure 入口網站中查看金鑰的詳細資訊，請參閱[管理驗證](https://aka.ms/amauthdetails)。

> [!Tip]
> 建議您定期重新產生金鑰。 系統會提供您兩個金鑰，以便您在重新產生另一個金鑰時維持連線。 當您重新產生金鑰時，您需要使用新的金鑰來更新任何存取您帳戶的應用程式。

## <a name="azure-ad-authentication"></a>Azure AD 驗證

Azure 訂用帳戶會與 Azure AD 租使用者一起提供，以啟用更精細的存取控制。 Azure 地圖服務使用 Azure AD 提供 Azure 地圖服務服務的驗證。 Azure AD 為在 Azure AD 租使用者中註冊的使用者和應用程式提供身分識別型驗證。

針對與包含 Azure 地圖服務帳戶的 Azure 訂用帳戶相關聯的 Azure AD 租用戶，Azure 地圖服務可接受 **OAuth 2.0** 存取權杖。 Azure 地圖服務也會接受的權杖：

* Azure AD 使用者
* 使用使用者所委派許可權的合作夥伴應用程式
* 適用於 Azure 資源的受控識別

Azure 地圖服務會為每個 Azure 地圖服務帳戶產生「唯一識別碼 (用戶端識別碼)」**。 當您將此用戶端識別碼與其他參數結合時，您可以從 Azure AD 要求權杖。

如需如何為 Azure 地圖服務設定 Azure AD 和要求權杖的詳細資訊，請參閱[管理 Azure 地圖服務中的驗證](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)。

如需使用 Azure AD 進行驗證的一般資訊，請參閱[什麼是驗證？](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)。

### <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Azure 資源和 Azure 地圖服務的受控識別

[適用于 azure 資源的受控](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)識別可為 azure 服務提供自動受控的以應用程式為基礎的安全性主體，以 Azure AD 進行驗證。 透過角色型存取控制（RBAC），受控識別安全性主體可以獲得授權來存取 Azure 地圖服務服務。 受控識別的一些範例包括： Azure App Service、Azure Functions 和 Azure 虛擬機器。 如需受控識別的清單，請參閱[適用于 Azure 資源的受控](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)識別。

### <a name="configuring-application-azure-ad-authentication"></a>設定應用程式 Azure AD 驗證

應用程式會使用 Azure AD 提供的一或多個支援案例，向 Azure AD 租使用者進行驗證。 每個 Azure AD 應用程式案例都代表以商務需求為基礎的不同需求。 有些應用程式可能需要使用者登入體驗，而其他應用程式可能需要應用程式登入體驗。 如需詳細資訊，請參閱[驗證流程和應用程式案例](https://docs.microsoft.com/azure/active-directory/develop/authentication-flows-app-scenarios)。

在應用程式收到存取權杖之後，除了其他 REST API HTTP 標頭以外，SDK 和/或應用程式會使用下列一組必要的 HTTP 標頭來傳送 HTTPS 要求：

| 標頭名稱    | 值               |
| :------------- | :------------------ |
| x-ms-client-id | 30d7cc….9f55        |
| 授權  | Bearer eyJ0e….HNIVN |

> [!Note]
> `x-ms-client-id` 是以 Azure 地圖服務帳戶為基礎的 GUID，其顯示在 Azure 地圖服務的驗證頁面上。

以下是使用 Azure AD OAuth 持有人權杖的 Azure 地圖服務路由要求範例：

```http
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872
Host: atlas.microsoft.com
x-ms-client-id: 30d7cc….9f55
Authorization: Bearer eyJ0e….HNIVN
```

如需檢視用戶端識別碼的相關資訊，請參閱[檢視驗證詳細資料](https://aka.ms/amauthdetails)。

## <a name="authorization-with-role-based-access-control"></a>使用角色型存取控制進行授權

Azure 地圖服務支援存取 Azure[角色型存取控制](https://docs.microsoft.com/azure/role-based-access-control/overview)的所有主體類型，包括：個別 Azure AD 使用者、群組、應用程式、azure 資源和 azure 受控識別。 主體類型會被授與一組許可權，也稱為角色定義。 角色定義提供 REST API 動作的許可權。 將存取權套用至一或多個 Azure 地圖服務帳戶稱為「範圍」。 套用主體、角色定義和範圍時，會建立角色指派。 

下一節將討論 Azure 地圖服務與 Azure AD 角色型存取控制整合的概念和元件。 在設定 Azure 地圖服務帳戶的過程中，Azure AD 目錄會與 Azure 地圖服務帳戶所在的 Azure 訂用帳戶相關聯。 

當您設定 Azure RBAC 時，您可以選擇安全性主體，並將其套用至角色指派。 若要瞭解如何在 Azure 入口網站上新增角色指派，請參閱[新增或移除 Azure 角色指派](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)。

### <a name="picking-a-role-definition"></a>挑選角色定義

有下列角色定義類型可支援應用程式案例。

| Azure 角色定義       | 說明                                                                                              |
| :-------------------------- | :------------------------------------------------------------------------------------------------------- |
| Azure 地圖服務資料讀者      | 提供不可變 Azure 地圖服務 REST Api 的存取。                                                       |
| Azure 地圖服務資料參與者 | 提供可變 Azure 地圖服務 REST Api 的存取權。 可變動性是由 [動作] 所定義： [寫入] 和 [刪除]。 |
| 自訂角色定義      | 建立特製化的角色，以對 Azure 地圖服務 REST Api 啟用彈性的限制存取。                      |

某些 Azure 地圖服務服務可能需要較高的許可權，才能在 Azure 地圖服務 REST Api 上執行寫入或刪除動作。 提供寫入或刪除動作的服務需要 Azure 地圖服務資料參與者角色。 下表描述在指定的服務上使用寫入或刪除動作時，哪些服務 Azure 地圖服務資料參與者適用于。 如果只在服務上使用讀取動作，則可以使用 Azure 地圖服務的資料讀取器，而不是 Azure 地圖服務的資料參與者。

| Azure 地圖服務服務 | Azure 地圖服務角色定義  |
| :----------------- | :-------------------------- |
| 資料               | Azure 地圖服務資料參與者 |
| 建立者            | Azure 地圖服務資料參與者 |
| 空間            | Azure 地圖服務資料參與者 |

如需檢視 RBAC 設定的相關資訊，請參閱[如何設定 Azure 地圖服務的 RBAC](https://aka.ms/amrbac)。

#### <a name="custom-role-definitions"></a>自訂角色定義

應用程式安全性的其中一個層面是套用最低許可權的原則。 此原則表示安全性主體應該只允許存取，這是必要的，而且沒有額外的存取權。 建立自訂角色定義可支援使用案例，而需要更細微的存取控制。 若要建立自訂角色定義，您可以選取要在定義中包含或排除的特定資料動作。 

接著，您可以在任何安全性主體的角色指派中使用自訂角色定義。 若要深入瞭解 Azure 自訂角色定義，請參閱[azure 自訂角色](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)。

以下是一些範例案例，其中的自訂角色可以改善應用程式安全性。

| 案例                                                                                                                                                                                                                 | 自訂角色資料動作                                                                                                                  |
| :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| 具有基本地圖磚和沒有其他 REST Api 的公開或互動式登入網頁。                                                                                                                              | `Microsoft.Maps/accounts/services/render/read`                                                                                              |
| 只需要反向地理編碼和其他 REST Api 的應用程式。                                                                                                                                             | `Microsoft.Maps/accounts/services/search/read`                                                                                              |
| 安全性主體的角色，會要求讀取以 Azure 地圖服務建立者為基礎的地圖資料和基本地圖磚 REST Api。                                                                                                 | `Microsoft.Maps/accounts/services/data/read`, `Microsoft.Maps/accounts/services/render/read`                                                |
| 安全性主體的角色，需要讀取、寫入和刪除以建立者為基礎的對應資料。 這可以定義為地圖資料編輯器角色，但不允許存取其他 REST Api，例如基本地圖底圖。 | `Microsoft.Maps/accounts/services/data/read`, `Microsoft.Maps/accounts/services/data/write`, `Microsoft.Maps/accounts/services/data/delete` |

### <a name="understanding-scope"></a>瞭解範圍

建立角色指派時，它會在 Azure 資源階層中定義。 階層的頂端是[管理群組](https://docs.microsoft.com/azure/governance/management-groups/overview)，而最低是 Azure 資源，如 Azure 地圖服務帳戶。
指派角色指派給資源群組，可讓您存取多個 Azure 地圖服務帳戶或群組中的資源。

> [!Tip]
> Microsoft 的一般建議是指派 Azure 地圖服務帳戶範圍的存取權，因為它可防止**非預期地存取**相同 Azure 訂用帳戶中現有的其他 Azure 地圖服務帳戶。

## <a name="next-steps"></a>後續步驟

* 若要深入瞭解 RBAC，請參閱[角色型存取控制的總覽](https://docs.microsoft.com/azure/role-based-access-control/overview)

* 若要深入了解如何向 Azure AD 和 Azure 地圖服務驗證應用程式，請參閱[管理 Azure 地圖服務中的驗證](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)。

* 若要深入了解如何驗證 Azure 地圖服務、地圖控制項和 Azure AD，請參閱[使用 Azure 地圖服務的地圖控制項](https://aka.ms/amaadmc)。
