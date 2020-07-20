---
title: 使用 dsregcmd.exe 命令進行疑難排解-Azure Active Directory
description: 使用 dsregcmd.exe 的輸出來瞭解中的裝置狀態 Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cd782cdab625934fe60617142e5ac0baf756398
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80128767"
---
# <a name="troubleshooting-devices-using-the-dsregcmd-command"></a>使用 dsregcmd.exe 命令針對裝置進行疑難排解

Dsregcmd.exe/status 公用程式必須以網域使用者帳戶的身分執行。

## <a name="device-state"></a>裝置狀態

此區段會列出裝置聯結狀態參數。 下表列出裝置處於各種聯結狀態的準則。

| AzureAdJoined | EnterpriseJoined | Enterpriseregistration.windows.net domainjoined | 裝置狀態 |
| ---   | ---   | ---   | ---   |
| YES | 否 | 否 | Azure AD 聯結 |
| 否 | 否 | YES | 已加入網域 |
| YES | 否 | YES | 已加入混合式 AD |
| 否 | YES | YES | 已聯結內部部署 DRS |

> [!NOTE]
> Workplace Join （Azure AD 已註冊）狀態會顯示在 [使用者狀態] 區段中

- **AzureAdJoined：** 如果裝置已加入 Azure AD，則設定為 [是]。 否則為 "NO"。
- **EnterpriseJoined：** 如果裝置已加入內部部署 DRS，則設定為 [是]。 裝置不可同時為 EnterpriseJoined 和 AzureAdJoined。
- **Enterpriseregistration.windows.net domainjoined：** 如果裝置已加入網域（AD），則設定為 [是]。
- **DomainName：** 如果裝置已加入網域，請將設定為網域的名稱。

### <a name="sample-device-state-output"></a>範例裝置狀態輸出

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+
             AzureAdJoined : YES
          EnterpriseJoined : NO
              DomainJoined : YES
                DomainName : HYBRIDADFS
+----------------------------------------------------------------------+
```

## <a name="device-details"></a>裝置詳細資訊

只有在裝置已加入 Azure AD 或混合式 Azure AD （未 Azure AD 註冊）時，才會顯示。 本節列出雲端中儲存的裝置識別詳細資料。

- **DeviceId：** -Azure AD 租使用者中裝置的唯一識別碼
- **指紋：** -裝置憑證的指紋 
- **DeviceCertificateValidity：** -裝置憑證的有效性
- **KeyContainerId：** -與裝置憑證相關聯之裝置私密金鑰的 ContainerId
- **KeyProvider：** -用來儲存裝置私密金鑰的 KeyProvider （硬體/軟體）。
- **TpmProtected：** 如果裝置私密金鑰儲存在硬體 TPM 中，則為-"YES"。

### <a name="sample-device-details-output"></a>範例裝置詳細資料輸出

```
+----------------------------------------------------------------------+
| Device Details                                                       |
+----------------------------------------------------------------------+

                  DeviceId : e92325d0-xxxx-xxxx-xxxx-94ae875dxxxx
                Thumbprint : D293213EF327483560EED8410CAE36BB67208179
 DeviceCertificateValidity : [ 2019-01-11 21:02:50.000 UTC -- 2029-01-11 21:32:50.000 UTC ]
            KeyContainerId : 13e68a58-xxxx-xxxx-xxxx-a20a2411xxxx
               KeyProvider : Microsoft Software Key Storage Provider
              TpmProtected : NO
+----------------------------------------------------------------------+
```

## <a name="tenant-details"></a>租用戶詳細資料

只有在裝置已加入 Azure AD 或混合式 Azure AD （未 Azure AD 註冊）時，才會顯示。 此區段會列出裝置加入 Azure AD 時的一般租使用者詳細資料。

> [!NOTE]
> 如果本節中的 MDM Url 是空的，則表示 MDM 未設定，或目前的使用者不在 MDM 註冊的範圍內。 檢查 Azure AD 中的行動設定，以檢查您的 MDM 設定。

> [!NOTE]
> 即使您看到 MDM Url，這並不表示裝置是由 MDM 所管理。 如果租使用者具有自動註冊的 MDM 設定，即使裝置本身不受管理，也會顯示此資訊。 

### <a name="sample-tenant-details-output"></a>範例租使用者詳細資料輸出

```
+----------------------------------------------------------------------+
| Tenant Details                                                       |
+----------------------------------------------------------------------+

                TenantName : HybridADFS
                  TenantId : 96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
                       Idp : login.windows.net
               AuthCodeUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/authorize
            AccessTokenUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/token
                    MdmUrl : https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
                 MdmTouUrl : https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
          MdmComplianceUrl : https://portal.manage-beta.microsoft.com/?portalAction=Compliance
               SettingsUrl : eyJVxxxxIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyxxxx==
            JoinSrvVersion : 1.0
                JoinSrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/device/
                 JoinSrvId : urn:ms-drs:enterpriseregistration.windows.net
             KeySrvVersion : 1.0
                 KeySrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/key/
                  KeySrvId : urn:ms-drs:enterpriseregistration.windows.net
        WebAuthNSrvVersion : 1.0
            WebAuthNSrvUrl : https://enterpriseregistration.windows.net/webauthn/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
             WebAuthNSrvId : urn:ms-drs:enterpriseregistration.windows.net
    DeviceManagementSrvVer : 1.0
    DeviceManagementSrvUrl : https://enterpriseregistration.windows.net/manage/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
     DeviceManagementSrvId : urn:ms-drs:enterpriseregistration.windows.net
+----------------------------------------------------------------------+
```

## <a name="user-state"></a>使用者狀態

此區段會列出目前登入裝置之使用者的各種屬性狀態。

> [!NOTE]
> 此命令必須在使用者內容中執行，才能取得有效的狀態。

- **NgcSet：** 如果已針對目前登入的使用者設定 Windows Hello 金鑰，則設定為 [是]。
- **NgcKeyId：** -Windows Hello 金鑰的識別碼（如果已針對目前登入的使用者設定的話）。
- **CanReset：** -表示使用者是否可以重設 Windows Hello 金鑰。 
- **可能的值：** -DestructiveOnly、NonDestructiveOnly、DestructiveAndNonDestructive 或 Unknown （如果發生錯誤）。 
- **WorkplaceJoined：** 如果已在目前的 ntuser.dat 內容中，將 Azure AD 已註冊的帳戶新增至裝置，請設定為 [是]。
- **WamDefaultSet：** 如果已針對登入的使用者建立 WAM 預設 WebAccount，則設定為 [是]。 如果從提高許可權的命令提示字元執行 dsreg/status，此欄位可能會顯示錯誤。 
- **WamDefaultAuthority：** 設定為 Azure AD 的 [組織]。
- Azure AD 的**WamDefaultId：** -Always " https://login.microsoft.com "。
- **WamDefaultGUID：** -預設 WAM WEBACCOUNT 的 WAM 提供者（Azure AD/MICROSOFT 帳戶） GUID。 

### <a name="sample-user-state-output"></a>範例使用者狀態輸出

```
+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

                    NgcSet : YES
                  NgcKeyId : {FA0DB076-A5D7-4844-82D8-50A2FB42EC7B}
                  CanReset : DestructiveAndNonDestructive
           WorkplaceJoined : NO
             WamDefaultSet : YES
       WamDefaultAuthority : organizations
              WamDefaultId : https://login.microsoft.com
            WamDefaultGUID : { B16898C6-A148-4967-9171-64D755DA8520 } (AzureAd)

+----------------------------------------------------------------------+
```

## <a name="sso-state"></a>SSO 狀態

Azure AD 註冊的裝置可以忽略此區段。

> [!NOTE]
> 此命令必須在使用者內容中執行，才能取得該使用者的有效狀態。

- **AzureAdPrt：** 如果登入的使用者在裝置上有 PRT，則設定為 [是]。
- **AzureAdPrtUpdateTime：** -設定為上次更新 PRT 時的 UTC 時間。
- **AzureAdPrtExpiryTime：** -設定為 UTC 時間（若未更新，PRT 將會到期）。
- **AzureAdPrtAuthority：** -Azure AD 授權單位 URL
- **EnterprisePrt：** 如果裝置已從內部部署 ADFS PRT，則設定為 [是]。 針對已加入混合式 Azure AD 裝置，裝置可以同時從 Azure AD 和內部部署 AD PRT。 已加入內部部署的裝置只會有企業 PRT。
- **EnterprisePrtUpdateTime：** -設定為上次更新企業 PRT 時的 UTC 時間。
- **EnterprisePrtExpiryTime：** -設定為 UTC 時間（若未更新，PRT 將會到期）。
- **EnterprisePrtAuthority：** -ADFS 授權單位 URL

### <a name="sample-sso-state-output"></a>SSO 狀態輸出範例

```
+----------------------------------------------------------------------+
| SSO State                                                            |
+----------------------------------------------------------------------+

                AzureAdPrt : YES
      AzureAdPrtUpdateTime : 2019-01-24 19:15:26.000 UTC
      AzureAdPrtExpiryTime : 2019-02-07 19:15:26.000 UTC
       AzureAdPrtAuthority : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
             EnterprisePrt : YES
   EnterprisePrtUpdateTime : 2019-01-24 19:15:33.000 UTC
   EnterprisePrtExpiryTime : 2019-02-07 19:15:33.000 UTC
    EnterprisePrtAuthority : https://fs.hybridadfs.nttest.microsoft.com:443/adfs

