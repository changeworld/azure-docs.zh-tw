---
title: 使用 dsregcmd 命令進行故障排除 - Azure 活動目錄
description: 使用 dsregcmd 的輸出瞭解 Azure AD 中的設備狀態
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128767"
---
# <a name="troubleshooting-devices-using-the-dsregcmd-command"></a>使用 dsregcmd 命令對設備進行故障排除

dsregcmd /狀態實用程式必須作為域使用者帳戶運行。

## <a name="device-state"></a>裝置狀態

本節列出設備聯接狀態參數。 下表列出了設備處於各種聯接狀態的條件。

| AzureAdad | 企業加入 | 域已加入 | 裝置狀態 |
| ---   | ---   | ---   | ---   |
| YES | 否 | 否 | Azure AD 已加入 |
| 否 | 否 | YES | 已加入域 |
| YES | 否 | YES | 混合 AD 加入 |
| 否 | YES | YES | 本地 DRS 已加入 |

> [!NOTE]
> 工作區加入（Azure AD 已註冊）狀態顯示在"使用者狀態"部分

- **AzureAdad：-** 如果設備已加入 Azure AD，則設置為"是"。 否則"否"。
- **企業加入：** - 如果設備已加入本地 DRS，則設置為"是"。 設備不能同時是企業加入和 AzureAdAd.。
- **域已加入：** - 如果設備已加入域 （AD），則設置為"是"。
- **功能變數名稱：** - 如果設備已加入域，則設置為域的名稱。

### <a name="sample-device-state-output"></a>示例設備狀態輸出

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

## <a name="device-details"></a>裝置詳細資料

僅當設備加入 Azure AD 或混合 Azure AD 加入（未註冊 Azure AD）時顯示。 本節列出了存儲在雲中的設備標識詳細資訊。

- **裝置識別碼：** - Azure AD 租戶中設備的唯一 ID
- **指紋：** - 設備證書的指紋 
- **設備證書有效性：** - 設備證書的有效性
- **金鑰容器Id：** - 與設備證書關聯的設備私密金鑰的容器 Id
- **金鑰提供程式：** - 用於存放裝置私密金鑰的金鑰提供程式（硬體/軟體）。
- **Tpm 保護：** - 如果設備私密金鑰存儲在硬體 TPM 中，則"是"。

### <a name="sample-device-details-output"></a>示例設備詳細資訊輸出

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

僅當設備加入 Azure AD 或混合 Azure AD 加入（未註冊 Azure AD）時顯示。 本節列出設備加入 Azure AD 時的常見租戶詳細資訊。

> [!NOTE]
> 如果此部分中的 MDM URL 為空，則表示 MDM 未配置，或者當前使用者不在 MDM 註冊範圍內。 檢查 Azure AD 中的移動設置以查看 MDM 配置。

> [!NOTE]
> 即使您看到 MDM URL，也不表示設備由 MDM 管理。 如果租戶具有用於自動註冊的 MDM 配置，即使設備本身未進行管理，也會顯示該資訊。 

### <a name="sample-tenant-details-output"></a>示例租戶詳細資訊輸出

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

本節列出當前登錄到設備的使用者的各種屬性的狀態。

> [!NOTE]
> 該命令必須在使用者上下文中運行才能檢索有效狀態。

- **NgcSet：** - 如果為當前登錄的使用者設置了 Windows Hello 金鑰，則設置為"是"。
- **NgcKeyId：** - Windows Hello 金鑰的 ID（如果為當前登錄使用者設置了金鑰）。
- **Can重置：** - 表示使用者是否可以重置 Windows Hello 金鑰。 
- **可能的值：** - 僅破壞性、僅破壞性、破壞性和非破壞性值，如果錯誤，則未知。 
- **工作區：-** 如果 Azure AD 註冊帳戶已添加到當前 NTUSER 上下文中的設備，則設置為"是"。
- **WamDefaultSet：** - 如果為登錄使用者創建了 WAM 預設 Web 帳戶，則設置為"是"。 如果 dsreg /狀態從提升的命令提示符運行，則此欄位可能會顯示錯誤。 
- **WamDefaultAuthority：** - 設置為 Azure AD 的"組織"。
- **WamDefaultId：** -https://login.microsoft.com始終"""為 Azure AD。
- **WamDefaultGUID：** - WAM 供應商的（Azure AD/Microsoft 帳戶）GUID 用於預設 WAM Web 帳戶。 

### <a name="sample-user-state-output"></a>示例使用者狀態輸出

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

對於 Azure AD 註冊設備，可以忽略此部分。

> [!NOTE]
> 該命令必須在使用者上下文中運行才能檢索該使用者的有效狀態。

- **AzureAdPrt：** - 如果設備上存在已登錄使用者的 PRT，則設置為"是"。
- **AzureAdPrt 更新時間：** - 設置為上次更新 PRT 時以 UTC 表示的時間。
- **AzureAdPrt 過期時間：** - 設置為在 UTC 中，如果 PRT 未續訂，它將過期。
- **Azure AdPrt 授權：** - Azure AD 許可權 URL
- **企業 Prt：** - 如果設備具有來自本地 ADFS 的 PRT，則設置為"是"。 對於混合 Azure AD 聯接設備，設備可以同時從 Azure AD 和本地 AD 同時具有 PRT。 本地連接的設備將僅具有企業 PRT。
- **企業 Prt 更新時間：** - 設置為上次更新企業 PRT 時 UTC 的時間。
- **企業 Prt 過期時間：** - 設置為在 UTC 中，如果 PRT 未續訂，它將過期。
- **企業授權：** - ADFS 授權 URL

### <a name="sample-sso-state-output"></a>樣本 SSO 狀態輸出

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

### <a name="pre-join-diagnostics"></a>加入前診斷

僅當設備已加入域且無法混合 Azure AD 聯接時，才會顯示此部分。

本節執行各種測試以説明診斷聯接失敗。 本節還包括上一個 （？） 的詳細資訊。 此資訊包括錯誤階段、錯誤代碼、伺服器請求 ID、伺服器回應 HTTP 狀態、伺服器回應錯誤訊息。

- **使用者上下文：** - 運行診斷的上下文。 可能的值：系統、UN-高架使用者、提升使用者。 

   > [!NOTE]
   > 由於實際聯接是在 SYSTEM 上下文中執行的，因此在 SYSTEM 上下文中運行診斷最接近實際聯接方案。 要在 SYSTEM 上下文中運行診斷，必須從提升的命令提示符運行 dsregcmd /狀態命令。

- **用戶端時間：** - 系統時間（以 UTC 表示）。
- **AD 連接測試：** - 測試對網域控制站執行連接測試。 此測試中的錯誤可能會導致預檢查階段的聯接錯誤。
- **AD 配置測試：** - 測試讀取並驗證 SCP 物件是否在本地 AD 林中正確配置。 此測試中的錯誤可能會導致發現階段的聯接錯誤，錯誤代碼 0x801c001d。
- **DRS 發現測試：** - 測試從發現中繼資料終結點獲取 DRS 終結點，並執行使用者域請求。 此測試中的錯誤可能會導致發現階段的聯接錯誤。
- **DRS 連接測試：** - 測試執行與 DRS 終結點的基本連接測試。
- **權杖獲取測試：** - 如果使用者租戶聯合，測試嘗試獲取 Azure AD 身份驗證權杖。 此測試中的錯誤可能會導致 auth 階段的聯接錯誤。 如果 auth 失敗，將嘗試將同步聯接作為回退，除非使用以下註冊表鍵設置顯式禁用回退。
```
    Keyname: Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ
    Value: FallbackToSyncJoin
    Type:  REG_DWORD
    Value: 0x0 -> Disabled
    Value: 0x1 -> Enabled
    Default (No Key): Enabled
 ```
- **回退到同步連接：** - 設置為"已啟用"，如果上述登錄機碼，以防止回退同步聯接與 auth 失敗， 不存在。 此選項可從 Windows 10 1803 及更高版本獲得。
- **上一次註冊：** - 上次"加入"嘗試發生的時間。 只記錄失敗的聯接嘗試。
- **錯誤階段：** - 已中止的聯接階段。 可能的值是預先檢查、發現、auth、聯接。
- **用戶端錯誤代碼：** - 返回的用戶端錯誤代碼 （HRESULT）。
- **伺服器錯誤代碼：** - 如果要求傳送到伺服器，伺服器會使用錯誤代碼進行回復，則伺服器錯誤代碼。 
- **伺服器消息：** - 伺服器消息隨錯誤代碼一起返回。
- **Https 狀態：** - 伺服器返回的 Http 狀態。
- **請求 ID：** - 發送到伺服器的用戶端請求 ID。 可用於與伺服器端日誌關聯。

### <a name="sample-pre-join-diagnostics-output"></a>樣品預加入診斷輸出

下面的示例顯示診斷測試失敗，並出現發現錯誤。

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

下面的示例顯示診斷測試正在通過，但註冊嘗試失敗，出現目錄錯誤，該錯誤預期用於同步聯接。 Azure AD 連接同步作業完成後，設備將能夠加入。

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

本節顯示在加入雲的設備上執行的健全性檢查的輸出。

- **啟用 AadRecovery：** - 如果"是"，則存儲在設備中的金鑰不可用，並且設備標記為恢復。 下一個登錄將觸發恢復流並重新註冊設備。
- **金鑰測試：** - 如果"通過"設備金鑰處於良好運行狀況。 如果 KeySignTest 失敗，設備通常會標記為恢復。 下一個登錄將觸發恢復流並重新註冊設備。 對於混合 Azure AD 聯接設備，恢復為靜默狀態。 在 Azure AD 加入或 Azure AD 註冊時，設備將提示使用者身份驗證以在必要時恢復和重新註冊設備。 **金鑰簽名測試需要較高的權限。**

#### <a name="sample-post-join-diagnostics-output"></a>樣品連接後診斷輸出

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

         AadRecoveryEnabled: NO
               KeySignTest : PASSED
+----------------------------------------------------------------------+
```

## <a name="ngc-prerequisite-check"></a>NGC 先決條件檢查

本節對為企業 （WHFB） 預配 Windows Hello 執行滲透檢查。 

> [!NOTE]
> 如果使用者已成功配置 WHFB，則可能無法在 dsregcmd /狀態中看到 NGC 先決條件檢查詳細資訊。

- **已加入：** - 如果設備已加入 Azure AD，則設置為"是"。
- **IsUserAzureAD：** - 如果登錄的使用者存在於 Azure AD 中，則設置為"是"。
- **策略啟用：** - 如果設備上啟用了 WHFB 策略，則設置為"是"。
- **啟用後日誌：** - 如果 WHFB 註冊由平臺本機觸發，則設置為"是"。 如果設置為"否"，則表示"適用于企業的 Windows Hello"註冊由自訂機制觸發
- **設備合格：** - 如果設備滿足使用 WHFB 註冊的硬體要求，則設置為"是"。
- **會話不是遠端：** - 如果當前使用者直接登錄到設備而不是遠端登入到設備，則設置為"是"。
- **憑證註冊：** - 特定于 WHFB 證書信任部署，指示 WHFB 的憑證註冊許可權。 如果 WHFB 策略的來源是群組原則，則設置為"註冊許可權"，如果源為 MDM，則設置為"行動裝置管理"。 否則"無"
- **AdfsRefreshToken：** - 特定于 WHFB 證書信任部署。 僅當 Cert註冊是"註冊許可權"時才存在。 指示設備是否具有針對使用者的企業 PRT。
- **AdfsRaIs 就緒：** - 特定于 WHFB 證書信任部署。  僅當 Cert註冊是"註冊許可權"時才存在。 如果 ADFS 在發現中繼資料中指示它支援*WHFB，並且*登錄憑證範本可用，則設置為"是"。
- **登錄證書就緒：** - 特定于 WHFB 證書信任部署。 僅當 Cert註冊是"註冊許可權"時才存在。 如果登錄憑證範本的狀態有效，則設置為"是"，並有助於排除 ADFS RA 的故障。
- **預結果：** - 提供所有 WHFB 先決條件評估的結果。 如果 WHFB 註冊將在使用者下次登錄時作為登錄後任務啟動，則設置為"將預配"。

### <a name="sample-ngc-prerequisite-check-output"></a>示例 NGC 先決條件檢查輸出

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
