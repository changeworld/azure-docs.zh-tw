---
title: 使用 Microsoft Azure Maps 進行驗證
titleSuffix: Azure Maps
description: 瞭解 Azure 地圖服務中驗證要求的兩種方式：共用金鑰驗證和 Azure Active Directory (Azure AD) 驗證。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: dc3792b5eff1b0ba51f5d7938e52e6914660109a
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92889879"
---
# <a name="authentication-with-azure-maps"></a>向 Azure 地圖服務驗證

Azure 地圖服務支援兩種驗證要求的方式：共用金鑰驗證和 [Azure Active Directory (Azure AD) ](../active-directory/fundamentals/active-directory-whatis.md) 驗證。 本文說明這兩種驗證方法，可協助引導您的 Azure 地圖服務服務的執行。

> [!NOTE]
> 為了改善與 Azure 地圖服務的通訊安全，我們現在支援 (TLS) 1.2 的傳輸層安全性，而且我們即將淘汰對 TLS 1.0 和1.1 的支援。 如果您目前使用 TLS 1.x，請評估您的 TLS 1.2 就緒程度，並使用 [解決 TLS 1.0 問題](/security/solving-tls1-problem)所述的測試來開發遷移計畫。

## <a name="shared-key-authentication"></a>共用金鑰驗證

 建立 Azure 地圖服務帳戶之後，就會產生主要和次要金鑰。 使用共用金鑰驗證呼叫 Azure 地圖服務時，建議您使用主要金鑰作為訂用帳戶金鑰。 共用金鑰驗證會將 Azure 地圖服務帳戶所產生的金鑰傳遞給 Azure 地圖服務服務。 針對 Azure 地圖服務服務的每個要求，將 *訂* 用帳戶金鑰新增為 URL 的參數。 次要金鑰可用於輪流金鑰變更這類案例。  