+----------------------------------------------------------------------+
```

## <a name="diagnostic-data"></a>診斷資料

### <a name="pre-join-diagnostics"></a>預先加入診斷

只有當裝置已加入網域，且無法進行混合式 Azure AD 聯結時，才會顯示此區段。

本節會執行各種測試，以協助診斷聯結失敗。 本節也包含前一個（？）的詳細資料。 此資訊包括錯誤階段、錯誤碼、伺服器要求識別碼、伺服器回應 HTTP 狀態、伺服器回應錯誤訊息。

- **使用者內容：** -診斷執行所在的內容。 可能的值：系統、未提高許可權的使用者、提高許可權的使用者。 

   > [!NOTE]
   > 因為實際聯結是在系統內容中執行，所以在系統內容中執行診斷最接近實際的聯結案例。 若要在系統內容中執行診斷，必須從提高許可權的命令提示字元執行 dsregcmd.exe/status 命令。

- **用戶端時間：** -UTC 的系統時間。
- **AD 連線能力測試：** -測試會對網域控制站執行連線測試。 此測試中的錯誤可能會導致前置檢查階段發生聯結錯誤。
- **AD 設定測試：** -Test 會讀取並驗證是否已在內部部署 AD 樹系中正確設定 SCP 物件。 這項測試中的錯誤可能會導致探索階段發生聯結錯誤，錯誤碼為0x801c001d。
- **DRS 探勘測試：** -Test 從探索中繼資料端點取得 DRS 端點，並執行使用者領域要求。 這項測試中的錯誤可能會導致探索階段發生聯結錯誤。
- **Drs 連線能力測試：** -Test 會對 DRS 端點執行基本的連線測試。
- **權杖取得測試：** -如果使用者租使用者為同盟，測試會嘗試取得 Azure AD 驗證權杖。 這項測試中的錯誤可能會導致驗證階段發生聯結錯誤。 如果驗證失敗，則會嘗試將同步聯結視為回溯，除非使用下列登錄機碼設定明確停用回溯。
```
    Keyname: Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ
    Value: FallbackToSyncJoin
    Type:  REG_DWORD
    Value: 0x0 -> Disabled
    Value: 0x1 -> Enabled
    Default (No Key): Enabled
 ```
- **回到同步-聯結：** -如果上述登錄機碼導致無法與驗證失敗同步的聯結，則設定為 [已啟用]，則不會顯示。 此選項可從 Windows 10 1803 和更新版本中取得。
- **先前的註冊：** -發生先前聯結嘗試的時間。 只會記錄失敗的聯結嘗試。
- **錯誤階段：** -已中止之聯結的階段。 可能的值為預先檢查、探索、驗證、聯結。
- **用戶端 ErrorCode：** -傳回用戶端錯誤碼（HRESULT）。
- **伺服器 ErrorCode：** -如果將要求傳送至伺服器，且伺服器已回應錯誤碼，則為-server 錯誤碼。 
- **伺服器訊息：** -連同錯誤碼一起傳回的伺服器訊息。
- **Https 狀態：** -伺服器傳回的 Http 狀態。
- **要求識別碼：** -傳送至伺服器的用戶端 requestId。 與伺服器端記錄相互關聯很有用。

### <a name="sample-pre-join-diagnostics-output"></a>聯結前診斷輸出範例

下列範例顯示診斷測試失敗，並出現探索錯誤。

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:25:31.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x801c000c]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:23:30.000 UTC
               Error Phase : discover
          Client ErrorCode : 0x801c0021

+----------------------------------------------------------------------+
```

下列範例顯示診斷測試已通過，但註冊嘗試失敗，發生目錄錯誤，這是同步聯結的預期方式。 一旦 Azure AD Connect 同步處理工作完成後，裝置就能夠加入。

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:16:50.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : PASS
     DRS Connectivity Test : PASS
    Token acquisition Test : PASS
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-7ac4-4714-88a1-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e

