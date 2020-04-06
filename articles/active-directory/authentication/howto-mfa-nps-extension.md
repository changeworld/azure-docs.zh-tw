---
title: 使用 NPS - Azure 的活動目錄提供 Azure MFA 功能
description: 將雲端式雙步驟驗證功能新增至現有的驗證基礎結構
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: f884f4c0ea3a610f28a8fdbb34b081f0b0a64d08
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2020
ms.locfileid: "80666956"
---
# <a name="integrate-your-existing-nps-infrastructure-with-azure-multi-factor-authentication"></a>將現有的 NPS 基礎結構與 Azure Multi-Factor Authentication 整合

Azure MFA 的網路原則伺服器 (NPS) 擴充功能可使用現有伺服器將雲端式 MFA 功能新增至驗證基礎結構。 利用 NPS 擴充功能，您可以在現有驗證流程中新增通話、簡訊或電話應用程式驗證，而不必安裝、設定及維護新的伺服器。 

這個擴充功能是針對想要保護 VPN 連線但無須部署 Azure MFA Server 的組織所建立。 NPS 擴充功能是用於在 RADIUS 和以雲端為基礎的 Azure MFA 之間作為配接器，為聯盟或同步處理的使用者提供第二個驗證因素。

使用 Azure MFA 的 NPS 擴充功能時，驗證流程會包含下列元件︰ 

1. **NAS/VPN 伺服器**會從 VPN 用戶端接收要求，並將其轉換為對 NPS 伺服器的 RADIUS 要求。 
2. **NPS 伺服器**會連線至 Active Directory，以對 RADIUS 要求執行主要驗證，並於成功時將要求傳遞至任何已安裝的擴充功能。  
3. **NPS 擴充功能**會觸發 Azure MFA 要求以進行第二項驗證。 當擴充功能收到回應後，如果 MFA 挑戰成功，擴充功能便會藉由為 NPS 伺服器提供包含 Azure STS 所發行之 MFA 宣告的安全性權杖來完成驗證要求。  
4. **Azure MFA**與 Azure 活動目錄通信,以檢索使用者的詳細資訊,並使用配置到使用者的驗證方法執行輔助身份驗證。

下圖說明此高階驗證要求流程︰ 

![驗證流程圖](./media/howto-mfa-nps-extension/auth-flow.png)

## <a name="plan-your-deployment"></a>規劃您的部署

NPS 延伸模組會自動處理備援，因此您不需要特殊組態。

您可以視需要建立數量不拘且具有 Azure MFA 功能的 NPS 伺服器。 如果您安裝多部伺服器，您應該為每部伺服器使用不同的用戶端憑證。 建立每個伺服器的憑證，意味著您可以個別更新每個憑證，無須擔心所有的伺服器皆停機。

VPN 伺服器會路由驗證要求，因此伺服器必須留意新的 Azure MFA 啟用 NPS 伺服器。

## <a name="prerequisites"></a>Prerequisites

NPS 擴充功能是為了搭配現有基礎結構來運作。 請確定您已備妥這些必要條件，然後再開始。

### <a name="licenses"></a>授權

Azure MFA 的 NPS 擴充功能可透過 [Azure Multi-Factor Authentication 授權](multi-factor-authentication.md) (隨附於 Azure AD Premium、EMS 或 MFA 獨立授權) 來提供給客戶使用。 Azure MFA 以使用情況為基礎的授權 (例如每位使用者或每次驗證授權) 與 NPS 擴充功能並不相容。 

### <a name="software"></a>軟體

Windows Server 2008 R2 SP1 或更新版本。

### <a name="libraries"></a>程式庫

這些程式庫會自動連同延伸模組一起安裝。

