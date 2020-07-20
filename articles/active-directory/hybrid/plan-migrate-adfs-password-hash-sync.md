---
title: Azure AD Connect： Azure AD 的從同盟遷移至 PHS |Microsoft Docs
description: 本文有如何將混合式身分識別環境從同盟移至密碼雜湊同步處理的相關資訊。
services: active-directory
author: billmath
manager: daveba
ms.reviewer: martincoetzer
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/29/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fe9fe10b66aa6eb5fcdaafbf8e0132918e9645c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85356674"
---
# <a name="migrate-from-federation-to-password-hash-synchronization-for-azure-active-directory"></a>從同盟移轉至 Azure Active Directory 的密碼雜湊同步處理

本文說明如何將組織的網域從 Active Directory 同盟服務 (AD FS) 移至密碼雜湊同步處理。

> [!NOTE]
> 變更您的驗證方法需要規劃及測試，而且可能造成停機。 [分段推出](how-to-connect-staged-rollout.md)提供了一種替代方式，可以使用密碼雜湊同步處理，從同盟進行測試並逐漸遷移至雲端驗證。
>
> 如果您打算使用分段推出，請記得在完成剪下後關閉分段推出功能。  如需詳細資訊，請參閱[使用分段推出遷移至雲端驗證](how-to-connect-staged-rollout.md)


## <a name="prerequisites-for-migrating-to-password-hash-synchronization"></a>用於遷移至密碼雜湊同步處理的必要條件

若要從使用 AD FS 遷移至使用密碼雜湊同步處理，您必須滿足下列必要條件。


### <a name="update-azure-ad-connect"></a>更新 Azure AD Connect

