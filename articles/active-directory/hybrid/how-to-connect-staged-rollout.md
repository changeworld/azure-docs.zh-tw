---
title: Azure AD 連接：通過分階段推出進行雲身份驗證 |微軟文檔
description: 本文介紹如何使用分階段推出從識別身分同盟遷移到雲身份驗證。
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3044ebdd716eb85dc63d3a77089912d0d51d8b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74915222"
---
# <a name="migrate-to-cloud-authentication-by-using-staged-rollout-preview"></a>使用分階段推出（預覽）遷移到雲身份驗證

通過使用分階段的推出方法，可以從識別身分同盟遷移到雲身份驗證。 本文討論了如何進行切換。 但是，在開始分階段推出之前，如果以下一個或多個條件為 true，則應考慮其影響：
    
-  您當前正在使用本地多因素身份驗證服務器。 
-  您使用的是智慧卡進行身份驗證。 
-  當前伺服器提供某些僅聯合功能。

在嘗試此功能之前，我們建議您查看我們的指南，選擇正確的身份驗證方法。 有關詳細資訊，請參閱[選擇 Azure 活動目錄混合標識解決方案的正確身份驗證方法中的](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn#comparing-methods)"比較方法"表。

有關該功能的概述，請查看此"Azure 活動目錄：什麼是暫存部署？ 視頻：

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>Prerequisites

-   您具有具有聯合域的 Azure 活動目錄 （Azure AD） 租戶。

-   您已決定轉到兩個選項之一：
    - **選項** - *密碼雜湊同步（同步）* + *無縫單一登入 （SSO）*
    - **選項 B** - *傳遞身份驗證* + *無縫 SSO*
    
    儘管*無縫 SSO*是可選的，但我們建議啟用它，以便從公司網路內運行域加入電腦的使用者實現靜默登錄體驗。

-   您已為遷移到雲身份驗證的使用者配置了所需的所有適當的租戶品牌和條件訪問策略。

-   如果計畫使用 Azure 多重要素驗證，我們建議您使用[融合註冊進行自助服務密碼重設 （SSPR） 和多重要素驗證](../authentication/concept-registration-mfa-sspr-combined.md)，以便使用者註冊一次身份驗證方法。

-   要使用暫存的推出功能，您需要是租戶上的全域管理員。

-   要在特定活動目錄林上啟用*無縫 SSO，* 您需要是域管理員。

## <a name="supported-scenarios"></a>支援的案例

以下方案支援分階段推出。 該功能僅適用于：

- 使用 Azure AD 連接預配到 Azure AD 的使用者。 它不適用於僅雲使用者。

- 瀏覽器*和現代身份驗證*用戶端上的使用者登錄流量。 使用舊版身份驗證的應用程式或雲服務將回退到識別身分同盟流。 例如，關閉現代身份驗證的 Exchange 連線或不支援現代身份驗證的 Outlook 2010。

## <a name="unsupported-scenarios"></a>不支援的情節

暫存的推出不支援以下方案：

- 某些應用程式在身份驗證期間將"domain_hint"查詢參數發送到 Azure AD。 這些流將繼續，啟用暫存部署的使用者將繼續使用識別身分同盟。

<!-- -->

- 管理員可以使用安全性群組推出雲身份驗證。 為了避免在使用本地活動目錄安全性群組時出現同步延遲，我們建議您使用雲安全性群組。 以下條件適用：

    - 每個要素最多可以使用 10 個組。 也就是說，您可以使用 10 個組進行*密碼雜湊同步*、*傳遞身份驗證*和*無縫 SSO。*
    - *不支援嵌套*組。 此範圍也適用于公共預覽版。
    - 暫存推出*不支援*動態組。
    - 組內的連絡人物件將阻止添加該組。

- 您仍然需要使用 Azure AD 連接或 PowerShell 進行從識別身分同盟到雲身份驗證的最終選擇。 分階段推出不會將域從聯合切換到託管域。

- 首次為分階段推出添加安全性群組時，將限制為 200 個使用者，以避免 UX 超時。添加組後，可以根據需要直接向其添加更多使用者。

## <a name="get-started-with-staged-rollout"></a>開始分階段推出

要使用暫存部署來測試*密碼雜湊同步*登錄，請按照下一節中的前期說明進行操作。

有關要使用的 PowerShell Cmdlet 的資訊，請參閱[Azure AD 2.0 預覽](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout)。

## <a name="pre-work-for-password-hash-sync"></a>密碼雜湊同步的預熱工作

1. 從 Azure AD Connect 中的 [可選功能](how-to-connect-install-custom.md#optional-features) 頁啟用 *密碼雜湊同步* 。 

   ![Azure 活動目錄連接中"可選功能"頁面的螢幕截圖](media/how-to-connect-staged-rollout/sr1.png)

1. 確保已運行完整的*密碼雜湊同步*週期，以便所有使用者的密碼雜湊已同步到 Azure AD。 要檢查*密碼雜湊同步*的狀態，可以使用"[疑難排解"密碼雜湊同步中的](tshoot-connect-password-hash-synchronization.md)PowerShell 診斷與 Azure AD 連接同步。

   ![AADConnect 故障排除日誌的螢幕截圖](./media/how-to-connect-staged-rollout/sr2.png)

如果要使用暫存部署來測試*直通身份驗證*登錄，請按照下一節中的前期說明啟用它。

## <a name="pre-work-for-pass-through-authentication"></a>傳遞身份驗證的前期工作

1. 標識運行 Windows Server 2012 R2 或更高版本的伺服器，您希望*傳遞身份驗證*代理運行的位置。 

   *不要*選擇 Azure AD 連接伺服器。確保伺服器已加入域，可以使用 Active Directory 對選定的使用者進行身份驗證，並且可以在出站埠和 URL 上與 Azure AD 通信。 有關詳細資訊，請參閱[快速入門：Azure AD 無縫單一登入](how-to-connect-sso-quick-start.md)的"步驟 1：檢查先決條件"部分。

1. [下載 Azure AD 連接身份驗證代理](https://aka.ms/getauthagent)，並將其安裝到伺服器上。 

1. 要啟用 [高可用性](how-to-connect-sso-quick-start.md)，請在其他伺服器上安裝其他身份驗證代理。

1. 確保已正確配置[智慧鎖定設置](../authentication/howto-password-smart-lockout.md)。 這樣做有助於確保使用者的本地活動目錄帳戶不會被不法分子鎖定。

我們建議啟用*無縫的 SSO，* 而不考慮您為分階段推出選擇的登錄方法（*密碼雜湊同步*或*傳遞身份驗證*）。 要啟用*無縫的 SSO，* 請按照下一節中的前期說明進行操作。

## <a name="pre-work-for-seamless-sso"></a>無縫 SSO 的前期工作

使用 PowerShell 在活動目錄林上啟用 *無縫 SSO。*   如果您有多個活動目錄林，請為每個林單獨啟用它。 *無縫 SSO*僅針對選擇分階段推出的使用者觸發。 它不會影響現有的聯合設置。

通過執行以下操作實現*無縫 SSO：*

1. 登錄到 Azure AD 連接伺服器。

2. 轉到 *%程式檔%\\Microsoft Azure 活動目錄連接* 資料夾。

3. 通過運行以下命令導入*無縫的 SSO* PowerShell 模組： 

   `Import-Module .\AzureADSSO.psd1`

4. 以系統管理員身分執行 PowerShell。 在 PowerShell `New-AzureADSSOAuthenticationContext`中，調用 。 此命令將打開一個窗格，您可以在其中輸入租戶的全域管理員憑據。

5. 致電 `Get-AzureADSSOStatus | ConvertFrom-Json`。 此命令顯示已啟用此功能的活動目錄林的清單（請參閱"域"清單）。 預設情況下，它在租戶級別設置為 false。

   ![Windows 電源外殼輸出的示例](./media/how-to-connect-staged-rollout/sr3.png)

6. 致電 `$creds = Get-Credential`。 在提示符處，輸入預期的活動目錄林的域管理員憑據。

7. 呼叫 `Enable-AzureADSSOForest -OnPremCredentials $creds`。 此命令從活動目錄林的本地網域控制站創建 AZUREADSSOACC 電腦帳戶，這是*無縫 SSO*所需的。

8. *無縫 SSO*要求 URL 位於 Intranet 區域中。 要使用群組原則部署這些 URL，請參閱[快速入門：Azure AD 無縫單一登入](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature)。

9. 對於完整的演練，您還可以下載我們的*無縫 SSO*[部署計畫](https://aka.ms/SeamlessSSODPDownload)。

## <a name="enable-staged-rollout"></a>啟用分階段推出

要將特定功能（*直通身份驗證*、*密碼雜湊同步*或*無縫 SSO）* 推出到組中的一組選定的使用者，請按照以下各節中的說明進行操作。

### <a name="enable-a-staged-rollout-of-a-specific-feature-on-your-tenant"></a>在租戶上啟用特定功能的分階段推出

您可以推出以下選項之一：

- **選項** - *密碼雜湊同步* + *無縫 SSO*
- **選項 B** - *傳遞身份驗證* + *無縫 SSO*
- **不支援** - *密碼雜湊同步* + *直通身份驗證* + *無縫 SSO*

執行下列動作：

1. 要訪問預覽 UX，請登錄到[Azure AD 門戶](https://aka.ms/stagedrolloutux)。

2. 為**託管使用者登錄（預覽）連結選擇啟用暫存推出**。

   例如，如果要啟用*選項 A，* 請將**密碼雜湊同步**和**無縫單一登入**控制項滑動到**On**，如下圖所示。

   ![Azure AD 連接頁](./media/how-to-connect-staged-rollout/sr4.png)

   !["啟用分階段推出功能（預覽）"頁面](./media/how-to-connect-staged-rollout/sr5.png)

3. 將組添加到功能中，以啟用*直通身份驗證*和*無縫 SSO。* 為了避免 UX 超時，請確保安全性群組最初包含的成員不超過 200 個。

   !["管理密碼雜湊同步組（預覽）"頁面](./media/how-to-connect-staged-rollout/sr6.png)

   >[!NOTE]
   >組中的成員將自動啟用以進行分階段推出。 暫存的推出不支援嵌套組和動態組。

## <a name="auditing"></a>稽核

我們為分階段部署執行的各種操作啟用了審核事件：

- 當您為*密碼雜湊同步*、*傳遞身份驗證*或*無縫 SSO*啟用分階段推出時，將發生審核事件。

  >[!NOTE]
  >使用暫存部署打開*無縫 SSO*時，將記錄審核事件。

  !["為功能創建推出策略"窗格 - 活動選項卡](./media/how-to-connect-staged-rollout/sr7.png)

  !["為功能創建推出策略"窗格 - 修改屬性選項卡](./media/how-to-connect-staged-rollout/sr8.png)

- 當組添加到*密碼雜湊同步*、*傳遞身份驗證*或*無縫 SSO*時，審核事件。

  >[!NOTE]
  >當組添加到分階段推出時的*密碼雜湊同步*時，將記錄審核事件。

  !["添加組以功能推出"窗格 - 活動選項卡](./media/how-to-connect-staged-rollout/sr9.png)

  !["添加組以功能推出"窗格 - 修改屬性選項卡](./media/how-to-connect-staged-rollout/sr10.png)

- 啟用添加到組的使用者進行暫存部署時的審核事件。

  !["將使用者添加到功能推出"窗格 - 活動選項卡](media/how-to-connect-staged-rollout/sr11.png)

  !["將使用者添加到功能推出"窗格 - 目標選項卡](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>驗證

要使用*密碼雜湊同步*或*傳遞身份驗證*（使用者名和密碼登錄）測試登錄，請執行以下操作：

1. 在外聯網上，轉到專用瀏覽器會話中的["應用"頁面](https://myapps.microsoft.com)，然後輸入為分階段推出而選擇的使用者帳戶的使用者主體名稱 （UPN）。

   被暫有部署目標的使用者不會重定向到您的聯合登錄頁。 相反，他們被要求在 Azure AD 租戶品牌的登錄頁上登錄。

1. 通過使用 UserTheName 進行篩選，確保登錄成功顯示在[Azure AD 登錄活動報表](../reports-monitoring/concept-sign-ins.md)中。

要使用*無縫 SSO*測試登錄：

1. 在 Intranet 上，轉到專用瀏覽器會話中的["應用"頁](https://myapps.microsoft.com)，然後輸入為分階段推出而選擇的使用者帳戶的使用者主體名稱 （UPN）。

   被鎖定為*分階段推出無縫 SSO*的使用者將顯示一個"嘗試登錄..."消息之前，他們默默地登錄。

1. 通過使用 UserTheName 進行篩選，確保登錄成功顯示在[Azure AD 登錄活動報表](../reports-monitoring/concept-sign-ins.md)中。

   要跟蹤在活動目錄聯合服務 （AD FS） 上仍針對所選暫有推出使用者的使用者登錄，請按照[AD FS 故障排除中的說明：事件和日誌記錄](https://docs.microsoft.com/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events)。 查看有關如何檢查協力廠商聯合提供程式的供應商文檔。

## <a name="remove-a-user-from-staged-rollout"></a>從分階段推出中刪除使用者

從群組移除使用者將禁用該使用者的暫存推出。 要禁用暫存的推出功能，請將控制項滑回 **"關閉**"。

## <a name="frequently-asked-questions"></a>常見問題集

**問：是否可以在生產中使用此功能？**

答：是的，您可以在生產租戶中使用此功能，但我們建議您首先在測試租戶中試用此功能。

**問：此功能是否可用於維護永久的"共存"，即某些使用者使用識別身分同盟，而其他使用者使用雲身份驗證？**

答：不，此功能旨在分階段從識別身分同盟遷移到雲身份驗證，然後最終遷移到雲身份驗證。 我們不建議使用永久混合狀態，因為此方法可能會導致意外的身份驗證流。

**問：我可以使用 PowerShell 執行分階段的推出嗎？**

答： 會。 要瞭解如何使用 PowerShell 執行暫存部署，請參閱[Azure AD 預覽](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout)。

## <a name="next-steps"></a>後續步驟
- [Azure AD 2.0 預覽版](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout )
