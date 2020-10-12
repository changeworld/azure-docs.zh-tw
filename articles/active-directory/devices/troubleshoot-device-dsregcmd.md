---
title: 使用 dsregcmd.exe 命令進行疑難排解-Azure Active Directory
description: 使用 dsregcmd.exe 的輸出來瞭解 Azure AD 中的裝置狀態
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80128767"
---
# <a name="troubleshooting-devices-using-the-dsregcmd-command"></a>使用 dsregcmd.exe 命令針對裝置進行疑難排解

Dsregcmd.exe/status 公用程式必須以網域使用者帳戶的形式執行。

## <a name="device-state"></a>裝置狀態

此區段會列出裝置加入狀態參數。 下表列出裝置在不同聯結狀態中的準則。

| >azureadjoined | EnterpriseJoined | DomainJoined | 裝置狀態 |
| ---   | ---   | ---   | ---   |
| YES | 否 | 否 | Azure AD 聯結 |
| 否 | 否 | YES | 已加入網域 |
| YES | 否 | YES | 混合式 AD 已加入 |
| 否 | YES | YES | 已加入內部部署 DRS |

> [!NOTE]
> Workplace Join (Azure AD 註冊的) 狀態會顯示在 [使用者狀態] 區段中。

- **>azureadjoined：** -如果裝置已加入 Azure AD，則設定為 [是]。 否則為 "NO"。
- **EnterpriseJoined：** -如果裝置已加入內部部署 DRS，請設定為 [是]。 裝置不能同時為 EnterpriseJoined 和 >azureadjoined。
- **DomainJoined：** -如果裝置已加入網域 (AD) ，則設定為 [是]。
- **DomainName：** 如果裝置已加入網域，則設定為網域的名稱。

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

只有在裝置已加入 Azure AD 或加入混合式 Azure AD (未 Azure AD 註冊的) 時，才會顯示。 此區段會列出儲存在雲端的裝置識別詳細資料。

- **DeviceId：** -Azure AD 租使用者中裝置的唯一識別碼
- **指紋：** -裝置憑證的指紋 
- **DeviceCertificateValidity：** -裝置憑證的有效性
- **KeyContainerId：** 與裝置憑證相關聯之裝置私密金鑰的 ContainerId
- **KeyProvider：** -KeyProvider (用來儲存裝置私密金鑰的硬體/軟體) 。
- **TpmProtected：** -如果裝置私密金鑰儲存在硬體 TPM 中，則為 "YES"。

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

只有在裝置已加入 Azure AD 或加入混合式 Azure AD (未 Azure AD 註冊的) 時，才會顯示。 此區段會列出裝置加入 Azure AD 時的常見租使用者詳細資料。

> [!NOTE]
> 如果本節中的 MDM Url 是空的，表示 MDM 未設定，或目前的使用者不在 MDM 註冊範圍內。 檢查 Azure AD 中的行動設定，以檢查您的 MDM 設定。

> [!NOTE]
> 即使您看到 MDM Url，這並不表示裝置是由 MDM 管理。 如果租使用者的 MDM 設定適用于自動註冊，即使裝置本身未受管理，也會顯示此資訊。 

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
> 命令必須在使用者內容中執行，才能取得有效的狀態。

- **NgcSet：** 如果已針對目前登入的使用者設定 Windows Hello 索引鍵，則設定為 "YES"。
- **NgcKeyId：** 如果為目前登入的使用者設定了 Windows Hello 索引鍵的識別碼。
- **CanReset：** -表示使用者是否可以重設 Windows Hello 金鑰。 
- **可能的值：** -DestructiveOnly、NonDestructiveOnly、DestructiveAndNonDestructive 或 Unknown （如果發生錯誤）。 
- **WorkplaceJoined：** 如果 Azure AD 已註冊的帳戶已新增至目前 ntuser.man 內容中的裝置，則設定為 [是]。
- **WamDefaultSet：** -如果已針對登入的使用者建立 WAM 預設 WebAccount，則設定為 "YES"。 如果從提升許可權的命令提示字元執行 dsreg/status，此欄位可能會顯示錯誤。 
- **WamDefaultAuthority：** -針對 Azure AD 設定為 [組織]。
- **WamDefaultId：** -Always " https://login.microsoft.com " 表示 Azure AD。
- **WamDefaultGUID：** -WAM provider 的 (Azure AD/MICROSOFT 帳戶預設 WAM WebAccount 的) GUID。 

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

Azure AD 已註冊的裝置可以忽略此區段。

> [!NOTE]
> 命令必須在使用者內容中執行，才能取得該使用者的有效狀態。

- **AzureAdPrt：** -如果已登入使用者的裝置上有 PRT，則設定為 [是]。
- **AzureAdPrtUpdateTime：** -設定為上次更新 PRT 時的 UTC 時間。
- **AzureAdPrtExpiryTime：** 設定為 PRT 在未更新時即將到期的時間（UTC 時間）。
- **AzureAdPrtAuthority：** -Azure AD 授權單位 URL
- **EnterprisePrt：** -如果裝置已從內部部署 ADFS PRT，則設定為 [是]。 針對已加入混合式 Azure AD 的裝置，裝置可能會同時從 Azure AD 和內部部署 AD PRT。 已加入內部部署的裝置只會有企業 PRT。
- **EnterprisePrtUpdateTime：** -設定為上次更新企業 PRT 的 UTC 時間。
- **EnterprisePrtExpiryTime：** 設定為 PRT 在未更新時即將到期的時間（UTC 時間）。
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

### <a name="pre-join-diagnostics"></a>預先聯結診斷

只有當裝置已加入網域，而且無法混合式 Azure AD 聯結時，才會顯示此區段。

本節會執行各種測試，以協助診斷聯結失敗。 本節也包含先前 (？ ) 的詳細資料。 此資訊包括錯誤階段、錯誤碼、伺服器要求識別碼、伺服器回應 HTTP 狀態、伺服器回應錯誤訊息。

- **使用者內容：** 用來執行診斷的內容。 可能的值：系統、未提升許可權的使用者、提高許可權的使用者。 

   > [!NOTE]
   > 因為實際聯結是在系統內容中執行，所以在系統內容中執行診斷最接近實際的聯結案例。 若要在系統內容中執行診斷，必須從提升許可權的命令提示字元執行 dsregcmd.exe/status 命令。

- **用戶端時間：** -UTC 格式的系統時間。
- **AD 連線能力測試：** -Test 會執行對網域控制站的連線測試。 這項測試中的錯誤可能會導致在前置檢查階段發生聯結錯誤。
- **AD 設定測試：** -測試讀取並驗證是否已在內部部署 AD 樹系中正確設定 SCP 物件。 這項測試中的錯誤可能會導致在探索階段發生聯結錯誤，錯誤碼為0x801c001d。
- **DRS 探勘測試：** -Test 從探索中繼資料端點取得 DRS 端點，並執行使用者領域要求。 這項測試中的錯誤可能會導致在探索階段發生聯結錯誤。
- **Drs 連線能力測試：** -Test 會執行對 DRS 端點的基本連線能力測試。
- **權杖取得測試：** -如果使用者租使用者已同盟，測試會嘗試取得 Azure AD 的驗證權杖。 這項測試中的錯誤可能會導致在驗證階段發生聯結錯誤。 如果驗證失敗，將會嘗試將同步聯結視為回復，除非已使用下列登錄機碼設定明確停用回復。
```
    Keyname: Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ
    Value: FallbackToSyncJoin
    Type:  REG_DWORD
    Value: 0x0 -> Disabled
    Value: 0x1 -> Enabled
    Default (No Key): Enabled
 ```
