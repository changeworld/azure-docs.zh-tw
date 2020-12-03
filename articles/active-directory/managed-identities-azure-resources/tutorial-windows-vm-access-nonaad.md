---
title: 教學課程`:` 使用受控識別來存取 Azure Key Vault - Windows - Azure AD
description: 本教學課程會逐步引導您使用 Windows VM 系統指派的受控識別，以存取 Azure Key Vault。
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
ms.openlocfilehash: fa17a18de8e71b099d6ed717974486203c4379f4
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96180501"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>教學課程：使用 Windows VM 系統指派的受控識別來存取 Azure Key Vault 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

本教學課程說明 Windows 虛擬機器 (VM) 可以如何使用系統指派的受控識別，以存取 [Azure Key Vault](../../key-vault/general/overview.md)。 作為啟動程序，金鑰保存庫可讓您的用戶端應用程式，接著使用密碼存取未受 Azure Active Directory (AD) 保護的資源。 受控服務身分識別由 Azure 自動管理，並可讓您驗證支援 Azure AD 驗證的服務，而不需要將驗證資訊包含在您的程式碼中。

您會了解如何：

> [!div class="checklist"]
> * 授與 VM 存取權以取得 Key Vault 中的密碼
> * 使用 VM 身分識別取得存取權杖，並使用它來擷取 Key Vault 的祕密 

## <a name="prerequisites"></a>Prerequisites

- 了解受控識別。 如果您不熟悉適用於 Azure 資源的受控識別功能，請參閱此[概觀](overview.md)。 
- Azure 帳戶，[註冊免費帳戶](https://azure.microsoft.com/free/)。
- 若要執行所需的資源建立和角色管理步驟，需要在適當的範圍 (您的訂用帳戶或資源群組) 上具備「擁有者」權限。 如果您需要角色指派的協助，請參閱[使用角色型存取控制來管理 Azure 訂用帳戶資源的存取權](../../role-based-access-control/role-assignments-portal.md)。
- 您也需要已啟用系統指派受控識別的 Windows 虛擬機器。
  - 如果您需要為本教學課程建立虛擬機器，您可以遵循標題為[建立已啟用系統指派身分識別的虛擬機器](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)的文章

## <a name="create-a-key-vault"></a>建立金鑰保存庫  

本節將說明如何授與 VM 存取權以取得 Key Vault 中的祕密。 您的程式碼可以使用適用於 Azure 資源的受控識別來取得存取權杖，以向支援 Azure AD 驗證的資源進行驗證。但是，並非所有 Azure 服務都支援 Azure AD 驗證。 若要使用適用於 Azure 資源的受控識別搭配那些服務，請將服務認證儲存在 Azure Key Vault 中，並使用 VM 的受控識別來存取 Key Vault，以擷取認證。

首先，我們需要建立 Key Vault，並將存取 Key Vault 的權利授予 VM 系統指派的受控識別。

1. 開啟 Azure [入口網站](https://portal.azure.com/)
1. 在左側導覽列的頂端，選取 [建立資源]  
1. 在 [搜尋 Marketplace] 方塊中輸入 **Key Vault** 然後點擊 **Enter** 鍵。  
1. 從結果中選取 [Key Vault]。
1. 選取 [建立] 
1. 提供新 Key Vault 的 **名稱**。

    ![建立金鑰保存庫畫面](./media/msi-tutorial-windows-vm-access-nonaad/create-key-vault.png)

1. 填寫所有必要的資訊，並確定您選擇的訂用帳戶和資源群組，是您在本教學課程中用來建立虛擬機器的位置。
1. 選取 [檢閱 + 建立]
1. 選取 [建立] 

## <a name="grant-access"></a>授與存取權

虛擬機器所使用的受控識別必須被授與存取權，以讀取我們將儲存在 Key Vault 中的祕密。

1. 瀏覽到您新建立的 Key Vault
1. 從左側的功能表中選取 [存取原則]。
1. 選取 [新增存取原則]

   ![金鑰保存庫建立存取原則畫面](./media/msi-tutorial-windows-vm-access-nonaad/key-vault-access-policy.png)

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

   ![建立祕密](./media/msi-tutorial-windows-vm-access-nonaad/create-secret.png)

## <a name="access-data"></a>存取資料  

本節將說明如何使用 VM 身分識別取得存取權杖，並用其來擷取 Key Vault 的密碼。 如果您未安裝 PowerShell 4.3.1 或更新版本，則必須[下載並安裝最新版](/powershell/azure/)。

首先，我們會使用 VM 系統指派的受控識別來取得存取權杖，以向 Key Vault 進行驗證：
 
1. 在入口網站中，瀏覽至 [虛擬機器] 並移至您的 Windows 虛擬機器，在 [概觀] 中按一下 [連線]。
2. 輸入您建立 **Windows VM** 時新增的 **使用者名稱** 和 **密碼**。  
3. 現在您已經建立虛擬機器的 **遠端桌面連線**，請在遠端工作階段中開啟 PowerShell。  
4. 在 PowerShell 中，叫用租用戶上的 Web 要求，以在 VM 的特定連接埠中取得本機主機的權杖。  

PowerShell 要求：

```powershell
$Response = Invoke-RestMethod -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -Method GET -Headers @{Metadata="true"} 
```

您可以在下方看到回應的外觀：

![具有權杖回應的要求](./media/msi-tutorial-windows-vm-access-nonaad/token.png)

再來，從回應中擷取存取權杖。  

```powershell
   $KeyVaultToken = $Response.access_token
```

最後，使用 PowerShell 的 Invoke-WebRequest 命令來擷取您稍早在 Key Vault 中建立的祕密，在授權標頭中傳遞存取權杖。  您會需要 Key Vault 的 URL，其位於 Key Vault [概觀] 頁面的 [基本資訊] 區段。  

```powershell
Invoke-RestMethod -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}
```

回應如下所示： 

```powershell
  value       id                                                                                    attributes
  -----       --                                                                                    ----------
  'My Secret' https://mi-lab-vault.vault.azure.net/secrets/mi-test/50644e90b13249b584c44b9f712f2e51 @{enabled=True; created=16…
```

一旦您已從 Key Vault 擷取密碼，您便可以將其用來向需要名稱和密碼的服務進行驗證。

## <a name="clean-up-resources"></a>清除資源

當您想要清除資源時，請造訪 [Azure 入口網站](https://portal.azure.com)，選取 [資源群組]，找到並選取在本教學課程的程序中建立的資源群組 (例如 `mi-test`)，然後使用 **Delete resource group** 命令。

或者，您也可以透過 [PowerShell 或 CLI](../../azure-resource-manager/management/delete-resource-group.md) 來執行此動作

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何使用 Windows VM 系統指派的受控識別，來存取 Azure Key Vault。  若要深入了解 Azure Key Vault，請參閱：

> [!div class="nextstepaction"]
>[Azure Key Vault](../../key-vault/general/overview.md)