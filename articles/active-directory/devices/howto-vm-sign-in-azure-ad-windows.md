---
title: 使用 Azure Active Directory （預覽）登入 Azure 中的 Windows 虛擬機器
description: Azure AD 登入執行 Windows 的 Azure VM
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8fe33f78b96dbfe780c94fbddfc5c8821148279
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78672597"
---
# <a name="sign-in-to-windows-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>使用 Azure Active Directory authentication （預覽）登入 Azure 中的 Windows 虛擬機器

組織現在可以針對執行**Windows Server 2019 Datacenter edition**或**windows 10 1809**及更新版本的 Azure 虛擬機器（vm），使用 Azure Active Directory （AD）驗證。 使用 Azure AD 向 Vm 進行驗證，可讓您集中控制及強制執行原則。 Azure 角色型存取控制（RBAC）和 Azure AD 條件式存取之類的工具，可讓您控制誰可以存取 VM。 本文說明如何建立和設定 Windows Server 2019 VM，以使用 Azure AD 驗證。

|     |
| --- |
| 適用于 Azure Windows Vm 的 Azure AD 登入是 Azure Active Directory 的公開預覽功能。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

使用 Azure AD 驗證來登入 Azure 中的 Windows Vm 有許多好處，包括：

- 利用您通常使用的相同同盟或受控 Azure AD 認證。
- 不再需要管理本機系統管理員帳戶。
- Azure RBAC 可讓您根據需求授與 Vm 適當的存取權，並在不再需要時將其移除。
- 在允許存取 VM 之前，Azure AD 條件式存取可以強制執行額外的需求，例如： 
   - Multi-Factor Authentication
   - 登入風險檢查
- 將屬於您 VDI 部署的 Azure Windows Vm 的 Azure AD 聯結自動化並加以調整。

> [!NOTE]
> 一旦您啟用此功能，您在 Azure 中的 Windows Vm 將會 Azure AD 聯結。 您不能將它加入其他網域，例如內部部署 AD 或 Azure AD DS。 如果您需要這樣做，您必須卸載擴充功能，將 VM 從您的 Azure AD 租使用者中斷連線。

## <a name="requirements"></a>需求

### <a name="supported-azure-regions-and-windows-distributions"></a>支援的 Azure 區域和 Windows 發行版本

這項功能的預覽期間目前支援下列 Windows 散發版本：

- Windows Server 2019 Datacenter
- Windows 10 1809 和更新版本

> [!IMPORTANT]
> 只有已加入 Azure AD 的 Windows 10 電腦或混合式 Azure AD 加入與 VM**相同**的目錄，才允許從遠端連線至已加入 Azure AD 的 vm。 

以下是目前在這項功能的預覽期間支援的 Azure 區域：

- 所有 Azure 全球區域

> [!IMPORTANT]
> 若要使用這項預覽功能，只需在支援的 Azure 區域中部署支援的 Windows 散發套件。 Azure Government 或主權雲端目前不支援此功能。

### <a name="network-requirements"></a>網路需求

若要在 Azure 中為您的 Windows Vm 啟用 Azure AD 驗證，您必須確定您的 Vm 網路設定允許透過 TCP 埠443對下列端點進行輸出存取：

- HTTPs：\//enterpriseregistration.windows.net
- https:\//login.microsoftonline.com
- HTTPs：\//device.login.microsoftonline.com
- HTTPs：\//pas.windows.net

## <a name="enabling-azure-ad-login-in-for-windows-vm-in-azure"></a>在 Azure 中啟用 Windows VM 的 Azure AD 登入

若要在 Azure 中使用適用于 Windows VM 的 Azure AD 登入，您必須先啟用 Windows VM 的 Azure AD 登入選項，然後您必須為已獲授權可登入 VM 的使用者設定 RBAC 角色指派。
有多種方式可讓您啟用 Windows VM 的 Azure AD 登入：

- 在建立 Windows VM 時使用 Azure 入口網站體驗
- 在建立 Windows VM**或現有 WINDOWS vm**時使用 Azure Cloud Shell 體驗

### <a name="using-azure-portal-create-vm-experience-to-enable-azure-ad-login"></a>使用 Azure 入口網站建立 VM 體驗來啟用 Azure AD 登入

您可以為 Windows Server 2019 Datacenter 或 Windows 10 1809 及更新版本的 VM 映射啟用 Azure AD 登入。 

若要在 Azure 中使用 Azure AD 登入來建立 Windows Server 2019 Datacenter VM： 

1. 使用有權建立 Vm 的帳戶登入[Azure 入口網站](https://portal.azure.com)，然後選取 [ **+ 建立資源**]。
1. 在 [搜尋 Marketplace] 搜尋列中輸入**Windows Server** 。
   1. 按一下 [ **Windows server** ]，然後從 [選取軟體方案] 下拉式清單中選擇 [ **Windows server 2019 Datacenter** ]。
   1. 按一下 [建立]。
1. 在 [管理] 索引標籤上，啟用 [Azure Active Directory] 區段下的 [**使用 AAD 認證登入（預覽）** ] 選項（從 [關閉] 到 [**開啟**]）。
1. 請確定 [身分識別] 區段下的 [**系統指派的受控識別**] 設定為 [**開啟**]。 當您使用 Azure AD 認證來啟用登入之後，應該會自動進行此動作。
1. 請流覽建立虛擬機器的其餘體驗。 在此預覽期間，您將必須為 VM 建立系統管理員使用者名稱和密碼。

![使用 Azure AD 認證登入建立 VM](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-login-with-azure-ad.png)

> [!NOTE]
> 若要使用 Azure AD 認證來登入 VM，您必須先設定 VM 的角色指派，如下一節中所述。

### <a name="using-the-azure-cloud-shell-experience-to-enable-azure-ad-login"></a>使用 Azure Cloud Shell 體驗來啟用 Azure AD 登入

Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 Cloud Shell 中已預先安裝和設定共用 Azure 工具，以便您搭配自己的帳戶使用。 只要選取 [複製] 按鈕即可複製程式碼，將它貼到 Cloud Shell 中，然後按 Enter 鍵加以執行。 以下有幾種開啟 Cloud Shell 的方式：

選取程式碼區塊右上角的 [試試看]。
在您的瀏覽器中開啟 Cloud Shell。
在[Azure 入口網站](https://portal.azure.com)右上角的功能表上，選取 [Cloud Shell] 按鈕。

如果您選擇在本機安裝和使用 CLI，本文會要求您執行 Azure CLI 版2.0.31 版或更新版本。 執行 az --version 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)一文。

1. 使用 [az group create](/cli/azure/group#az-group-create) 來建立資源群組。 
1. 在支援的區域中使用支援的散發套件，透過[az vm create](/cli/azure/vm#az-vm-create)建立 VM。 
1. 安裝 Azure AD 登入 VM 擴充功能。 

下列範例會將名為 myVM 的 VM （其使用 Win2019Datacenter）部署至 southcentralus 區域中名為 myResourceGroup 的資源群組。 在下列範例中，您可以視需要提供自己的資源群組和 VM 名稱。

```AzureCLI
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Win2019Datacenter \
    --assign-identity \
    --admin-username azureuser \
    --admin-password yourpassword
```

> [!NOTE]
> 您必須先在虛擬機器上啟用系統指派的受控識別，然後再安裝 Azure AD 登入 VM 擴充功能。

建立虛擬機器和支援資源需要幾分鐘的時間。

最後，安裝 Azure AD 登入 VM 擴充功能，以啟用 Windows VM 的 Azure AD 登入。 VM 擴充功能是小型的應用程式，可在「Azure 虛擬機器」上提供部署後設定及自動化工作。 使用[az vm extension](/cli/azure/vm/extension#az-vm-extension-set) set，在 myResourceGroup 資源群組中名為 MYVM 的 vm 上安裝 AADLoginForWindows 擴充功能：

> [!NOTE]
> 您可以在現有的 Windows Server 2019 或 Windows 10 1809 和更新版本的 VM 上安裝 AADLoginForWindows 擴充功能，以啟用 Azure AD 驗證。 AZ CLI 的範例如下所示。

```AzureCLI
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory \
    --name AADLoginForWindows \
    --resource-group myResourceGroup \
    --vm-name myVM
```

在 VM 上安裝擴充功能之後，就會顯示 `Succeeded` 的 `provisioningState`。

## <a name="configure-role-assignments-for-the-vm"></a>設定 VM 的角色指派

既然您已建立 VM，您必須設定 Azure RBAC 原則來決定誰可以登入 VM。 有兩個 RBAC 角色可用來授權 VM 登入：

- **虛擬機器系統管理員登**入：已指派此角色的使用者可以使用系統管理員許可權登入 Azure 虛擬機器。
- **虛擬機器使用者登入**：被指派此角色的使用者能夠以一般使用者權限登入 Azure 虛擬機器。

> [!NOTE]
> 若要允許使用者透過 RDP 登入 VM，您必須指派「虛擬機器系統管理員登入」或「虛擬機器使用者登入」角色。 具有指派給 VM 之「擁有者」或「參與者」角色的 Azure 使用者，不會自動擁有透過 RDP 登入 VM 的許可權。 這是為了在控制虛擬機器的一組人員與可以存取虛擬機器的人員之間，提供了已審核的分隔。
有多種方式可讓您設定 VM 的角色指派：

- 使用 Azure AD 入口網站體驗
- 使用 Azure Cloud Shell 體驗

### <a name="using-azure-ad-portal-experience"></a>使用 Azure AD 入口網站體驗

若要為您 Azure AD 啟用的 Windows Server 2019 Datacenter Vm 設定角色指派：

1. 流覽至特定虛擬機器的 [總覽] 頁面
1. 從功能表選項中選取 [**存取控制（IAM）** ]
1. 選取 [**新增**]、[**新增角色指派**] 以開啟 [新增角色指派] 窗格。
1. 在 [**角色**] 下拉式清單中，選取角色，例如 **[虛擬機器系統管理員登**入] 或 [**虛擬機器使用者登**入]。
1. 在 [**選取**] 欄位中，選取使用者、群組、服務主體或受控識別。 如果在清單中未看到安全性主體，您可以在 [選取] 方塊中輸入，以在目錄中搜尋顯示名稱、電子郵件地址和物件識別碼。
1. 選取 [**儲存**] 以指派角色。

在幾分鐘之後，即會在所選範圍中指派安全性主體的角色。

![將角色指派給將存取 VM 的使用者](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-access-control-assign-role.png)

### <a name="using-the-azure-cloud-shell-experience"></a>使用 Azure Cloud Shell 體驗

下列範例會使用[az 角色指派 create](/cli/azure/role/assignment#az-role-assignment-create) ，將虛擬機器系統管理員登入角色指派給您目前 Azure 使用者的 VM。 您使用中 Azure 帳戶的使用者名稱是[透過 az account show](/cli/azure/account#az-account-show)取得，而範圍則設定為上一個步驟中使用[az vm show](/cli/azure/vm#az-vm-show)所建立的 VM。 範圍也可指派於資源群組或訂用帳戶層級上，並套用一般 RBAC 繼承權限。 如需詳細資訊，請參閱[角色型存取控制](../../virtual-machines/linux/login-using-aad.md)。

```   zureCLI
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> 如果您的 AAD 網域和登入使用者名稱網域不相符，您必須使用 `--assignee-object-id`來指定使用者帳戶的物件識別碼，而不只是 `--assignee`的使用者名稱。 您可以使用 [az ad user list](/cli/azure/ad/user#az-ad-user-list) 取得使用者帳戶的物件識別碼。

如需有關如何使用 RBAC 來管理 Azure 訂用帳戶資源存取權的詳細資訊，請參閱下列文章：

- [使用 RBAC 和 Azure CLI 來管理 Azure 資源的存取權](/azure/role-based-access-control/role-assignments-cli)
- [使用 RBAC 和 Azure 入口網站管理 Azure 資源的存取權](/azure/role-based-access-control/role-assignments-portal)
- [使用 RBAC 和 Azure PowerShell 來管理 Azure 資源的存取權](/azure/role-based-access-control/role-assignments-powershell)。
'
## <a name="using-conditional-access"></a>使用條件式存取

您可以先強制執行條件式存取原則（例如多重要素驗證或使用者登入風險檢查），再授權存取 Azure 中啟用 Azure AD 登入的 Windows Vm。 若要套用條件式存取原則，您必須從 [雲端應用程式] 或 [動作] 指派選項中選取 [Azure Windows VM 登入] 應用程式，然後使用 [登入風險] 作為條件，並（或）要求多重要素驗證做為 [授與存取控制]。 

> [!NOTE]
> 如果您使用「需要多重要素驗證」做為要求存取「Azure Windows VM 登入」應用程式的授與存取控制，則您必須提供多因素驗證宣告作為用戶端的一部分，以對目標視窗起始 RDP 會話Azure 中的 VM。 在 Windows 10 用戶端上達成此目標的唯一方法，就是使用 Windows Hello 企業版 PIN 或搭配 RDP 用戶端的生物識別驗證。 對生物識別驗證的支援已新增至 Windows 10 1809 版中的 RDP 用戶端。 使用 Windows Hello 企業版驗證的遠端桌面僅適用于使用憑證信任模型的部署，而且目前不適用於金鑰信任模型。

## <a name="log-in-using-azure-ad-credentials-to-a-windows-vm"></a>使用 Azure AD 認證登入 Windows VM

> [!IMPORTANT]
> 只有已加入 Azure AD 的 Windows 10 電腦或混合式 Azure AD 加入與 VM**相同**的目錄，才允許從遠端連線至已加入 Azure AD 的 vm。 此外，若要使用 Azure AD 認證的 RDP，使用者必須屬於下列其中一個 RBAC 角色： [虛擬機器系統管理員登入] 或 [虛擬機器使用者登入]。 目前，Azure 防禦無法用來以 AADLoginForWindows 延伸模組的 Azure Active Directory 驗證進行登入。 僅支援直接 RDP。

若要使用 Azure AD 登入您的 Windows Server 2019 虛擬機器： 

1. 流覽至已使用 Azure AD 登入啟用之虛擬機器的 [總覽] 頁面。
1. 選取 **[連線]** 以開啟 [連線至虛擬機器] 分頁。
1. 選取 [下載 RDP 檔案]。
1. 選取 [**開啟**] 以啟動遠端桌面連線用戶端。
1. 選取 **[連線]** 以啟動 Windows 登入對話方塊。
1. 使用您的 Azure AD 認證登入。

您現在已使用指派的角色許可權（例如 VM 使用者或 VM 系統管理員）登入 Windows Server 2019 Azure 虛擬機器。 

> [!NOTE]
> 您可以儲存。RDP 檔案在您的電腦本機上，用來啟動虛擬機器的未來遠端桌面連線，而不需要在 Azure 入口網站中流覽至虛擬機器 [總覽] 頁面，並使用 [連線] 選項。

## <a name="troubleshoot"></a>疑難排解

### <a name="troubleshoot-deployment-issues"></a>為部署問題進行疑難排解

AADLoginForWindows 擴充功能必須成功安裝，VM 才能完成 Azure AD 聯結程式。 如果 VM 擴充功能無法正確安裝，請執行下列步驟。

1. 使用本機系統管理員帳戶以 RDP 連線至 VM，並檢查底下的 CommandExecuti'n  
   
   C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.ActiveDirectory.AADLoginForWindows\0.3.1.0. 

   > [!NOTE]
   > 如果在初始失敗後重新開機延伸模組，則會將含有部署錯誤的記錄儲存為 CommandExecution_YYYYMMDDHHMMSSSSS .log。 "
1. 在 VM 上開啟命令提示字元，並針對在 Azure 主機上執行的 Instance Metadata Service （IMDS）端點，確認這些查詢會傳回：

   | 要執行的命令 | 預期的輸出 |
   | --- | --- |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/instance?api-version=2017-08-01"` | 更正 Azure VM 的相關資訊 |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/info?api-version=2018-02-01"` | 與 Azure 訂用帳戶相關聯的有效租使用者識別碼 |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/oauth2/token?resource=urn:ms-drs:enterpriseregistration.windows.net&api-version=2018-02-01"` | 為指派給此 VM 的受控識別 Azure Active Directory 所簽發的有效存取權杖 |

   > [!NOTE]
   > 您可以使用[http://calebb.net/](http://calebb.net/)之類的工具來解碼存取權杖。 確認存取權杖中的「appid」符合指派給 VM 的受控識別。

1. 請使用命令列，確定可從 VM 存取所需的端點：
   
   - 捲曲的 HTTPs：\//login.microsoftonline.com/-D –
   - 捲曲的 HTTPs：\//login.microsoftonline.com/`<TenantID>`/-D –

   > [!NOTE]
   > 將 `<TenantID>` 取代為與 Azure 訂用帳戶相關聯的 Azure AD 租使用者識別碼。

   - 捲曲的 HTTPs：\//enterpriseregistration.windows.net/-D-
   - 捲曲的 HTTPs：\//device.login.microsoftonline.com/-D-
   - 捲曲的 HTTPs：\//pas.windows.net/-D-

1. 您可以藉由執行 `dsregcmd /status`來查看裝置狀態。 目標是讓裝置狀態顯示為 `AzureAdJoined : YES`。

   > [!NOTE]
   > Azure AD 聯結活動會在 [事件檢視器] 的 [使用者裝置 Registration\Admin] 記錄下加以捕捉。

如果 AADLoginForWindows 延伸模組因特定的錯誤碼而失敗，您可以執行下列步驟：

#### <a name="issue-1-aadloginforwindows-extension-fails-to-install-with-terminal-error-code-1007-and-exit-code--2145648574"></a>問題1： AADLoginForWindows 延伸模組安裝失敗，終端機錯誤碼為 ' 1007 '，結束代碼：-2145648574。

此結束代碼會轉譯為 DSREG_E_MSI_TENANTID_UNAVAILABLE，因為此延伸模組無法查詢 Azure AD 的租使用者資訊。

1. 確認 Azure VM 可以從 Instance Metadata Service 取得 TenantID。

   - 以本機系統管理員的身分對 VM 進行 RDP，並確認端點會從 VM 上提高許可權的命令列執行下列命令，以傳回有效的租使用者識別碼：
      
      - 捲曲-H 中繼資料： true http://169.254.169.254/metadata/identity/info?api-version=2018-02-01

1. VM 管理員嘗試安裝 AADLoginForWindows 擴充功能，但系統指派的受控識別尚未先啟用 VM。 流覽至 VM 的 [身分識別] 分頁。 從 [系統指派] 索引標籤中，確認 [狀態] 已切換為 [開啟]。

#### <a name="issue-2-aadloginforwindows-extension-fails-to-install-with-exit-code--2145648607"></a>問題2： AADLoginForWindows 延伸模組安裝失敗，結束代碼：-2145648607

此結束代碼會轉譯為 DSREG_AUTOJOIN_DISC_FAILED，因為延伸模組無法連線到 https://enterpriseregistration.windows.net 端點。

1. 使用命令列確認可從 VM 存取所需的端點：

   - 捲曲的 HTTPs：\//login.microsoftonline.com/-D –
   - 捲曲的 HTTPs：\//login.microsoftonline.com/`<TenantID>`/-D –
   
   > [!NOTE]
   > 將 `<TenantID>` 取代為與 Azure 訂用帳戶相關聯的 Azure AD 租使用者識別碼。 如果您需要尋找 [租使用者識別碼]，您可以將滑鼠停留在帳戶名稱上以取得目錄/租使用者識別碼，或在 Azure 入口網站中選取 [Azure Active Directory > 屬性] > [目錄識別碼]。

   - 捲曲的 HTTPs：\//enterpriseregistration.windows.net/-D-
   - 捲曲的 HTTPs：\//device.login.microsoftonline.com/-D-
   - 捲曲的 HTTPs：\//pas.windows.net/-D-

1. 如果有任何命令因「無法解析主機 `<URL>`」而失敗，請嘗試執行此命令來判斷 VM 正在使用的 DNS 伺服器。
   
   `nslookup <URL>`

   > [!NOTE] 
   > 以端點使用的完整功能變數名稱（例如 "login.microsoftonline.com"）取代 `<URL>`。

1. 接下來，請查看是否指定公用 DNS 伺服器允許命令成功：

   `nslookup <URL> 208.67.222.222`

1. 如有需要，請變更指派給 Azure VM 所屬之網路安全性群組的 DNS 伺服器。

#### <a name="issue-3-aadloginforwindows-extension-fails-to-install-with-exit-code-51"></a>問題3： AADLoginForWindows 延伸模組安裝失敗，結束代碼：51

結束代碼51會轉譯為「VM 的作業系統上不支援此延伸模組」。

在公開預覽版本中，AADLoginForWindows 延伸模組僅適用于安裝在 Windows Server 2019 或 Windows 10 （組建1809或更新版本）上。 請確定支援的 Windows 版本。 如果不支援 Windows 組建，請將 VM 擴充功能卸載。

### <a name="troubleshoot-sign-in-issues"></a>對登入問題進行疑難排解

當您嘗試使用 Azure AD 認證來進行 RDP 時，有一些常見的錯誤包括不會指派任何 RBAC 角色、未授權的用戶端或2FA 登入方法。 請使用下列資訊來更正這些問題。

您可以藉由執行 `dsregcmd /status`來查看裝置和 SSO 狀態。 其目標是讓裝置狀態顯示為 `AzureAdJoined : YES`，並 `SSO State` 顯示 `AzureAdPrt : YES`。

此外，使用 Azure AD 帳戶的 RDP 登入會在事件檢視器中的 AAD\Operational 事件記錄下加以捕捉。

#### <a name="rbac-role-not-assigned"></a>未指派 RBAC 角色

當您對 VM 起始遠端桌面連線時，如果看到下列錯誤訊息： 

- 您的帳戶已設定為防止您使用此裝置。 如需詳細資訊，請洽詢您的系統管理員

![您的帳戶已設定為防止您使用此裝置。](./media/howto-vm-sign-in-azure-ad-windows/rbac-role-not-assigned.png)

請確認您已為 VM[設定 RBAC 原則](../../virtual-machines/linux/login-using-aad.md)，以授與使用者「虛擬機器系統管理員登入」或「虛擬機器使用者登入」角色：
 
#### <a name="unauthorized-client"></a>未經授權的用戶端

當您對 VM 起始遠端桌面連線時，如果看到下列錯誤訊息： 

- 您的認證無法使用

![您的認證無法使用](./media/howto-vm-sign-in-azure-ad-windows/your-credentials-did-not-work.png)

請確認您用來起始遠端桌面連線的 Windows 10 電腦，是 Azure AD 已加入，或是已加入您 VM 加入之相同 Azure AD 目錄的混合式 Azure AD。 如需裝置身分識別的詳細資訊，請參閱[什麼是裝置身分識別一](/azure/active-directory/devices/overview)文。

> [!NOTE]
> Windows 10 20H1 會新增 Azure AD 已註冊電腦的支援，以起始對您 VM 的遠端桌面連線。 加入 Windows 測試人員計畫以試用並探索 Windows 10 的新功能。

此外，請確認在 Azure AD 聯結完成後，尚未卸載 AADLoginForWindows 延伸模組。
 
#### <a name="mfa-sign-in-method-required"></a>需要 MFA 登入方法

當您對 VM 起始遠端桌面連線時，如果看到下列錯誤訊息： 

- 您嘗試使用的登入方法不允許。 請嘗試不同的登入方法，或洽詢您的系統管理員。

![您嘗試使用的登入方法不允許。](./media/howto-vm-sign-in-azure-ad-windows/mfa-sign-in-method-required.png)

如果您已設定需要多重要素驗證（MFA）的條件式存取原則，才能存取資源，則您需要確定 Windows 10 電腦起始 VM 的遠端桌面連線使用強式登入驗證方法，例如 Windows Hello。 如果您未針對遠端桌面連線使用增強式驗證方法，就會看到先前的錯誤。

如果您尚未部署 Windows Hello 企業版，而且目前無法使用，則您可以設定條件式存取原則，從需要 MFA 的雲端應用程式清單中排除「Azure Windows VM 登入」應用程式，以排除 MFA 需求。 若要深入瞭解 Windows Hello 企業版，請參閱[Windows Hello 企業版總覽](/windows/security/identity-protection/hello-for-business/hello-identity-verification)。

> [!NOTE]
> Windows 10 已針對數個版本支援使用 RDP 的 windows Hello 企業版 PIN 驗證，但在 Windows 10 1809 版中已新增使用 RDP 的生物特徵辨識驗證支援。 在 RDP 期間使用 Windows Hello 企業版驗證僅適用于使用憑證信任模型的部署，而且目前無法用於金鑰信任模型。
 
## <a name="preview-feedback"></a>預覽意見反應

請在[Azure AD 意見反應論壇](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)上，分享您對這項預覽功能的意見反應，或報告問題。

## <a name="next-steps"></a>後續步驟
如需 Azure Active Directory 的詳細資訊，請參閱[什麼是 Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)