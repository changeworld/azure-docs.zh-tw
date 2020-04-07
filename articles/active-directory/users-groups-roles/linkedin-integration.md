---
title: LinkedIn帳戶連接的管理員同意 - Azure AD |微軟文件
description: 說明如何在 Azure 活動目錄中的 Microsoft 應用程式中啟用或關閉LinkedIn整合帳戶連線
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
ms.openlocfilehash: 54e3821d269d11397ec4f9f5833e33ac6b555abc
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755111"
---
# <a name="integrate-linkedin-account-connections-in-azure-active-directory"></a>在 Azure 活動目錄中整合LinkedIn帳戶連線

您可以允許組織中的用戶訪問其在某些 Microsoft 應用中LinkedIn連接。 在使用者同意連接其帳戶之前,不會共用任何數據。 您可以將您的組織整合到 Azure 活動目錄 (Azure AD)[管理中心](https://aad.portal.azure.com)中。

> [!IMPORTANT]
> LinkedIn帳戶連接設置當前正在向 Azure AD 組織推出。 當它被推出到您的組織時,默認情況下將啟用它。
>
> 例外狀況：
>
> * 此設定不適用於使用 Microsoft Cloud for US Government、Microsoft Cloud Germany 或中國 21Vianet 管理之 Azure 和 Office 365 的客戶。
> * 對於佈建於德國的租用戶，此設定預設為關閉。 請注意，此設定不適用於使用 Microsoft Cloud Germany 的客戶。
> * 對於佈建於法國的租用戶，此設定預設為關閉。
>
> 為您的組織啟用LinkedIn帳戶連接後,在使用者同意代表他們訪問公司數據的應用后,帳戶連接將起作用。 有關使用者同意設定的資訊,請參閱[如何刪除使用者對應用程式的存取](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment)。

## <a name="enable-linkedin-account-connections-in-the-azure-portal"></a>在 Azure 門戶開啟LinkedIn帳戶連線

您只能為希望訪問的使用者啟用LinkedIn帳戶連接,從整個組織僅對組織中選定的使用者。

1. 使用 Azure [AD](https://aad.portal.azure.com/)組織的全域管理員的帳戶登錄到 Azure AD 管理中心。
1. 選擇**使用者**。
1. 在 **「使用者」** 頁上,選擇 **「使用者設置**」。
1. 在**LinkedIn 帳戶連接**下,允許使用者連接其帳戶以造訪某些 Microsoft 應用中LinkedIn連接。 在使用者同意連接其帳戶之前,不會共用任何數據。

    * 選擇 **「可**為組織中的所有使用者啟用該服務
    * 選擇 **"選定"組**,僅為組織中一組選定的使用者啟用該服務
    * 選擇 **「否**」 以撤銷組織所有使用者的同意

    ![在組織中整合式LinkedIn帳戶連線](./media/linkedin-integration/linkedin-integration.png)

1. 完成後,選擇 **「保存」** 以保存設置。

> [!Important]
> 在使用者同意連接其帳戶之前,不會完全啟用LinkedIn集成。 為使用者啟用帳戶連接時,不會共用任何數據。

### <a name="assign-selected-users-with-a-group"></a>使用群組配置所選取的使用者

我們已替換了"選擇"選項,該選項指定使用者清單,並選擇一組使用者,以便您可以啟用連接單個組(而不是許多單個使用者)LinkedIn和 Microsoft 帳戶的能力。 如果未為選擇的單個使用者啟用LinkedIn帳戶連接,則無需執行任何操作。 如果您以前為選取的單個使用者啟用了LinkedIn帳戶連接,則應:

1. 取得單一使用者的目前清單
1. 將目前啟用的單個使用者移至群組
1. 在 Azure AD 管理中心中的LinkedIn帳戶連接設置中,將上一個組用作所選組。

> [!NOTE]
> 即使您未將當前選定的單個用戶移動到組,他們仍可以在 Microsoft 應用中看到 LinkedIn 資訊。

### <a name="move-currently-selected-users-to-a-group"></a>將目前選取的使用者移至群組

1. 創建為LinkedIn帳戶連接選擇的使用者的CSV檔案。
1. 使用管理員帳戶登錄 Microsoft 365。
1. 啟動 PowerShell。
1. 透過執行安裝 Azure AD 模組`Install-Module AzureAD`
1. 執行下列指令碼：

  ``` PowerShell
  $groupId = "GUID of the target group"
  
  $users = Get-Content 
  Path to the CSV file
  
  $i = 1
  foreach($user in $users} { Add-AzureADGroupMember -ObjectId $groupId -RefObjectId $user ; Write-Host $i Added $user ; $i++ ; Start-Sleep -Milliseconds 10 }
  ```

在 Azure AD 管理中心中的LinkedIn帳戶連線設定中使用步驟 2 中的群組作為所選群組,請參閱[在 Azure 門戶中啟用LinkedIn帳戶連接](#enable-linkedin-account-connections-in-the-azure-portal)。

## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>使用群組策略啟用LinkedIn帳戶連線

1. 下載 [Office 2016 系統管理範本檔案 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
1. 解壓縮 **ADMX** 檔案，並將其複製到中央存放區。
1. 開啟群組原則管理。
1. 建立具有以下設定的群組政策物件:**使用者設定** > **管理樣本** > **Microsoft Office 2016** > **雜項** > 顯示 office**應用程式中的LinkedIn功能**。
1. 選取 [啟用]**** 或 [停用]****。
  
   State | 效果
   ------ | ------
   **啟用** | Office 2016 選項中的 [在 Office 應用程式中顯示 LinkedIn 功能]**** 設定已啟用。 組織中的用戶可以在其 Office 2016 應用程式中使用LinkedIn功能。
   **已停用** | Office 2016 選項中的 [在 Office 應用程式中顯示 LinkedIn 功能]**** 設定已停用，而且終端使用者無法變更此設定。 組織中的使用者無法在其 Office 2016 應用程式中使用 LinkedIn 功能。

這個群組原則只會影響本機電腦的 Office 2016 應用程式。 如果使用者在其 Office 2016 應用中禁用LinkedIn,他們仍然可以在 Office 365 中看到LinkedIn功能。

## <a name="next-steps"></a>後續步驟

* [LinkedIn 的使用者同意和資料共享](linkedin-user-consent.md)

* [Microsoft 應用程式中的 LinkedIn 資訊和功能](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn 說明中心](https://www.linkedin.com/help/linkedin)

* [在 Azure 入口網站中檢視您目前的 LinkedIn 整合設定](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
