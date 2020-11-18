---
title: 教學課程`:` 使用受控識別來存取 Azure Key Vault - Linux - Azure AD
description: 本教學課程會逐步引導您使用 Linux VM 系統指派的受控識別，以存取 Azure Resource Manager。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/03/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cfcaec38a939291090da7d2229c4a95f984bf28
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360429"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>教學課程：使用 VM 系統指派的受控識別來存取 Azure Key Vault 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

本教學課程說明 Linux 虛擬機器 (VM) 可以如何使用系統指派的受控識別，以存取 [Azure Key Vault](../../key-vault/general/overview.md)。 作為啟動程序，金鑰保存庫可讓您的用戶端應用程式，接著使用密碼存取未受 Azure Active Directory (AD) 保護的資源。 受控服務身分識別由 Azure 自動管理，並可讓您驗證支援 Azure AD 驗證的服務，而不需要將驗證資訊包含在您的程式碼中。

您會了解如何：

> [!div class="checklist"]
> * 授與 VM 存取權以取得 Key Vault 中的密碼 
> * 使用 VM 身分識別取得存取權杖，並使用它來擷取 Key Vault 的密碼 
 
## <a name="prerequisites"></a>Prerequisites

- 了解受控識別。 如果您不熟悉適用於 Azure 資源的受控識別功能，請參閱此[概觀](overview.md)。 
- Azure 帳戶，[註冊免費帳戶](https://azure.microsoft.com/free/)。
- 若要執行所需的資源建立和角色管理步驟，需要在適當的範圍 (您的訂用帳戶或資源群組) 上具備「擁有者」權限。 如果您需要角色指派的協助，請參閱[使用角色型存取控制來管理 Azure 訂用帳戶資源的存取權](../../role-based-access-control/role-assignments-portal.md)。
- 您也需要已啟用系統指派受控識別的 Linux 虛擬機器。
  - 如果您需要為本教學課程建立虛擬機器，您可以遵循標題為[使用 Azure 入口網站建立 Linux 虛擬機器](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)的文章


## <a name="create-a-key-vault"></a>建立金鑰保存庫  

本節將說明如何授與 VM 存取權以取得 Key Vault 中的祕密。 您的程式碼可以使用適用於 Azure 資源的受控識別來取得存取權杖，以向支援 Azure AD 驗證的資源進行驗證。但是，並非所有 Azure 服務都支援 Azure AD 驗證。 若要使用適用於 Azure 資源的受控識別搭配那些服務，請將服務認證儲存在 Azure Key Vault 中，並使用 VM 的受控識別來存取 Key Vault，以擷取認證。

首先，我們需要建立 Key Vault，並將存取 Key Vault 的權利授予 VM 系統指派的受控識別。

1. 開啟 Azure [入口網站](https://portal.azure.com/)
1. 在左側導覽列的頂端，選取 [建立資源]  
1. 在 [搜尋 Marketplace] 方塊中輸入 **Key Vault** 然後點擊 **Enter** 鍵。  
1. 從結果中選取 [Key Vault]。
1. 選取 [建立] 
1. 提供新 Key Vault 的 **名稱**。

    ![建立金鑰保存庫畫面](./media/tutorial-linux-vm-access-nonaad/create-key-vault.png)

1. 填寫所有必要的資訊，並確定您選擇的訂用帳戶和資源群組，是您在本教學課程中用來建立虛擬機器的位置。
1. 選取 [檢閱 + 建立]
1. 選取 [建立] 

## <a name="grant-access"></a>授與存取權

虛擬機器所使用的受控識別必須被授與存取權，以讀取我們將儲存在 Key Vault 中的祕密。

1. 瀏覽到您新建立的 Key Vault
1. 從左側的功能表中選取 [存取原則]。
1. 選取 [新增存取原則]

   ![金鑰保存庫建立存取原則畫面](./media/tutorial-linux-vm-access-nonaad/key-vault-access-policy.png)

1. 在 [從範本設定 (選擇性)] 下的 [新增存取原則] 區段中，從下拉式功能表中選擇 [祕密管理]。
1. 選擇 [選取主體]，並在 [搜尋] 欄位中輸入您稍早建立的 VM 名稱。  在結果清單中選取 VM，然後選擇 [選取]。
1. 選取 [新增]
1. 選取 [儲存]。

## <a name="create-a-secret"></a>建立祕密

接下來，將祕密新增至 Key Vault，好讓您稍後可以使用在 VM 中執行的程式碼來擷取祕密。 基於本教學課程的目的，我們使用 PowerShell，但是相同的概念適用於在此虛擬機器中執行的任何程式碼。

1. 瀏覽到您新建立的 Key Vault。
1. 選取 [密碼]，然後按一下 [新增]。
1. 選取 [產生/匯入]
1. 在 [上傳選項] 的 [建立祕密] 畫面中，保持選取 [手動]。
1. 輸入密碼的名稱和值。  值可以是任何您想要的項目。 
1. 將啟動日期和到期日期保留空白，並將 [啟用] 設定為 [是]。 
1. 按一下 [建立] 來建立密碼。

   ![建立祕密](./media/tutorial-linux-vm-access-nonaad/create-secret.png)
 
## <a name="access-data"></a>存取資料

若要完成這些步驟，您需要 SSH 用戶端。  如果您使用 Windows，您可以在[適用於 Linux 的 Windows 子系統](/windows/wsl/about)中使用 SSH 用戶端。 如果您需要設定 SSH 用戶端金鑰的協助，請參閱[如何在 Azure 上搭配 Windows 使用 SSH 金鑰](../../virtual-machines/linux/ssh-from-windows.md)，或[如何在 Azure 中建立和使用 Linux VM 的 SSH 公開和私密金鑰組](../../virtual-machines/linux/mac-create-ssh-keys.md)。
 
1. 在入口網站中，瀏覽至 [Linux VM]，並在 [概觀]  中按一下 [連線]  。 
2. 使用您所選擇的 SSH 用戶端來 **連線** 到 VM。 
3. 在終端機視窗中，使用 CURL 向 Azure 資源端點的本機受控識別提出要求，以取得 Azure Key Vault 的存取權杖。  
 
    存取權杖的 CURL 要求如下。  
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true  
    ```
    此回應包含您存取 Resource Manager 所需的存取權杖。 
    
    回應：  
    
    ```bash
    {"access_token":"eyJ0eXAi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://vault.azure.net",
    "token_type":"Bearer"} 
    ```
    
    您可以使用此存取權杖來向 Azure Key Vault 進行驗證。  下一個 CURL 要求會說明如何使用 CURL 和 Key Vault REST API 從 Key Vault 讀取密碼。  您會需要 Key Vault 的 URL，其位於 Key Vault [概觀] 頁面的 [基本資訊] 區段。  您也需要在前一次呼叫取得的存取權杖。 
        
    ```bash
    curl https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    回應如下所示： 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
一旦您已從 Key Vault 擷取密碼，您便可以將其用來向需要名稱和密碼的服務進行驗證。

## <a name="clean-up-resources"></a>清除資源

當您想要清除資源時，請造訪 [Azure 入口網站](https://portal.azure.com)，選取 [資源群組]，找到並選取在本教學課程的程序中建立的資源群組 (例如 `mi-test`)，然後使用 **Delete resource group** 命令。

或者，您也可以透過 [PowerShell 或 CLI](../../azure-resource-manager/management/delete-resource-group.md) 來執行此動作

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何使用 Linux VM 系統指派的受控識別，來存取 Azure Key Vault。  若要深入了解 Azure Key Vault，請參閱：

> [!div class="nextstepaction"]
>[Azure Key Vault](../../key-vault/general/overview.md)