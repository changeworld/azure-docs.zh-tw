---
title: 針對混合式 Azure Active Directory 已加入裝置進行疑難排解
description: 針對已加入混合式 Azure Active Directory 的 Windows 10 和 Windows Server 2016 裝置進行疑難排解。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26e52930211611673b6fe2309e2dca067a91ebc8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80331778"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-devices"></a>針對混合式 Azure Active Directory 已加入裝置進行疑難排解 

本文內容適用于執行 Windows 10 或 Windows Server 2016 的裝置。

針對其他 Windows 用戶端，請參閱針對[混合式 Azure Active Directory 已聯結的下層裝置進行疑難排解一](troubleshoot-hybrid-join-windows-legacy.md)文。

本文章假設您[設定已加入混合式 Azure Active Directory 的裝置](hybrid-azuread-join-plan.md)來支援下列案例：

- 以裝置為基礎的條件式存取
- [設定的企業漫遊](../active-directory-windows-enterprise-state-roaming-overview.md)
- [Windows Hello 企業版](../active-directory-azureadjoin-passport-deployment.md)

本檔提供解決潛在問題的疑難排解指引。 

對於 Windows 10 和 Windows Server 2016，混合式 Azure Active Directory 會加入對 Windows 10 2015 年 11 月更新 (含) 以上版本的支援。

## <a name="troubleshoot-join-failures"></a>針對聯結失敗進行疑難排解

### <a name="step-1-retrieve-the-join-status"></a>步驟 1：擷取加入狀態 

**若要擷取加入狀態：**

1. 以系統管理員身分開啟命令提示字元
2. 輸入 `dsregcmd /status`

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+

    AzureAdJoined: YES
 EnterpriseJoined: NO
         DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7
       Thumbprint: B753A6679CE720451921302CA873794D94C6204A
   KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9
      KeyProvider: Microsoft Platform Crypto Provider
     TpmProtected: YES
     KeySignTest: : MUST Run elevated to test.
              Idp: login.windows.net
         TenantId: 72b988bf-xxxx-xxxx-xxxx-2d7cd011xxxx
       TenantName: Contoso
      AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize
   AccessTokenUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token
           MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
        MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
  dmComplianceUrl: https://portal.manage-beta.microsoft.com/?portalAction=Compliance
      SettingsUrl: eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ==
   JoinSrvVersion: 1.0
       JoinSrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/device/
        JoinSrvId: urn:ms-drs:enterpriseregistration.windows.net
    KeySrvVersion: 1.0
        KeySrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/key/
         KeySrvId: urn:ms-drs:enterpriseregistration.windows.net
     DomainJoined: YES
       DomainName: CONTOSO

+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

             NgcSet: YES
           NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
    WorkplaceJoined: NO
      WamDefaultSet: YES
WamDefaultAuthority: organizations
       WamDefaultId: https://login.microsoft.com
     WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)
         AzureAdPrt: YES
```

### <a name="step-2-evaluate-the-join-status"></a>步驟 2：評估加入狀態 

請檢閱下列欄位，並確定它們具有預期的值：

#### <a name="domainjoined--yes"></a>DomainJoined : YES  

此欄位指出裝置是否已加入內部部署 Active Directory。 如果值為 **NO**，則裝置無法執行混合式 Azure AD 加入。  

#### <a name="workplacejoined--no"></a>WorkplaceJoined : NO  

此欄位指出裝置是否已向 Azure AD 註冊為個人裝置 (標示為「已加入工作場所」**)。 如果已加入網域的電腦同時加入混合式 Azure AD，此值應為 **NO**。 如果值為 **YES**，則在完成混合式 Azure AD 加入之前已新增工作或學校帳戶。 在此情況下，使用年度更新版的 Windows 10 (1607) 時會忽略該帳戶。

#### <a name="azureadjoined--yes"></a>AzureAdJoined : YES  

此欄位指出裝置是否已加入。 如果裝置是已加入 Azure AD 的裝置或混合式 Azure AD 裝置，此值會是 **[是]** 。
如果值為 **NO**，則尚未完成加入 Azure AD。 

繼續進行後續步驟，以進行進一步的疑難排解。

### <a name="step-3-find-the-phase-in-which-join-failed-and-the-errorcode"></a>步驟3：尋找聯結失敗的階段和 errorcode

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 和更新版本

在聯結狀態輸出的 [診斷資料] 區段中，尋找「先前的註冊」小節。 只有當裝置已加入網域，且無法進行混合式 Azure AD 聯結時，才會顯示此區段。
「錯誤階段」欄位代表聯結失敗的階段，而「用戶端 ErrorCode」則代表聯結作業的錯誤碼。

```
+----------------------------------------------------------------------+
     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-xxxx-xxxx-xxxx-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e
+----------------------------------------------------------------------+
```

#### <a name="older-windows-10-versions"></a>較舊的 Windows 10 版本

使用事件檢視器記錄來找出聯結失敗的階段和錯誤碼。

1. 在 [事件檢視器] 中開啟**使用者裝置註冊**事件記錄檔。 位於 [**應用程式及服務** > ] [記錄] [**Microsoft** > **Windows** > **使用者裝置註冊**] 底下
2. 尋找具有下列介於到304、305、307的事件。

![失敗記錄事件](./media/troubleshoot-hybrid-join-windows-current/1.png)

![失敗記錄事件](./media/troubleshoot-hybrid-join-windows-current/2.png)

### <a name="step-4-check-for-possible-causes-and-resolutions-from-the-lists-below"></a>步驟4：檢查下列清單中的可能原因和解決方式

#### <a name="pre-check-phase"></a>前置檢查階段

失敗的可能原因：

- 裝置不會看到網域控制站。
   - 裝置必須位在組織內部網路上，或在具有網路連線能力的 VPN 上，以供內部部署 Active Directory （AD）網域控制站使用。

#### <a name="discover-phase"></a>探索階段

失敗的可能原因：

- 服務連接點（SCP）物件設定錯誤/無法從 DC 讀取 SCP 物件。
   - 在裝置所屬的 AD 樹系中，必須有有效的 SCP 物件，指向 Azure AD 中已驗證的功能變數名稱。
   - 如需詳細資訊，請參閱[設定服務連接點](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join)一節。
- 無法連線至探索端點並從中提取探索中繼資料。
   - 裝置應該能夠存取`https://enterpriseregistration.windows.net`系統內容中的，以探索註冊和授權端點。 
   - 如果內部部署環境需要輸出 proxy，IT 系統管理員必須確定裝置的電腦帳戶能夠探索並以無訊息方式向輸出 proxy 進行驗證。
- 無法連線到使用者領域端點，並執行領域探索。 （僅限 Windows 10 1809 版和更新版本）
   - 裝置應該能夠存取`https://login.microsoftonline.com`系統內容中的，以執行已驗證網域的領域探索，並決定網欄位型別（受管理/同盟）。
   - 如果內部部署環境需要輸出 proxy，IT 管理員必須確定裝置上的系統內容能夠探索並以無訊息方式向輸出 proxy 進行驗證。

**一般錯誤代碼：**

- **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED** （0x801c001d/-2145648611）
   - 原因：無法讀取 SCP 物件，並取得 Azure AD 的租使用者資訊。
   - 解決方式：請參閱[設定服務連接點](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join)一節。
- **DSREG_AUTOJOIN_DISC_FAILED** （0x801c0021/-2145648607）
   - 原因：一般探索失敗。 無法取得 DRS 的探索中繼資料。
   - 解決方式：若要進一步調查，請尋找下面的將。
- **DSREG_AUTOJOIN_DISC_WAIT_TIMEOUT** （0x801c001f/-2145648609）
   - 原因：執行探索時發生作業超時。
   - 解決方式：確定`https://enterpriseregistration.windows.net`可以在系統內容中存取。 如需詳細資訊，請參閱[網路連線需求](hybrid-azuread-join-managed-domains.md#prerequisites)一節。
- **DSREG_AUTOJOIN_USERREALM_DISCOVERY_FAILED** （0x801c0021/-2145648611）
   - 原因：一般領域探索失敗。 無法從 STS 判斷網欄位型別（受管理/同盟）。 
   - 解決方式：若要進一步調查，請尋找下面的將。

**通用將碼：**

若要尋找探索錯誤碼的將程式碼，請使用下列其中一種方法。

##### <a name="windows-10-1803-and-above"></a>Windows 10 1803 和更新版本

在聯結狀態輸出的 [診斷資料] 區段中，尋找 [DRS 探勘測試]。 只有當裝置已加入網域，且無法進行混合式 Azure AD 聯結時，才會顯示此區段。

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : UN-ELEVATED User
               Client Time : 2019-06-05 08:25:29.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x80072ee2]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

+----------------------------------------------------------------------+
```

##### <a name="older-windows-10-versions"></a>較舊的 Windows 10 版本

使用事件檢視器記錄來找出聯結失敗的階段和 errorcode。

1. 在 [事件檢視器] 中開啟**使用者裝置註冊**事件記錄檔。 位於 [**應用程式及服務** > ] [記錄] [**Microsoft** > **Windows** > **使用者裝置註冊**] 底下
2. 使用下列介於到201尋找事件

![失敗記錄事件](./media/troubleshoot-hybrid-join-windows-current/5.png)

###### <a name="network-errors"></a>網路錯誤

- **WININET_E_CANNOT_CONNECT** （0x80072efd/-2147012867）
   - 原因：無法建立與伺服器的連接
   - 解決方式：確定網路連線到所需的 Microsoft 資源。 如需詳細資訊，請參閱[網路連接需求](hybrid-azuread-join-managed-domains.md#prerequisites)。
- **WININET_E_TIMEOUT** （0x80072ee2/-2147012894）
   - 原因：一般網路超時。
   - 解決方式：確定網路連線到所需的 Microsoft 資源。 如需詳細資訊，請參閱[網路連接需求](hybrid-azuread-join-managed-domains.md#prerequisites)。
- **WININET_E_DECODING_FAILED** （0x80072f8f/-2147012721）
   - 原因：網路堆疊無法解碼伺服器的回應。
   - 解決方式：確定網路 proxy 不會干擾及修改伺服器回應。

###### <a name="http-errors"></a>HTTP 錯誤

- **DSREG_DISCOVERY_TENANT_NOT_FOUND** （0x801c003a/-2145648582）
   - 原因：設定了錯誤的租使用者識別碼的 SCP 物件。 或在租使用者中找不到有效的訂用帳戶。
   - 解決方式：請確定 SCP 物件已設定正確的 Azure AD 租使用者識別碼和作用中的訂用帳戶，或存在於租使用者中。
- **DSREG_SERVER_BUSY** （0x801c0025/-2145648603）
   - 原因：來自 DRS 伺服器的 HTTP 503。
   - 解決方式：目前無法使用伺服器。 一旦伺服器恢復上線，未來的聯結嘗試可能會成功。

###### <a name="other-errors"></a>其他錯誤

- **E_INVALIDDATA** （0x8007000d/-2147024883）
   - 原因：無法剖析伺服器回應 JSON。 可能是因為 proxy 傳回 HTTP 200 和 HTML 驗證頁面。
   - 解決方式：如果內部部署環境需要輸出 proxy，IT 系統管理員必須確定裝置上的系統內容能夠探索並以無訊息方式向輸出 proxy 進行驗證。

#### <a name="authentication-phase"></a>驗證階段

僅適用于同盟網域帳戶。

失敗的原因：

- 無法以無訊息方式取得 DRS 資源的存取權杖。
   - Windows 10 裝置會使用整合式 Windows 驗證，從同盟服務取得驗證權杖到作用中的 WS-TRUST 端點。 詳細資料：[同盟服務](hybrid-azuread-join-manual.md#set-up-issuance-of-claims)設定

**一般錯誤代碼：**

使用事件檢視器記錄檔來找出錯誤碼、將碼、伺服器錯誤碼和伺服器錯誤訊息。

1. 在 [事件檢視器] 中開啟**使用者裝置註冊**事件記錄檔。 位於 [**應用程式及服務** > ] [記錄] [**Microsoft** > **Windows** > **使用者裝置註冊**] 底下
2. 尋找具有下列 eventID 305 的事件

![失敗記錄事件](./media/troubleshoot-hybrid-join-windows-current/3.png)

##### <a name="configuration-errors"></a>組態錯誤

- **ERROR_ADAL_PROTOCOL_NOT_SUPPORTED** （0xcaa90017/-894894057）
   - 原因：驗證通訊協定不是 WS-TRUST。
   - 解決方式：內部部署身分識別提供者必須支援 WS-TRUST 
- **ERROR_ADAL_FAILED_TO_PARSE_XML** （0xcaa9002c/-894894036）
   - 原因：內部部署 federation service 未傳回 XML 回應。
   - 解決方式：確定 MEX 端點傳回有效的 XML。 請確定 proxy 未干擾並傳回非 xml 回應。
- **ERROR_ADAL_COULDNOT_DISCOVER_USERNAME_PASSWORD_ENDPOINT** （0xcaa90023/-894894045）
   - 原因：無法探索使用者名稱/密碼驗證的端點。
   - 解決方式：檢查內部部署身分識別提供者設定。 請確定已啟用 WS-TRUST 端點，並確定 MEX 回應包含這些正確的端點。

##### <a name="network-errors"></a>網路錯誤

- **ERROR_ADAL_INTERNET_TIMEOUT** （0xcaa82ee2/-894947614）
   - 原因：一般網路超時。
   - 解決方式：確定`https://login.microsoftonline.com`可以在系統內容中存取。 確保內部部署身分識別提供者可在系統內容中存取。 如需詳細資訊，請參閱[網路連接需求](hybrid-azuread-join-managed-domains.md#prerequisites)。
