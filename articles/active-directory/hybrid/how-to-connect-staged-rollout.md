---
title: Azure AD Connect：分段推出進行雲端驗證 |Microsoft Docs
description: 本文說明如何使用分段推出從同盟驗證移轉至雲端驗證。
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/12/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9fbe76fb18e33efaa161d2e2b488b48fa5c8580d
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83644160"
---
# <a name="migrate-to-cloud-authentication-using-staged-rollout-preview"></a>使用分段推出移轉至雲端驗證 (預覽)

藉由使用分段推出方法，您可從同盟驗證移轉至雲端驗證。 本文討論如何進行此切換。 不過，在開始分段推出前，如果下列一或多個條件成立，則您應思考其含意：
    
-  您目前正在使用內部部署 Multi-Factor Authentication 伺服器。 
-  您正在使用智慧卡進行驗證。 
-  目前伺服器提供某些僅限同盟的功能。

在嘗試這項功能前，建議先檢閱指南，以了解如何選擇正確的驗證方法。 如需詳細資訊，請參閱[針對 Azure Active Directory 混合式身分識別解決方案選擇正確的驗證方法](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn#comparing-methods) (機器翻譯)。

如需此功能的概觀，請檢視此「Azure Active Directory：什麼是階段式推出？」 影片：

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>Prerequisites

-   您擁有具有同盟網域的 Azure Active Directory (Azure AD) 租用戶。

-   您決定移至這兩個選項的其中之一：
    - **選項 A**  -  「密碼雜湊同步處理 (同步)」 + 「無縫單一登入 (SSO)」
    - **選項 B**  - 「傳遞驗證」 + 「無縫 SSO」
    
    雖然「無縫 SSO」為選擇，但我們建議將其啟用，以為正在公司網路內部執行已加入網域電腦的使用者提供無訊息登入體驗。

-   針對正在移轉至雲端驗證的使用者，您已為其設定了所需的所有適當租用戶商標和條件式存取原則。

-   如果打算使用 Azure Multi-Factor Authentication，建議使用[自助式密碼重設 (SSPR) 和 Multi-Factor Authentication 的聚合式註冊](../authentication/concept-registration-mfa-sspr-combined.md)，以供使用者一次註冊其驗證方法。

-   若要使用分段推出功能，則您必須是租用戶的全域管理員。

-   若要在特定 Active Directory 樹系上啟用「無縫 SSO」，則您必須是網域系統管理員。


## <a name="supported-scenarios"></a>支援的案例

分段推出支援下列案例。 此功能僅適用於：

- 使用 Azure AD Connect 佈建到 Azure AD 的使用者。 不適用於僅限雲端的使用者。

- 瀏覽器和「新式驗證」用戶端上的使用者登入流量。 使用舊版驗證的應用程式或雲端服務，將會回到同盟驗證流程。 例如已關閉新式驗證的 Exchange online，或不支援新式驗證的 Outlook 2010。
- 群組大小目前限制為 50,000 位使用者。  如果的群組大於 50,000 位使用者，建議將此群組分割為多個群組以進行分段推出。

## <a name="unsupported-scenarios"></a>不支援的情節

分段推出不支援下列案例：

- 某些應用程式會在驗證期間將 "domain_hint" 查詢參數傳送至 Azure AD。 這些流程將會繼續，而啟用分段推出的使用者將繼續使用同盟進行驗證。

<!-- -->

- 系統管理員可使用安全性群組來推出雲端驗證。 若要避免在使用內部部署 Active Directory 安全性群組時發生同步延遲，建議使用雲端安全性群組。 適用條件如下：

    - 每項功能最多可使用 10 個群組。 亦即，您可將 10 個群組分別用於「密碼雜湊同步」、「傳遞驗證」及「無縫 SSO」。
    - 「不支援」巢狀群組。 此範圍也適用於公開預覽。
    - 「不支援」動態群組進行分段推出。
    - 群組內的連絡人物件會禁止新增群組。

- 您仍然需要使用 Azure AD Connect 或 PowerShell 來進行從同盟到雲端驗證最後的完全移轉。 分段推出不會將網域從同盟切換至受控。

- 在第一次新增安全性群組以進行分段推出時，會限制為 200 位使用者，以避免 UX 逾時。新增群組後，即可視需要將更多使用者直接新增至群組。

>[!NOTE]
> 因為租用戶端點不會傳送登入提示，因此不支援進行分段推出。  SAML 應用程式使用租用戶端點，且不支援分段推出。

## <a name="get-started-with-staged-rollout"></a>開始使用分段推出

若要使用分段推出測試「密碼雜湊同步」登入，請遵循下一節中的前置工作指示。

如需應使用哪些 PowerShell Cmdlet 的資訊，請參閱 [Azure AD 2.0 Preview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout)。

## <a name="pre-work-for-password-hash-sync"></a>密碼雜湊同步的前置工作

1. 從 Azure AD Connect 中的 [選用功能](how-to-connect-install-custom.md#optional-features) 頁面，以啟用「密碼雜湊同步」 **  。 

   ![Azure Active Directory Connect 中 [選用功能] 頁面的螢幕擷取畫面](media/how-to-connect-staged-rollout/sr1.png)

1. 請確定已執行完整的「密碼雜湊同步」週期，以便所有使用者的密碼雜湊都同步至 Azure AD。 若要檢查「密碼雜湊同步」的狀態，您可使用[透過 Azure AD Connect 同步對密碼雜湊同步進行疑難排解](tshoot-connect-password-hash-synchronization.md)中的 PowerShell 診斷。

   ![AADConnect 疑難排解記錄的螢幕擷取畫面](./media/how-to-connect-staged-rollout/sr2.png)

如果想要使用分段推出來測試「傳遞驗證」登入，請遵循下一節中的前置工作指示來將其啟用。

## <a name="pre-work-for-pass-through-authentication"></a>傳遞驗證的前置工作

1. 找出想要執行「傳遞驗證」代理程式的伺服器 (其執行 Windows Server 2012 R2 或更新版本)。 

   「請勿」選擇 Azure AD Connect 伺服器。 請確認伺服器已加入網域、可使用 Active Directory 來驗證所選使用者，並可與輸出連接埠和 URL 上的 Azure AD 通訊。 如需詳細資訊，請參閱[快速入門：Azure AD 無縫單一登入](how-to-connect-sso-quick-start.md)中的＜步驟 1：檢查先決條件＞一節。

1. [下載 Azure AD Connect 驗證代理程式](https://aka.ms/getauthagent)並在伺服器上安裝。 

1. 若要獲得 [高可用性](how-to-connect-sso-quick-start.md)，請在其他伺服器上安裝額外驗證代理程式。

1. 請確認已適當設定[智慧型鎖定設定](../authentication/howto-password-smart-lockout.md)。 這樣有助於確保使用者的內部部署 Active Directory 帳戶不會遭到不良執行者鎖定。

不論選取哪個登入方法 (「密碼雜湊同步」或「傳遞驗證」) 來進行分段推出，都建議啟用「無縫 SSO」。 若要啟用「無縫 SSO」，請遵循下一節中的前置工作指示。

## <a name="pre-work-for-seamless-sso"></a>無縫 SSO 的前置工作

使用 PowerShell 在 Active Directory 樹系中啟用「無縫 SSO」 **  。 如果有超過一個 Active Directory 樹系，請為每個樹系個別啟用此功能。「無縫 SSO」 ** 只會針對已選取要進行分段推出的使用者觸發。 不會影響現有的同盟設定。

執行下列動作以啟用「無縫 SSO」：

1. 登入 Azure AD Connect 伺服器。

2. 前往  *%programfiles%\\Microsoft Azure Active Directory Connect*  資料夾。

3. 使用下列命令匯入「無縫 SSO」PowerShell 模組： 

   `Import-Module .\AzureADSSO.psd1`

4. 以系統管理員身分執行 PowerShell。 在 PowerShell 中，呼叫  `New-AzureADSSOAuthenticationContext`。 此命令會開啟一個窗格，您可在其中輸入租用戶的全域管理員認證。

5. 呼叫 `Get-AzureADSSOStatus | ConvertFrom-Json`。 此命令會顯示已啟用這項功能的 Active Directory 樹系清單 (查看 [網域] 清單)。 根據預設，在租用戶層級會設為 False。

   ![Windows PowerShell 輸出的範例](./media/how-to-connect-staged-rollout/sr3.png)

6. 呼叫 `$creds = Get-Credential`。 出現提示時，輸入預定 Active Directory 樹系的網域管理員認證。

7. 呼叫 `Enable-AzureADSSOForest -OnPremCredentials $creds`。 此命令會從 Active Directory 樹系的內部部署網域控制站建立「無縫 SSO」所需 AZUREADSSOACC 電腦帳戶。

8. 「無縫 SSO」需要 URL 位於內部網路區域中。 若要使用群組原則來部署這些 URL，請參閱＜快速入門：[Azure AD 無縫單一登入＞](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature)。

9. 如需完整的逐步解說，您也可下載「無縫 SSO」的[部署計劃](https://aka.ms/SeamlessSSODPDownload)。

## <a name="enable-staged-rollout"></a>啟用分段推出

若要將特定功能 (「傳遞驗證」、「密碼雜湊同步」或「無縫 SSO」) 推出至群組中的一組所選使用者，請遵循下一節中的指示。

### <a name="enable-a-staged-rollout-of-a-specific-feature-on-your-tenant"></a>在租用戶上啟用特定功能的分段推出

您可推出下列選項的其中之一：

- **選項 A**  - 「密碼雜湊同步」 + 「無縫 SSO」
- **選項 B**  - 「傳遞驗證」 + 「無縫 SSO」
- **不支援**  - 「密碼雜湊同步」 + 「傳遞驗證」 + 「無縫 SSO」

執行下列動作：

1. 若要存取預覽 UX，請登入 [Azure AD 入口網站](https://aka.ms/stagedrolloutux)。

2. 選取 [啟用受控使用者登入的分段推出 (預覽)] 連結。

   例如，如果想要啟用「選項 A」，請將「密碼雜湊同步」和「無縫單一登入」控制項滑動至**開啟**，如下列影像所示。

   ![[Azure AD Connect] 頁面](./media/how-to-connect-staged-rollout/sr4.png)

   ![[啟用分段推出功能 (預覽)] 頁面](./media/how-to-connect-staged-rollout/sr5.png)

3. 將群組新增至功能以啟用「傳遞驗證」和「無縫 SSO」。 若要避免 UX 逾時，請確認安全性群組一開始不包含超過 200 位成員。

   ![[管理密碼雜湊同步 (預覽) 的群組] 頁面](./media/how-to-connect-staged-rollout/sr6.png)

   >[!NOTE]
   >群組中的成員會自動啟用，以進行分段推出。 不支援巢狀和動態群組進行分段推出。

## <a name="auditing"></a>稽核

我們已針對分段推出所執行的各種動作啟用了稽核事件：

- 當啟用「密碼雜湊同步」、「傳遞驗證」或「無縫 SSO」的分段推出時，就會發生稽核事件。

  >[!NOTE]
  >使用分段推出開啟「無縫 SSO」時，會記錄稽核事件。

  ![[為功能建立推出原則] 窗格 - [活動] 索引標籤](./media/how-to-connect-staged-rollout/sr7.png)

  ![[為功能建立推出原則] 窗格 - [已修改的屬性] 索引標籤](./media/how-to-connect-staged-rollout/sr8.png)

- 將群組新增至「密碼雜湊同步」、「傳遞驗證」或「無縫 SSO」時，就會發生稽核事件。

  >[!NOTE]
  >將群組新增至「密碼雜湊同步」以進行分段推出時，會記錄稽核事件。

  ![[將群組新增至功能推出] 窗格 - [活動] 索引標籤](./media/how-to-connect-staged-rollout/sr9.png)

  ![[將群組新增至功能推出] 窗格 - [已修改的屬性] 索引標籤](./media/how-to-connect-staged-rollout/sr10.png)

- 當已新增至群組的使用者啟用分段推出時，就會發生稽核事件。

  ![[將使用者新增至功能推出] 窗格 - [活動] 索引標籤](media/how-to-connect-staged-rollout/sr11.png)

  ![[將使用者新增至功能推出] 窗格 - [目標] 索引標籤](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>驗證

若要使用「密碼雜湊同步」或「傳遞驗證」(使用者名稱和密碼登入) 來測試登入，請執行下列操作：

1. 在外部網路上，前往私密瀏覽器工作階段中的[應用程式](https://myapps.microsoft.com)頁面，然後輸入已選取要進行分段推出的使用者帳戶 UserPrincipalName (UPN)。

   已預定要進行分段推出的使用者，不會重新導向至同盟登入頁面。 反之，系統會要求其登入 Azure AD 租用戶商標的登入頁面。

1. 藉由篩選 UserPrincipalName 來確保 [Azure AD 登入活動報告](../reports-monitoring/concept-sign-ins.md)中顯示登入成功。

使用「無縫 SSO」來測試登入：

1. 在內部網路上，前往私密瀏覽器工作階段中的[應用程式](https://myapps.microsoft.com)頁面，然後輸入已選取要進行分段推出的使用者帳戶 UserPrincipalName (UPN)。

   已預定要進行「無縫 SSO」分段推出的使用者會看到「正在嘗試登入...」訊息，然後才會無訊息登入。

1. 藉由篩選 UserPrincipalName 來確保 [Azure AD 登入活動報告](../reports-monitoring/concept-sign-ins.md)中顯示登入成功。

   若要追蹤仍會在所選分段推出使用者 Active Directory 同盟服務 (AD FS) 上發生的使用者登入，請遵循 [AD FS 疑難排解：事件與記錄](https://docs.microsoft.com/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events) (機器翻譯) 中的指示。 請參閱廠商文件，以了解如何透過協力廠商同盟提供者對此進行檢查。

## <a name="remove-a-user-from-staged-rollout"></a>將使用者從分段推出移除

從群組移除使用者時，會停用該使用者的分段推出。 若要停用分段推出功能，請將控制項滑動回**關閉**。

## <a name="frequently-asked-questions"></a>常見問題集

**問：我可在生產中使用這項功能嗎？**

A：是，您可在生產租用戶者中使用這項功能，但建議先在測試租用戶中試用。

**問：這項功能是否可用來維護永久性的「共存」，其中某些使用者使用同盟驗證，而其他則使用雲端驗證？**

A：不可以，這項功能設計用來在各階段從同盟移轉至雲端驗證，最後再完全移轉至雲端驗證。 我們不建議使用永久性混合狀態，因為此方法可能會導致非預期的驗證流程。

**問：我可使用 PowerShell 來執行分段推出嗎？**

A：是。 若要了解如何使用 PowerShell 來執行分段推出，請參閱 [Azure AD Preview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout) (英文)。

## <a name="next-steps"></a>後續步驟
- [Azure AD 2.0 Preview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout )
