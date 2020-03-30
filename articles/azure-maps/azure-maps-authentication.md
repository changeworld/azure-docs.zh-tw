---
title: 身份驗證方法 |微軟 Azure 地圖
description: 在本文中，您將瞭解 Azure 活動目錄 （Azure AD） 和共用金鑰身份驗證。 兩者都用於 Microsoft Azure 映射服務。 瞭解如何獲取 Azure 地圖訂閱金鑰。
author: philmea
ms.author: philmea
ms.date: 01/28/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 21d29cba85adfc147ec9deb6ab362a5da943bf10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335710"
---
# <a name="authentication-with-azure-maps"></a>向 Azure 地圖服務驗證

Azure 映射支援兩種對請求進行身份驗證的方法：共用金鑰身份驗證和 Azure 活動目錄身份驗證。 本文介紹這些身份驗證方法，以説明指導 Azure 地圖服務的實現。

> [!NOTE]
> 為了改進與 Azure 地圖的安全通信，我們現在支援傳輸層安全 （TLS） 1.2，並且我們將停用對 TLS 1.0 和 1.1 的支援。 為避免任何服務中斷，**請補救伺服器和應用程式，在 2020 年 4 月 2 日之前使用 TLS 1.2**。  如果您當前使用 TLS 1.x，請評估 TLS 1.2 就緒性，並使用[解決 TLS 1.0 問題](https://docs.microsoft.com/security/solving-tls1-problem)中描述的測試製定遷移計畫。

## <a name="shared-key-authentication"></a>共用金鑰驗證

 創建 Azure 地圖帳戶後生成主鍵和輔助鍵。 使用共用金鑰身份驗證調用 Azure 映射時，鼓勵使用主金鑰作為訂閱金鑰。 共用金鑰身份驗證將 Azure 映射帳戶生成的金鑰傳遞給 Azure 映射服務。 對於對 Azure 映射服務的每個請求，將*訂閱金鑰*作為參數添加到 URL。 輔助鍵可用於滾動鍵更改等方案。  

有關在 Azure 門戶中查看金鑰的資訊，請參閱[管理身份驗證](https://aka.ms/amauthdetails)。

> [!Tip]
> 建議您定期重新產生金鑰。 為您提供兩個金鑰，以便在重新生成另一個金鑰時，可以使用一個金鑰保持連接。 重新生成金鑰時，需要使用新金鑰更新存取帳戶的任何應用程式。

## <a name="authentication-with-azure-active-directory-preview"></a>使用 Azure Active Directory 進行驗證 (預覽)

Azure 地圖現在使用[Azure 活動目錄 （Azure AD）](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)為 Azure 地圖服務提供請求身份驗證。 Azure AD 提供基於標識的身份驗證，包括[基於角色的存取控制 （RBAC）。](https://docs.microsoft.com/azure/role-based-access-control/overview) RBAC 用於授予對 Azure 地圖資源的使用者級、組級或應用程式級存取權限。 接下來的部分將討論 Azure 映射與 Azure AD 集成的概念和元件。

## <a name="authentication-with-oauth-access-tokens"></a>使用 OAuth 存取權杖進行驗證

針對與包含 Azure 地圖服務帳戶的 Azure 訂用帳戶相關聯的 Azure AD 租用戶，Azure 地圖服務可接受 **OAuth 2.0** 存取權杖。 Azure 地圖還接受用於：

* Azure AD 使用者
* 使用使用者委派的許可權的合作夥伴應用程式
* 適用於 Azure 資源的受控識別

Azure 地圖服務會為每個 Azure 地圖服務帳戶產生「唯一識別碼 (用戶端識別碼)」**。 將此用戶端 ID 與其他參數合併時，可以從 Azure AD 請求權杖。 要請求權杖，請根據 Azure 環境指定下表中的值。

| Azure 環境   | Azure AD 權杖終結點 |
| --------------------|-------------------------|
| Azure 公用        | https://login.microsoftonline.com |
| Azure Government    | https://login.microsoftonline.us |


如需如何為 Azure 地圖服務設定 Azure AD 和要求權杖的詳細資訊，請參閱[管理 Azure 地圖服務中的驗證](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)。

如需如何向 Azure AD 要求權杖的一般資訊，請參閱[何謂驗證？](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)。

## <a name="request-azure-map-resources-with-oauth-tokens"></a>使用 OAuth 權杖要求 Azure 地圖服務資源

Azure AD 收到權杖後，Azure 地圖會發送具有以下一組所需請求標頭的請求：

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

在 Azure AD 中，使用 RBAC 控制對安全資源的訪問。 設置 Azure 地圖帳戶並註冊 Azure 映射 Azure AD 租戶。 Azure 地圖支援通過 Azure 資源的託管標識對單個 Azure AD 使用者、組、應用程式、Azure 資源和 Azure 服務進行讀取存取控制。 在 Azure 地圖門戶頁上，可以為所選角色設置 RBAC。

![Azure 地圖服務資料讀者 (預覽)](./media/azure-maps-authentication/concept.png)

如需檢視 RBAC 設定的相關資訊，請參閱[如何設定 Azure 地圖服務的 RBAC](https://aka.ms/amrbac)。

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Azure 資源和 Azure 地圖服務的受控識別

[Azure 資源的託管標識](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)為 Azure 服務提供自動託管標識，該標識可以授權訪問 Azure 地圖服務。 託管標識的一些示例包括：Azure 應用服務、Azure 函數和 Azure 虛擬機器。

## <a name="next-steps"></a>後續步驟

* 若要深入了解如何向 Azure AD 和 Azure 地圖服務驗證應用程式，請參閱[管理 Azure 地圖服務中的驗證](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)。

* 若要深入了解如何驗證 Azure 地圖服務、地圖控制項和 Azure AD，請參閱[使用 Azure 地圖服務的地圖控制項](https://aka.ms/amaadmc)。