- **ERROR_ADAL_INTERNET_CONNECTION_ABORTED** （0xcaa82efe/-894947586）
   - 原因：與驗證端點的連接已中止。
   - 解決方式：在一段時間後重試，或嘗試從替代穩定的網路位置進行聯結。
- **ERROR_ADAL_INTERNET_SECURE_FAILURE** （0xcaa82f8f/-894947441）
   - 原因：傳輸層安全性（TLS）（先前稱為安全通訊端層（SSL））無法驗證服務器所傳送的憑證。
   - 解決方式：檢查用戶端時間誤差。 請在一段時間後重試，或嘗試從替代穩定的網路位置進行聯結。 
- **ERROR_ADAL_INTERNET_CANNOT_CONNECT** （0xcaa82efd/-894947587）
   - 原因：嘗試連接`https://login.microsoftonline.com`失敗。
   - 解決方式：檢查與`https://login.microsoftonline.com`的網路連接。

##### <a name="other-errors"></a>其他錯誤

- **ERROR_ADAL_SERVER_ERROR_INVALID_GRANT** （0xcaa20003/-895352829）
   - 原因： Azure AD 不接受來自內部部署身分識別提供者的 SAML 權杖。
   - 解決方式：檢查同盟伺服器設定。 在驗證記錄中尋找伺服器錯誤碼。
- **ERROR_ADAL_WSTRUST_REQUEST_SECURITYTOKEN_FAILED** （0xcaa90014/-894894060）
   - 原因：伺服器 WS-TRUST 回應回報了錯誤例外狀況，無法取得判斷提示
   - 解決方式：檢查同盟伺服器設定。 在驗證記錄中尋找伺服器錯誤碼。
- **ERROR_ADAL_WSTRUST_TOKEN_REQUEST_FAIL** （0xcaa90006/-894894074）
   - 原因：嘗試從權杖端點取得存取權杖時收到錯誤。
   - 解決方式：尋找 ADAL 記錄檔中的基礎錯誤。 
- **ERROR_ADAL_OPERATION_PENDING** （0xcaa1002d/-895418323）
   - 原因：一般 ADAL 失敗
   - 解決方式：從驗證記錄尋找將碼或伺服器錯誤碼。
    
#### <a name="join-phase"></a>聯結階段

失敗的原因：