若要成功執行移轉到密碼雜湊同步處理的步驟，至少應有 [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0。 此版本包含執行登入轉換方式的重大變更，而且可將從同盟移轉到雲端驗證的整體時間從數小時縮減至數分鐘。


> [!IMPORTANT]
> 過時的文件、工具和部落格中可能會提到，在將網域從同盟身分識別轉換為受控識別時，必須進行使用者轉換。 現已不再需要*轉換使用者*。 Microsoft 正著手更新文件和工具，以反映這項變更。

若要更新 Azure AD Connect，請完成 [Azure AD Connect：升級至最新版本](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version)中的步驟。

### <a name="password-hash-synchronization-required-permissions"></a>密碼雜湊同步處理所需的權限

您可以使用快速設定或自訂安裝來設定 Azure AD Connect。 如果您使用自訂安裝選項，可能就不會有密碼雜湊同步處理的[必要許可權](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-accounts-permissions)。

Azure AD Connect Active Directory Domain Services (AD DS) 服務帳戶需要有下列權限才能同步處理密碼雜湊：

* 複寫目錄變更
* 複寫目錄變更 (全部)

現在是時候驗證這些權限是否已準備好供樹系中的所有網域使用。

### <a name="plan-the-migration-method"></a>規劃移轉方法

您可以從兩種方法中做選擇，以從同盟身分識別管理遷移至密碼雜湊同步處理和無縫單一登入 (SSO)。 所應使用的方法取決於您 AD FS 執行個體原本的設定方式。

* **Azure AD Connect**。 如果您原本使用 Azure AD Connect 來設定 AD FS，則「必須」** 使用 Azure AD Connect 精靈來變更密碼雜湊同步處理。

   ‎當您變更使用者登入方法時，Azure AD Connect 會自動執行 **Set-MsolDomainAuthentication** Cmdlet。 Azure AD Connect 會自動將您 Azure AD 租用戶中所有已驗證的同盟網域解除同盟。

   > [!NOTE]
   > 目前，如果您原本使用 Azure AD Connect 來設定 AD FS，則在將使用者登入變更為密碼雜湊同步處理時，系統會將租用戶中所有網域解除同盟的情形就無法避免。 ‎
* **Azure AD Connect 搭配 PowerShell**。 只有原本並非使用 Azure AD Connect 來設定 AD FS 時，才可使用此方法。 使用此選項時，您仍須透過 Azure AD Connect 精靈變更使用者登入方法。 此選項最主要的差異在於，精靈不會自動執行 **Set-MsolDomainAuthentication** Cmdlet。 使用此選項時，您可以完整控制要轉換哪些網域，及其轉換順序。

若要了解應使用何種方法，請完成後續幾節的步驟。

#### <a name="verify-current-user-sign-in-settings"></a>請確認目前的使用者登入設定

若要確認目前的使用者登入設定：

1. 使用全域管理員帳戶登入 [Azure AD 入口網站](https://aad.portal.azure.com/)。
2. 在 [使用者登入]**** 區段中，確認下列設定：
   * [同盟]**** 設為 [啟用]****。
   * [無縫單一登入]**** 設為 [停用]****。
   * [傳遞驗證]**** 設為 [停用]****。

   ![此螢幕擷取畫面顯示 Azure AD Connect [使用者登入] 區段中的設定](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image1.png)

#### <a name="verify-the-azure-ad-connect-configuration"></a>確認 Azure AD Connect 設定

1. 在 Azure AD Connect 伺服器上，開啟 Azure AD Connect。 選取 [設定] 。
2. 在 [其他工作]**** 頁面上選取 [檢視目前的設定]****，然後選取 [下一步]****。<br />

   ![螢幕擷取畫面：[其他工作] 頁面上已選取 [檢視目前的設定] 選項](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image2.png)<br />
3. 在 [檢閱解決方案]**** 頁面上，請注意 [密碼雜湊同步處理]**** 狀態。<br /> 

   * 如果 [密碼雜湊同步處理]**** 設定為 [停用]****，請完成本文中的步驟將其啟用。
   * 如果 [密碼雜湊同步處理]**** 設定為 [啟用]****，則可以略過本文中的**步驟 1：啟用密碼雜湊同步處理**一節。
4. 在 [檢閱解決方案]**** 頁面上，捲動至 [Active Directory 同盟服務 (AD FS)]****。<br />

   * ‎如果本區段中出現 AD FS 設定，就可以放心地假設 AD FS 原本是使用 Azure AD Connect 進行設定的。 您可以使用 Azure AD Connect 的 [變更使用者登入]**** 選項，將網域從同盟身分識別轉換為受控識別。 **選項 A：使用 Azure AD Connect 從同盟轉換至密碼雜湊同步處理**一節會詳述此程序。
   * 如果目前的設定未列出 AD FS，則必須手動使用 PowerShell 將網域從同盟身分識別轉換為受控識別。 如需此程序的詳細資訊，請參閱**選項 B：使用 Azure AD Connect 和 PowerShell 從同盟轉換至密碼雜湊同步處理**一節。

### <a name="document-current-federation-settings"></a>文件目前的同盟設定

若要尋找您目前的同盟設定，請執行 **Get-MsolDomainFederationSettings** Cmdlet：

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

範例：

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```

請確認可能已為您的同盟設計和部署文件自訂的任何設定。 具體來說，請尋找 **PreferredAuthenticationProtocol**、**SupportsMfa** 和 **PromptLoginBehavior** 中的自訂。

如需詳細資訊，請參閱下列文章：

* [AD FS prompt=login 參數支援](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)
* [Set-MsolDomainAuthentication](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainauthentication?view=azureadps-1.0)

> [!NOTE]
> 如果 **SupportsMfa** 設定為 **True**，表示您是使用內部部署多重要素驗證解決方案，將第二個要素挑戰納入使用者驗證流程中。 將此網域從同盟轉換為受控驗證之後，此安裝程式不再適用于 Azure AD 驗證案例。 停用同盟之後，您可以將與內部部署同盟的關係進行伺服器，這包括內部部署 MFA 介面卡。 
>
> 請改用 Azure Multi-factor Authentication 雲端式服務來執行相同的功能。 在繼續之前，請仔細評估您的多重要素驗證需求。 在轉換網域之前，請先確定您已了解 Azure Multi-Factor Authentication 的使用方式、授權含意和使用者註冊程序。

#### <a name="back-up-federation-settings"></a>備份同盟設定

雖然在本文所說明的程序執行期間並不會變更 AD FS 伺服器陣列中的其他信賴憑證者，但建議您先確定 AD FS 伺服器陣列目前有有效的備份可供還原。 您可以使用免費的 Microsoft [AD FS 快速還原工具](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool)來建立目前的有效備份。 您可以使用此工具來備份 AD FS，以及還原現有的伺服器陣列或建立新的伺服器陣列。

如果您選擇不使用 AD FS 快速還原工具，則至少應匯出「Microsoft Office 365 身分識別平台」信賴憑證者信任，以及您已新增的任何相關聯自訂宣告規則。 您可以使用下列 PowerShell 範例，匯出信賴憑證者信任和相關聯的宣告規則：

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## <a name="deployment-considerations-and-using-ad-fs"></a>部署考量和 AD FS 的使用方式

本節說明部署考量和關於使用 AD FS 的詳細資料。

### <a name="current-ad-fs-use"></a>目前的 AD FS 使用方式

從同盟身分識別轉換成受控身分識別之前，應先仔細查看您目前對 Azure AD、Office 365 和其他應用程式 (信賴憑證者信任) 使用 AD FS 的方式。 具體而言，請考量下表所說明的案例：

| If | 結果為 |
|-|-|
| 您打算繼續將 AD FS 用於 Azure AD 和 Office 365 以外的其他應用程式。 | 在轉換網域之後，您將使用 AD FS 和 Azure AD。 請考量使用者體驗。 在某些情況下，使用者可能需要進行兩次驗證：一次是對 Azure AD (此時使用者會取得對其他應用程式的 SSO 存取權，例如 Office 365)，另一次則是針對任何仍繫結至 AD FS 作為信賴憑證者信任的應用程式。 |
| 您的 AD FS 執行個體經過高度自訂，且仰賴 onload.js 檔案中特定的自訂設定 (例如您已變更登入體驗，使得使用者的使用者名稱必須採用 **SamAccountName** 格式，而非使用者主體名稱 (UPN)，或是您的組織已將登入體驗高度品牌化)。 在 Azure AD 中無法複製 onload.js 檔案。 | 在繼續作業之前，您必須確認 Azure AD 可符合您目前的自訂需求。 如需詳細資訊和指引，請參閱 AD FS 商標和 AD FS 自訂的相關章節。|
| 您 AD FS 使用來封鎖舊版驗證用戶端。| 請考慮使用[條件式存取控制](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions)和[Exchange Online 用戶端存取規則](https://aka.ms/EXOCAR)的組合，取代封鎖舊版驗證用戶端的 AD FS 控制項。 |
| 當使用者向 AD FS 進行驗證時，您要求使用者依據內部部署多重要素驗證伺服器解決方案執行多重要素驗證。| 在受控識別網域中，您無法透過內部部署多重要素驗證解決方案將多重要素驗證挑戰插入驗證流程中。 不過，您可以在轉換網域後使用 Azure Multi-factor Authentication 服務進行多重要素驗證。<br /><br /> 如果您的使用者目前未使用 Azure Multi-factor Authentication，則需執行一次性的使用者註冊步驟。 您必須準備好計劃性的註冊，並將其傳達給使用者。 |
| 您目前使用 AD FS 中的存取控制原則 (AuthZ 規則) 來控管對 Office 365 的存取。| 請考慮將原則取代為對等的 Azure AD[條件式存取原則](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)和[Exchange Online 用戶端存取規則](https://aka.ms/EXOCAR)。|

### <a name="common-ad-fs-customizations"></a>常見的 AD FS 自訂

本節說明常見的 AD FS 自訂。

#### <a name="insidecorporatenetwork-claim"></a>InsideCorporateNetwork 宣告

如果進行驗證的使用者位於公司網路內部，AD FS 就會發出 **InsideCorporateNetwork** 宣告。 此宣告後續可傳至 Azure AD。 此宣告可根據使用者的網路位置用來略過多重要素驗證。 若要了解如何判斷 AD FS 中目前是否已啟用這項功能，請參閱[同盟使用者的可信任 IP](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud)。

在您的網域轉換成密碼雜湊同步處理後，即無法使用 **InsideCorporateNetwork** 宣告。 您可以使用 [Azure AD 中的具名位置](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations)來取代這項功能。

設定命名位置之後，您必須更新所有已設定為包含或排除網路**所有信任位置**或**MFA 信任的 Ip**值的條件式存取原則，以反映新的命名位置。

如需條件式存取中**位置**條件的詳細資訊，請參閱[Active Directory 條件式存取位置](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations)。

#### <a name="hybrid-azure-ad-joined-devices"></a>已加入混合式 Azure AD 的裝置

當您將裝置加入 Azure AD 時，您可以建立條件式存取規則，強制裝置符合您的安全性和合規性存取標準。 此外，使用者也可使用組織的公司或學校帳戶 (而非個人帳戶) 來登入裝置。 使用已加入混合式 Azure AD 的裝置時，您可以將已加入 Active Directory 網域的裝置加入 Azure AD。 您的同盟環境可能已設定成使用這項功能。

為確保在網域轉換為密碼雜湊同步處理之後，加入網域的任何裝置都能繼續使用混合式聯結，針對 Windows 10 用戶端，您必須使用 Azure AD Connect 裝置選項，將 Active Directory 電腦帳戶同步至 Azure AD。 

對於 Windows 8 和 Windows 7 電腦帳戶，混合式加入會使用無縫 SSO 在 Azure AD 中註冊電腦。 您不需要像 Windows 10 裝置那樣同步處理 Windows 8 和 Windows 7 電腦帳戶。 但您必須將更新的 workplacejoin.exe 檔案 (透過 .msi 檔案) 部署到 Windows 8 和 Windows 7 用戶端，使其可使用無縫 SSO 進行註冊。 [下載 .msi 檔案](https://www.microsoft.com/download/details.aspx?id=53554)。

如需詳細資訊，請參閱[設定已加入混合式 Azure AD 的裝置](https://docs.microsoft.com/azure/active-directory/device-management-hybrid-azuread-joined-devices-setup)。

#### <a name="branding"></a>Branding

如果您的組織[自訂了 ADFS 登入頁面](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization)以顯示更多有關組織的資訊，請考慮以類似的方式[自訂 Azure AD 登入頁面](https://docs.microsoft.com/azure/active-directory/customize-branding)。

雖然可進行類似的自訂，但轉換後的登入頁面在外觀上應該會有些許改變。 您可以在與使用者的通訊中提供關於預期改變的資訊。

> [!NOTE]
> 只有在購買 Azure Active Directory 的「進階」或「基本」授權後，或擁有 Office 365 授權時，才可使用組織商標。

## <a name="plan-deployment-and-support"></a>規劃部署和支援

完成本節說明的工作有助於您規劃部署和支援。

### <a name="plan-the-maintenance-window"></a>規劃維護時間範圍

雖然網域轉換程序很快就可完成，但在網域轉換完成後，Azure AD 可能會繼續將某些驗證要求傳送到您的 AD FS 伺服器，時間最長可達四小時。 在這四小時內，視服務端快取之不同，Azure AD 可能不會接受這些驗證。 使用者可能會收到錯誤。 使用者仍可順利對 AD FS 進行驗證，但 Azure AD 不會再接受使用者發出的權杖，因為該同盟信任此時已移除。

只有在服務端快取尚未清除的這段轉換後期間透過網頁瀏覽器存取服務的使用者，才會受到影響。 舊版用戶端 (Exchange ActiveSync、Outlook 2010/2013) 應該不受影響，因為 Exchange Online 會將其認證的快取保留一段時間。 快取可用來以無訊息方式重新驗證使用者。 使用者不需要返回 AD FS。 在此快取清除後，將會使用為這些用戶端儲存在裝置上的認證重新進行自我驗證。 使用者應該不會因為網域轉換程序而看到任何密碼提示。 

新式驗證用戶端 (Office 2016 和 Office 2013、iOS 和 Android 應用程式) 會使用有效的重新整理權杖取得新的存取權杖，以繼續存取資源，而不會返回 AD FS。 這些用戶端不會因為網域轉換程序而收到任何密碼提示。 用戶端無需額外設定即可繼續運作。

> [!IMPORTANT]
> 在您確認所有使用者都可使用雲端驗證成功通過驗證之前，請勿關閉您的 AD FS 環境或移除 Office 365 信賴憑證者信任。

### <a name="plan-for-rollback"></a>規劃復原作業

如果發生無法快速解決的重大問題，您可能會決定將解決方案復原至同盟。 請務必規劃無法如預期進行部署時的因應措施。 如果部署期間的網域或使用者轉換失敗，或是需要復原至同盟，您必須了解如何因應任何中斷情況，並降低對使用者的影響。

#### <a name="to-roll-back"></a>復原

若要進行復原規劃，請參閱同盟設計和部署文件，以取得特定部署的詳細資料。 此程序應該包含下列工作：

* 使用 **Convert-MSOLDomainToFederated** Cmdlet 將受控網域轉換成同盟網域。
* 必要時設定其他宣告規則。

### <a name="plan-communications"></a>規劃溝通

規劃部署和支援的要點之一，是務必主動告知使用者即將發生的變更。 使用者須事先得知他們可能面臨的情況，以及必要的應變措施。 

在部署密碼雜湊同步處理和無縫 SSO 之後，使用者在存取 Office 365 以及透過 Azure AD 進行驗證的其他資源時，其登入體驗將有所變更。 在網路外部的使用者只會看到 Azure AD 登入頁面。 這些使用者不會重新導向至對外的 Web 應用程式 Proxy 伺服器所顯示的表單式頁面。

請在您的通訊策略中納入下列要項︰

* 使用以下方式通知使用者即將推出和已發行的功能：
   * 電子郵件和其他內部通訊管道。
   * 海報等視覺物件。
   * 執行即時或其他通訊。
* 決定自訂通訊的人員、傳送通訊的人員，及其時間。

## <a name="implement-your-solution"></a>實作您的解決方案

您已規劃解決方案。 現在，您可加以實作。 實作涉及下列要素：

* 啟用密碼雜湊同步處理。
* 準備無縫 SSO。
* 將登入方法變更成密碼雜湊同步處理，並啟用無縫 SSO。

### <a name="step-1-enable-password-hash-synchronization"></a>步驟1：啟用密碼雜湊同步處理

實作此解決方案的第一個步驟，便是使用 Azure AD Connect 精靈來啟用密碼雜湊同步處理。 密碼雜湊同步處理是選用功能，在使用同盟的環境中即可加以啟用。 驗證流程不會受到影響。 在此情況下，Azure AD Connect 將會開始同步處理密碼雜湊，而不影響使用同盟來登入的使用者。

因此，建議您在變更網域的登入方法之前，先完成此步驟以做好準備工作。 然後，您便會擁有充裕的時間來確認密碼雜湊同步處理是否有正常運作。

若要啟用密碼雜湊同步處理：

1. 在 Azure AD Connect 伺服器上，開啟 Azure AD Connect 精靈，然後選取 [設定]****。
2. 選取 **[自訂同步處理選項**]，然後選取 **[下一步]**。
3. 在 [連線到 Azure AD]**** 頁面上，輸入全域管理員帳戶的使用者名稱和密碼。
4. 在 [連接您的目錄]**** 頁面上，選取 [下一步]****。
5. 在 [網域和 OU 篩選]**** 頁面上，選取 [下一步]****。
6. 在 [選用功能]**** 頁面上選取 [密碼同步處理]****，然後選取 [下一步]****。
 
   ![螢幕擷取畫面：[選用功能] 頁面上已選取密碼同步處理選項](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image6.png)<br />
7. 在其餘頁面上選取 [下一步]****。 在最後一個頁面上，選取 [設定]****。
8. Azure AD Connect 會在下次同步時開始同步處理密碼雜湊。

在啟用密碼雜湊同步處理後，Azure AD Connect 同步處理範圍中所有使用者的密碼雜湊將會重新進行雜湊處理並寫入至 Azure AD。 根據使用者數量，這項作業可能需要數分鐘或數小時。

若是用於規劃目的，請估計 1 小時內大約會處理 20,000 名使用者。

若要確認密碼雜湊同步處理是否有正常運作，請在 Azure AD Connect 精靈中完成**疑難排解**工作：

1. 在您的 Azure AD Connect 伺服器上，使用 [以系統管理員身分執行] 選項開啟新的 Windows PowerShell 工作階段。
2. 執行 `Set-ExecutionPolicy RemoteSigned` 或 `Set-ExecutionPolicy Unrestricted`。
3. 啟動 Azure AD Connect 精靈。
4. 移至 [其他工作]**** 頁面，選取 [疑難排解]****，然後選取 [下一步]****。
5. 在 [疑難排解]**** 頁面上，選取 [啟動]****，以在 PowerShell 中啟動疑難排解功能表。
6. 在主功能表上，選取 [針對密碼雜湊同步處理進行疑難排解]****。
7. 在子功能表上，選取 [密碼雜湊同步處理完全無法運作]****。

如需針對問題進行疑難排解，請參閱[針對使用 Azure AD Connect 同步執行的密碼雜湊同步處理進行疑難排解](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization)。

### <a name="step-2-prepare-for-seamless-sso"></a>步驟2：準備無縫 SSO

若要讓裝置使用無縫 SSO，必須使用 Active Directory 中的群組原則，將 Azure AD URL 新增至使用者的內部網路區域設定。

根據預設，網頁瀏覽器會自動從 URL 計算正確的區域 (網際網路或內部網路)。 例如， **HTTP： \/ \/ contoso/** 會對應到內部網路區域，而**HTTP： \/ \/ intranet.contoso.com**會對應到網際網路區域（因為 URL 包含句點）。 您必須明確地將 URL 新增至瀏覽器的內部網路區域，瀏覽器才會將 Kerberos 票證傳送給雲端端點 (例如 Azure AD URL)。

請完成向您的裝置[推行](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start)必要變更的步驟。

> [!IMPORTANT]
> 執行這項變更並不會修改使用者登入 Azure AD 的方式。 不過，請務必先將此組態套用到您所有的裝置，再繼續作業。 使用者在登入尚未取得此組態的裝置時，只需輸入使用者名稱和密碼即可登入 Azure AD。

### <a name="step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso"></a>步驟3：將登入方法變更為密碼雜湊同步處理，並啟用無縫 SSO

您有兩個選項可將登入方法變更為密碼雜湊同步處理，並啟用無縫 SSO。

#### <a name="option-a-switch-from-federation-to-password-hash-synchronization-by-using-azure-ad-connect"></a>選項 A：使用 Azure AD Connect 從同盟切換至密碼雜湊同步處理

如果您最初是以 Azure AD Connect 設定 AD FS 環境，請使用此方法。 如果您最初「並非」** 使用 Azure AD Connect 來設定 AD FS 環境，請勿使用此方法。

首先，請變更登入方法：

1. 在 Azure AD Connect 伺服器上，開啟 Azure AD Connect 精靈。
2. 選取 [**變更使用者登入**]，然後選取 **[下一步]**。 

   ![螢幕擷取畫面：[其他工作] 頁面上的 [變更使用者登入] 選項](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image7.png)<br />
3. 在 [連線到 Azure AD]**** 頁面上，輸入全域管理員帳戶的使用者名稱和密碼。
4. 在 [**使用者登入**] 頁面上，選取 [**密碼雜湊同步**處理] 按鈕。 請務必要選取 [不要轉換使用者帳戶]**** 核取方塊。 該選項已淘汰。 選取 [啟用單一登入]****，然後選取 [下一步]****。

   ![螢幕擷取畫面：[啟用單一登入] 頁面](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image8.png)<br />

   > [!NOTE]
   > 開始使用 Azure AD Connect 1.1.880.0 版，在預設情況下會選取 [無縫單一登入]**** 核取方塊。

   > [!IMPORTANT]
   > 您可以放心地忽略警告；這些警告是用於說明使用者轉換和完整的密碼雜湊同步處理均是從同盟轉換成雲端驗證時所需的步驟。 請注意，您不必再執行這些步驟。 如果仍會看到這些警告，請確定您執行的是否是最新版的 Azure AD Connect，以及是否在使用本指南的最新版本。 如需詳細資訊，請參閱[更新 Azure AD Connect](#update-azure-ad-connect) 一節。

5. 在 [啟用單一登入]**** 頁面上，輸入網域系統管理員帳戶的認證，然後選取 [下一步]****。

   ![螢幕擷取畫面：[啟用單一登入] 頁面](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image9.png)<br />

   > [!NOTE]
   > 必須要有網域系統管理員帳戶認證，才能啟用無縫 SSO。 此程序會完成下列需要更高權限的動作。 網域系統管理員帳戶認證不會儲存在 Azure AD Connect 或 Azure AD 中。 網域系統管理員帳戶認證只會用來開啟功能。 此程序順利完成後，即會捨棄認證。
   >
   > 1. 在您的內部部署 Active Directory 執行個體中建立名為 AZUREADSSOACC 的電腦帳戶 (代表 Azure AD)。
   > 2. 安全地與 Azure AD 共用電腦帳戶的 Kerberos 解密金鑰。
   > 3. 建立兩個 Kerberos 服務主體名稱 (SPN)，以代表 Azure AD 在登入期間使用的兩個 URL。

6. 在 [已可設定]**** 頁面上，確定已選取 [在設定完成時開始同步處理程序]**** 核取方塊。 然後，選取 [**設定**]。

      ![[已可設定] 頁面的螢幕擷取畫面](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image10.png)<br />

   > [!IMPORTANT]
   > 至此，所有已同盟的網域都會變更為受控驗證。 密碼雜湊同步處理是新的驗證方法。

7. 在 Azure AD 入口網站中，選取 [ **Azure Active Directory**  >  **Azure AD Connect**]。
8. 確認下列設定：
   * [同盟]**** 設為 [停用]****。
   * [無縫單一登入]**** 設為 [啟用]****。
   * [密碼同步處理]**** 設為 [啟用]****。<br /> 

   ![此螢幕擷取畫面顯示 [使用者登入] 區段中的設定](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image11.png)<br />

跳到[測試和下一個步驟](#testing-and-next-steps)。

   > [!IMPORTANT]
   > 略過**選項 B：使用 Azure AD Connect 和 PowerShell 從同盟轉換至密碼雜湊同步處理**一節。 如果您選擇了選項 A，將登入方法變更成密碼雜湊同步處理並啟用無縫 SSO，則不適用該節提供的步驟。

#### <a name="option-b-switch-from-federation-to-password-hash-synchronization-using-azure-ad-connect-and-powershell"></a>選項 B：使用 Azure AD Connect 和 PowerShell 從同盟轉換至密碼雜湊同步處理

如果您最初並不是以 Azure AD Connect 設定同盟網域，請使用此選項。 在此程序進行期間，您會啟用無縫 SSO，並從同盟網域轉換至受控網域。

1. 在 Azure AD Connect 伺服器上，開啟 Azure AD Connect 精靈。
2. 選取 [**變更使用者登入**]，然後選取 **[下一步]**。
3. 在 [連線到 Azure AD]**** 頁面上，輸入全域管理員帳戶的使用者名稱和密碼。
4. 在 [使用者登入]**** 頁面上，選取 [密碼雜湊同步處理]**** 按鈕。 選取 [啟用單一登入]****，然後選取 [下一步]****。

   啟用密碼雜湊同步處理之前： ![ 顯示 [使用者登入] 頁面上的 [不要設定] 選項的螢幕擷取畫面](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image12.png)<br />

   啟用密碼雜湊同步處理之後： ![ 螢幕擷取畫面，顯示 [使用者登入] 頁面上的新選項](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image13.png)<br />
   
   > [!NOTE]
   > 開始使用 Azure AD Connect 1.1.880.0 版，在預設情況下會選取 [無縫單一登入]**** 核取方塊。

5. 在 [啟用單一登入]**** 頁面上，輸入網域系統管理員帳戶的認證，然後選取 [下一步]****。

   > [!NOTE]
   > 必須要有網域系統管理員帳戶認證，才能啟用無縫 SSO。 此程序會完成下列需要更高權限的動作。 網域系統管理員帳戶認證不會儲存在 Azure AD Connect 或 Azure AD 中。 網域系統管理員帳戶認證只會用來開啟功能。 此程序順利完成後，即會捨棄認證。
   >
   > 1. 在您的內部部署 Active Directory 執行個體中建立名為 AZUREADSSOACC 的電腦帳戶 (代表 Azure AD)。
   > 2. 安全地與 Azure AD 共用電腦帳戶的 Kerberos 解密金鑰。
   > 3. 建立兩個 Kerberos 服務主體名稱 (SPN)，以代表 Azure AD 在登入期間使用的兩個 URL。

6. 在 [已可設定]**** 頁面上，確定已選取 [在設定完成時開始同步處理程序]**** 核取方塊。 然後，選取 [**設定**]。

   ![螢幕擷取畫面：顯示 [準備好設定] 頁面上的 [設定] 按鈕](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image15.png)<br />
   當您選取 [設定]**** 按鈕時，系統便會依照上一個步驟的指定來設定無縫 SSO。 其不會修改密碼雜湊同步處理設定，因為已於之前啟用。

   > [!IMPORTANT]
   > 這次不會變更使用者的登入方式。

7. 在 Azure AD 入口網站中，確認這些設定：
   * [同盟]**** 設為 [啟用]****。
   * [無縫單一登入]**** 設為 [啟用]****。
   * [密碼同步處理]**** 設為 [啟用]****。

   ![此螢幕擷取畫面顯示 [使用者登入] 區段中的設定](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image16.png)

#### <a name="convert-domains-from-federated-to-managed"></a>從同盟網域轉換成受控網域

此時，同盟仍是已啟用狀態，而且可供您的網域運作。 若要繼續部署，每個網域必須從同盟轉換為受控，以便強迫透過密碼雜湊同步處理執行使用者驗證。

> [!IMPORTANT]
> 您不需要同時轉換所有網域。 您可以選擇從生產租用戶上的測試網域開始，或是從使用者數量最少的網域開始。

使用 Azure AD PowerShell 模組完成轉換：

1. 在 PowerShell 中，使用全域管理員帳戶登入 Azure AD。
2. 若要轉換第一個網域，請執行下列命令：

   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
   ```

3. 在 Azure AD 入口網站中，選取 [ **Azure Active Directory**  >  **Azure AD Connect**]。
4. 確認已透過執行下列命令的方式，將網域轉換成受控：

   ``` PowerShell
   Get-MsolDomain -DomainName <domain name>
   ```

## <a name="testing-and-next-steps"></a>測試和下一個步驟

完成下列工作以確認密碼雜湊同步處理，並完成轉換程序。

### <a name="test-authentication-by-using-password-hash-synchronization"></a>使用密碼雜湊同步處理來測試驗證 

如果您的租用戶先前使用同盟身分識別，則使用者會從 Azure AD 登入頁面重新導向至您的 AD FS 環境。 現在，租用戶已設定為使用密碼雜湊同步處理，而不是同盟驗證，因此使用者不會重新導向至 AD FS。 此時使用者會直接在 Azure AD 登入頁面中登入。

若要測試密碼雜湊同步處理：

1. 以 InPrivate 模式開啟 Internet Explorer，使無縫 SSO 不會將您自動登入。
2. 移至 Office 365 登入頁面（ [https://portal.office.com](https://portal.office.com/) ）。
3. 輸入使用者 UPN，然後選取 [下一步]****。 請務必輸入從內部部署 Active Directory 執行個體同步，且先前已使用同盟驗證的混合式使用者 UPN。 您輸入使用者名稱和密碼的頁面顯示如下：

   ![此螢幕擷取畫面顯示您輸入使用者名稱的登入頁面](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image18.png)

   ![此螢幕擷取畫面顯示您輸入密碼的登入頁面](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image19.png)

4. 在您輸入密碼並選取 [登入]**** 後，會重新導向至 Office 365 入口網站。

   ![顯示 Office 365 入口網站的螢幕擷取畫面](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image20.png)


### <a name="test-seamless-sso"></a>測試無縫 SSO

1. 登入連線到公司網路的網域加入機器。
2. 在 Internet Explorer 或 Chrome 中，移至下列其中一個 URL (請將 "contoso" 取代為您的網域)：

   * https:\/\/myapps.microsoft.com/contoso.com
   * https:\/\/myapps.microsoft.com/contoso.onmicrosoft.com

   使用者會隨即重新導向至 Azure AD 登入頁面，且頁面上會顯示「正在嘗試將您登入」訊息。 系統不會提示使用者輸入使用者名稱和密碼。<br />

   ![顯示 Azure AD 登入頁面和訊息的螢幕擷取畫面](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image21.png)<br />
3. 使用者會重新導向，並成功登入存取面板：

   > [!NOTE]
   > 無縫 SSO 適用於支援網域提示的 Office 365 服務 (例如 myapps.microsoft.com/contoso.com)。 Office 365 入口網站 (portal.office.com) 目前不支援網域提示。 使用者必須輸入 UPN。 輸入 UPN 之後，無縫 SSO 會代表使用者擷取 Kerberos 票證。 使用者無須輸入密碼即可登入。

   > [!TIP]
   > 請考慮[在 Windows 10 上部署混合式 Azure AD Join](https://docs.microsoft.com/azure/active-directory/device-management-introduction)，以改善 SSO 體驗。

### <a name="remove-the-relying-party-trust"></a>移除信賴憑證者信任

在您確認所有使用者和用戶端都已透過 Azure AD 成功驗證之後，即可安全地移除 Office 365 信賴憑證者信任。

如果 AD FS 未用於其他用途 (即其他信賴憑證者信任)，則此時即可安全地解除委任 AD FS。

### <a name="rollback"></a>復原

如果您發現無法快速解決的重大問題，您可以選擇將解決方案復原至同盟。

請參閱同盟設計和部署文件，以取得特定部署的詳細資料。 此程序應會進行下列工作：

* 使用 **Convert-MSOLDomainToFederated** Cmdlet 將受控網域轉換成同盟驗證。
* 如有必要，請設定其他宣告規則。

### <a name="sync-userprincipalname-updates"></a>同步 userPrincipalName 更新

在過去，除非下列兩項條件都成立，否則即無法從內部部署環境使用同步服務更新 **UserPrincipalName** 屬性：

* 使用者位於受控 (非同盟) 識別網域中。
* 使用者尚未獲得指派的授權。

若要了解如何驗證或開啟這項功能，請參閱[同步 userPrincipalName 更新](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsyncservice-features)。

### <a name="troubleshooting"></a>疑難排解

您的支援團隊應該了解如何針對從同盟變更至受控期間或之後所引起的任何驗證問題，進行疑難排解。 請使用下列疑難排解說明文件協助您的支援團隊熟悉常見的疑難排解步驟，以及有助於隔離並解決問題的適當動作。

[針對 Azure Active Directory 密碼雜湊同步處理進行疑難排解](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization)

[針對 Azure Active Directory 無縫單一登入進行疑難排解](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sso)

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>變換無縫 SSO Kerberos 解密金鑰

請務必經常變換 AZUREADSSOACC 電腦帳戶 (代表 Azure AD) 的 Kerberos 解密金鑰。 AZUREADSSOACC 電腦帳戶會建立於您的內部部署 Active Directory 樹系中。 強烈建議至少每 30 天變換一次 Kerberos 解密金鑰，以便和 Active Directory 網域成員提交密碼變更的方式保持一致。 AZUREADSSOACC 電腦帳戶物件並未連結任何相關聯的裝置，因此您必須手動執行變換。

請在執行 Azure AD Connect 的內部部署伺服器上起始無縫 SSP Kerberos 解密金鑰的變換。

如需詳細資訊，請參閱[如何才能變換 AZUREADSSOACC 電腦帳戶的 Kerberos 解密金鑰？](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-faq)。

## <a name="next-steps"></a>後續步驟

* 了解 [Azure AD Connect 設計概念](plan-connect-design-concepts.md)。
* 選擇[正確的驗證](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn)。
* 了解[支援的拓撲](plan-connect-design-concepts.md)。