- **回到同步-聯結：** -如果上述登錄機碼不存在，則會設定為「已啟用」，以防止與驗證失敗的同步聯結。 此選項可從 Windows 10 1803 和更新版本中取得。
- **先前的註冊：** -先前的聯結嘗試發生時間。 只會記錄失敗的聯結嘗試。
- **錯誤階段：** -已中止之聯結的階段。 可能的值為預先檢查、探索、驗證、聯結。
- **用戶端 ErrorCode：** - (HRESULT) 傳回用戶端錯誤碼。
- **伺服器 ErrorCode：** -如果將要求傳送至伺服器，且伺服器回應錯誤碼，則會傳回伺服器錯誤碼。 
- **Server message：** -傳回的伺服器訊息以及錯誤碼。
- **Https 狀態：** 伺服器傳回的 Http 狀態。
- **要求識別碼：** -傳送到伺服器的用戶端 requestId。 與伺服器端記錄相互關聯相當有用。

### <a name="sample-pre-join-diagnostics-output"></a>範例預先聯結診斷輸出

下列範例顯示發生探索錯誤的診斷測試失敗。

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

下列範例顯示診斷測試已通過，但登錄嘗試失敗，並出現目錄錯誤，這是同步聯結的預期。 Azure AD Connect 同步處理工作完成後，裝置就能加入。

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

### <a name="post-join-diagnostics"></a>聯結後診斷

此區段會顯示已加入雲端的裝置上執行的健全檢查輸出。

- **AadRecoveryEnabled：** -如果是 "YES"，則裝置中儲存的金鑰將無法使用，且裝置會標示為要復原。 下一次登入將會觸發復原流程，並重新註冊裝置。
- **KeySignTest：** -如果「通過」裝置金鑰的健康情況良好。 如果 KeySignTest 失敗，通常會將裝置標示為進行復原。 下一次登入將會觸發復原流程，並重新註冊裝置。 針對已加入混合式 Azure AD 的裝置，復原是無訊息的。 當 Azure AD 加入或 Azure AD 註冊時，裝置會提示使用者進行驗證，以便在必要時復原並重新註冊裝置。 **KeySignTest 需要較高的許可權。**

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

本節會執行必要條件檢查，以 Windows Hello 企業版 (WHFB) 布建。 

> [!NOTE]
> 如果使用者已成功設定 WHFB，您可能不會在 dsregcmd.exe/status 中看到 NGC 先決條件檢查詳細資料。

- **IsDeviceJoined：** -如果裝置已加入 Azure AD，則設定為 [是]。
- **IsUserAzureAD：** 如果登入的使用者存在於 Azure AD 中，則設定為 [是]。
- **PolicyEnabled：** -如果裝置上已啟用 WHFB 原則，請設定為 [是]。
- **PostLogonEnabled：** -如果 WHFB 註冊是由平臺以原生方式觸發，則設定為 [是]。 如果設定為「否」，則表示 Windows Hello 企業版的註冊是由自訂機制觸發
- **DeviceEligible：** -如果裝置符合使用 WHFB 註冊的硬體需求，請設定為 [是]。
- **SessionIsNotRemote：** -如果目前使用者直接登入裝置而非從遠端登入，請設定為 [是]。
- **CertEnrollment：** 特定于 WHFB 憑證信任部署，指出 WHFB 的憑證註冊授權單位。 如果 WHFB 原則的來源群組原則，則設定為「註冊授權單位」，如果來源是 MDM，則為「行動裝置管理」。 否則為 "none"
- **AdfsRefreshToken：** -適用于 WHFB 憑證信任部署。 只有在 CertEnrollment 為「註冊授權單位」時才存在。 指出裝置是否有使用者的企業 PRT。
- **AdfsRaIsReady：** -適用于 WHFB 憑證信任部署。  只有在 CertEnrollment 為「註冊授權單位」時才存在。 如果 ADFS 在支援 WHFB 的探索中繼資料中指出 *，且* 有可用的登入憑證範本，請設定為 "YES"。
- **LogonCertTemplateReady：** -適用于 WHFB 憑證信任部署。 只有在 CertEnrollment 為「註冊授權單位」時才存在。 如果登入憑證範本的狀態有效，且有助於對 ADFS RA 進行疑難排解，請設定為 [是]。
- **PreReqResult：** -提供所有 WHFB 先決條件評估的結果。 如果 WHFB 註冊會在使用者下次登入時以登入後工作的形式啟動，則設定為 [將會布建]。

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
