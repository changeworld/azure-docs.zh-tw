---
title: Windows 的自助服務密碼重置 - Azure 活動目錄
description: 如何在 Windows 登入螢幕上使用忘記的密碼啟用自助服務密碼重置
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a8675756aeef1140dbebd94023d7f7fb4c7af99
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652248"
---
# <a name="how-to-enable-password-reset-from-the-windows-login-screen"></a>如何:從 Windows 登入螢幕啟用密碼重置

對於運行 Windows 7、8、8.1 和 10 的電腦,您可以允許使用者在 Windows 登錄螢幕重置其密碼。 使用者不再需要使用 Web 瀏覽器尋找裝置才能存[取 SSPR 門戶](https://aka.ms/sspr)。

![範例 Windows 7 和 10 個登入螢幕,顯示 SSPR 連結](./media/howto-sspr-windows/windows-reset-password.png)

## <a name="general-limitations"></a>一般限制

- 當前不支援遠端桌面或 Hyper-V 增強會話進行密碼重置。
- 已知某些第三方憑據提供程式會導致此功能出現問題。
- 已知通過修改[啟用LUA註冊表](https://docs.microsoft.com/openspecs/windows_protocols/ms-gpsb/958053ae-5397-4f96-977f-b7700ee461ec)項禁用 UAC 會導致問題。
- 此功能不適用於部署了 802.1x 網路身份驗證和「在使用者登錄前立即執行」選項的網路。 針對已部署 802.1x 網路驗證的網路，建議您使用機器驗證來啟用此功能。
- 混合 Azure AD 聯接的計算機必須具有與網域控制器的網路連接視線才能使用新密碼並更新緩存的認證。
- 如果使用映射,則在運行 sysprep 之前,請確保在執行 CopyProfile 步驟之前為內置管理員清除 Web 快取。 有關此步驟的詳細資訊,請參閱[使用自定義預設使用者配置檔時的性能不佳](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile)的支援文章。
- 已知以下設定會干擾在 Windows 10 裝置上使用和重置密碼的能力
    - 如果 v1809 前的 Windows 10 版本中的原則需要 Ctrl+Alt+Del，[重設密碼]**** 將無法運作。
    - 如果鎖定螢幕通知已關閉，[重設密碼]**** 將無法運作。
    - HideFastUserSwitching 設定為已啟用或 1
    - DontDisplayLastUserName 設定為已啟用或 1
    - NoLockScreen 設定為已啟用或 1
    - EnableLostMode 已設定於裝置上
    - Explorer.exe 會取代為自訂殼層
- 以下特定三個設置的組合可能會導致此功能不起作用。
    - 互動式登入:不需要 CTRL_ALT_DEL = 已關閉
    - 關閉鎖定螢幕的通知 = 1 或已開啟
    - 是內容交付策略強制 = 1 或 true

## <a name="windows-10-password-reset"></a>Windows 10 密碼重置

### <a name="windows-10-prerequisites"></a>Windows 10 必要條件

- 管理員必須啟用 Azure 門戶重置 Azure AD 自助服務密碼。
- **使用者在使用此功能之前必須註冊 SSPR**
- 網路代理要求
   - Windows 10 裝置 
       - 連接埠 443`passwordreset.microsoftonline.com`到與`ajax.aspnetcdn.com`
       - Windows 10 裝置僅支援電腦級代理設定
- 至少運行 Windows 10,版本 2018 年 4 月更新 (v1803),設備必須為:
    - 已聯結的 Azure AD
    - 已聯結的混合式 Azure AD

### <a name="enable-for-windows-10-using-intune"></a>使用 Intune 為 Windows 10 啟用

使用 Intune 部署組態變更，以啟用從登入畫面重設密碼的功能，是最具彈性的方法。 Intune 可讓您將組態變更部署至您所定義的特定機器群組。 必須完成裝置的 Intune 註冊，才能使用此方法。

#### <a name="create-a-device-configuration-policy-in-intune"></a>在 Intune 中建立裝置設定原則

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後按一下 [Intune]****。
1. 使用裝置**設定** > **檔** > **建立設定檔建立新**裝置設定檔
   - 為設定檔提供有意義的名稱
   - 選擇性地提供有意義的設定檔描述
   - 平台：**Windows 10 和更新版本**
   - 設定檔類型：**自訂**
1. 設定**設定**
   - **新增**下列 OMA-URI 設定來啟用 [重設密碼] 連結
      - 提供有意義的名稱，以說明正在進行的設定
      - 選擇性地提供有意義的設定描述
      - **OMA URI** 設為 `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`
      - **資料類型** 設為 **整數**
      - **值** 設為 **1**
      - 按一下 [確定]****。
   - 按一下 [確定]****。
1. 按下 **"創建"**
1. 此策略可以分配給特定使用者、設備或組。 更多資訊可以在[Microsoft Intune 中分配使用者和設備配置檔](https://docs.microsoft.com/intune/device-profile-assign)的文章中找到。

### <a name="enable-for-windows-10-using-the-registry"></a>使用註冊表為 Windows 10 啟用

1. 使用系統管理認證登入 Windows PC
1. 以系統管理員身分執行 **regedit**
1. 設定下列登錄機碼
   - `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      - `"AllowPasswordReset"=dword:00000001`

#### <a name="troubleshooting-windows-10-password-reset"></a>容錯排除 Windows 10 密碼重置

Azure AD 稽核記錄會包含 IP 位址相關資訊以及發生密碼重設的 ClientType。

![Azure AD 稽核紀錄中的範例 Windows 7 密碼重設](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

當使用者從 Windows 10 設備的登錄螢幕重置其密碼時,將創建`defaultuser1`一個稱為低特權的臨時帳戶。 此帳戶用來保護密碼重設程序。 帳戶本身具有隨機生成的密碼,不會顯示設備登錄,並且在使用者重置密碼後將自動刪除。 可能存在`defaultuser`多個配置檔,但可以安全地忽略。

## <a name="windows-7-8-and-81-password-reset"></a>Windows 7、8 和 8.1 密碼重置

### <a name="windows-7-8-and-81-prerequisites"></a>Windows 7、8 和 8.1 先決條件

- 管理員必須啟用 Azure 門戶重置 Azure AD 自助服務密碼。
- **使用者在使用此功能之前必須註冊 SSPR**
- 網路代理要求
   - Windows 7、8 和 8.1 裝置
       - 連接埠 443 到`passwordreset.microsoftonline.com`
- 已修補的 Windows 7 或 Windows 8.1 作業系統。
- 已使用[傳輸層安全性 (TLS) 登錄設定](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12)中所找到的指南啟用 TLS 1.2。
- 如果在您的電腦上啟用了多個第三方憑據提供程式,使用者將在登錄螢幕上看到多個使用者配置檔。

> [!WARNING]
> TLS 1.2 必須啟用,而不僅僅是設置為自動協商

### <a name="install"></a>安裝

1. 下載適用於所要啟用 Windows 版本的安裝程式。
   - 軟體可在微軟下載中心[https://aka.ms/sspraddin](https://aka.ms/sspraddin)
1. 登入要安裝的電腦，並執行安裝程式。
1. 安裝之後，強烈建議您重新開機。
1. 重新啟動後,在登錄屏幕選擇使用者,然後單擊「忘記密碼? 來起始密碼重設工作流程。
1. 完成工作流程，遵循螢幕上的步驟來重設密碼。

![在 Windows7 中按下「忘記密碼？」的範例 SSPR 流程](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>無訊息安裝

- 對於靜默安裝,請使用命令"msiexec /i SsprWindowsLogon.PROD.msi /qn"
- 對於靜默卸載,請使用命令"msiexec /x SsprWindowsLogon.PROD.msi /qn"

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>容損排除 Windows 7、8 和 8.1 密碼重置

電腦和 Azure AD 中都會記錄事件。 Azure AD 事件會包含 IP 位址相關資訊以及發生密碼重設的 ClientType。

![Azure AD 稽核紀錄中的範例 Windows 7 密碼重設](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

如果需要額外的記錄，則可變更電腦上的登錄機碼來啟用詳細資訊記錄。 只能為了進行疑難排解而啟用詳細資訊記錄。

`HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}`

- 要啟用詳細日誌記錄,請創建`REG_DWORD: "EnableLogging"`一個 ,並將其設置為 1。
- 要禁用詳細日誌記錄,請`REG_DWORD: "EnableLogging"`將更改為 0。

## <a name="what-do-users-see"></a>使用者看到的內容

現在,您已為 Windows 裝置配置了密碼重置,使用者有哪些更改? 他們如何得知可在登入畫面重設其密碼？

![範例 Windows 7 和 10 個登入螢幕,顯示 SSPR 連結](./media/howto-sspr-windows/windows-reset-password.png)

當用戶嘗試登錄時,他們現在會看到**重置密碼**或**忘記密碼**連結,在登錄螢幕上打開自助服務密碼重置體驗。 這項功能可讓使用者重設其密碼，而不需使用另一個裝置來存取網頁瀏覽器。

您的使用者將在[重設您的工作或學校密碼](../user-help/active-directory-passwords-update-your-own-password.md)中發現使用這項功能的指引

## <a name="next-steps"></a>後續步驟

[計畫認證認證方法以允許](concept-authentication-methods.md)

[設定 Windows 10](https://docs.microsoft.com/windows/configuration/)