尋找註冊類型，並從下列清單尋找錯誤碼。

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 和更新版本

在聯結狀態輸出的 [診斷資料] 區段中，尋找「先前的註冊」小節。 只有當裝置已加入網域，且無法進行混合式 Azure AD 聯結時，才會顯示此區段。
[註冊類型] 欄位表示已執行的聯結類型。

```
+----------------------------------------------------------------------+
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

#### <a name="older-windows-10-versions"></a>較舊的 Windows 10 版本

使用事件檢視器記錄來找出聯結失敗的階段和 errorcode。

1. 在 [事件檢視器] 中開啟**使用者裝置註冊**事件記錄檔。 位於 [**應用程式及服務** > ] [記錄] [**Microsoft** > **Windows** > **使用者裝置註冊**] 底下
2. 使用下列介於到204尋找事件

![失敗記錄事件](./media/troubleshoot-hybrid-join-windows-current/4.png)

##### <a name="http-errors-returned-from-drs-server"></a>從 DRS 伺服器傳回的 HTTP 錯誤

- **DSREG_E_DIRECTORY_FAILURE** （0x801c03f2/-2145647630）
   - 原因：已收到來自 DRS 的錯誤回應，錯誤碼： "DirectoryError"
   - 解決方式：如需可能的原因和解決方法，請參閱伺服器錯誤碼。
- **DSREG_E_DEVICE_AUTHENTICATION_ERROR** （0x801c0002/-2145648638）
   - 原因：已收到來自 DRS 的錯誤回應，錯誤碼： "AuthenticationError"，而 ErrorSubCode 不是 "DeviceNotFound"。 
   - 解決方式：如需可能的原因和解決方法，請參閱伺服器錯誤碼。
- **DSREG_E_DEVICE_INTERNALSERVICE_ERROR** （0x801c0006/-2145648634）
   - 原因：已收到來自 DRS 的錯誤回應，錯誤碼： "DirectoryError"
   - 解決方式：如需可能的原因和解決方法，請參閱伺服器錯誤碼。

##### <a name="tpm-errors"></a>TPM 錯誤

- **NTE_BAD_KEYSET** （0x80090016/-2146893802）
   - 原因： TPM 操作失敗或無效
   - 解決方式：可能是因為 sysprep 映射錯誤。 確定建立 sysprep 映射的電腦未 Azure AD 聯結、混合式 Azure AD 聯結，或 Azure AD 註冊。
- **TPM_E_PCP_INTERNAL_ERROR** （0x80290407/-2144795641）
   - 原因：一般 TPM 錯誤。 
   - 解決方式：在發生此錯誤的裝置上停用 TPM。 Windows 10 1809 版和更新版本會自動偵測 TPM 失敗，並在不使用 TPM 的情況下完成混合式 Azure AD 聯結。
- **TPM_E_NOTFIPS** （0x80280036/-2144862154）
   - 原因：目前不支援 FIPS 模式中的 TPM。
   - 解決方式：在發生此錯誤的裝置上停用 TPM。 Windows 1809 會自動偵測 TPM 失敗，並在不使用 TPM 的情況下完成混合式 Azure AD 聯結。
- **NTE_AUTHENTICATION_IGNORED** （0x80090031/-2146893775）
   - 原因： TPM 已鎖定。
   - 解決方式：暫時性錯誤。 等候 cooldown 期限。 請在一段時間後嘗試聯結。 如需詳細資訊，請參閱[TPM 基本](/windows/security/information-protection/tpm/tpm-fundamentals#anti-hammering)概念一文

##### <a name="network-errors"></a>網路錯誤

- **WININET_E_TIMEOUT** （0x80072ee2/-2147012894）
   - 原因：一般網路時間已在 DRS 嘗試註冊裝置
   - 解決方式：檢查的`https://enterpriseregistration.windows.net`網路連線能力。
- **WININET_E_NAME_NOT_RESOLVED** （0x80072ee7/-2147012889）
   - 原因：無法解析伺服器名稱或位址。
   - 解決方式：檢查的`https://enterpriseregistration.windows.net`網路連線能力。 確保主機名稱的 DNS 解析在 n/w 和裝置上是正確的。
