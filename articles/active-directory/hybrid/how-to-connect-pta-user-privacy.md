---
title: 使用者隱私權與 Azure Active Directory 傳遞驗證 | Microsoft Docs
description: 本文說明 Azure Active Directory (Azure AD) 傳遞驗證和 GDPR 合規性。
services: active-directory
keywords: Azure AD Connect 傳遞驗證、GDPR、Azure AD 的必要元件、SSO、單一登入
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/23/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 396344ba90aa3850d7d23dc40d6df95f6d1f6c3f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996571"
---
# <a name="user-privacy-and-azure-active-directory-pass-through-authentication"></a>使用者隱私權與 Azure Active Directory 傳遞驗證


[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>概觀

Azure AD 傳遞驗證會建立下列記錄類型，其中可能會包含「個人資料」：

- Azure AD Connect 追蹤記錄檔。
- 驗證代理程式追蹤記錄檔。
- Windows 事件記錄檔。

使用以下兩種方式來改善傳遞驗證的使用者隱私權：

1.  在接到要求時擷取使用者的資料，並從安裝中移除該使用者的資料。
2.  確保沒有資料會保留超過 48 小時。

強烈建議您採用第二個選項，因為它比較容易實作和維護。 以下是每個記錄類型的指示：

### <a name="delete-azure-ad-connect-trace-log-files"></a>刪除 Azure AD Connect 追蹤記錄檔

檢查 **%ProgramData%\AADConnect** 資料夾的內容，並且在安裝或升級 Azure AD Connect 或修改傳遞驗證組態的 48 小時內，刪除此資料夾的追蹤記錄內容 (**trace-\*.log** 檔案)，因為這個動作可能會建立 GDPR 涵蓋的資料。

>[!IMPORTANT]
>請勿刪除此資料夾中的 **PersistedState.xml** 檔案，因為此檔案是用來維護上一個 Azure AD Connect 安裝的狀態，以及在升級安裝完成時使用。 此檔案從未包含任何個人相關資料，所以不應刪除。

您可以使用 Windows 檔案總管檢閱和刪除這些追蹤記錄檔，或者可以使用下列 PowerShell 指令碼來執行必要的動作：

```
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

將指令碼儲存在副檔名為 ".PS1" 的檔案中。 視需要執行此指令碼。

若要深入了解相關的 Azure AD Connect GDPR 需求，請參閱[這篇文章](reference-connect-user-privacy.md)。

### <a name="delete-authentication-agent-event-logs"></a>刪除驗證代理程式事件記錄

此產品也可能會建立 **Windows 事件記錄**。 若要深入了解，請閱讀[這篇文章](/windows/win32/wes/windows-event-log)。

若要檢視與傳遞驗證代理程式相關的記錄，請開啟伺服器上的 **事件檢視器** 應用程式，並於 **Application and Service Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin** 下查看。

### <a name="delete-authentication-agent-trace-log-files"></a>刪除驗證代理程式追蹤記錄檔

您應定期檢查 **%PROGRAMDATA%\MICROSOFT\AZURE AD Connect Authentication Agent\Trace** 的內容，並每隔48小時刪除此資料夾的內容。 

>[!IMPORTANT]
>如果驗證代理程式服務正在執行中，您就無法刪除資料夾中目前的記錄檔。 在重試之前停止服務。 若要避免使用者登入失敗，您應該為傳遞驗證設定[高可用性](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)。

您可以使用 Windows 檔案總管檢閱和刪除這些檔案，也可以使用下列指令碼來執行必要的動作：

```
$Files = ((Get-childitem -Path "$env:programdata\microsoft\azure ad connect authentication agent\trace" -Recurse).VersionInfo).FileName 
 
Foreach ($file in $files) { 
    {Remove-Item -Path $File -Force} 
}
```

若要將此指令碼排程為每 48 小時執行一次，請遵循下列步驟：

1.  將指令碼儲存在副檔名為 ".PS1" 的檔案中。
2.  開啟 [控制台]，然後按一下 [系統與安全性]。
3.  在 [系統 **管理工具** ] 標題下，按一下 [**排程** 工作]。
4.  在 [工作排程器] 中，以滑鼠右鍵按一下 [工作排程程式庫]，然後按一下 [建立基本工作...]。
5.  輸入新工作的名稱，然後按 [下一步]。
6.  針對 [工作觸發程序] 選取 [每天]，然後按 [下一步]。
7.  將週期設定為 2 天，然後按 [下一步]。
8.  選取 [啟動程式] 作為動作，然後按 [下一步]。
9.  在程式/腳本的方塊中輸入 "**PowerShell**"，然後在標示為 [**新增引數 (選擇性)**] 的方塊中，輸入您稍早建立之腳本的完整路徑，然後按 **[下一步]**。
10. 下一個畫面會顯示您即將建立之工作的摘要。 確認值並按一下 **[完成]** ，以建立工作：
 
### <a name="note-about-domain-controller-logs"></a>請注意網域控制站記錄

如果已啟用稽核記錄，這項產品可能會針對您的網域控制站產生安全性記錄。 若要深入了解設定稽核原則，請閱讀[這篇文章](/previous-versions/tn-archive/dd277403(v=technet.10))。

## <a name="next-steps"></a>後續步驟
* [在信任中心檢閱 Microsoft 隱私權原則](https://www.microsoft.com/trustcenter)
* [**疑難排解**](tshoot-connect-pass-through-authentication.md) - 了解如何解決此功能的常見問題。