+----------------------------------------------------------------------+
```

### <a name="post-join-diagnostics"></a>加入後診斷

此區段會顯示已加入雲端的裝置上執行之健全檢查的輸出。

- **AadRecoveryEnabled：** 如果為 "YES"，則裝置中儲存的金鑰無法使用，且裝置會標示為進行復原。 下一次登入將會觸發復原流程，並重新註冊裝置。
- **KeySignTest：** 如果 "通過"，裝置金鑰就會處於良好的健康狀態。 如果 KeySignTest 失敗，裝置通常會標示為進行復原。 下一次登入將會觸發復原流程，並重新註冊裝置。 針對已加入混合式 Azure AD 的裝置，復原是無訊息的。 當 Azure AD 加入或 Azure AD 註冊時，裝置會提示使用者進行驗證，以在必要時復原和重新註冊裝置。 **KeySignTest 需要更高的許可權。**

#### <a name="sample-post-join-diagnostics-output"></a>聯結後診斷輸出範例

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

         AadRecoveryEnabled: NO
               KeySignTest : PASSED
+----------------------------------------------------------------------+
```

## <a name="ngc-prerequisite-check"></a>NGC 先決條件檢查

本節會執行 Windows Hello 企業版（WHFB）布建的必要條件檢查。 

> [!NOTE]
> 如果使用者已成功設定 WHFB，您可能不會在 dsregcmd.exe/status 中看到 [NGC 必要條件檢查詳細資料]。

- **IsDeviceJoined：** 如果裝置已加入 Azure AD，則設定為 [是]。
- **IsUserAzureAD：** 如果登入的使用者存在於 Azure AD 中，則設定為 [是]。
- **PolicyEnabled：** 如果裝置上已啟用 WHFB 原則，則設定為 [是]。
- **PostLogonEnabled：** 如果平臺原本就觸發 WHFB 註冊，則設定為 "YES"。 如果設定為 [否]，則表示 Windows Hello 企業版註冊是由自訂機制所觸發
- **DeviceEligible：** 如果裝置符合向 WHFB 註冊的硬體需求，則設定為 [是]。
- **SessionIsNotRemote：** 如果目前的使用者是直接登入裝置，而不是從遠端登入，則設定為 [是]。
- **CertEnrollment：** 特定于 WHFB 憑證信任部署，表示 WHFB 的憑證註冊授權單位。 如果來源為 MDM，則設定為「註冊授權」，如果 WHFB 原則的來源為群組原則，則為「行動裝置管理」。 否則為 "none"
- **AdfsRefreshToken：** 特定于 WHFB 憑證信任部署。 只有在 CertEnrollment 為「註冊授權單位」時才會出現。 指出裝置是否有使用者的企業 PRT。
- **AdfsRaIsReady：** 特定于 WHFB 憑證信任部署。  只有在 CertEnrollment 為「註冊授權單位」時才會出現。 如果 ADFS 在支援 WHFB 的探索中繼資料中指出，而且登入憑證範本可供使用 *，* 請將設定為 [是]。
- **LogonCertTemplateReady：** 特定于 WHFB 憑證信任部署。 只有在 CertEnrollment 為「註冊授權單位」時才會出現。 如果登入憑證範本的狀態有效，請將設定為 [是]，並協助針對 ADFS RA 進行疑難排解。
- **PreReqResult：** -提供所有 WHFB 先決條件評估的結果。 如果 WHFB 註冊會在使用者下次登入時以登入後工作的形式啟動，則設定為 [將布建]。

### <a name="sample-ngc-prerequisite-check-output"></a>範例 NGC 先決條件檢查輸出

```
+----------------------------------------------------------------------+
| Ngc Prerequisite Check                                               |
+----------------------------------------------------------------------+

            IsDeviceJoined : YES
             IsUserAzureAD : YES
             PolicyEnabled : YES
          PostLogonEnabled : YES
            DeviceEligible : YES
        SessionIsNotRemote : YES
            CertEnrollment : enrollment authority
          AdfsRefreshToken : YES
             AdfsRaIsReady : YES
    LogonCertTemplateReady : YES ( StateReady )
              PreReqResult : WillProvision
+----------------------------------------------------------------------+
```

## <a name="next-steps"></a>後續步驟

如有問題，請參閱[裝置管理常見問題集](faq.md)