- [適用於 Visual Studio 2013 的 Visual C++ 可轉散發套件 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
- [適用於 Windows PowerShell 1.1.1660 版本的 Microsoft Azure Active Directory 模組](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

如果您還沒有適用於 Windows PowerShell 的 Microsoft Azure Active Directory 模組，系統會透過您在安裝過程中執行的設定指令碼來加以安裝。 因此，如果您尚未安裝此模組，就不必事先安裝。

### <a name="azure-active-directory"></a>Azure Active Directory

使用 NPS 擴充功能的每位使用者都必須使用 Azure AD Connect 同步到 Azure Active Directory ，且必須註冊 MFA。

當您安裝擴充功能時，您的 Azure AD 租用戶需要目錄識別碼和系統管理員認證。 您可以在 [Azure 入口網站](https://portal.azure.com)中找到您的目錄識別碼。 以系統管理員身分登入。 搜尋並選擇**Azure 的目錄**,然後選擇**屬性**。 複製 [目錄識別碼]**** 方塊中的 GUID，然後儲存。 當您安裝 NPS 擴充功能時，將使用此 GUID 作為租用戶識別碼。

![在 Azure Active Directory 屬性下尋找您的目錄識別碼](./media/howto-mfa-nps-extension/properties-directory-id.png)

### <a name="network-requirements"></a>網路需求

NPS 伺服器必須能夠透過連接埠 80 和 443 與下列 URL 通訊。

- https:\//adnotifications.windowsazure.com
- https:\//login.microsoftonline.com
- https:\//credentials.azure.com

此外,需要連接到以下網址才能[使用提供的 PowerShell 文稿完成配接器的設定](#run-the-powershell-script)

- https:\//login.microsoftonline.com
- Ht:\//provisioningapi.microsoftonline.com
- Https:\//aadcdn.msauth.net

## <a name="prepare-your-environment"></a>準備您的環境

在安裝 NPS 延伸模組之前，您會想要將您的環境準備就緒，以處理驗證流量。

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>啟用已加入網域之伺服器上的 NPS 角色

NPS 伺服器會連線到 Azure Active Directory，並驗證 MFA 要求。 為此角色選擇一部伺服器。 建議您選擇不處理來自其他服務之要求的伺服器，因為 NPS 延伸模組會對任何不是 RADIUS 的要求擲回錯誤。 NPS 伺服器必須設定為您環境的主要及次要驗證伺服器，它不可將 RADIUS 要求 Proxy 傳送到其他伺服器。

1. 在伺服器上，從 [伺服器管理員快速入門] 功能表開啟 [新增角色及功能精靈]****。
2. 將您的安裝類型選為 [角色型或功能型安裝]****。
3. 選取 [網路原則與存取服務]**** 伺服器角色。 隨即顯示快顯視窗，通知您執行這個角色所需的功能。
4. 繼續執行精靈，直到顯示 [確認] 頁面為止。 選取 [安裝]  。

現在您已經具備指定給 NPS 的伺服器，因此也應設定這部伺服器以處理從 VPN 解決方案傳入的 RADIUS 要求。

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>設定您的 VPN 解決方案與 NPS 伺服器通訊

根據您所使用 VPN 解決方案的不同，設定您 RADIUS 驗證原則的步驟也有所不同。 設定此原則以指向 RADIUS NPS 伺服器。

### <a name="sync-domain-users-to-the-cloud"></a>將網域使用者同步處理至雲端

這個步驟在租用戶上可能已經完成，但建議最好再次檢查，確認 Azure AD Connect 最近已同步處理您的資料庫。

1. 以管理者登入[Azure 門戶](https://portal.azure.com)。
2. 選擇**Azure 的活動目錄** > **Azure AD 連線**
3. 確認同步處理狀態為 [已啟用]****，且上次同步處理為不到一小時前。

如果您必須展開新一回合的同步處理，請使用 [Azure AD Connect 同步處理：排程器](../hybrid/how-to-connect-sync-feature-scheduler.md#start-the-scheduler)中的指示。

### <a name="determine-which-authentication-methods-your-users-can-use"></a>判斷您的使用者可以使用的驗證方法

有兩個因素會影響與 NPS 擴充部署搭配提供的驗證方法：

1. 在 RADIUS 用戶端 (VPN、Netscaler 伺服器或其他) 與 NPS 伺服器之間使用的密碼加密演算法。
   - **PAP**支援雲中 Azure MFA 的所有身份驗證方法:電話呼叫、單向簡訊、行動應用通知、OATH 硬體權杖和行動應用程式驗證碼。
   - **CHAPV2**和**EAP**支援電話呼叫和行動應用程式通知。

      > [!NOTE]
      > 當您部署 NPS 擴充時，使用這些因素來評估哪些方法可供您的使用者使用。 如果您的 RADIUS 用戶端支援 PAP，但用戶端 UX 沒有驗證碼的輸入欄位，則通話和行動裝置應用程式通知是兩個支援的選項。
      >
      > 此外,如果您的 VPN 用戶端 UX 支援輸入欄位,並且您已配置了網路存取策略 -身份驗證可能會成功,但是網路策略中配置的 RADIUS 屬性都不會應用於網路存取裝置(如 RRAS 伺服器)和 VPN 用戶端。 因此,VPN 用戶端可能具有比預期更多的訪問許可權,或者對無法訪問的訪問更少。
      >

2. 用戶端應用程式 (VPN、Netscaler 伺服器或其他) 可以處理的輸入法。 例如，VPN 用戶端是否有一些方法可讓使用者從文字或行動裝置應用程式輸入驗證程式碼？

您可以在 Azure 中[停用不受支援的驗證方法](howto-mfa-mfasettings.md#verification-methods)。

### <a name="register-users-for-mfa"></a>針對 MFA 註冊使用者

在您部署與使用 NPS 擴充功能之前，必須先針對 MFA 註冊需執行雙步驟驗證的使用者。 緊接著，若要同時部署與測試延伸模組，您必須至少有一個測試帳戶，並應已針對 Multi-Factor Authentication 完全註冊此帳戶。

使用下列步驟啟動測試帳戶：

1. [https://aka.ms/mfasetup](https://aka.ms/mfasetup)使用測試帳戶登錄。
2. 遵循提示來設定驗證方法。
3. [創建條件訪問策略](howto-mfa-getstarted.md#create-conditional-access-policy)以要求測試帳戶的多重身份驗證。

## <a name="install-the-nps-extension"></a>安裝 NPS 擴充功能

> [!IMPORTANT]
> 在與 VPN 存取點不同的伺服器上安裝 NPS 擴充功能。

### <a name="download-and-install-the-nps-extension-for-azure-mfa"></a>下載並安裝 Azure MFA 的 NPS 延伸模組

1. 從 Microsoft 下載中心[下載 NPS 延伸](https://aka.ms/npsmfa)。
2. 將二進位檔複製到您要設定的網路原則伺服器。
3. 運行*安裝程式.exe*並按照安裝說明操作。 如果您遇到錯誤，請根據必要條件一節再次檢查兩個已成功安裝的程式庫。

#### <a name="upgrade-the-nps-extension"></a>升級 NPS 延伸

升級現有 NPS 擴展安裝時,為了避免重新啟動基礎伺服器,請完成以下步驟:

1. 卸載現有版本
1. 執行新的安裝程式
1. 重新啟動網路原則伺服器 (IAS) 服務

### <a name="run-the-powershell-script"></a>執行 PowerShell 指令碼

安裝程式會在下列位置建立 PowerShell 指令碼︰`C:\Program Files\Microsoft\AzureMfa\Config` (其中 C:\ 是您的安裝磁碟機)。 此 PowerShell 指令碼會在每次執行時執行下列動作：

- 建立自我簽署憑證。
- 讓憑證的公開金鑰與 Azure AD 的服務主體產生關聯。
- 將憑證儲存在本機電腦的憑證存放區。
- 向網路使用者授予對證書私鑰的訪問許可權。
- 重新啟動 NPS。

除非您想要使用自己的憑證 (而非 PowerShell 指令碼產生的自我簽署憑證)，否則請執行 PowerShell 指令碼來完成安裝。 如果您在多部伺服器上安裝延伸模組，每一部伺服器都應該有自己的憑證。

1. 以系統管理理員身分執行 Windows PowerShell。
2. 變更目錄。

   `cd "C:\Program Files\Microsoft\AzureMfa\Config"`

3. 執行安裝程式建立的 PowerShell 指令碼。

   `.\AzureMfaNpsExtnConfigSetup.ps1`

4. 以系統管理員身分登入 Azure AD。
5. PowerShell 會提示您輸入您的租用戶識別碼。 使用您在必要條件一節中從 Azure 入口網站複製的目錄識別碼 GUID。
6. PowerShell 會在指令碼完成時顯示成功訊息。  

在您想要進行設定以取得負載平衡的任何其他 NPS 伺服器上，重複上述步驟。

如果以前的計算機證書已過期,並且已生成新證書,則應刪除任何過期的證書。 證書過期可能會導致 NPS 擴展啟動出現問題。

> [!NOTE]
> 如果您使用自己的憑證，而不是透過 PowerShell 指令碼產生憑證，請確定這些憑證遵守 NPS 命名慣例。 主體名稱必須是 **CN=\<租用戶識別碼\>,OU=Microsoft NPS Extension**。 

### <a name="microsoft-azure-government-additional-steps"></a>微軟 Azure 政府其他步驟

對於使用 Azure 政府雲的客戶,每個 NPS 伺服器上都需要執行以下其他配置步驟:

1. 在 NPS 伺服器上開啟**註冊表編輯器**。
1. 瀏覽至 `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa`。 設定以下鍵值:

    | 登錄機碼       | 值 |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.us   |
    | STS_URL            | https://login.microsoftonline.us/ |

1. 重複前兩個步驟,為每個 NPS 伺服器設置註冊表鍵值。
1. 重新啟動每個 NPS 伺服器的 NPS 服務。

    為了最小的影響,將每個 NPS 伺服器從 NLB 旋轉中一次拉出一個,並等待所有連接耗盡。

### <a name="certificate-rollover"></a>憑證變換

使用 NPS 擴展版本 1.0.1.32 時,現在支援讀取多個證書。 此功能將有助於在滾動證書更新到期之前進行滾動更新。 如果您的組織正在運行 NPS 擴展的早期版本,則應升級到版本 1.0.1.32 或更高版本。

`AzureMfaNpsExtnConfigSetup.ps1`腳本創建的證書的有效期為2年。 IT 組織應監視證書的過期情況。 NPS 擴展證書放置在"個人"下的本地計算機證書存儲中,並頒發給提供給腳本的租戶 ID。

當證書接近到期日期時,應創建新證書以替換它。  此過程是通過`AzureMfaNpsExtnConfigSetup.ps1`再次運行 並在提示時保留相同的租戶 ID 來完成的。 此過程應在環境中的每個 NPS 伺服器上重複。

## <a name="configure-your-nps-extension"></a>設定 NPS 擴充功能

本節包含成功部署 NPS 擴充功能的設計考量和建議。

### <a name="configuration-limitations"></a>設定限制

- Azure MFA 的 NPS 延伸模組並未包含可將使用者與設定從 MFA Server 移轉至雲端的工具。 有基於此，建議將此延伸模組用於新的部署，而非用於現有部署。 如果您在現有部署上使用此延伸模組，您的使用者必須再次執行證明，以便在雲端中填入其 MFA 詳細資料。  
- NPS 延伸使用本地活動目錄中的 UPN 來識別 Azure MFA 上的使用者以執行輔助 Auth。可以將擴充配置為使用其他識別碼,如備用登錄 ID 或自訂活動目錄欄位(UPN) 以外的欄位。 如需詳細資訊，請參閱 [NPS 擴充功能的 Multi-Factor Authentication 所適用的進階設定選項](howto-mfa-nps-extension-advanced.md)一文。
- 並非所有的加密通訊協定都支援所有的驗證方法。
   - **PAP** 支援通話、單向簡訊、行動裝置應用程式通知和行動裝置應用程式驗證碼
   - **CHAPV2** 和 **EAP** 支援通話和行動裝置應用程式通知

### <a name="control-radius-clients-that-require-mfa"></a>控制需要 MFA 的 RADIUS 用戶端

為使用 NPS 擴充功能的 RADIUS 用戶端啟用 MFA 後，便需要此用戶端的所有驗證才能執行 MFA。 如果您想要為一些 RADIUS 用戶端啟用 MFA，但其他的用戶端則不啟用，您可以設定兩部 NPS 伺服器，並只在其中一部上安裝擴充功能。 將您想要讓其必須使用 MFA 來傳送要求的 RADIUS 用戶端設定到設定了擴充功能的 NPS 伺服器，並將其他 RADIUS 用戶端設定到未設定擴充功能的 NPS 伺服器。

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>針對未註冊 MFA 的使用者做準備

如果您有未註冊 MFA 的使用者，您可以決定在其嘗試驗證時會有什麼結果。 使用登錄路徑 HKLM\Software\Microsoft\AzureMFA** 中的登錄設定 *REQUIRE_USER_MATCH* 來控制功能的行為。 此設定具有單一組態選項︰

| Key | 值 | 預設 |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | TRUE/FALSE | 未設定 (相當於 TRUE) |

此設定的目的是要決定當使用者未註冊 MFA 時的行為。 當此索引鍵不存在、未設定或是設為 TRUE 時，若使用者未註冊，則擴充功能將無法通過 MFA 挑戰。 當此索引鍵設為 FALSE 時，若使用者未註冊，將會繼續驗證但不執行 MFA。 如果使用者在 MFA 中註冊，則他們必須向 MFA 驗證，即使 REQUIRE_USER_MATCH 設定為 FALSE 亦然。

在使用者要上架但尚未全部註冊 Azure MFA 時，您可以選擇建立此金鑰，並將它設為 FALSE。 但是，設定金鑰可讓未註冊 MFA 的使用者進行登入，因此您應該先移除此金鑰，然後再移至生產環境。

## <a name="troubleshooting"></a>疑難排解

### <a name="nps-extension-health-check-script"></a>NPS 延伸執行狀況檢查文稿

在對 NPS 擴展進行故障排除時,可以使用以下腳本執行基本運行狀況檢查步驟。

[MFA_NPS_Troubleshooter.ps1](https://docs.microsoft.com/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/)

---

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>如何確認已如預期安裝用戶端憑證？

在憑證存放區中尋找安裝程式所建立的自我簽署憑證，並確認私密金鑰已將權限授與給使用者 **NETWORK SERVICE**。 憑證的主體名稱為 **CN \<tenantid\>, OU = Microsoft NPS Extension**

由*AzureMfaNpsExttttttconfigSetup.ps1*腳本生成的自簽名證書也有兩年的有效性存留期。 驗證是否安裝了證書時,還應檢查證書是否尚未過期。

---

### <a name="how-can-i-verify-that-my-client-cert-is-associated-to-my-tenant-in-azure-active-directory"></a>如何確認用戶端憑證是否已和 Azure Active Directory 中的租用戶相關聯？

開啟 PowerShell 命令提示字元並執行下列命令：

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1
```

這些命令會列印出將租用戶與 PowerShell 工作階段中之 NPS 擴充功能執行個體相關聯的所有憑證。 將用戶端憑證匯出為不含私密金鑰的 "Base-64 encoded X.509(.cer)" 檔案，然後與 PowerShell 中的清單比較，以尋找您的憑證。

下列命令會在您的 "C:" 磁碟機上建立名為 "npscertificate" 且格式為 .cer 的檔案。

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 | select -ExpandProperty "value" | out-file c:\npscertficicate.cer
```

運行此命令後,轉到 C 驅動器,找到該檔並按兩檔。 移至 [詳細資料] 並向下捲動至 [指紋]，將安裝在伺服器上的憑證指紋與此指紋進行比較。 這兩個憑證指紋應該相符。

如果命令傳回多個憑證，則可以使用採人類看得懂之格式的 Valid-From 和 Valid-Until 時間戳記來篩選出明顯不符者。

---

### <a name="why-cannot-i-sign-in"></a>為什麼我不能登錄?

檢查您的密碼尚未到期。 NPS 延伸模組不支援在登入工作流程期間變更密碼。 請與組織的 IT 員工聯繫以獲得進一步説明。

---

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>為何要求會失敗並出現 ADAL 權杖錯誤？

此錯誤可能來自多種原因之一。 請使用下列步驟來協助疑難排解︰

1. 重新啟動 NPS 伺服器。
2. 確認已如預期安裝用戶端憑證。
3. 確認憑證已與 Azure AD 上的租用戶相關聯。
4. 確認可以從執行延伸模組的伺服器存取 `https://login.microsoftonline.com/`。

---

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>驗證為何失敗，並且 HTTP 記錄中有指出找不到使用者的錯誤？

確認 AD Connect 正在執行，且使用者已存在於 Windows Active Directory 與 Azure Active Directory。

---

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>為何我會在記錄中看到 HTTP 連線錯誤，且我的所有驗證都失敗？

確認可以從執行 NPS 延伸模組的伺服器存取 https://adnotifications.windowsazure.com。

---

### <a name="why-is-authentication-not-working-despite-a-valid-certificate-being-present"></a>為什麼身份驗證不起作用,儘管存在有效的證書?

如果以前的計算機證書已過期,並且已生成新證書,則應刪除任何過期的證書。 證書過期可能會導致 NPS 擴展啟動出現問題。

要檢查您是否具有有效的證書,請使用 MMC 檢查本地電腦帳戶的證書存儲,並確保證書尚未超過其到期日期。 要生成新有效的證書,應重新運行「[執行 PowerShell 文稿](#run-the-powershell-script)」 一節下的步驟

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>管理的 TLS/SSL 通訊協定和加密套件

除非您的組織需要較舊的和較弱的加密套件，否則建議您加以停用或移除。 如需如何完成這項工作的相關資訊，請參閱[管理 AD FS 的 SSL/TLS 通訊協定和加密套件](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs)一文

### <a name="additional-troubleshooting"></a>其他故障排除

其他故障排除指南和可能的解決方案可以在「[解決來自 Azure 多重身份驗證的 NPS 擴展名中的錯誤訊息](howto-mfa-nps-extension-errors.md)」一文中找到。

## <a name="next-steps"></a>後續步驟

- [Windows 伺服器網路原則伺服器的概述和設定](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)

- 在 [Multi-Factor Authentication 之 NPS 延伸模組的進階設定選項](howto-mfa-nps-extension-advanced.md)中，設定登入的替代識別碼，或為不應該執行雙步驟驗證之 IP 設定的例外狀況清單

- 了解如何使用 NPS 延伸模組來整合[遠端桌面閘道](howto-mfa-nps-extension-rdg.md)和 [VPN 伺服器](howto-mfa-nps-extension-vpn.md)

- [解決 Azure Multi-Factor Authentication NPS 擴充功能的錯誤訊息](howto-mfa-nps-extension-errors.md)
