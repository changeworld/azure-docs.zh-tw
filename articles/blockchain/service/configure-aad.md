---
title: 設定 Azure Active Directory 存取-Azure 區塊鏈 Service
description: 如何使用 Azure Active Directory 存取來設定 Azure 區塊鏈 Service
ms.date: 11/22/2019
ms.topic: how-to
ms.reviewer: janders
ms.openlocfilehash: 337d01abc51d310d06aeea3427b770132be4824c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85208768"
---
# <a name="how-to-configure-azure-active-directory-access-for-azure-blockchain-service"></a>如何設定 Azure 區塊鏈 Service 的 Azure Active Directory 存取

在本文中，您將瞭解如何使用 Azure Active Directory （Azure AD）使用者、群組或應用程式識別碼，授與存取權並聯機至 Azure 區塊鏈服務節點。

Azure AD 提供以雲端為基礎的身分識別管理，並可讓您在整個企業中使用單一身分識別，並存取 Azure 中的應用程式。 Azure 區塊鏈 Service 已與 Azure AD 整合，並提供識別碼同盟、單一登入和多重要素驗證等優點。

## <a name="prerequisites"></a>必要條件

* [使用 Azure 入口網站建立區塊鏈成員](create-member.md)

## <a name="grant-access"></a>授與存取權

您可以同時授與成員層級和節點層級的存取權。 授與成員層級的存取權限時，會將存取權授與成員底下的所有節點。

### <a name="grant-member-level-access"></a>授與成員層級存取權

授與成員層級的存取權限。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 流覽至 **[存取控制（IAM）] > 新增 > 新增角色指派**]。
1. 選取 [**區塊鏈成員節點存取（預覽）** ] 角色，並新增您想要授與存取權的 Azure AD ID 物件。 Azure AD 識別碼物件可以是：

    | Azure AD 物件 | 範例 |
    |-----------------|---------|
    | Azure AD 使用者   | `kim@contoso.onmicrosoft.com` |
    | Azure AD 群組  | `sales@contoso.onmicrosoft.com` |
    | 應用程式識別碼  | `13925ab1-4161-4534-8d18-812f5ca1ab1e` |

    ![新增角色指派](./media/configure-aad/add-role-assignment.png)

1. 選取 [儲存]。

### <a name="grant-node-level-access"></a>授與節點層級存取權

您可以流覽至 [節點安全性]，然後按一下您想要授與存取權的節點名稱，以授與節點層級的存取權。

選取 [區塊鏈成員節點存取（預覽）] 角色，並新增您想要授與存取權的 Azure AD ID 物件。

如需詳細資訊，請參閱[設定 Azure 區塊鏈 Service 交易節點](configure-transaction-nodes.md#azure-active-directory-access-control)。

## <a name="connect-using-azure-blockchain-connector"></a>使用 Azure 區塊鏈 Connector 進行連線

從 GitHub 下載或複製[Azure 區塊鏈連接器](https://github.com/Microsoft/azure-blockchain-connector/)。

```bash
git clone https://github.com/Microsoft/azure-blockchain-connector.git
```

遵循**讀我檔案**中的 [快速入門] 區段，以從原始程式碼建立連接器。

### <a name="connect-using-an-azure-ad-user-account"></a>使用 Azure AD 使用者帳戶連接

1. 執行下列命令，以使用 Azure AD 使用者帳戶進行驗證。 取代 \<myAADDirectory\> 為 Azure AD 網域。 例如： `yourdomain.onmicrosoft.com` 。

    ```
    connector.exe -remote <myMemberName>.blockchain.azure.com:3200 -method aadauthcode -tenant-id <myAADDirectory> 
    ```

1. Azure AD 提示輸入認證。
1. 使用您的使用者名稱和密碼登入。
1. 驗證成功之後，您的本機 proxy 會連線到您的區塊鏈節點。 您現在可以附加 Geth 用戶端與本機端點。

    ```bash
    geth attach http://127.0.0.1:3100
    ```

### <a name="connect-using-an-application-id"></a>使用應用程式識別碼連接

許多應用程式會使用應用程式識別碼來驗證 Azure AD，而不是 Azure AD 的使用者帳戶。

若要使用應用程式識別碼連線到您的節點，請將**aadauthcode**取代為**aadclient**。

```
connector.exe -remote <myBlockchainEndpoint>  -method aadclient -client-id <myClientID> -client-secret "<myClientSecret>" -tenant-id <myAADDirectory>
```

| 參數 | 說明 |
|-----------|-------------|
| 租使用者識別碼 | Azure AD 網域，例如`yourdomain.onmicrosoft.com`
| client-id | 中已註冊應用程式的用戶端識別碼 Azure AD
| client-secret | Azure AD 中已註冊應用程式的用戶端密碼

如需如何在 Azure AD 中註冊應用程式的詳細資訊，請參閱[如何：使用入口網站建立可存取資源的 Azure AD 應用程式和服務主體](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="connect-a-mobile-device-or-text-browser"></a>連接行動裝置或文字瀏覽器

若為行動裝置或以文字為基礎的瀏覽器，其中不可能 Azure AD 驗證快顯顯示，Azure AD 會產生一次性密碼。 您可以複製密碼，然後在另一個環境中 Azure AD 驗證繼續進行。

若要產生密碼，請將**aadauthcode**取代為**aaddevice**。 取代 \<myAADDirectory\> 為 Azure AD 網域。 例如 `yourdomain.onmicrosoft.com`。

```
connector.exe -remote <myBlockchainEndpoint>  -method aaddevice -tenant-id <myAADDirectory>
```

## <a name="next-steps"></a>後續步驟

如需有關 Azure 區塊鏈 Service 中資料安全性的詳細資訊，請參閱[Azure 區塊鏈 Service 安全性](data-security.md)。