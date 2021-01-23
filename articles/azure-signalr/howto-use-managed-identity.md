---
title: Azure SignalR Service 中的受控識別
description: 瞭解受控識別在 Azure SignalR Service 中的運作方式，以及如何在無伺服器案例中使用受控識別。
author: chenyl
ms.service: signalr
ms.topic: article
ms.date: 06/8/2020
ms.author: chenyl
ms.openlocfilehash: dee15977318eda7bcd0b1950286bb33f621221dd
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98731579"
---
# <a name="managed-identities-for-azure-signalr-service"></a>Azure SignalR Service 的受控識別

本文說明如何建立 Azure SignalR Service 的受控識別，以及如何在無伺服器案例中使用它。

> [!Important] 
> Azure SignalR Service 只能支援一個受控識別。 這表示您可以新增系統指派的身分識別或使用者指派的身分識別。 

## <a name="add-a-system-assigned-identity"></a>新增系統指派的身分識別

若要在 Azure 入口網站中設定受控識別，您必須先建立 Azure SignalR Service 的實例，然後再啟用此功能。

1. 像平常一樣在入口網站中建立 Azure SignalR Service 實例。 在入口網站中流覽至該網站。

2. 選取 [身分識別]。

4. 在 [ **系統指派** ] 索引標籤上，將 [ **狀態** ] 切換為 **開啟**。 選取 [儲存]。

    :::image type="content" source="media/signalr-howto-use-managed-identity/system-identity-portal.png" alt-text="在入口網站中新增系統指派的身分識別":::

## <a name="add-a-user-assigned-identity"></a>新增使用者指派的身分識別

若要建立具有使用者指派身分識別的 Azure SignalR Service 實例，您必須建立身分識別，然後將其資源識別碼新增至您的服務。

1. 根據[這些指示](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)建立使用者指派的受控識別資源。

2. 像平常一樣在入口網站中建立 Azure SignalR Service 實例。 在入口網站中流覽至該網站。

3. 選取 [身分識別]。

4. 在 [ **使用者指派** ] 索引標籤上，選取 [ **新增**]。

5. 搜尋您稍早建立的身分識別，並選取它。 選取 [新增]  。

    :::image type="content" source="media/signalr-howto-use-managed-identity/user-identity-portal.png" alt-text="在入口網站中新增使用者指派的身分識別":::

## <a name="use-a-managed-identity-in-serverless-scenarios"></a>在無伺服器案例中使用受控識別

Azure SignalR Service 是完全受控的服務，所以您無法使用受控識別手動取得權杖。 相反地，Azure SignalR Service 會使用您設定的受控識別來取得存取權杖。 服務接著會在無伺服器案例的上游要求中，將存取權杖設定為 `Authorization` 標頭。

### <a name="enable-managed-identity-authentication-in-upstream-settings"></a>在上游設定中啟用受控識別驗證

1. 新增系統指派的身分識別或使用者指派的身分識別。

2. 新增一個上游設定，然後按一下任何星號以進入詳細頁面，如下所示。
    :::image type="content" source="media/signalr-howto-use-managed-identity/pre-msi-settings.png" alt-text="預先 msi-設定":::
    
    :::image type="content" source="media/signalr-howto-use-managed-identity/msi-settings.png" alt-text="msi-設定":::

3. 在受控識別驗證設定中，您可以在 [ **資源**] 中指定目標資源。 資源將成為 `aud` 取得的存取權杖中的宣告，可作為上游端點驗證的一部分。 資源可以是下列其中一項：
    - 空白
    - 服務主體的應用程式 (用戶端) 識別碼
    - 服務主體的應用程式識別碼 URI
    - [Azure 服務的資源識別碼](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

    > [!NOTE]
    > 如果您在服務中自行驗證存取權杖，您可以選擇任何一種資源格式。 請確定 **驗證** 設定中的 **資源** 值和驗證都一致。 如果您使用 Azure 角色型存取控制 (適用于資料平面的 Azure RBAC) ，您必須使用服務提供者要求的資源。

### <a name="validate-access-tokens"></a>驗證存取權杖

標頭中的權杖 `Authorization` 是 [Microsoft 身分識別平臺存取權杖](../active-directory/develop/access-tokens.md#validating-tokens)。

若要驗證存取權杖，您的應用程式也應該驗證物件和簽署權杖。 這些都需要對 OpenID 探索文件中的值進行驗證。 例如，請參閱 [租使用者獨立版本的檔](https://login.microsoftonline.com/common/.well-known/openid-configuration)。

Azure Active Directory (Azure AD) 中介軟體具有可驗證存取權杖的內建功能。 您可以流覽我們的 [範例](../active-directory/develop/sample-v2-code.md) ，以您選擇的語言尋找一個。

我們提供示範如何處理權杖驗證的程式庫和程式碼範例。 另外還有數個開放原始碼夥伴程式庫可供 JSON Web 權杖 (JWT) 驗證。 幾乎每個平臺和語言都有至少一個選項。 如需 Azure AD 驗證程式庫和程式碼範例的詳細資訊，請參閱 [Microsoft 身分識別平臺驗證程式庫](../active-directory/develop/reference-v2-libraries.md)。

#### <a name="authentication-in-function-app"></a>函數應用程式中的驗證

在函數應用程式中設定存取權杖驗證很簡單且有效率，而且沒有程式碼的作用。

1. 在 [ **驗證/授權** ] 頁面中，將 **App Service Authentication** 切換為 [ **開啟**]。

2. 選取 [ **使用 Azure Active Directory 的登入** ] **，以在要求未通過驗證時採取行動**。

3. 在驗證提供者中，按一下 [加入] **Azure Active Directory**

4. 在新頁面中。 選取 [**快速**] 並 **建立新的 AD 應用程式**，然後按一下 **[確定**:::image type="content" source="media/signalr-howto-use-managed-identity/function-aad.png" alt-text="函數 Aad"::: ]

5. 流覽至 SignalR Service，並遵循 [步驟](howto-use-managed-identity.md#add-a-system-assigned-identity) 來新增系統指派的身分識別或使用者指派的身分識別。

6. 進入 SignalR Service 的 **上游設定** ，然後選擇 [ **使用受控識別** ]，然後 **選取 [從現有的應用程式**]。 選取您先前建立的應用程式。

在這些設定之後，函式應用程式將會拒絕標頭中沒有存取權杖的要求。

## <a name="use-a-managed-identity-for-key-vault-reference"></a>使用受控識別進行 Key Vault 參考

SignalR Service 可以存取 Key Vault，以使用受控識別取得秘密。

1. 針對 Azure SignalR Service 新增系統指派的身分識別或使用者指派的身分識別。

2. 在 Key Vault 的存取原則中，授與受控識別的密碼讀取權限。 請參閱 [使用 Azure 入口網站指派 Key Vault 存取原則](../key-vault/general/assign-access-policy-portal.md)

目前，這項功能可用於下列案例：

- [上游 URL 模式中的參考秘密](./concept-upstream.md#key-vault-secret-reference-in-url-template-settings)


## <a name="next-steps"></a>後續步驟

- [使用 Azure SignalR Service 來開發與設定 Azure Functions](signalr-concept-serverless-development-config.md)