---
title: Azure AD Connect：透過分段推出進行雲端驗證 |Microsoft Docs
description: 本文說明如何使用分段推出，從同盟驗證遷移至雲端驗證。
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/23/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80b7536704d68e96429d715705a0518410db399a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82112315"
---
# <a name="migrate-to-cloud-authentication-using-staged-rollout-preview"></a>使用分段推出進行遷移至雲端驗證（預覽）

藉由使用分段推出的方法，您可以從同盟驗證遷移至雲端驗證。 本文討論如何進行切換。 不過，在開始進行分段推出之前，您應該考慮下列一或多個條件成立時的含意：
    
-  您目前正在使用內部部署多重要素驗證服務器。 
-  您正在使用智慧卡進行驗證。 
-  目前的伺服器提供某些僅限同盟的功能。

在您嘗試這項功能之前，建議您先參閱我們的指南，以瞭解如何選擇正確的驗證方法。 如需詳細資訊，請參閱[為您的 Azure Active Directory 混合式身分識別解決方案選擇正確的驗證方法](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn#comparing-methods)中的「比較方法」表格。

如需此功能的總覽，請參閱此「Azure Active Directory：什麼是階段式推出？」 影片

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>先決條件

-   您有一個具有同盟網域的 Azure Active Directory （Azure AD）租使用者。

-   您已決定移至兩個選項的其中一個：
    - **選項**-*密碼雜湊同步處理（同步）* + *無縫單一登入（SSO）*  - 
    - **選項 B** - *傳遞驗證* + *無縫 SSO*
    
    雖然*無縫 SSO*是選擇性的，但建議您啟用它，以針對從公司網路內執行已加入網域之電腦的使用者，達到無訊息的登入體驗。

-   您已針對正在遷移至雲端驗證的使用者，設定您需要的所有適當的租使用者品牌和條件式存取原則。

-   如果您打算使用 Azure 多重要素驗證，我們建議您使用[聚合式註冊來進行自助式密碼重設（SSPR）和多重要素驗證](../authentication/concept-registration-mfa-sspr-combined.md)，讓您的使用者註冊其驗證方法一次。

-   若要使用分段推出功能，您必須是租使用者的全域管理員。

-   若要在特定 Active Directory 樹系上啟用*無縫 SSO* ，您必須是網域系統管理員。

## <a name="supported-scenarios"></a>支援的案例

分段推出支援下列案例。 此功能僅適用于：

- 使用 Azure AD Connect 布建到 Azure AD 的使用者。 不適用於僅限雲端的使用者。

- 瀏覽器和*新式驗證*用戶端上的使用者登入流量。 使用舊版驗證的應用程式或雲端服務將會回到同盟驗證流程。 範例可能是已關閉新式驗證的 Exchange online，或是不支援新式驗證的 Outlook 2010。

## <a name="unsupported-scenarios"></a>不支援的情節

分段首度發行不支援下列案例：

- 某些應用程式會在驗證期間傳送 "domain_hint" 查詢參數給 Azure AD。 這些流程將會繼續，而啟用分段推出的使用者會繼續使用同盟進行驗證。

<!-- -->

- 系統管理員可以使用安全性群組來推出雲端驗證。 若要避免在使用內部部署 Active Directory 安全性群組時的同步延遲，建議您使用雲端安全性群組。 適用的條件如下：

    - 每項功能最多可以使用10個群組。 也就是說，您可以使用10個群組來進行*密碼雜湊同步**處理、傳遞驗證*和*無縫 SSO*。
    - *不支援*嵌套的群組。 此範圍也適用于公開預覽。
    - 分段首度發行*不支援*動態群組。
    - 群組內的 Contact 物件會封鎖群組的加入。

- 您仍然需要使用 Azure AD Connect 或 PowerShell，將最後的轉換從同盟轉換成雲端驗證。 分段推出並不會將網域從同盟切換到受控。

- 當您第一次新增用於分段推出的安全性群組時，會限制為200個使用者，以避免 UX 超時。新增群組之後，您可以視需要將更多使用者直接新增到其中。

## <a name="get-started-with-staged-rollout"></a>開始使用分段推出

若要使用分段推出來測試*密碼雜湊同步*登入，請遵循下一節中的前置工作指示。

如需所要使用之 PowerShell Cmdlet 的詳細資訊，請參閱[Azure AD 2.0 preview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout)。

## <a name="pre-work-for-password-hash-sync"></a>密碼雜湊同步處理的前置工作

1. 從 Azure AD Connect 中的 [ [選用功能](how-to-connect-install-custom.md#optional-features) ] 頁面啟用 [ *密碼雜湊同步* 處理]。 

   ![Azure Active Directory Connect 中 [選用功能] 頁面的螢幕擷取畫面](media/how-to-connect-staged-rollout/sr1.png)

1. 請確定已執行完整的*密碼雜湊同步*處理週期，讓所有使用者的密碼雜湊都已同步處理到 Azure AD。 若要檢查*密碼雜湊同步*處理的狀態，您可以使用 PowerShell 診斷來[疑難排解密碼雜湊同步作業與 Azure AD Connect 同步](tshoot-connect-password-hash-synchronization.md)。

   ![AADConnect 疑難排解記錄的螢幕擷取畫面](./media/how-to-connect-staged-rollout/sr2.png)

如果您想要使用分段推出來測試*傳遞驗證*登入，請遵循下一節中的前置工作指示來加以啟用。

## <a name="pre-work-for-pass-through-authentication"></a>傳遞驗證的前置工作

1. 識別執行 Windows Server 2012 R2 或更新版本的伺服器，而您想要讓*傳遞驗證*代理程式執行。 

   *請勿*選擇 Azure AD Connect 伺服器。確定伺服器已加入網域，可以使用 Active Directory 驗證選取的使用者，並可與輸出埠和 Url 上的 Azure AD 通訊。 如需詳細資訊，請參閱[快速入門： Azure AD 無縫單一登入](how-to-connect-sso-quick-start.md)的「步驟1：檢查必要條件」一節。

1. [下載 Azure AD Connect authentication 代理程式](https://aka.ms/getauthagent)，並將它安裝在伺服器上。 

1. 若要啟用 [高可用性](how-to-connect-sso-quick-start.md)，請在其他伺服器上安裝額外的驗證代理程式。

1. 請確定您已適當地設定[智慧鎖定設定](../authentication/howto-password-smart-lockout.md)。 這麼做有助於確保使用者的內部部署 Active Directory 帳戶不會遭到不良執行者的鎖定。

我們建議您啟用*無縫 SSO* ，而不論您選取來進行分段推出的登入方法（*密碼雜湊同步**處理或傳遞驗證*）。 若要啟用*無縫 SSO*，請遵循下一節中的前置工作指示。

## <a name="pre-work-for-seamless-sso"></a>無縫 SSO 的前置工作

使用 PowerShell 在 Active Directory 樹系上啟用 *無縫 SSO* 。 如果您有一個以上的 Active Directory 樹系，請為每個樹系個別啟用它。只有針對已選取要進行分段推出的使用者，才會觸發 *無縫 SSO* 。 它不會影響您現有的同盟設定。

執行下列動作以啟用*無縫 SSO* ：

1. 登入 Azure AD Connect Server。

2. 移至 *% programfiles%\\Microsoft Azure Active Directory Connect* 資料夾。

3. 執行下列命令來匯入*無縫 SSO* PowerShell 模組： 

   `Import-Module .\AzureADSSO.psd1`

4. 以系統管理員身分執行 PowerShell。 在 PowerShell 中， `New-AzureADSSOAuthenticationContext`呼叫。 此命令會開啟一個窗格，您可以在其中輸入租使用者的全域管理員認證。

5. 呼叫 `Get-AzureADSSOStatus | ConvertFrom-Json`。 此命令會顯示已啟用此功能的 Active Directory 樹系列表（請參閱「網域」清單）。 根據預設，租使用者層級的設定為 false。

   ![Windows PowerShell 輸出的範例](./media/how-to-connect-staged-rollout/sr3.png)

6. 呼叫 `$creds = Get-Credential`。 在提示中，輸入預定 Active Directory 樹系的網域系統管理員認證。

7. 呼叫 `Enable-AzureADSSOForest -OnPremCredentials $creds`。 此命令會從 Active Directory 樹系的內部部署網域控制站中，建立*無縫 SSO*所需的 AZUREADSSOACC 電腦帳戶。

8. *無縫 SSO*要求 url 必須位於內部網路區域中。 若要使用群組原則來部署這些 Url，請參閱[快速入門： Azure AD 無縫單一登入](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature)。

9. 如需完整的逐步解說，您也可以下載適用于*無縫 SSO*的[部署計畫](https://aka.ms/SeamlessSSODPDownload)。

## <a name="enable-staged-rollout"></a>啟用分段推出

若要將特定功能（*傳遞驗證*、*密碼雜湊同步*或*無縫 SSO*）推出至群組中的一組使用者，請遵循下一節中的指示。

### <a name="enable-a-staged-rollout-of-a-specific-feature-on-your-tenant"></a>在您的租使用者上啟用特定功能的分段推出

您可以推出下列其中一個選項：

- **選項**密碼*雜湊同步* + 處理*無縫 SSO*  - 
- **選項 B** - *傳遞驗證* + *無縫 SSO*
- **不支援** - 的*密碼雜湊同步* + *傳遞驗證* + *無縫 SSO*

執行下列動作：

1. 若要存取預覽 UX，請登入[Azure AD 入口網站](https://aka.ms/stagedrolloutux)。

2. 選取 [**啟用受控使用者登入的分段推出（預覽）** ] 連結。

   例如，如果您想要啟用*選項 A*，請將 [**密碼雜湊同步**] 和 [**無縫單一登入**] 控制項滑成 [**開啟**]，如下列影像所示。

   ![[Azure AD Connect] 頁面](./media/how-to-connect-staged-rollout/sr4.png)

   ![[啟用分段推出功能（預覽）] 頁面](./media/how-to-connect-staged-rollout/sr5.png)

3. 將群組新增至功能，以啟用*傳遞驗證*和*無縫 SSO*。 若要避免 UX 超時，請確定安全性群組一開始不包含超過200的成員。

   ![[管理密碼雜湊同步（預覽）的群組] 頁面](./media/how-to-connect-staged-rollout/sr6.png)

   >[!NOTE]
   >群組中的成員會自動啟用以進行分段推出。 分段推出不支援嵌套和動態群組。

## <a name="auditing"></a>稽核

我們針對分段推出所執行的各種動作啟用了 audit 事件：

- 當您啟用*密碼雜湊同步**處理、傳遞驗證*或*無縫 SSO*的分段推出時，就會發生 Audit 事件。

  >[!NOTE]
  >使用分段推出來開啟*無縫 SSO*時，會記錄一個 audit 事件。

  ![[建立功能的推出原則] 窗格-[活動] 索引標籤](./media/how-to-connect-staged-rollout/sr7.png)

  ![[建立功能的首度發行原則] 窗格-已修改的屬性索引標籤](./media/how-to-connect-staged-rollout/sr8.png)

- 將群組新增至*密碼雜湊同步**處理、傳遞驗證*或*無縫 SSO*時的 Audit 事件。

  >[!NOTE]
  >將群組新增至*密碼雜湊同步*處理以進行分段推出時，會記錄一個 audit 事件。

  ![[將群組新增至功能推出] 窗格-[活動] 索引標籤](./media/how-to-connect-staged-rollout/sr9.png)

  ![[將群組新增至功能推出] 窗格-[已修改的屬性] 索引標籤](./media/how-to-connect-staged-rollout/sr10.png)

- 當已新增至群組的使用者啟用分段推出時，Audit 事件。

  ![[將使用者新增至功能推出] 窗格-[活動] 索引標籤](media/how-to-connect-staged-rollout/sr11.png)

  ![[將使用者新增至功能推出] 窗格-[目標] 索引標籤](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>驗證

若要使用*密碼雜湊同步**處理或傳遞驗證*（使用者名稱和密碼登入）來測試登入，請執行下列動作：

1. 在外部網路上，移至私用瀏覽器會話中的 [[應用程式] 頁面](https://myapps.microsoft.com)，然後輸入針對分段推出所選取之使用者帳戶的 USERPRINCIPALNAME （UPN）。

   已針對分段推出的使用者不會重新導向至您的同盟登入頁面。 相反地，系統會要求他們登入 Azure AD 租使用者品牌的登入頁面。

1. 藉由篩選 UserPrincipalName，確保登入成功出現在[Azure AD 登入活動報告](../reports-monitoring/concept-sign-ins.md)中。

測試*無縫 SSO*的登入：

1. 在內部網路上，移至私用瀏覽器會話中的 [[應用程式] 頁面](https://myapps.microsoft.com)，然後輸入針對分段推出所選取之使用者帳戶的 USERPRINCIPALNAME （UPN）。

   已針對*無縫 SSO*分段推出的使用者，會看到「正在嘗試將您登入 ...」訊息，然後才以無訊息模式登入。

1. 藉由篩選 UserPrincipalName，確保登入成功出現在[Azure AD 登入活動報告](../reports-monitoring/concept-sign-ins.md)中。

   若要追蹤所選分段推出使用者的 Active Directory 同盟服務（AD FS）上仍發生的使用者登入，請遵循[AD FS 疑難排解：事件和記錄](https://docs.microsoft.com/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events)中的指示。 請參閱廠商檔，以瞭解如何在協力廠商同盟提供者上進行檢查。

## <a name="remove-a-user-from-staged-rollout"></a>從分段推出移除使用者

從群組中移除使用者時，會停用該使用者的分段推出。 若要停用分段推出功能，請將控制項滑回 [**關閉**]。

## <a name="frequently-asked-questions"></a>常見問題集

**問：我可以在生產環境中使用這項功能嗎？**

答：是，您可以在生產環境租使用者中使用這項功能，但建議您先在測試租使用者中試用。

**問：此功能可以用來維護永久的「共存」，其中有些使用者會使用同盟驗證，而其他則使用雲端驗證？**

答：否，這項功能的設計目的是要在階段從同盟轉換至雲端驗證，最後再轉換到雲端驗證。 我們不建議使用永久混合狀態，因為此方法可能會導致未預期的驗證流程。

**問：我可以使用 PowerShell 來執行分段推出嗎？**

答： 會。 若要瞭解如何使用 PowerShell 來執行分段推出，請參閱[Azure AD Preview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout)。

## <a name="next-steps"></a>後續步驟
- [Azure AD 2.0 preview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout )
