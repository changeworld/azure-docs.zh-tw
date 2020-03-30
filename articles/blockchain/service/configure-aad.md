---
title: 配置 Azure 活動目錄訪問 - Azure 區塊鏈服務
description: 如何使用 Azure 活動目錄訪問配置 Azure 區塊鏈服務
ms.date: 11/22/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: 682ab282036fcd592e66942d08a84cdce46d8915
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455864"
---
# <a name="how-to-configure-azure-active-directory-access-for-azure-blockchain-service"></a>如何為 Azure 區塊鏈服務配置 Azure 活動目錄訪問

在本文中，您將瞭解如何使用 Azure 活動目錄 （Azure AD） 使用者、組或應用程式標識授予存取權限並連接到 Azure 區塊鏈服務節點。

Azure AD 提供基於雲的標識管理，並允許您在整個企業和 Azure 中訪問應用程式中使用單個標識。 Azure 區塊鏈服務與 Azure AD 集成，並提供 ID 聯合、單一登入和多重要素驗證等優勢。

## <a name="prerequisites"></a>Prerequisites

* [使用 Azure 門戶創建區塊鏈成員](create-member.md)

## <a name="grant-access"></a>授與存取權

您可以在成員級別和節點級別授予存取權限。 在成員級別授予存取權限反過來將授予對成員下的所有節點的存取權限。

### <a name="grant-member-level-access"></a>授予成員級別存取權限

授予成員級別的存取權限。

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 導航到**存取控制 （IAM） >添加>添加角色指派**。
1. 選擇**區塊鏈成員節點訪問（預覽）** 角色，並添加要授予存取權限的 Azure AD ID 物件。 Azure AD ID 物件可以是：

    | Azure AD 物件 | 範例 |
    |-----------------|---------|
    | Azure AD 使用者   | `kim@contoso.onmicrosoft.com` |
    | Azure AD 組  | `sales@contoso.onmicrosoft.com` |
    | 應用程式識別碼  | `13925ab1-4161-4534-8d18-812f5ca1ab1e` |

    ![新增角色指派](./media/configure-aad/add-role-assignment.png)

1. 選取 [儲存]****。

### <a name="grant-node-level-access"></a>授予節點級別訪問

您可以通過導航到節點安全性並按一下要授予存取權限的節點名稱來授予節點級別的存取權限。

選擇區塊鏈成員節點訪問（預覽）角色，並添加要授予存取權限的 Azure AD ID 物件。

有關詳細資訊，請參閱配置[Azure 區塊鏈服務事務節點](configure-transaction-nodes.md#azure-active-directory-access-control)。

## <a name="connect-using-azure-blockchain-connector"></a>使用 Azure 區塊鏈連接器進行連接

從 GitHub 下載或克隆[Azure 區塊鏈連接器](https://github.com/Microsoft/azure-blockchain-connector/)。

```bash
git clone https://github.com/Microsoft/azure-blockchain-connector.git
```

遵循**讀中的**快速入門部分從原始程式碼構建連接器。

### <a name="connect-using-an-azure-ad-user-account"></a>使用 Azure AD 使用者帳戶進行連接

1. 運行以下命令以使用 Azure AD 使用者帳戶進行身份驗證。 將\<myAADDirectory\>替換為 Azure AD 域。 例如： `yourdomain.onmicrosoft.com` 。

    ```
    connector.exe -remote <myMemberName>.blockchain.azure.com:3200 -method aadauthcode -tenant-id <myAADDirectory> 
    ```

1. Azure AD 提示憑據。
1. 使用使用者名和密碼登錄。
1. 身份驗證成功後，本地代理將連接到區塊鏈節點。 現在，您可以將 Geth 用戶端與本地終結點連接。

    ```bash
    geth attach http://127.0.0.1:3100
    ```

### <a name="connect-using-an-application-id"></a>使用應用程式 ID 進行連接

許多應用程式使用應用程式 ID 而不是 Azure AD 使用者帳戶對 Azure AD 進行身份驗證。

要使用應用程式 ID 連接到節點，請將**aadauth 碼**替換為**aadclient**。

```
connector.exe -remote <myBlockchainEndpoint>  -method aadclient -client-id <myClientID> -client-secret "<myClientSecret>" -tenant-id <myAADDirectory>
```

| 參數 | 描述 |
|-----------|-------------|
| 租戶 id | Azure AD 域，例如，`yourdomain.onmicrosoft.com`
| client-id | Azure AD 中註冊應用程式的用戶端 ID
| client-secret | Azure AD 中註冊應用程式的用戶端機密

有關如何在 Azure AD 中註冊應用程式的詳細資訊，請參閱[：使用門戶創建可以訪問資源的 Azure AD 應用程式和服務主體](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="connect-a-mobile-device-or-text-browser"></a>連接行動裝置或文本瀏覽器

對於無法顯示 Azure AD 身份驗證快顯視窗的行動裝置或基於文本的瀏覽器，Azure AD 將生成一次性密碼。 您可以複製密碼，並在其他環境中繼續 Azure AD 身份驗證。

要生成密碼，請將**aadauth 碼**替換為**aaddevice**。 將\<myAADDirectory\>替換為 Azure AD 域。 例如： `yourdomain.onmicrosoft.com` 。

```
connector.exe -remote <myBlockchainEndpoint>  -method aaddevice -tenant-id <myAADDirectory>
```

## <a name="next-steps"></a>後續步驟

有關 Azure 區塊鏈服務中的資料安全性的詳細資訊，請參閱[Azure 區塊鏈服務安全性](data-security.md)。