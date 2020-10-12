---
title: 設定 Azure Active Directory 存取-Azure 區塊鏈服務
description: 如何使用 Azure Active Directory 存取設定 Azure 區塊鏈服務
ms.date: 11/22/2019
ms.topic: how-to
ms.reviewer: janders
ms.openlocfilehash: 337d01abc51d310d06aeea3427b770132be4824c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85208768"
---
# <a name="how-to-configure-azure-active-directory-access-for-azure-blockchain-service"></a>如何設定 Azure 區塊鏈服務的 Azure Active Directory 存取

在本文中，您將瞭解如何使用 Azure Active Directory (Azure AD) 使用者、群組或應用程式識別碼，來授與存取權並連接至 Azure 區塊鏈服務節點。

Azure AD 提供雲端式身分識別管理，並可讓您在整個企業中使用單一身分識別，並存取 Azure 中的應用程式。 Azure 區塊鏈服務與 Azure AD 整合，並提供一些優點，例如識別碼同盟、單一登入和多重要素驗證。

## <a name="prerequisites"></a>必要條件

* [使用 Azure 入口網站建立區塊鏈成員](create-member.md)

## <a name="grant-access"></a>授與存取權

您可以在成員層級和節點層級授與存取權。 在成員層級授與存取權限，將會接著授與成員下所有節點的存取權。

### <a name="grant-member-level-access"></a>授與成員層級存取權

授與成員層級的存取權限。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 流覽至 [ **存取控制] (IAM) > 新增 > 新增角色指派**]。
1. 選取 **區塊鏈成員節點存取 (預覽版) ** 角色，然後新增您想要授與存取權的 Azure AD 識別碼物件。 Azure AD ID 物件可以是：

    | Azure AD 物件 | 範例 |
    |-----------------|---------|
    | Azure AD 使用者   | `kim@contoso.onmicrosoft.com` |
    | Azure AD 群組  | `sales@contoso.onmicrosoft.com` |
    | 應用程式識別碼  | `13925ab1-4161-4534-8d18-812f5ca1ab1e` |

    ![新增角色指派](./media/configure-aad/add-role-assignment.png)

1. 選取 [儲存]。

### <a name="grant-node-level-access"></a>授與節點層級存取權

您可以藉由流覽至節點安全性，然後按一下您想要授與存取權的節點名稱，在節點層級授與存取權。

選取區塊鏈成員節點存取 (預覽版) 角色，然後新增您想要授與存取權的 Azure AD 識別碼物件。

如需詳細資訊，請參閱 [設定 Azure 區塊鏈服務交易節點](configure-transaction-nodes.md#azure-active-directory-access-control)。

## <a name="connect-using-azure-blockchain-connector"></a>使用 Azure 區塊鏈 Connector 連接

從 GitHub 下載或複製 [Azure 區塊鏈 Connector](https://github.com/Microsoft/azure-blockchain-connector/)。

```bash
git clone https://github.com/Microsoft/azure-blockchain-connector.git
```

遵循 **讀我檔案** 中的快速入門一節，從原始程式碼建立連接器。

### <a name="connect-using-an-azure-ad-user-account"></a>使用 Azure AD 使用者帳戶連接

1. 執行下列命令，以使用 Azure AD 使用者帳戶進行驗證。 取代 \<myAADDirectory\> 為 Azure AD 的網域。 例如： `yourdomain.onmicrosoft.com` 。

    ```
    connector.exe -remote <myMemberName>.blockchain.azure.com:3200 -method aadauthcode -tenant-id <myAADDirectory> 
    ```

1. Azure AD 提示輸入認證。
1. 使用您的使用者名稱和密碼登入。
1. 驗證成功之後，您的本機 proxy 會連線至您的區塊鏈節點。 您現在可以附加 Geth 用戶端與本機端點。

    ```bash
    geth attach http://127.0.0.1:3100
    ```

### <a name="connect-using-an-application-id"></a>使用應用程式識別碼連接

許多應用程式會使用應用程式識別碼（而不是 Azure AD 使用者帳戶）向 Azure AD 進行驗證。

若要使用應用程式識別碼連接到您的節點，請將 **aadauthcode** 取代為 **aadclient**。

```
connector.exe -remote <myBlockchainEndpoint>  -method aadclient -client-id <myClientID> -client-secret "<myClientSecret>" -tenant-id <myAADDirectory>
```

| 參數 | 描述 |
|-----------|-------------|
| 租使用者識別碼 | Azure AD 網域，例如 `yourdomain.onmicrosoft.com`
| client-id | Azure AD 中已註冊之應用程式的用戶端識別碼
| client-secret | Azure AD 中已註冊之應用程式的用戶端密碼

如需如何在 Azure AD 中註冊應用程式的詳細資訊，請參閱 [如何：使用入口網站來建立可存取資源的 Azure AD 應用程式和服務主體](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="connect-a-mobile-device-or-text-browser"></a>連接行動裝置或文字瀏覽器

若為行動裝置或以文字為基礎的瀏覽器，其中不可能 Azure AD authentication 彈出顯示，Azure AD 會產生一次性密碼。 您可以複製密碼，並在另一個環境中繼續 Azure AD 驗證。

若要產生密碼，請將 **aadauthcode** 取代為 **aaddevice**。 取代 \<myAADDirectory\> 為 Azure AD 的網域。 例如 `yourdomain.onmicrosoft.com`。

```
connector.exe -remote <myBlockchainEndpoint>  -method aaddevice -tenant-id <myAADDirectory>
```

## <a name="next-steps"></a>後續步驟

如需 Azure 區塊鏈服務中資料安全性的詳細資訊，請參閱 [Azure 區塊鏈服務安全性](data-security.md)。