如需有關在 Azure 入口網站中查看金鑰的詳細資訊，請參閱 [管理驗證](./how-to-manage-authentication.md#view-authentication-details)。

> [!TIP]
> 基於安全性考慮，建議您在主要和次要金鑰之間輪替。 若要輪替金鑰，將您的應用程式更新為使用次要金鑰，部署，然後按下主要金鑰旁的循環/重新整理按鈕，以產生新的主要金鑰。 舊的主要金鑰將會停用。 如需金鑰輪替的詳細資訊，請參閱[使用金鑰輪替和稽核來設定 Azure Key Vault](../key-vault/secrets/tutorial-rotation-dual.md)

## <a name="azure-ad-authentication"></a>Azure AD 驗證

Azure 訂用帳戶隨附于 Azure AD 租使用者，以啟用更細緻的存取控制。 Azure 地圖服務使用 Azure AD 提供 Azure 地圖服務服務的驗證。 Azure AD 針對 Azure AD 租使用者中註冊的使用者和應用程式，提供以身分識別為基礎的驗證。

針對與包含 Azure 地圖服務帳戶的 Azure 訂用帳戶相關聯的 Azure AD 租用戶，Azure 地圖服務可接受 **OAuth 2.0** 存取權杖。 Azure 地圖服務也接受下列權杖：

* Azure AD 使用者
* 使用由使用者委派之許可權的夥伴應用程式
* 適用於 Azure 資源的受控識別

Azure 地圖服務會為每個 Azure 地圖服務帳戶產生「唯一識別碼 (用戶端識別碼)」  。 當您將此用戶端識別碼與其他參數合併時，可以要求 Azure AD 的權杖。

如需如何為 Azure 地圖服務設定 Azure AD 和要求權杖的詳細資訊，請參閱[管理 Azure 地圖服務中的驗證](./how-to-manage-authentication.md)。

如需有關使用 Azure AD 進行驗證的一般資訊，請參閱 [什麼是驗證？](../active-directory/develop/authentication-vs-authorization.md)。

### <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Azure 資源和 Azure 地圖服務的受控識別

[適用于 azure 資源的受控](../active-directory/managed-identities-azure-resources/overview.md) 識別會為 azure 服務提供自動管理的應用程式安全性主體，以 Azure AD 進行驗證。 透過 Azure 角色型存取控制 (Azure RBAC) ，受控識別安全性主體可獲得授權以存取 Azure 地圖服務服務。 受控識別的一些範例包括： Azure App Service、Azure Functions 和 Azure 虛擬機器。 如需受控識別的清單，請參閱 [適用于 Azure 資源的受控](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)識別。

### <a name="configuring-application-azure-ad-authentication"></a>設定應用程式 Azure AD 驗證

應用程式會使用 Azure AD 所提供的一或多個支援案例，向 Azure AD 租使用者進行驗證。 每個 Azure AD 應用程式案例都是根據商務需求來表示不同的需求。 有些應用程式可能需要使用者登入體驗，而其他應用程式可能需要應用程式登入體驗。 如需詳細資訊，請參閱 [驗證流程和應用程式案例](../active-directory/develop/authentication-flows-app-scenarios.md)。

當應用程式收到存取權杖之後，除了其他 REST API HTTP 標頭之外，SDK 和/或應用程式會使用下列一組必要的 HTTP 標頭來傳送 HTTPS 要求：

| 標頭名稱    | 值               |
| :------------- | :------------------ |
| x-ms-client-id | 30d7cc….9f55        |
| 授權  | Bearer eyJ0e….HNIVN |

> [!NOTE]
> `x-ms-client-id` 是以 Azure 地圖服務帳戶為基礎的 GUID，其顯示在 Azure 地圖服務的驗證頁面上。

以下是使用 Azure AD OAuth 持有人權杖的 Azure 地圖服務路由要求範例：

```http
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872
Host: atlas.microsoft.com
x-ms-client-id: 30d7cc….9f55
Authorization: Bearer eyJ0e….HNIVN
```

如需檢視用戶端識別碼的相關資訊，請參閱[檢視驗證詳細資料](./how-to-manage-authentication.md#view-authentication-details)。

## <a name="authorization-with-role-based-access-control"></a>使用角色型存取控制進行授權

Azure 地圖服務支援 [ (AZURE RBAC) 存取 azure 角色型存取控制 ](../role-based-access-control/overview.md) 的所有主體類型，包括：個別 Azure AD 使用者、群組、應用程式、azure 資源和 azure 受控識別。 主體類型會被授與一組許可權，也稱為角色定義。 角色定義會提供 REST API 動作的許可權。 將存取權套用至一或多個 Azure 地圖服務帳戶稱為「範圍」（scope）。 套用主體、角色定義和範圍時，會建立角色指派。 

下一節將討論 Azure 地圖服務與 Azure RBAC 整合的概念和元件。 在設定 Azure 地圖服務帳戶的過程中，Azure AD 目錄會與 Azure 地圖服務帳戶所在的 Azure 訂用帳戶相關聯。 

當您設定 Azure RBAC 時，您可以選擇安全性主體，並將其套用至角色指派。 若要瞭解如何在 Azure 入口網站上新增角色指派，請參閱 [新增或移除 Azure 角色指派](../role-based-access-control/role-assignments-portal.md)。

### <a name="picking-a-role-definition"></a>挑選角色定義

下列角色定義類型存在，可支援應用程式案例。

| Azure 角色定義       | 描述                                                                                              |
| :-------------------------- | :------------------------------------------------------------------------------------------------------- |
| Azure 地圖服務資料讀者      | 提供不可變 Azure 地圖服務 REST Api 的存取權。                                                       |
| Azure 地圖服務資料參與者 | 提供可變動 Azure 地圖服務 REST Api 的存取權。 可變動性是由 [動作：寫入] 和 [刪除] 所定義。 |
| 自訂角色定義      | 建立精心打造的角色，以啟用 Azure 地圖服務 REST Api 的彈性限制存取。                      |

某些 Azure 地圖服務服務可能需要較高的許可權，才能在 Azure 地圖服務 REST Api 上執行寫入或刪除動作。 提供寫入或刪除動作的服務需要 Azure 地圖服務資料參與者角色。 下表描述在指定的服務上使用寫入或刪除動作時，哪些服務 Azure 地圖服務資料參與者適用。 如果服務上只使用讀取動作，則可以使用 Azure 地圖服務資料讀取器，而不是 Azure 地圖服務資料參與者。

| Azure 地圖服務服務 | Azure 地圖服務角色定義  |
| :----------------- | :-------------------------- |
| 資料               | Azure 地圖服務資料參與者 |
| 建立者            | Azure 地圖服務資料參與者 |
| 空間            | Azure 地圖服務資料參與者 |

如需有關觀看 Azure RBAC 設定的詳細資訊，請參閱 [如何設定適用于 Azure 地圖服務的 AZURE rbac](./how-to-manage-authentication.md)。

#### <a name="custom-role-definitions"></a>自訂角色定義

應用程式安全性的一個層面是套用最低許可權的原則。 此原則表示安全性主體應該只允許必要的存取，而且沒有額外的存取權。 建立自訂角色定義可以支援使用案例，這需要更細微的存取控制。 若要建立自訂角色定義，您可以選取要包含或排除定義的特定資料動作。

自訂角色定義可以用於任何安全性主體的角色指派。 若要深入瞭解 Azure 自訂角色定義，請參閱 [azure 自訂角色](../role-based-access-control/custom-roles.md)。

以下是一些範例案例，其中的自訂角色可以改善應用程式安全性。

| 案例                                                                                                                                                                                                                 | 自訂角色資料動作 (s)                                                                                                                   |
| :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| 具有基底地圖磚且沒有其他 REST Api 的公開或互動式登入網頁。                                                                                                                              | `Microsoft.Maps/accounts/services/render/read`                                                                                              |
| 只需要反向地理編碼且沒有其他 REST Api 的應用程式。                                                                                                                                             | `Microsoft.Maps/accounts/services/search/read`                                                                                              |
| 安全性主體的角色，要求讀取以 Azure 地圖服務建立者為基礎的地圖資料和基底地圖磚 REST Api。                                                                                                 | `Microsoft.Maps/accounts/services/data/read`, `Microsoft.Maps/accounts/services/render/read`                                                |
| 安全性主體的角色，需要讀取、寫入和刪除以建立者為基礎的對應資料。 這可以定義為地圖資料編輯器角色，但不允許存取其他 REST Api，例如基底地圖磚。 | `Microsoft.Maps/accounts/services/data/read`, `Microsoft.Maps/accounts/services/data/write`, `Microsoft.Maps/accounts/services/data/delete` |

### <a name="understanding-scope"></a>瞭解範圍

建立角色指派時，會在 Azure 資源階層中定義。 階層的頂端是一個 [管理群組](../governance/management-groups/overview.md) ，而最低的是 Azure 資源，例如 Azure 地圖服務帳戶。
將角色指派指派給資源群組可讓您存取多個 Azure 地圖服務帳戶或群組中的資源。

> [!TIP]
> Microsoft 的一般建議是將存取權指派給 Azure 地圖服務帳戶範圍，因為它可防止非預期的存取相同 Azure 訂用帳戶中現有的 **其他 Azure 地圖服務帳戶** 。

## <a name="next-steps"></a>後續步驟

若要深入瞭解 Azure RBAC，請參閱
> [!div class="nextstepaction"]
> [Azure 角色型存取控制](../role-based-access-control/overview.md) \(部分機器翻譯\)

若要深入瞭解如何使用 Azure AD 和 Azure 地圖服務驗證應用程式，請參閱
> [!div class="nextstepaction"]
> [管理 Azure 地圖服務中的驗證](./how-to-manage-authentication.md)

若要深入瞭解如何使用 Azure AD 驗證 Azure 地圖服務地圖控制項，請參閱
> [!div class="nextstepaction"]
> [使用 Azure 地圖服務的地圖控制項](./how-to-use-map-control.md)