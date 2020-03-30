---
title: 故障排除混合 Azure 活動目錄聯接的設備
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331778"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-devices"></a>故障排除混合 Azure 活動目錄聯接的設備 

本文的內容適用于運行 Windows 10 或 Windows 伺服器 2016 的設備。

有關其他 Windows 用戶端，請參閱"[故障排除混合 Azure 活動目錄"加入低級設備](troubleshoot-hybrid-join-windows-legacy.md)。

本文章假設您[設定已加入混合式 Azure Active Directory 的裝置](hybrid-azuread-join-plan.md)來支援下列案例：

- 以裝置為基礎的條件式存取
- [設定的企業漫遊](../active-directory-windows-enterprise-state-roaming-overview.md)
- [Windows Hello 企業版](../active-directory-azureadjoin-passport-deployment.md)

本文檔提供故障排除指南，以解決潛在問題。 

對於 Windows 10 和 Windows Server 2016，混合式 Azure Active Directory 會加入對 Windows 10 2015 年 11 月更新 (含) 以上版本的支援。

## <a name="troubleshoot-join-failures"></a>排除聯接故障

### <a name="step-1-retrieve-the-join-status"></a>步驟 1：擷取加入狀態 

**若要擷取加入狀態：**

1. 以管理員身份打開命令提示
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

此欄位指示設備是否已加入。 如果設備是 Azure AD 聯接設備或混合 Azure AD 聯接設備，則該值將為 **"是**"。
如果值為 **NO**，則尚未完成加入 Azure AD。 

繼續執行後續步驟以進行進一步的故障排除。

### <a name="step-3-find-the-phase-in-which-join-failed-and-the-errorcode"></a>步驟 3：查找聯接失敗和錯誤代碼的階段

#### <a name="windows-10-1803-and-above"></a>視窗 10 1803 及以上

在聯接狀態輸出的"診斷資料"部分中查找"以前的註冊"小節。 僅當設備已加入域且無法混合 Azure AD 聯接時，才會顯示此部分。
"錯誤階段"欄位表示聯接失敗階段，而"用戶端錯誤代碼"表示聯接操作的錯誤代碼。

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

使用事件檢視器日誌查找聯接失敗的階段和錯誤代碼。

1. 在事件檢視器中打開**使用者設備註冊**事件日誌。 位於**應用程式和服務下日誌** > **微軟** > **Windows** > **使用者設備註冊**
2. 查找具有以下事件 I304、305、307 的事件。

![故障日誌事件](./media/troubleshoot-hybrid-join-windows-current/1.png)

![故障日誌事件](./media/troubleshoot-hybrid-join-windows-current/2.png)

### <a name="step-4-check-for-possible-causes-and-resolutions-from-the-lists-below"></a>第 4 步：從以下清單中檢查可能的原因和解決方法

#### <a name="pre-check-phase"></a>預檢查階段

故障的可能原因：

- 設備對網域控制站沒有視線。
   - 設備必須位於組織的內部網路或 VPN 上，其網路視線必須連接到本地活動目錄 （AD） 網域控制站。

#### <a name="discover-phase"></a>發現階段

故障的可能原因：

