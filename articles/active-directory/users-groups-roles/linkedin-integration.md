---
title: LinkedIn帳戶連接的管理員同意 - Azure AD |微軟文檔
description: 說明如何在 Azure 活動目錄中的 Microsoft 應用中啟用或禁用LinkedIn集成帳戶連接
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0bf65f69d9dcaf6de2236c98b56b58ec7e021099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025404"
---
# <a name="integrate-linkedin-account-connections-in-azure-active-directory"></a>在 Azure 活動目錄中集成LinkedIn帳戶連接

您可以允許組織中的使用者訪問其在某些 Microsoft 應用中LinkedIn連接。 在使用者同意連接其帳戶之前，不會共用任何資料。 您可以將您的組織集成到 Azure 活動目錄 （Azure AD）[管理中心](https://aad.portal.azure.com)中。

> [!IMPORTANT]
> LinkedIn帳戶連接設置當前正在向 Azure AD 組織推出。 當它被推出到您的組織時，預設情況下將啟用它。
> 
> 例外狀況：
> * 此設定不適用於使用 Microsoft Cloud for US Government、Microsoft Cloud Germany 或中國 21Vianet 管理之 Azure 和 Office 365 的客戶。
> * 對於佈建於德國的租用戶，此設定預設為關閉。 請注意，此設定不適用於使用 Microsoft Cloud Germany 的客戶。
> * 對於佈建於法國的租用戶，此設定預設為關閉。
>
> 為您的組織啟用LinkedIn帳戶連接後，在使用者同意代表他們訪問公司資料的應用後，帳戶連接將起作用。 有關使用者同意設置的資訊，請參閱[如何刪除使用者對應用程式的訪問](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment)。

## <a name="enable-linkedin-account-connections-in-the-azure-portal"></a>在 Azure 門戶中啟用LinkedIn帳戶連接

您只能為希望訪問的使用者啟用LinkedIn帳戶連接，從整個組織僅對組織中選定的使用者。

1. 使用 Azure [AD](https://aad.portal.azure.com/)組織的全域管理員的帳戶登錄到 Azure AD 管理中心。
1. 選擇**使用者**。
1. 在 [使用者]**** 刀鋒視窗上，選取 [使用者設定]****。
1. 在**LinkedIn帳戶連接**下，允許使用者連接其帳戶以訪問某些 Microsoft 應用中LinkedIn連接。 在使用者同意連接其帳戶之前，不會共用任何資料。

    * 選擇 **"是**"可為組織中的所有使用者啟用該服務
    * 選擇 **"選定"組**，僅為組織中一組選定的使用者啟用該服務
    * 選擇 **"否**"以撤銷組織所有使用者的同意

    ![在組織中集成LinkedIn帳戶連接](./media/linkedin-integration/linkedin-integration.png)

1. 完成後，選擇 **"保存"** 以保存設置。

> [!Important]
> 在使用者同意連接其帳戶之前，不會完全啟用LinkedIn集成。 為使用者啟用帳戶連接時，不會共用任何資料。

### <a name="assign-selected-users-with-a-group"></a>使用組分配選定的使用者
我們已替換了"選擇"選項，該選項指定使用者清單，並選擇一組使用者，以便您可以啟用連接單個組（而不是許多單個使用者）LinkedIn和 Microsoft 帳戶的能力。 如果未為選定的單個使用者啟用LinkedIn帳戶連接，則無需執行任何操作。 如果您以前為選定的單個使用者啟用了LinkedIn帳戶連接，則應：

1. 獲取單個使用者的當前清單
1. 將當前啟用的單個使用者移動到組
1. 在 Azure AD 管理中心中的LinkedIn帳戶連接設置中，將上一個組用作所選組。

> [!NOTE]
> 即使您未將當前選定的單個使用者移動到組，他們仍可以在 Microsoft 應用中看到LinkedIn資訊。

### <a name="get-the-current-list-of-selected-users"></a>獲取所選使用者的當前清單

1. 使用管理員帳戶登錄 Microsoft 365。
1. 移至 https://linkedinselectedusermigration.azurewebsites.net/。 您將看到為LinkedIn帳戶連接選擇的使用者清單。
1. 將清單匯出到 CSV 檔。

### <a name="move-the-currently-selected-individual-users-to-a-group"></a>將當前選定的單個使用者移動到組

1. 啟動 PowerShell
1. 通過運行安裝 Azure AD 模組`Install-Module AzureAD`
1. 執行下列指令碼：

  ``` PowerShell
  $groupId = "GUID of the target group"
  
  $users = Get-Content 
  Path to the CSV file
  
  $i = 1
  foreach($user in $users} { Add-AzureADGroupMember -ObjectId $groupId -RefObjectId $user ; Write-Host $i Added $user ; $i++ ; Start-Sleep -Milliseconds 10 }
  ```

要在 Azure AD 管理中心中的LinkedIn帳戶連接設置中使用步驟 2 中的組作為所選組，請參閱[在 Azure 門戶中啟用LinkedIn帳戶連接](#enable-linkedin-account-connections-in-the-azure-portal)。

## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>使用群組原則啟用LinkedIn帳戶連接

1. 下載 [Office 2016 系統管理範本檔案 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
1. 解壓縮 **ADMX** 檔案，並將其複製到中央存放區。
1. 開啟群組原則管理。
1. 創建具有以下設置的群組原則物件：**使用者配置** > **管理範本** > **Microsoft Office 2016** > **雜項** > **顯示 office 應用程式中的LinkedIn功能**。
1. 選取 [啟用]**** 或 [停用]****。
  
   State | 效果
   ------ | ------
   **啟用** | Office 2016 選項中的 [在 Office 應用程式中顯示 LinkedIn 功能]**** 設定已啟用。 組織中的使用者可以在其 Office 2016 應用程式中使用LinkedIn功能。
   **禁用** | Office 2016 選項中的 [在 Office 應用程式中顯示 LinkedIn 功能]**** 設定已停用，而且終端使用者無法變更此設定。 組織中的使用者無法在其 Office 2016 應用程式中使用 LinkedIn 功能。

這個群組原則只會影響本機電腦的 Office 2016 應用程式。 如果使用者在其 Office 2016 應用中禁用LinkedIn，他們仍然可以在 Office 365 中看到LinkedIn功能。

## <a name="next-steps"></a>後續步驟

* [LinkedIn的使用者同意和資料共用](linkedin-user-consent.md)

* [Microsoft 應用程式中的 LinkedIn 資訊和功能](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn 說明中心](https://www.linkedin.com/help/linkedin)

* [在 Azure 入口網站中檢視您目前的 LinkedIn 整合設定](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
