---
title: Azure SignalR Service 中的受控識別
description: 瞭解受控識別在 Azure SignalR Service 中的運作方式，以及如何在無伺服器案例中使用受控識別。
author: chenyl
ms.service: signalr
ms.topic: article
ms.date: 06/8/2020
ms.author: chenyl
ms.openlocfilehash: cc7082744bc43baad2e26d09a83907540cf6a1df
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094070"
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

5. 搜尋您稍早建立的身分識別，然後選取它。 選取 [新增]  。

    :::image type="content" source="media/signalr-howto-use-managed-identity/user-identity-portal.png" alt-text="在入口網站中新增系統指派的身分識別":::

## <a name="use-a-managed-identity-in-serverless-scenarios"></a>在無伺服器案例中使用受控識別

Azure SignalR Service 是完全受控的服務，所以您無法使用受控識別手動取得權杖。 相反地，Azure SignalR Service 會使用您設定的受控識別來取得存取權杖。 服務接著會在無伺服器案例的上游要求中，將存取權杖設定為 `Authorization` 標頭。

### <a name="enable-managed-identity-authentication-in-upstream-settings"></a>在上游設定中啟用受控識別驗證

1. 新增系統指派的身分識別或使用者指派的身分識別。

2. 設定上游設定，並使用 **ManagedIdentity** 作為 **驗證** 設定。 若要瞭解如何使用驗證建立上游設定，請參閱 [上游設定](concept-upstream.md)。

3. 在受控識別驗證設定中，您可以在 [ **資源**] 中指定目標資源。 資源將成為 `aud` 取得的存取權杖中的宣告，可作為上游端點驗證的一部分。 資源可以是下列其中一項：
    - Empty
    - 服務主體的應用程式 (用戶端) 識別碼
    - 服務主體的應用程式識別碼 URI
    - [Azure 服務的資源識別碼](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities#azure-services-that-support-azure-ad-authentication)

    > [!NOTE]
    > 如果您在服務中自行驗證存取權杖，您可以選擇任何一種資源格式。 請確定**驗證**設定中的**資源**值和驗證都一致。 如果您使用 Azure 角色型存取控制 (適用于資料平面的 Azure RBAC) ，您必須使用服務提供者要求的資源。

### <a name="validate-access-tokens"></a>驗證存取權杖

標頭中的權杖 `Authorization` 是 [Microsoft 身分識別平臺存取權杖](https://docs.microsoft.com/azure/active-directory/develop/access-tokens#validating-tokens)。

若要驗證存取權杖，您的應用程式也應該驗證物件和簽署權杖。 這些都需要對 OpenID 探索文件中的值進行驗證。 例如，請參閱 [租使用者獨立版本的檔](https://login.microsoftonline.com/common/.well-known/openid-configuration)。

Azure Active Directory (Azure AD) 中介軟體具有可驗證存取權杖的內建功能。 您可以流覽我們的 [範例](https://docs.microsoft.com/azure/active-directory/develop/sample-v2-code) ，以您選擇的語言尋找一個。

我們提供示範如何處理權杖驗證的程式庫和程式碼範例。 另外還有數個開放原始碼夥伴程式庫可供 JSON Web 權杖 (JWT) 驗證。 幾乎每個平臺和語言都有至少一個選項。 如需 Azure AD 驗證程式庫和程式碼範例的詳細資訊，請參閱 [Microsoft 身分識別平臺驗證程式庫](https://docs.microsoft.com/azure/active-directory/develop/reference-v2-libraries)。

## <a name="next-steps"></a>後續步驟

- [使用 Azure SignalR Service 來開發與設定 Azure Functions](signalr-concept-serverless-development-config.md)