- 服務連接點 （SCP） 物件配置錯誤/無法從 DC 讀取 SCP 物件。
   - 在設備所屬的 AD 林中，需要有效的 SCP 物件，該物件指向 Azure AD 中的已驗證功能變數名稱。
   - 詳細資訊可在["配置服務連接點](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join)"一節中找到。
- 無法從發現終結點連接和提取發現中繼資料。
   - 設備應該能夠訪問`https://enterpriseregistration.windows.net`SYSTEM 上下文中的 註冊和授權終結點。 
   - 如果本地環境需要出站代理，IT 管理員必須確保設備的電腦帳戶能夠發現並靜默地對出站代理進行身份驗證。
- 無法連接到使用者域終結點並執行域發現。 （僅限 Windows 10 版本 1809 及更高版本）
   - 設備應該能夠訪問`https://login.microsoftonline.com`SYSTEM 上下文中，以執行已驗證域的域發現並確定欄位型別（託管/聯合）。
   - 如果本地環境需要出站代理，IT 管理員必須確保設備上的 SYSTEM 上下文能夠發現並靜默地對出站代理進行身份驗證。

**常見錯誤代碼：**

- **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED** （0x801c001d/-2145648611）
   - 原因：無法讀取 SCP 物件並獲取 Azure AD 租戶資訊。
   - 解決方法：請參閱[配置服務連接點](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join)的部分。
- **DSREG_AUTOJOIN_DISC_FAILED** （0x801c0021/-2145648607）
   - 原因：通用發現失敗。 無法從 DRS 獲取發現中繼資料。
   - 解決方法：查找下面的子錯誤以進一步調查。
- **DSREG_AUTOJOIN_DISC_WAIT_TIMEOUT** （0x801c001f/-2145648609）
   - 原因：執行發現時操作超時。
   - 解決方法：確保`https://enterpriseregistration.windows.net`在 SYSTEM 上下文中可以訪問該功能。 有關詳細資訊，請參閱"[網路連接要求](hybrid-azuread-join-managed-domains.md#prerequisites)"一節。
- **DSREG_AUTOJOIN_USERREALM_DISCOVERY_FAILED** （0x801c0021/-2145648611）
   - 原因：通用領域發現失敗。 無法從 STS 確定欄位型別（託管/聯合）。 
   - 解決方法：查找下面的子錯誤以進一步調查。

**常見子錯誤代碼：**

要查找發現錯誤代碼的子錯誤代碼，請使用以下方法之一。

##### <a name="windows-10-1803-and-above"></a>視窗 10 1803 及以上

在聯接狀態輸出的"診斷資料"部分中查找"DRS 發現測試"。 僅當設備已加入域且無法混合 Azure AD 聯接時，才會顯示此部分。

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

使用事件檢視器日誌查找聯接失敗的階段和錯誤代碼。

1. 在事件檢視器中打開**使用者設備註冊**事件日誌。 位於**應用程式和服務下日誌** > **微軟** > **Windows** > **使用者設備註冊**
2. 查找具有以下事件 201 的事件

![故障日誌事件](./media/troubleshoot-hybrid-join-windows-current/5.png)

###### <a name="network-errors"></a>網路錯誤

- **WININET_E_CANNOT_CONNECT** （0x80072efd/-2147012867）
   - 原因：無法與伺服器建立連接
   - 解決方法：確保與所需的 Microsoft 資源進行網路連接。 有關詳細資訊，請參閱[網路連接要求](hybrid-azuread-join-managed-domains.md#prerequisites)。
- **WININET_E_TIMEOUT** （0x80072ee2/-2147012894）
   - 原因：一般網路超時。
   - 解決方法：確保與所需的 Microsoft 資源進行網路連接。 有關詳細資訊，請參閱[網路連接要求](hybrid-azuread-join-managed-domains.md#prerequisites)。
- **WININET_E_DECODING_FAILED** （0x80072f8f/-2147012721）
   - 原因：網路堆疊無法解碼來自伺服器的回應。
   - 解決方法：確保網路代理不會干擾和修改伺服器回應。

###### <a name="http-errors"></a>HTTP 錯誤

- **DSREG_DISCOVERY_TENANT_NOT_FOUND** （0x801c003a/-2145648582）
   - 原因：配置錯誤的租戶 ID 的 SCP 物件。 或者在租戶中找不到活動訂閱。
   - 解決方法：確保 SCP 物件配置了正確的 Azure AD 租戶 ID 和活動訂閱，或存在於租戶中。
- **DSREG_SERVER_BUSY** （0x801c0025/-2145648603）
   - 原因：來自 DRS 伺服器的 HTTP 503。
   - 解決方法：伺服器當前不可用。 一旦伺服器重新連線，未來的聯接嘗試可能會成功。

###### <a name="other-errors"></a>其他錯誤

- **E_INVALIDDATA** （0x8007000d/-2147024883）
   - 原因：無法分析伺服器回應 JSON。 可能是由於代理返回 HTTP 200 與 HTML auth 頁面。
   - 解決方法：如果本地環境需要出站代理，IT 管理員必須確保設備上的 SYSTEM 上下文能夠發現並靜默地對出站代理進行身份驗證。

#### <a name="authentication-phase"></a>身份驗證階段

僅適用于聯合域帳戶。

失敗的原因：

- 無法為 DRS 資源靜默獲取訪問權杖。
   - Windows 10 設備使用集成 Windows 身份驗證從識別身分同盟服務獲取身份驗證權杖，以訪問活動的 WS-Trust 終結點。 詳細資訊：[聯合服務配置](hybrid-azuread-join-manual.md#set-up-issuance-of-claims)

**常見錯誤代碼：**

使用事件檢視器日誌查找錯誤代碼、子錯誤代碼、伺服器錯誤代碼和伺服器錯誤消息。

1. 在事件檢視器中打開**使用者設備註冊**事件日誌。 位於**應用程式和服務下日誌** > **微軟** > **Windows** > **使用者設備註冊**
2. 查找具有以下事件 ID 305 的事件

![故障日誌事件](./media/troubleshoot-hybrid-join-windows-current/3.png)

##### <a name="configuration-errors"></a>組態錯誤

- **ERROR_ADAL_PROTOCOL_NOT_SUPPORTED** （0xcaa90017/-894894057）
   - 原因：身份驗證協定不是 WS 信任。
   - 解決方法：本地標識提供程式必須支援 WS 信任 
- **ERROR_ADAL_FAILED_TO_PARSE_XML** （0xcaa9002c/-894894036）
   - 原因：本地識別身分同盟服務未返回 XML 回應。
   - 解決方法：確保 MEX 終結點返回有效的 XML。 確保代理不會干擾並返回非 xml 回應。
- **ERROR_ADAL_COULDNOT_DISCOVER_USERNAME_PASSWORD_ENDPOINT** （0xcaa90023/-894894045）
   - 原因：找不到使用者名/密碼身份驗證的終結點。
   - 解決方法：檢查本地標識提供程式設置。 確保啟用 WS 信任終結點，並確保 MEX 回應包含這些正確的終結點。

##### <a name="network-errors"></a>網路錯誤

- **ERROR_ADAL_INTERNET_TIMEOUT** （0xcaa82ee2/-894947614）
   - 原因：一般網路超時。
   - 解決方法：確保`https://login.microsoftonline.com`在 SYSTEM 上下文中可以訪問該功能。 確保本地標識提供程式在 SYSTEM 上下文中可訪問。 有關詳細資訊，請參閱[網路連接要求](hybrid-azuread-join-managed-domains.md#prerequisites)。
- **ERROR_ADAL_INTERNET_CONNECTION_ABORTED** （0xcaa82efe/-894947586）
   - 原因：與 auth 終結點的連接已中止。
   - 解決方法：在某個時間重試或嘗試從備用穩定網路位置加入。
- **ERROR_ADAL_INTERNET_SECURE_FAILURE** （0xcaa82f8f/-894947441）
   - 原因：無法驗證服務器發送的傳輸層安全 （TLS），以前稱為安全通訊端層 （SSL）。
   - 解析度：檢查用戶端時間偏差。 重試某個時間後，或嘗試從備用穩定網路位置加入。 
- **ERROR_ADAL_INTERNET_CANNOT_CONNECT** （0xcaa82efd/-894947587）
   - 原因：嘗試連接到`https://login.microsoftonline.com`失敗。
   - 解決方法：檢查到`https://login.microsoftonline.com`的網路連接。

##### <a name="other-errors"></a>其他錯誤

- **ERROR_ADAL_SERVER_ERROR_INVALID_GRANT** （0xcaa20003/-895352829）
   - 原因：Azure AD 不接受來自本地標識提供程式的 SAML 權杖。
   - 解決方法：檢查同盟伺服器設置。 在身份驗證日誌中查找伺服器錯誤代碼。
- **ERROR_ADAL_WSTRUST_REQUEST_SECURITYTOKEN_FAILED** （0xcaa90014/-894894060）
   - 原因：伺服器 WS-Trust 回應報告故障異常，無法獲取斷言
   - 解決方法：檢查同盟伺服器設置。 在身份驗證日誌中查找伺服器錯誤代碼。
- **ERROR_ADAL_WSTRUST_TOKEN_REQUEST_FAIL** （0xcaa90006/-894894074）
   - 原因：嘗試從權杖終結點獲取訪問權杖時收到錯誤。
   - 解決方法：查找 ADAL 日誌中的基礎錯誤。 
- **ERROR_ADAL_OPERATION_PENDING** （0xcaa1002d/-895418323）
   - 原因：一般 ADAL 失敗
   - 解決方法：從身份驗證日誌中查找子錯誤代碼或伺服器錯誤代碼。
    
#### <a name="join-phase"></a>加入階段

失敗的原因：

查找註冊類型並從下面的清單中查找錯誤代碼。

#### <a name="windows-10-1803-and-above"></a>視窗 10 1803 及以上

在聯接狀態輸出的"診斷資料"部分中查找"以前的註冊"小節。 僅當設備已加入域且無法混合 Azure AD 聯接時，才會顯示此部分。
"註冊類型"欄位表示執行的聯接類型。

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

使用事件檢視器日誌查找聯接失敗的階段和錯誤代碼。

1. 在事件檢視器中打開**使用者設備註冊**事件日誌。 位於**應用程式和服務下日誌** > **微軟** > **Windows** > **使用者設備註冊**
2. 查找具有以下事件 204 的事件

![故障日誌事件](./media/troubleshoot-hybrid-join-windows-current/4.png)

##### <a name="http-errors-returned-from-drs-server"></a>從 DRS 伺服器返回的 HTTP 錯誤

- **DSREG_E_DIRECTORY_FAILURE** （0x801c03f2/-2145647630）
   - 原因：收到來自帶有錯誤代碼的 DRS 的錯誤回應："目錄錯誤"
   - 解決方法：有關可能的原因和解決方法，請參閱伺服器錯誤代碼。
- **DSREG_E_DEVICE_AUTHENTICATION_ERROR** （0x801c0002/-2145648638）
   - 原因：收到帶有錯誤代碼的 DRS 的錯誤回應："身份驗證錯誤"和"錯誤子代碼"不是"設備未找到"。 
   - 解決方法：有關可能的原因和解決方法，請參閱伺服器錯誤代碼。
- **DSREG_E_DEVICE_INTERNALSERVICE_ERROR** （0x801c0006/-2145648634）
   - 原因：收到來自帶有錯誤代碼的 DRS 的錯誤回應："目錄錯誤"
   - 解決方法：有關可能的原因和解決方法，請參閱伺服器錯誤代碼。

##### <a name="tpm-errors"></a>TPM 錯誤

- **NTE_BAD_KEYSET** （0x80090016/-2146893802）
   - 原因：TPM 操作失敗或無效
   - 解析度：可能是由於系統準備映射錯誤。 確保從中創建 sysprep 映射的電腦不是 Azure AD 聯接、混合 Azure AD 加入或註冊 Azure AD。
- **TPM_E_PCP_INTERNAL_ERROR** （0x80290407/-2144795641）
   - 原因：通用 TPM 錯誤。 
   - 解決方法：禁用出現此錯誤的設備上的 TPM。 Windows 10 版本 1809 及以上會自動檢測 TPM 故障，無需使用 TPM 即可完成混合 Azure AD 聯接。
- **TPM_E_NOTFIPS** （0x80280036/-2144862154）
   - 原因：FIPS 模式下的 TPM 當前不受支援。
   - 解決方法：禁用出現此錯誤的設備上的 TPM。 Windows 1809 自動檢測 TPM 故障，無需使用 TPM 即可完成混合 Azure AD 聯接。
- **NTE_AUTHENTICATION_IGNORED** （0x80090031/-2146893775）
   - 原因：TPM 被鎖定。
   - 解析度：瞬態錯誤。 等待冷卻期。 一段時間後加入嘗試應該會成功。 更多資訊請參閱文章[TPM 基礎知識](/windows/security/information-protection/tpm/tpm-fundamentals#anti-hammering)

##### <a name="network-errors"></a>網路錯誤

- **WININET_E_TIMEOUT** （0x80072ee2/-2147012894）
   - 原因：嘗試在 DRS 註冊設備時常規網路超時
   - 解決方法：檢查到`https://enterpriseregistration.windows.net`的網路連接。
- **WININET_E_NAME_NOT_RESOLVED** （0x80072ee7/-2147012889）
   - 原因：無法解析伺服器名稱或位址。
   - 解決方法：檢查到`https://enterpriseregistration.windows.net`的網路連接。 確保主機名稱的 DNS 解析度在 n/w 和設備上準確無誤。
- **WININET_E_CONNECTION_ABORTED** （0x80072efe/-2147012866）
   - 原因：與伺服器的連接異常終止。
   - 解決方法：在某個時間重試或嘗試從備用穩定網路位置加入。

##### <a name="federated-join-server-errors"></a>聯合聯接伺服器錯誤

| 伺服器錯誤代碼 | 伺服器錯誤消息 | 可能的原因 | 解決方案 |
| --- | --- | --- | --- |
| 目錄錯誤 | 您的要求暫時受到節流。 請在 300 秒後重試。 | 預期錯誤。 可能是由於連續多次提出註冊請求。 | 冷卻期後重試聯接 |

##### <a name="sync-join-server-errors"></a>同步聯接伺服器錯誤

| 伺服器錯誤代碼 | 伺服器錯誤消息 | 可能的原因 | 解決方案 |
| --- | --- | --- | --- |
| 目錄錯誤 | AADSTS90002：未<UUID>找到租戶。 如果租戶沒有活動訂閱，則可能發生此錯誤。 請諮詢訂閱管理員。 | SCP 物件中的租戶 ID 不正確 | 確保 SCP 物件配置了正確的 Azure AD 租戶 ID 和活動訂閱，並存在於租戶中。 |
| 目錄錯誤 | 找不到給定 ID 的設備物件。 | 同步聯接的預期錯誤。 設備物件尚未從 AD 同步到 Azure AD | 等待 Azure AD 連接同步完成，同步完成後的下一次加入嘗試將解決此問題 |
| 身份驗證錯誤 | 目的電腦 SID 的驗證 | Azure AD 設備上的證書與用於在同步聯接期間對 Blob 進行簽名的證書不匹配。 此錯誤通常表示同步尚未完成。 |  等待 Azure AD 連接同步完成，同步完成後的下一次加入嘗試將解決此問題 |

### <a name="step-5-collect-logs-and-contact-microsoft-support"></a>第 5 步：收集日誌並聯系 Microsoft 支援

在此處獲取公共腳本[https://1drv.ms/u/s：！AkyTjQ17vtfagYkZ6VJzPg78e3o7PQ]( https://1drv.ms/u/s!AkyTjQ17vtfagYkZ6VJzPg78e3o7PQ)

1. 打開管理員命令提示符並運行`start_ngc_tracing_public.cmd`。
2. 執行重現問題的步驟。
3. 通過 執行 停止運行日誌記錄腳本`stop_ngc_tracing_public.cmd`。
4. Zip 併發送下`%SYSTEMDRIVE%\TraceDJPP\*`日誌進行分析。

## <a name="troubleshoot-post-join-issues"></a>解決加入後問題

### <a name="retrieve-the-join-status"></a>擷取加入狀態 

#### <a name="wamdefaultset-yes-and-azureadprt-yes"></a>WamDefaultSet：是，AzureADPrt：是
  
這些欄位指出使用者在登入裝置時，是否已順利向 Azure AD 進行驗證。 如果值為 **NO**，可能是因為：

- 註冊時與設備關聯的 TPM 中的存儲金鑰錯誤（在運行提升時選中 KeySignTest）。
- 替代登入識別碼
- 找不到 HTTP Proxy

## <a name="known-issues"></a>已知問題
- 在"設置 -> 帳戶 -> 訪問工作或學校"下，混合 Azure AD 加入的設備可能會顯示兩個不同的帳戶，一個用於 Azure AD，另一個用於本地 AD，當連接到移動熱點或外部 WiFi 網路時。 這只是一個 UI 問題，對功能沒有任何影響。 
 
## <a name="next-steps"></a>後續步驟

[使用 dsregcmd 命令繼續對設備進行故障排除](troubleshoot-device-dsregcmd.md)

如有問題，請參閱[裝置管理常見問題集](faq.md)
