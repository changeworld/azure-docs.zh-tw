---
title: 驗證方法 |Microsoft Azure 對應
description: 在本文中，您將瞭解 Azure Active Directory （Azure AD）和共用金鑰驗證。 這兩者都用於 Microsoft Azure Maps 服務。 瞭解如何取得 Azure 地圖服務訂用帳戶金鑰。
author: philmea
ms.author: philmea
ms.date: 01/28/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 21d29cba85adfc147ec9deb6ab362a5da943bf10
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335710"
---
# <a name="authentication-with-azure-maps"></a>向 Azure 地圖服務驗證

Azure 地圖服務支援兩種驗證要求的方式：共用金鑰驗證和 Azure Active Directory 驗證。 本文說明這些驗證方法，以協助引導您執行 Azure 地圖服務服務。

> [!NOTE]
> 為了改善與 Azure 地圖服務的安全通訊，我們現在支援傳輸層安全性（TLS）1.2，而且我們即將淘汰對 TLS 1.0 和1.1 的支援。 若要避免任何服務中斷，請在**2020 年4月2日前，將您的伺服器和應用程式更新為使用 TLS 1.2**。  如果您目前使用 TLS 1.x，請評估您的 TLS 1.2 就緒程度，並使用[解決 TLS 1.0 問題](https://docs.microsoft.com/security/solving-tls1-problem)中所述的測試來開發遷移計畫。

## <a name="shared-key-authentication"></a>共用金鑰驗證

 建立 Azure 地圖服務帳戶之後，會產生主要和次要金鑰。 當您使用共用金鑰驗證呼叫 Azure 地圖服務時，建議您使用主要金鑰做為訂用帳戶金鑰。 共用金鑰驗證會將 Azure 地圖服務帳戶所產生的金鑰傳遞給 Azure 地圖服務服務。 針對每個 Azure 地圖服務服務的要求，將*訂*用帳戶金鑰新增為 URL 的參數。 次要金鑰可用於輪流金鑰變更之類的案例中。  

如需在 Azure 入口網站中查看金鑰的詳細資訊，請參閱[管理驗證](https://aka.ms/amauthdetails)。

> [!Tip]
> 建議您定期重新產生金鑰。 系統會提供您兩個金鑰，以便您在重新產生另一個金鑰時維持連線。 當您重新產生金鑰時，您需要使用新的金鑰來更新任何存取您帳戶的應用程式。

## <a name="authentication-with-azure-active-directory-preview"></a>使用 Azure Active Directory 進行驗證 (預覽)

Azure 地圖服務現在會使用[Azure Active Directory （Azure AD）](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)提供 Azure 地圖服務服務的要求驗證。 Azure AD 提供以身分識別為基礎的驗證，包括以[角色為基礎的存取控制（RBAC）](https://docs.microsoft.com/azure/role-based-access-control/overview)。 RBAC 是用來授與使用者層級、群組層級或應用層級的 Azure 地圖服務資源存取權。 下一節將討論 Azure 地圖服務與 Azure AD 整合的概念和元件。

## <a name="authentication-with-oauth-access-tokens"></a>使用 OAuth 存取權杖進行驗證

針對與包含 Azure 地圖服務帳戶的 Azure 訂用帳戶相關聯的 Azure AD 租用戶，Azure 地圖服務可接受 **OAuth 2.0** 存取權杖。 Azure 地圖服務也會接受的權杖：

* Azure AD 使用者
* 使用使用者所委派許可權的合作夥伴應用程式
* 適用於 Azure 資源的受控識別

Azure 地圖服務會為每個 Azure 地圖服務帳戶產生「唯一識別碼 (用戶端識別碼)」**。 當您將此用戶端識別碼與其他參數結合時，您可以從 Azure AD 要求權杖。 若要要求權杖，請根據您的 Azure 環境，指定下表中的值。

| Azure 環境   | Azure AD token 端點 |
| --------------------|-------------------------|
| Azure 公用        | https://login.microsoftonline.com |
| Azure Government    | https://login.microsoftonline.us |


如需如何為 Azure 地圖服務設定 Azure AD 和要求權杖的詳細資訊，請參閱[管理 Azure 地圖服務中的驗證](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)。

如需如何向 Azure AD 要求權杖的一般資訊，請參閱[何謂驗證？](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)。

## <a name="request-azure-map-resources-with-oauth-tokens"></a>使用 OAuth 權杖要求 Azure 地圖服務資源

Azure AD 收到權杖之後，Azure 地圖服務會傳送具有下列必要要求標頭集合的要求：

| 要求標頭    |    值    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc….9f55|
| 授權     | Bearer eyJ0e….HNIVN |

> [!Note]
> `x-ms-client-id` 是以 Azure 地圖服務帳戶為基礎的 GUID，其顯示在 Azure 地圖服務的驗證頁面上。

以下是使用 OAuth 權杖的 Azure 地圖服務路由要求範例：

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

如需檢視用戶端識別碼的相關資訊，請參閱[檢視驗證詳細資料](https://aka.ms/amauthdetails)。

## <a name="control-access-with-rbac"></a>使用 RBAC 控制存取權

在 Azure AD 中，使用 RBAC 來控制受保護資源的存取權。 設定您的 Azure 地圖服務帳戶，並註冊您的 Azure 地圖服務 Azure AD 租使用者。 Azure 地圖服務可透過 Azure 資源的受控識別，支援個別 Azure AD 使用者、群組、應用程式、Azure 資源和 Azure 服務的讀取存取控制。 在 [Azure 地圖服務入口網站] 頁面上，您可以設定所選角色的 RBAC。

![Azure 地圖服務資料讀者 (預覽)](./media/azure-maps-authentication/concept.png)

如需檢視 RBAC 設定的相關資訊，請參閱[如何設定 Azure 地圖服務的 RBAC](https://aka.ms/amrbac)。

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Azure 資源和 Azure 地圖服務的受控識別

[適用于 azure 資源的受控](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)識別可為 azure 服務提供自動管理的身分識別，以獲得授權來存取 Azure 地圖服務服務。 受控識別的一些範例包括： Azure App Service、Azure Functions 和 Azure 虛擬機器。

## <a name="next-steps"></a>後續步驟

* 若要深入了解如何向 Azure AD 和 Azure 地圖服務驗證應用程式，請參閱[管理 Azure 地圖服務中的驗證](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)。

* 若要深入了解如何驗證 Azure 地圖服務、地圖控制項和 Azure AD，請參閱[使用 Azure 地圖服務的地圖控制項](https://aka.ms/amaadmc)。
