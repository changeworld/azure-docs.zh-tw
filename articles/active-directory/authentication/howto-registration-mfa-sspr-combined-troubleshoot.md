---
title: 針對合併註冊進行疑難排解-Azure Active Directory
description: 針對 Azure AD 多重要素驗證和自助式密碼重設合併註冊進行疑難排解
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 04/15/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c840df2c53554519f62a3d1d7a7d8b305187ffb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81450933"
---
# <a name="troubleshooting-combined-security-information-registration"></a>疑難排解結合的安全性資訊註冊

本文中的資訊旨在引導系統管理員針對合併註冊體驗的使用者所回報的問題進行疑難排解。

## <a name="audit-logs"></a>稽核記錄

針對合併註冊所記錄的事件會在 Azure AD audit 記錄檔的驗證方法類別目錄中。

![顯示註冊事件的 Azure AD Audit logs 介面](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

下表列出合併註冊所產生的所有 audit 事件：

| 活動 | 狀態 | 原因 | 描述 |
| --- | --- | --- | --- |
| 使用者已註冊所有必要的安全性資訊 | 成功 | 使用者已註冊所有必要的安全性資訊。 | 當使用者成功完成註冊時，就會發生此事件。|
| 使用者已註冊所有必要的安全性資訊 | 失敗 | 使用者已取消安全性資訊註冊。 | 當使用者從中斷模式取消註冊時，就會發生此事件。|
| 使用者已註冊的安全性資訊 | 成功 | 使用者註冊的*方法*。 | 當使用者註冊個別的方法時，就會發生此事件。 *方法*可以是驗證器應用程式、電話、電子郵件、安全性問題、應用程式密碼、替代電話等等。| 
| 使用者已審核的安全性資訊 | 成功 | 使用者已成功審查安全性資訊。 | 當使用者選取 [安全性資訊審查] 頁面上的 [**外觀良好**] 時，就會發生此事件。|
| 使用者已審核的安全性資訊 | 失敗 | 使用者無法檢查安全性資訊。 | 當使用者在 [安全性資訊審查] 頁面上選取 [**看起來良好**]，但後端發生問題時，就會發生此事件。|
| 使用者已刪除安全性資訊 | 成功 | 使用者刪除的*方法*。 | 當使用者刪除個別的方法時，就會發生此事件。 *方法*可以是驗證器應用程式、電話、電子郵件、安全性問題、應用程式密碼、替代電話等等。|
| 使用者已刪除安全性資訊 | 失敗 | 使用者無法刪除*方法*。 | 當使用者嘗試刪除方法但嘗試因某些原因而失敗時，就會發生此事件。 *方法*可以是驗證器應用程式、電話、電子郵件、安全性問題、應用程式密碼、替代電話等等。|
| 使用者已變更預設安全性資訊 | 成功 | 使用者已變更*方法*的預設安全性資訊。 | 當使用者變更預設方法時，就會發生此事件。 *方法*可以是驗證器代理程式更新、來自我的驗證器應用程式或權杖的代碼、呼叫 + X XXXXXXXXXX、將程式碼文字編碼至 + X XXXXXXXXX 等等。|
| 使用者已變更預設安全性資訊 | 失敗 | 使用者無法變更*方法*的預設安全性資訊。 | 當使用者嘗試變更預設方法但嘗試因某些原因而失敗時，就會發生此事件。 *方法*可以是驗證器代理程式更新、來自我的驗證器應用程式或權杖的代碼、呼叫 + X XXXXXXXXXX、將程式碼文字編碼至 + X XXXXXXXXX 等等。|

## <a name="troubleshooting-interrupt-mode"></a>中斷模式疑難排解

| 徵狀 | 疑難排解步驟 |
| --- | --- |
| 我看不到我預期看到的方法。 | 1. 檢查使用者是否有 Azure AD 系統管理員角色。 如果是，請查看 SSPR 管理原則的差異。 <br> 2. 判斷使用者是否因為多重要素驗證註冊強制執行或 SSPR 註冊強制而中斷。 請參閱「結合註冊模式」下的[流程圖](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md#combined-registration-modes)，以判斷應該顯示哪些方法。 <br> 3. 判斷最近的多重要素驗證或 SSPR 原則變更的方式。 如果變更是最近的，可能需要一些時間，更新的原則才會傳播。|

## <a name="troubleshooting-manage-mode"></a>疑難排解管理模式

| 徵狀 | 疑難排解步驟 |
| --- | --- |
| 我沒有新增特定方法的選項。 | 1. 判斷此方法是否已啟用多重要素驗證或 SSPR。 <br> 2. 如果已啟用此方法，請再次儲存原則並等候1-2 小時，然後再重新測試。 <br> 3. 如果已啟用此方法，請確定使用者尚未設定允許其設定之該方法的最大數目。|

## <a name="disable-combined-registration"></a>停用合併的註冊

當使用者在新的結合體驗中註冊電話號碼和/或行動裝置應用程式時，我們的服務會為該使用者的這些方法戳記一組旗標（StrongAuthenticationMethods）。 此功能可讓使用者在需要多重要素驗證時，使用這些方法來執行多重要素驗證。

如果系統管理員啟用預覽功能，使用者會透過新的體驗進行註冊，然後系統管理員停用預覽，也可能會在不知情的情況下註冊使用者進行多重要素驗證。

如果已完成合併註冊的使用者前往目前的自助式密碼重設（SSPR）註冊頁面[https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)，則會提示使用者執行多重要素驗證，然後才能存取該頁面。 從技術觀點來看，這是預期的步驟，但這對先前只註冊 SSPR 的使用者而言是新的。 雖然這個額外步驟會藉由提供另一層安全性來改善使用者的安全性狀態，但系統管理員可能會想要回復其使用者，使其無法再執行多重要素驗證。  

### <a name="how-to-roll-back-users"></a>如何復原使用者

如果您身為系統管理員，想要重設使用者的多重要素驗證設定，您可以使用下一節所提供的 PowerShell 腳本。 此腳本會清除使用者的行動應用程式和/或電話號碼的 StrongAuthenticationMethods 屬性。 如果您為使用者執行此腳本，他們必須在需要時重新註冊多重要素驗證。 建議您在回復所有受影響的使用者之前，先使用一或兩個使用者來測試復原。

接下來的步驟將協助您復原使用者或使用者群組。

#### <a name="prerequisites"></a>Prerequisites

1. 安裝適當的 Azure AD PowerShell 模組。 在 PowerShell 視窗中，執行下列命令來安裝模組：

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. 將受影響的使用者物件識別碼清單儲存到您的電腦，做為每行一個識別碼的文字檔。 記下檔案的所在位置。
1. 將下列腳本儲存到您的電腦，並記下腳本的位置：

   ```powershell
   <# 
   //********************************************************
   //*                                                      *
   //*   Copyright (C) Microsoft. All rights reserved.      *
   //*                                                      *
   //********************************************************
   #>

   param($path)

   # Define Remediation Fn
   function RemediateUser {

       param  
       (
           $ObjectId
       )

       $user = Get-MsolUser -ObjectId $ObjectId

       Write-Host "Checking if user is eligible for rollback: UPN: "  $user.UserPrincipalName  " ObjectId: "  $user.ObjectId -ForegroundColor Yellow

       $hasMfaRelyingParty = $false
       foreach($p in $user.StrongAuthenticationRequirements)
       {
           if ($p.RelyingParty -eq "*")
           {
               $hasMfaRelyingParty = $true
               Write-Host "User was enabled for per-user MFA." -ForegroundColor Yellow
           }
       }

       if ($user.StrongAuthenticationMethods.Count -gt 0 -and -not $hasMfaRelyingParty)
       {
           Write-Host $user.UserPrincipalName " is eligible for rollback" -ForegroundColor Yellow
           Write-Host "Rolling back user ..." -ForegroundColor Yellow
           Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName $user.UserPrincipalName
           Write-Host "Successfully rolled back user " $user.UserPrincipalName -ForegroundColor Green
       }
       else
       {
           Write-Host $user.UserPrincipalName " is not eligible for rollback. No action required."
       }

       Write-Host ""
       Start-Sleep -Milliseconds 750
   }

   # Connect
   Import-Module MSOnline
   Connect-MsolService

   foreach($line in Get-Content $path)
   {
       RemediateUser -ObjectId $line
   }
   ```

#### <a name="rollback"></a>復原

在 PowerShell 視窗中，執行下列命令，並提供腳本和使用者檔案位置。 出現提示時，輸入全域管理員認證。 該指令碼將輸出每個使用者更新作業的結果。

`<script location> -path <user file location>`

### <a name="disable-the-updated-experience"></a>停用更新的體驗

若要停用使用者的更新體驗，請完成下列步驟：

1. 以使用者系統管理員身分登入 Azure 入口網站。
2. 移至 [ **Azure Active Directory** > **使用者設定** > ] [**管理存取面板預覽功能的設定**]。
3. 在 [**使用者可以使用預覽功能來註冊及管理安全性資訊**] 底下，將選取器設定為 [**無**]，然後選取 [**儲存**]。

系統不會再提示使用者使用更新的體驗進行註冊。

## <a name="next-steps"></a>後續步驟

* [深入瞭解自助式密碼重設和 Azure 多重要素驗證的合併註冊](concept-registration-mfa-sspr-combined.md)