- **WININET_E_CONNECTION_ABORTED** （0x80072efe/-2147012866）
   - 原因：與伺服器的連接已異常終止。
   - 解決方式：在一段時間後重試，或嘗試從替代穩定的網路位置進行聯結。

##### <a name="federated-join-server-errors"></a>聯合聯結伺服器錯誤

| 伺服器錯誤碼 | 伺服器錯誤訊息 | 可能的原因 | 解決方法 |
| --- | --- | --- | --- |
| DirectoryError | 您的要求暫時受到節流。 請在300秒後再試一次。 | 預期的錯誤。 可能是因為快速地連續建立多個註冊要求。 | 在 cooldown 期間之後重試聯結 |

##### <a name="sync-join-server-errors"></a>同步聯結伺服器錯誤

| 伺服器錯誤碼 | 伺服器錯誤訊息 | 可能的原因 | 解決方法 |
| --- | --- | --- | --- |
| DirectoryError | AADSTS90002：找<UUID>不到租使用者。 如果沒有作用中的租使用者訂用帳戶，可能會發生此錯誤。 請洽詢您的訂用帳戶管理員。 | SCP 物件中的租使用者識別碼不正確 | 請確定已使用正確的 Azure AD 租使用者識別碼和作用中的訂用帳戶來設定 SCP 物件，並出現在租使用者中。 |
| DirectoryError | 找不到指定之識別碼的裝置物件。 | 同步聯結的預期錯誤。 裝置物件尚未從 AD 同步至 Azure AD | 等待 Azure AD Connect 同步完成，並在同步完成後的下一次聯結嘗試解決問題 |
| AuthenticationError | 目的電腦 SID 的驗證 | Azure AD 裝置上的憑證與同步聯結期間用來簽署 blob 的憑證不相符。 此錯誤通常表示同步尚未完成。 |  等待 Azure AD Connect 同步完成，並在同步完成後的下一次聯結嘗試解決問題 |

### <a name="step-5-collect-logs-and-contact-microsoft-support"></a>步驟5：收集記錄和連絡人 Microsoft 支援服務

在這裡取得公用腳本： [ https://1drv.ms/u/s！AkyTjQ17vtfagYkZ6VJzPg78e3o7PQ]( https://1drv.ms/u/s!AkyTjQ17vtfagYkZ6VJzPg78e3o7PQ)

1. 開啟系統管理命令提示字元， `start_ngc_tracing_public.cmd`然後執行。
2. 執行重現問題的步驟。
3. 藉由執行來停止執行`stop_ngc_tracing_public.cmd`記錄腳本。
4. Zip 並傳送下`%SYSTEMDRIVE%\TraceDJPP\*`的記錄以進行分析。

## <a name="troubleshoot-post-join-issues"></a>針對後續聯結問題進行疑難排解

### <a name="retrieve-the-join-status"></a>擷取加入狀態 

#### <a name="wamdefaultset-yes-and-azureadprt-yes"></a>WamDefaultSet： YES 和 AzureADPrt： YES
  
這些欄位指出使用者在登入裝置時，是否已順利向 Azure AD 進行驗證。 如果值為 **NO**，可能是因為：

- 註冊時，與裝置相關聯的 TPM 中有錯誤的儲存體金鑰（以提高許可權執行時檢查 KeySignTest）。
- 替代登入識別碼
- 找不到 HTTP Proxy

## <a name="known-issues"></a>已知問題
- 在 [設定-> 帳戶] 下 > [存取公司或學校]，混合式 Azure AD 加入的裝置可能會顯示兩個不同的帳戶，一個用於 Azure AD，另一個用於內部部署 AD，連線到行動裝置或外部 WiFi 網路時。 這只是 UI 問題，不會對功能造成任何影響。 
 
## <a name="next-steps"></a>後續步驟

[使用 dsregcmd.exe 命令繼續疑難排解裝置](troubleshoot-device-dsregcmd.md)

如有問題，請參閱[裝置管理常見問題集](faq.md)
