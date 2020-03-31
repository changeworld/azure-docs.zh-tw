---
title: 部署 Azure 管理的工作站 - Azure 活動目錄
description: 瞭解如何部署安全、Azure 管理的工作站，以降低由於配置錯誤或危害而導致的違規風險。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d02b0299b6267fdd9d880d5bc0fe8c93d0edadc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672611"
---
# <a name="deploy-a-secure-azure-managed-workstation"></a>部署安全的 Azure 管理工作站

現在，您[已經瞭解了安全工作站](concept-azure-managed-workstation.md)，是時候開始部署了。 使用本指南，您可以使用定義的設定檔創建從一開始就更安全的工作站。

![部署安全工作站](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

在部署解決方案之前選擇設定檔。 您可以在部署中同時使用多個設定檔，並使用標記或組分配它們。

> [!NOTE]
> 根據您的要求應用任何設定檔。 您可以通過在 Microsoft Intune 中分配它來移動到另一個設定檔。

| 設定檔 | 低 | 增強 | 高 | 特製化 | 擔保 | 隔離 |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Azure AD 中的使用者 | 是 | 是 | 是 | 是 | 是 | 是 |
| Intune 託管 | 是 | 是 | 是 | 是 | 是 | 是 |
| 設備 - 已註冊的 Azure AD | 是 |  |  |  |  | |   |
| 設備 - Azure AD 已加入 |   | 是 | 是 | 是 | 是 | 是 |
| 應用了 Intune 安全基線 |   | 是 <br> （增強） | 是 <br> （高安全性） | 是 <br> （NCSC） | 是 <br> （安全） | NA |
| 硬體符合安全 Windows 10 標準 |   | 是 | 是 | 是 | 是 | 是 |
| 微軟後衛ATP啟用 |   | 是  | 是 | 是 | 是 | 是 |
| 刪除管理許可權 |   |   | 是  | 是 | 是 | 是 |
| 使用微軟自動駕駛儀進行部署 |   |   | 是  | 是 | 是 | 是 |
| 僅由 Intune 安裝的應用 |   |   |   | 是 | 是 |是 |
| 僅限於已批准清單的 URL |   |   |   | 是 | 是 |是 |
| 互聯網被阻止（入站/出站） |   |   |   |  |  |是 |

> [!NOTE]
> 在安全**工作站中，** 將分配帶有設定檔和策略的設備。 使用者不會直接應用策略，從而允許設備共用（共用設備）生效。 如果在部署中未共用安全工作站，或者需要單個使用者策略，則可以將使用者策略設定檔的分配分配給使用者和設備。 

## <a name="license-requirements"></a>授權需求

本指南仲介紹的概念假定您擁有 Microsoft 365 企業版 E5 或等效 SKU。 本指南中的一些建議可以使用較低的 SKU 實現。 有關詳細資訊，請參閱[Microsoft 365 企業許可](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise)。

要自動進行許可證預配，請考慮為使用者[提供基於組的許可](../users-groups-roles/licensing-groups-assign.md)。

## <a name="azure-active-directory-configuration"></a>Azure 活動目錄配置

Azure 活動目錄 （Azure AD） 管理管理員工作站的使用者、組和設備。 使用[管理員帳戶](../users-groups-roles/directory-assign-admin-roles.md)啟用標識服務和功能。

創建安全的工作站管理員帳戶時，會將該帳戶公開給當前工作站。 請確保使用已知安全設備執行此初始配置和所有全域配置。 為了減少首次體驗的攻擊暴露，請考慮遵循[指南以防止惡意軟體感染](/windows/security/threat-protection/intelligence/prevent-malware-infection)。

需要多重要素驗證，至少對於管理員而言。 有關實施指南[，請參閱部署基於雲的 MFA。](../authentication/howto-mfa-getstarted.md)

### <a name="azure-ad-users-and-groups"></a>Azure AD 使用者和組

1. 從 Azure 門戶，流覽到**Azure 活動目錄** > **使用者** > **新使用者**。
1. 按照[創建使用者教程](/Intune/quickstart-create-user)中的步驟創建裝置管理員。
1. 輸入：

   * **名稱**- 安全工作站管理員
   * **使用者名** - `secure-ws-admin@identityitpro.com`
   * **目錄角色** - **受限管理員**並選擇**Intune 管理員**角色。

1. 選取 [建立]****。

接下來，您將創建兩個組：工作站使用者和工作站設備。

從 Azure 門戶，流覽到**Azure 活動目錄** > **組** > **"新組**"。

1. 對於工作站使用者組，您可能希望配置[基於組的許可](../users-groups-roles/licensing-groups-assign.md)，以自動向使用者預配許可證。
1. 對於工作站使用者組，輸入：

   * **組類型**- 安全性
   * **組名稱**- 安全工作站使用者
   * **成員資格類型**- 已分配

1. 添加安全工作站管理員使用者：`secure-ws-admin@identityitpro.com`
1. 您可以添加將管理安全工作站的任何其他使用者。
1. 選取 [建立]****。
1. 對於工作站設備組，輸入：

   * **組類型**- 安全性
   * **組名稱**- 安全工作站
   * **成員資格類型**- 已分配

1. 選取 [建立]****。

### <a name="azure-ad-device-configuration"></a>Azure AD 設備配置

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>指定誰可以將設備加入 Azure AD

在 Active Directory 中配置設備設置，以允許管理安全性群組將設備加入域。 要從 Azure 門戶配置此設置，請執行以下規定：

1. 轉到**Azure 活動目錄** > **設備** > **設備設置**。
1. 選擇"使用者"下的 **"選擇****"可以將設備加入 Azure AD**，然後選擇"安全工作站使用者"組。

#### <a name="removal-of-local-admin-rights"></a>刪除本地管理員許可權

此方法要求 VIP、DevOps 和安全級別的工作站的使用者在其電腦上沒有管理員許可權。 要從 Azure 門戶配置此設置，請執行以下規定：

1. 轉到**Azure 活動目錄** > **設備** > **設備設置**。
1. 在**Azure AD 聯接設備上的其他本地管理員下**選擇 **"無**"。

#### <a name="require-multi-factor-authentication-to-join-devices"></a>加入設備需要多重要素驗證

要進一步加強將設備連接到 Azure AD 的過程：

1. 轉到**Azure 活動目錄** > **設備** > **設備設置**。
1. 在 **"需要多因素身份驗證"** 下選擇 **"是**"以加入設備。
1. 選取 [儲存]****。

#### <a name="configure-mobile-device-management"></a>配置行動裝置管理

從 Azure 入口網站：

1. 流覽到**Azure 活動目錄** > **移動性 （MDM 和 MAM）** > **微軟 Intune**。
1. 將**MDM 使用者範圍**設置更改為 **"全部**"。
1. 選取 [儲存]****。

這些步驟允許您使用 Intune 管理任何設備。 有關詳細資訊，請參閱[Intune 快速啟動：為 Windows 10 設備設置自動註冊](/Intune/quickstart-setup-auto-enrollment)。 在後續步驟中創建 Intune 配置和合規性策略。

#### <a name="azure-ad-conditional-access"></a>Azure AD 條件訪問

Azure AD 條件訪問可説明將特權管理工作限制為符合的設備。 **安全工作站使用者**組的預定義成員在登錄到雲應用程式時需要執行多重要素驗證。 最佳做法是從策略中排除緊急存取帳戶。 有關詳細資訊，請參閱在[Azure AD 中管理緊急存取帳戶](../users-groups-roles/directory-emergency-access.md)。

## <a name="intune-configuration"></a>調諧配置

### <a name="configure-enrollment-status"></a>配置註冊狀態

請務必確保您的安全工作站是值得信賴的乾淨設備。 購買新設備時，您可以堅持它們出廠設置為[S 模式下的 Windows 10 Pro，](/Windows/deployment/Windows-10-pro-in-s-mode)這限制了供應鏈管理期間的漏洞暴露。 從供應商收到設備後，可以使用自動駕駛儀從 S 模式更改該設備。 以下指南提供了有關應用轉換過程的詳細資訊。

為了確保設備在使用前完全配置，Intune 提供了一種**在安裝所有應用和設定檔之前阻止設備使用**的方法。

從**Azure 門戶**：

1. 轉到**微軟 Intune** > **設備註冊** > **Windows** > **註冊狀態頁面** > **預設設置** > **Settings**。
1. **將"顯示應用設定檔安裝進度"** 設置為 **"是**"。
1. 設置**塊設備使用，直到所有應用和設定檔安裝**到**是**。

### <a name="create-an-autopilot-deployment-profile"></a>建立 Autopilot 部署設定檔

創建設備組後，必須創建部署設定檔以配置自動駕駛儀設備。

在 Azure 門戶中的 Intune 中：

1. 選擇**設備註冊** > **Windows 註冊** > **部署設定檔** > **創建設定檔**。
1. 輸入：

   * 名稱 -**安全工作站部署設定檔**。
   * 描述 -**部署安全工作站**。
   * 把 [將所有目標裝置轉換為 Autopilot]**** 設為 [是]****。 此設定可確保清單中的所有裝置都會向 AutoPilot 部署服務註冊。 等候 48 小時讓註冊處理完畢。

1. 選取 [下一步]****。

   * 對於**部署模式**，選擇**自部署（預覽）。** 具有此設定檔的設備與註冊該設備的使用者相關聯。 需有使用者認證，才能註冊裝置。 必須注意，在**自部署**模式下部署設備將允許您在共用模型中部署可擕式電腦。 在首次將設備分配給使用者之前，不會發生使用者分配。 因此，在完成使用者分配之前，不會啟用任何使用者策略（如 BitLocker）。 有關如何登錄到安全設備的詳細資訊，請參閱[選定的設定檔](/intune/device-profile-assign)。
   * "**以 Azure AD 身份加入"應**顯示**Azure AD 已加入**並灰顯。
   * 選擇您的語言（區域）、使用者帳戶類型**標準**。 

1. 選取 [下一步]****。

   * 如果已預配置作用域標記，請選擇作用域標記。

1. 選取 [下一步]****。
1. 選擇 > **Assign to**分配給 > **選定組****的分配。** 在 **"選擇要包括的組"** 中，請選擇 **"安全工作站**"。
1. 選取 [下一步]****。
1. 選取 [建立]**** 以建立設定檔。 現在可以指派 Autopilot 部署設定檔給裝置。

自動駕駛儀中的設備註冊根據裝置類型和角色提供不同的使用者體驗。 在我們的部署示例中，我們演示了一個模型，其中安全設備是批量部署的，可以共用，但當首次使用時，設備將分配給使用者。 有關詳細資訊，請參閱[Intune 自動駕駛儀設備註冊](/intune/device-enrollment)。

### <a name="configure-windows-update"></a>配置 Windows 更新

使 Windows 10 保持最新是您可以執行的最重要操作之一。 要將 Windows 保持在安全狀態，請部署更新環來管理將更新應用於工作站的速度。 

本指南建議您創建新的更新環並更改以下預設設置：

在 Azure 入口網站中：

1. 轉到**微軟 Intune** > **軟體更新** > **Windows 10 更新環**。
1. 輸入：

   * 名稱 - **Azure 管理的工作站更新**
   * 服務通道 - **Windows 內幕 - 快速**
   * 品質更新延遲（天） - **3**
   * 功能更新延遲期（天） - **3**
   * 自動更新行為 -**自動安裝和重新開機，無需最終使用者控制**
   * 阻止使用者暫停 Windows 更新 -**阻止**
   * 需要使用者批准才能在工作時間以外重新開機 -**必需**
   * 允許使用者重新開機（參與重新開機） -**必需**
   * 自動重新開機後將使用者轉換為參與式重新開機 （天） - **3**
   * 打盹接線式重啟提醒（天） - **3**
   * 設置待處理重新開機的截止時間（天） - **3**

1. 選取 [建立]****。
1. 在"**分配"** 選項卡上，添加 **"安全工作站"** 組。

有關 Windows 更新策略的詳細資訊，請參閱[策略 CSP - 更新](/windows/client-management/mdm/policy-csp-update)。

### <a name="windows-defender-atp-intune-integration"></a>視窗後衛ATP調諧集成

Windows Defender ATP 和微軟 Intune 協同工作，説明防止安全性漏洞。 它們還可以限制違規的影響。 ATP 功能提供即時威脅檢測，並可對端點設備進行廣泛的審核和日誌記錄。

要配置 Windows Defender ATP 和 Intune 的集成，請轉到 Azure 門戶。

1. 流覽到**微軟Intune** > **設備符合** > **Windows防禦者ATP。**
1. 在**配置 Windows 防禦者 ATP**下的步驟 1 中，選擇在 Windows**防禦器安全中心中選擇將 Windows 防禦者 ATP 連接到微軟 Intune。**
1. 在 [Windows Defender 資訊安全中心] 中：

   1. 選擇 **"設置** > **高級功能**"。
   1. 對於**微軟 Intune 連接**，請選擇 **"打開**"。
   1. 選取 [儲存喜好設定]****。

1. 建立連接後，返回到 Intune 並選擇頂部**的"刷新**"。
1. 將 [將 Windows 裝置 10.0.15063 版和更高版本連線至 Windows Defender ATP]**** 設定為 [開啟]****。
1. 選取 [儲存]****。

如需詳細資訊，請參閱 [Windows Defender 進階威脅防護](/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection)。

### <a name="finish-workstation-profile-hardening"></a>完成工作站輪廓硬化

要成功完成解決方案的強化，請下載並執行相應的腳本。 查找所需**設定檔級別的**下載連結：

| 設定檔 | 下載位置 | 檔案名稱 |
| --- | --- | --- |
| 低安全性 | N/A | N/A |
| 加強的安全性 | https://aka.ms/securedworkstationgit | 增強型工作站-Windows10-（1809）。ps1 |
| 高安全性 | https://aka.ms/securedworkstationgit | 高安全工作站-Windows10-（1809）。ps1 |
| 特製化 | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC - Windows10 （1803） 安全基線.ps1 |
| 專業化合規* | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-視窗10（1803）。ps1 |
| 擔保 | https://aka.ms/securedworkstationgit | 安全工作站-Windows10-（1809）-安全基線.ps1 |

\*專用合規性是強制執行 NCSC Windows10 安全基線中提供的專用配置的腳本。

腳本成功執行後，您可以對 Intune 中的設定檔和策略進行更新。 增強和安全設定檔的腳本為您創建策略和設定檔，但您必須將策略分配給**安全工作站**設備組。

* 在這裡，您可以找到由腳本創建的 Intune 設備設定檔 **：Azure 門戶** > **Microsoft Intune** > **設備設定檔** > **Profiles**。
* 在這裡，您可以找到由腳本創建的 Intune 設備合規性策略 **：Azure 門戶** > **Microsoft Intune** > **設備合規性** > **策略**。

要查看腳本所做的更改，可以匯出設定檔。 這樣，您可以確定[SECCON 文檔中](/windows/security/threat-protection/windows-security-configuration-framework/windows-security-configuration-framework)可能要求的其他強化。

從[設備配置 GiuHub](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) `DeviceConfiguration_Export.ps1`存儲庫運行 Intune 資料匯出腳本以匯出所有當前的 Intune 設定檔。

## <a name="additional-configurations-and-hardening-to-consider"></a>需要考慮的其他配置和強化

通過按照此處的指導，您部署了一個安全工作站。 但是，您還應考慮其他控制項。 例如：

* 限制對備用瀏覽器的訪問
* 允許出站 HTTP
* 阻止選擇網站
* 設置運行自訂 PowerShell 腳本的許可權

### <a name="set-rules-in-the-firewall-configuration-service-provider-csp"></a>在防火牆配置服務提供者 （CSP） 中設置規則

您可以根據需要對允許的和阻止的終結點的入站和出站規則的管理進行其他更改。 當您繼續強化安全工作站時，可以放鬆拒絕所有入站和出站流量的限制。 您可以添加允許的出站網站，以包括常見和受信任的網站。 有關詳細資訊，請參閱[防火牆佈建服務](/Windows/client-management/mdm/firewall-csp)。

限制性 URL 流量管理包括：

* 拒絕所有入站流量 - 在安全工作站設定檔腳本中設置。
* 拒絕除選定的 Azure 和 Microsoft 服務（包括 Azure 雲外殼）和允許 Azure 密碼重設之外的所有出站。

```
[HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings]
"ProxyEnable"=dword:00000001
"ProxyServer"="127.0.0.1:80"
"ProxyOverride"="*.azure.com;*.azure.net;*.microsoft.com;*.windowsupdate.com;*.microsoftonline.com;*.microsoftonline.cn;*.windows.net;*.windowsazure.com;*.windowsazure.cn;*.azure.cn;*.loganalytics.io;*.applicationinsights.io;*.vsassets.io;*.azure-automation.net;*.visualstudio.com,portal.office.com;*.aspnetcdn.com;*.sharepointonline.com;*.msecnd.net;*.msocdn.com;*.webtrends.com"
"AutoDetect"=dword:00000000
```

如果要為阻止規則提供更細細微性，可以參考維護[域阻止清單 （DBL）](https://www.spamhaus.org/dbl/)的 Spamhaus 專案：一種用作用於實現阻止網站的高級規則的高級資源。

### <a name="manage-local-applications"></a>管理本地應用程式

當本地應用程式（包括生產力應用程式）被刪除時，安全工作站將移動到真正強化的狀態。 在這裡，您將 Chrome 添加為預設瀏覽器，並使用 Intune 限制使用者修改瀏覽器及其外掛程式的能力。

#### <a name="deploy-applications-using-intune"></a>使用 Intune 部署應用程式

在某些情況下，安全工作站上需要使用 Google Chrome 瀏覽器等應用程式。 下面的示例提供了將 Chrome 安裝到**安全性群組"安全工作站"** 中的設備的說明。

1. 下載 Windows [64 位的](https://cloud.google.com/chrome-enterprise/browser/download/)離線安裝程式 Chrome 捆綁包。
1. 提取檔並記下`GoogleChromeStandaloneEnterprise64.msi`檔的位置。
1. 在**Azure 門戶**中流覽到**Microsoft Intune** > **用戶端** > **應用應用** > **添加**。
1. 在 **"應用類型**"下，選擇**業務線**。
1. 在**應用包檔**下，從`GoogleChromeStandaloneEnterprise64.msi`提取的位置選擇檔，然後選擇 **"確定**"。
1. 在**應用資訊**下，提供說明和發行者。 選取 [確定]****。
1. 選取 [加入]****。
1. 在"**分配"** 選項卡上，選擇 **"為"分配"類型**下的**已註冊設備**提供。
1. 在 **"包含的組**"下，添加**安全工作站**組。
1. 選取 [確定]****，然後選取 [儲存]****。

有關配置 Chrome 設置的詳細資訊，請參閱[使用 Microsoft Intune 管理 Chrome 瀏覽器](https://support.google.com/chrome/a/answer/9102677)。

#### <a name="configuring-the-company-portal-for-custom-apps"></a>為自訂應用配置公司門戶

在安全模式下，應用程式安裝僅限於 Intune 公司門戶。 但是，安裝門戶需要訪問 Microsoft 應用商店。 在安全解決方案中，您可以通過離線模式使公司門戶對所有設備可用。

[Intune 管理的公司門戶](/Intune/store-apps-company-portal-app)副本可讓您按需訪問其他工具，您可以將這些工具向下推送到安全工作站的使用者。

您可能需要安裝需要特殊準備的其他 Windows 32 位應用或其他應用。 在這種情況下[，Microsoft win32 內容準備工具](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool)可以提供即用`.intunewin`格式檔進行安裝。

### <a name="conditional-access-only-allowing-secured-workstation-ability-to-access-azure-portal"></a>條件訪問僅允許安全工作站訪問 Azure 門戶的能力

Azure AD 提供了管理和限制可以訪問 Azure 雲監管中心的人員和內容的能力。 啟用[條件訪問](../conditional-access/overview.md)將確保只有安全工作站才能管理或更改資源。 在部署此功能時，您必須考慮，如果[緊急訪問](../users-groups-roles/directory-emergency-access.md)功能可以或應該僅用於極端情況和通過策略管理的帳戶。

> [!NOTE]
> 您需要創建一個使用者組，並包括可以繞過條件訪問策略的緊急使用者。 例如，我們有一個安全小組，名為**緊急碎玻璃**

1. 流覽到**Azure 門戶** > **Microsoft Intune** > **條件訪問 - 策略** > **新策略**。
1. 為策略提供**名稱**。
1. 選擇**使用者和組** > **選擇使用者和組** 
1. 選擇 **"包括** > **目錄角色**>選擇>全域管理員、特權角色管理員、特權身份驗證管理員、安全管理員、合規性管理員、條件訪問管理員、應用程式管理員、雲應用程式管理員、Intune 服務管理員的角色
1. 選擇 **"排除**>選擇**使用者和組**>**選擇排除使用者**>選擇**緊急破玻璃**組。
1. 選擇 **"雲應用"或操作**>選擇**所有雲應用**
1. 選擇**條件**>選擇**設備平臺**>選擇配置**是**>**選擇設備平臺**選擇**視窗**
1. 選擇 **"存取控制**">選擇 **"授予訪問****是**">選擇 **"需要的設備"以標記為"符合"。** 
1. 選擇**啟用策略** >  ** **
 
此策略集將確保管理員必須使用符合的 Windows 設備（由 Intune 和 WDATP 設置）。 

組織還應考慮在其環境中阻止舊版身份驗證協定。 完成此任務的方法有多種，有關阻止舊版身份驗證協定的詳細資訊，請參閱一文：[使用條件訪問阻止對 Azure AD 的舊身份驗證](../conditional-access/block-legacy-authentication.md)。

### <a name="use-powershell-to-create-custom-settings"></a>使用 PowerShell 創建自訂設置

您還可以使用 PowerShell 擴展主機管理功能。 此示例腳本在主機上設置預設背景。 此功能也可通過 Azure 門戶和設定檔提供。 示例腳本僅用於說明 PowerShell 功能。

您可能需要在安全工作站上設置一些自訂控制項和設置。 此示例通過使用 Powershell 能夠輕鬆地將設備標識為隨時可以使用的安全工作站，從而更改工作站的背景。

來自 Microsoft 腳本中心的[SetDesktopBackground.ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/)腳本允許 Windows 在啟動時載入此[免費的通用背景映射](https://i.imgur.com/OAJ28zO.png)。

1. 將腳本下載到本地設備。
1. 更新客戶XXXX和背景圖像的下載位置。 在我們的示例中，我們將客戶XXXX替換為背景。
1. 流覽到**Azure 門戶** > **Microsoft Intune** > **設備配置** > **PowerShell 腳本** > **添加**。
1. 為腳本提供**名稱**並指定**腳本位置**。
1. 選擇 **"配置**"。
   1. **使用登錄的憑據將運行此腳本**設置為 **"是**"。
   1. 選取 [確定]****。
1. 選取 [建立]****。
1. 選擇**分配** > **選擇組**。
   1. 添加安全性群組 **"安全工作站**"。
   1. 選取 [儲存]****。

## <a name="enroll-and-validate-your-first-device"></a>註冊並驗證第一台設備

1. 要註冊設備，您需要以下資訊：
   * **序號**- 在設備主機殼上找到。
   * **Windows 產品識別碼**- 在"從 Windows 設置"功能表中找到 **"系統** > **有關**"。
   * 您可以運行[Get-WindowsAutoPilotInfo](https://aka.ms/Autopilotshell)來獲取包含設備註冊所需的所有資訊的 CSV 雜湊檔。
   
     運行`Get-WindowsAutoPilotInfo – outputfile device1.csv`以將資訊輸出為可導入到 Intune 的 CSV 檔。

     > [!NOTE]
     > 腳本需要較高的權限。 它以遠端簽名運行。 該`Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`命令允許腳本正確運行。

   * 您可以通過登錄到 Windows 10 版本 1809 或更高版本的設備來收集此資訊。 您的硬體轉銷商也可以提供此資訊。
1. 在**Azure 門戶**中，轉到**Microsoft Intune** > **設備註冊** > **Windows 註冊** > **設備 - 管理 Windows 自動駕駛儀設備**。
1. 選擇 **"導入**"並選擇 CSV 檔。
1. 將設備添加到**安全工作站**安全性群組。
1. 在要配置的 Windows 10 設備上，轉到**Windows 設置** > **更新&安全** > **恢復**。
   1. 選擇 **"****重置此電腦**"下開始。
   1. 按照提示，在配置了設定檔和合規性策略後重置和重新配置設備。

配置設備後，完成審閱並檢查配置。 在繼續部署之前，確認第一個設備配置正確。

## <a name="assign-devices"></a>分配設備

要分配設備和使用者，您需要將[所選設定檔](/intune/device-profile-assign)映射到安全性群組。 還必須將需要服務許可權的所有新使用者添加到安全性群組中。

## <a name="using-sentinel-and-windows-defender-atp-to-monitor-and-respond-to-security-incidents"></a>使用哨兵和 Windows 防禦者 ATP 監控和回應安全事件

通過啟用 [Sentinel] 並利用[威脅和漏洞管理](/windows/security/threat-protection/microsoft-defender-atp/next-gen-threat-and-vuln-mgt)可以監控安全工作站部署 該指南不會提供詳盡的威脅搜索，而是提供良好的常識工作，以監視和回應潛在的安全事件。

我們將使用**Azure 哨兵**： 

* 從 Intune、Azure 門戶和 Azure AD 收集資料，以便使用者和設備監視
* 説明調查使用者和設備配置可疑活動
* 並推動使用 WDATP 探索安全調查的能力

哨兵監視要求設置與資料來源（如 Azure AD）的連接器。

1. 在**Azure 門戶**中，轉到**Azure 哨兵（預覽）>** 選擇 **"添加"**
1. 在 **"選擇要添加到 Azure 哨兵的工作區****"中選擇創建新工作區**
1. 輸入：
   * **日誌分析工作區**- "安全工作站監控"
   * **訂閱**- 選擇活動訂閱
   * **資源組**- 選擇 [創建新] >安全工作站 RG >**確定**
   * **位置**- 選擇地理位置最適合您的部署的位置
   * **價格等級**- 選擇**每 GB （2018）**
1. 選擇 **"確定**"。

接下來，我們將將可用的安全工作站資料來源連接到監視。

1. 在**Azure 門戶**中，轉到**Azure 哨兵工作區**>選擇**安全工作站監視**工作區
1. 選擇**資料連線器**
1. 在查看先決條件後>選擇**Azure 活動目錄**>打開連接器頁。 繼續配置，然後同時為 Azure AD 登錄日誌和 Azure AD 稽核記錄選擇 **"連接**"。
1. 在查看先決條件後>選擇 **"打開**連接器"頁中的 Azure > 活動。 繼續配置 Azure 活動日誌>選擇訂閱>選擇**連接**

當 Sentinel 收集資料時，您將能夠通過選擇**Azure 門戶**來觀察活動，請轉到 Azure **Sentinel 概述** 

我們將使用**Windows 防禦者 ATP （WDATP）** 進行：

* 持續觀察和監控漏洞和錯誤配置
* 利用 WDATP 確定野外動態威脅的優先順序
* 驅動漏洞與端點檢測和回應 （EDR） 警報的相關性
* 在調查期間使用儀表板識別機器級漏洞
* 將修正推送到 Intune

配置[您的防禦者 ATP 儀表板](https://securitycenter.windows.com/machines)。 使用[威脅&漏洞管理儀表板概述](/windows/security/threat-protection/microsoft-defender-atp/tvm-dashboard-insights)中的指導。

## <a name="monitoring-application-activity-using-microsoft-monitoring-agent-mma"></a>使用 Microsoft 監視代理 （MMA） 監視應用程式活動
從專用工作站開始，應用程式儲物櫃可用於監視工作站上的應用程式活動。 要將監視集成到日誌分析工作區中，必須遵循 MMA 代理和配置。 

> [!NOTE]
> 專用工作站設定檔包含 AppLocker 監視策略。 需要部署策略來監視用戶端上的應用程式活動

使用 Intune 電源外殼腳本部署 MMA 代理

1. 將設置[腳本下載到本地設備](https://aka.ms/securedworkstationgit)。
1. 更新參數 **、$WorkSpaceID**和 **$WorkSpaceKey**
1. 流覽到**Azure 門戶** > **Microsoft Intune** > **設備配置** > **PowerShell 腳本** > **添加**。
1. 為腳本提供**名稱**並指定**腳本位置**。
1. 選擇 **"配置**"。
   1. **使用登錄的憑據將運行此腳本**設置為 **"是**"。
   1. 選取 [確定]****。
1. 選取 [建立]****。
1. 選擇**分配** > **選擇組**。
   1. 添加安全性群組 **"安全工作站**"。
   1. 選取 [儲存]****。

接下來，必須設置日誌分析以接收新日誌
1. 在**Azure 門戶**中，轉到**日誌分析工作區**>選擇 - "安全工作站監視"
1. 選擇**高級設置** > **資料** > **視窗事件日誌**
1. 在**從以下事件日誌收集事件** 
1. 輸入：
   * "微軟-Windows-AppLocker/EXE 和 DLL" >取消選擇**資訊**
   * "微軟-Windows-AppLocker/MSI 和腳本">取消選擇**資訊**
   * "微軟-Windows-AppLocker/打包應用部署">"取消選擇**資訊"**
   * "微軟-Windows-AppLocker/打包的應用程式執行">取消選擇**資訊**
1. 選擇 **"保存"**

應用程式日誌記錄將在您選擇的日誌分析工作區中可用。

## <a name="monitoring"></a>監視

* 瞭解如何使用[Azure 哨兵檢測威脅](/azure/sentinel/tutorial-detect-threats)
* [使用 Azure 哨兵調查事件](/azure/sentinel/tutorial-investigate-cases)
* [在 Azure Sentinel 中設定自動化威脅回應](/azure/sentinel/tutorial-respond-threats-playbook)
* 瞭解如何查看[您的曝光分數](/windows/security/threat-protection/microsoft-defender-atp/tvm-exposure-score)
* 查看[安全建議](/windows/security/threat-protection/microsoft-defender-atp/tvm-security-recommendation)
* 管理安全[補救](/windows/security/threat-protection/microsoft-defender-atp/tvm-remediation)
* 管理[端點檢測和回應](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)
* 使用[Intune 設定檔監視監視設定檔](/intune/device-profile-monitor)。

## <a name="next-steps"></a>後續步驟

* 瞭解有關微軟[Intune](/intune/index)的更多。
* 瞭解[Azure AD](../index.yml)。
* 與[微軟防禦者高級威脅保護合作](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)
* 發現[Azure 哨兵](/azure/sentinel/)
