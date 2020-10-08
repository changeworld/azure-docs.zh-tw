---
title: 使用 Azure Active Directory (預覽) 登入 Azure 中的 Windows 虛擬機器
description: Azure AD 登入執行 Windows 的 Azure VM
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.custom: references_regions
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27ffc176fc890d90e4201069ec1728eed69d4011
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91826658"
---
# <a name="sign-in-to-windows-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>使用 Azure Active Directory authentication (Preview 登入 Azure 中的 Windows 虛擬機器) 

組織現在可以針對其 Azure 虛擬機器使用 Azure Active Directory (AD) 驗證， (執行 **Windows Server 2019 Datacenter edition** 或 **windows 10 1809** 和更新版本的 vm) 。 使用 Azure AD 向 Vm 進行驗證，可讓您集中控制和強制執行原則。 Azure 角色型存取控制 (Azure RBAC) 和 Azure AD 條件式存取之類的工具，可讓您控制誰可以存取 VM。 本文說明如何建立和設定 Windows Server 2019 VM，以使用 Azure AD 驗證。

> [!NOTE]
> Azure Windows Vm Azure AD 登入是 Azure Active Directory 的公開預覽功能。 如需預覽的詳細資訊，請參閱  [Microsoft Azure 預覽的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

使用 Azure AD authentication 登入 Azure 中的 Windows Vm 有許多優點，包括：

- 利用您一般使用的相同同盟或受控 Azure AD 認證。
- 不再需要管理本機系統管理員帳戶。
- Azure RBAC 可讓您根據需求授與適當的 Vm 存取權，並在不再需要時將其移除。
- 在允許存取 VM 之前，Azure AD 條件式存取可以強制執行其他需求，例如： 
   - Multi-Factor Authentication
   - 登入風險檢查
- 將適用于您 VDI 部署一部分的 Azure Windows Vm 的 Azure AD 聯結自動化和調整。

> [!NOTE]
> 啟用這項功能之後，您在 Azure 中的 Windows Vm 將 Azure AD 聯結。 您無法將它加入其他網域（例如內部部署 AD 或 Azure AD DS）。 如果您需要這樣做，您必須卸載擴充功能，將 VM 與 Azure AD 租使用者中斷連線。

## <a name="requirements"></a>需求

### <a name="supported-azure-regions-and-windows-distributions"></a>支援的 Azure 區域和 Windows 散發套件

這項功能的預覽期間，目前支援下列 Windows 散發套件：

- Windows Server 2019 Datacenter
- Windows 10 1809 和更新版本

> [!IMPORTANT]
> 僅允許從 Azure AD 註冊的 Windows 10 電腦進行遠端連線（這些 Azure AD Vm 已註冊） (啟動 Windows 10 20H1) ，Azure AD 聯結或混合式 Azure AD 聯結至與 VM **相同** 的目錄。 

以下是目前在這項功能的預覽期間支援的 Azure 區域：

- 所有 Azure 全球區域

> [!IMPORTANT]
> 若要使用此預覽功能，請只在支援的 Azure 區域中部署支援的 Windows 散發套件。 Azure Government 或主權雲端目前不支援此功能。

### <a name="network-requirements"></a>網路需求

若要在 Azure 中為您的 Windows Vm 啟用 Azure AD 驗證，您必須確定您的 Vm 網路設定允許透過 TCP 埠443對下列端點進行輸出存取：

- HTTPs： \/ /enterpriseregistration.windows.net
- https:\//login.microsoftonline.com
- HTTPs： \/ /device.login.microsoftonline.com
- HTTPs： \/ /pas.windows.net

## <a name="enabling-azure-ad-login-in-for-windows-vm-in-azure"></a>在 Azure 中啟用 Windows VM 的 Azure AD 登入

若要在 Azure 中使用適用于 Windows VM 的 Azure AD 登入，您必須先啟用 Windows VM 的 Azure AD 登入選項，然後您需要為已獲授權登入 VM 的使用者設定 Azure 角色指派。
您可以透過多種方式來啟用 Windows VM 的 Azure AD 登入：

- 在建立 Windows VM 時使用 Azure 入口網站體驗
- 在建立 Windows VM**或針對現有的 WINDOWS vm**使用 Azure Cloud Shell 體驗

### <a name="using-azure-portal-create-vm-experience-to-enable-azure-ad-login"></a>使用 Azure 入口網站建立 VM 體驗來啟用 Azure AD 登入

您可以為 Windows Server 2019 Datacenter 或 Windows 10 1809 和更新版本的 VM 映射啟用 Azure AD 登入。 

若要在 Azure 中使用 Azure AD 登入來建立 Windows Server 2019 Datacenter VM： 

1. 使用具有建立 Vm 存取權的帳戶登入 [Azure 入口網站](https://portal.azure.com)，然後選取 [ **+ 建立資源**]。
1. 在 [搜尋 Marketplace 搜尋列] 中輸入 **Windows Server** 。
   1. 按一下 [ **Windows server** ]，然後從 [選取軟體方案] 下拉式清單中選擇 [ **Windows server 2019 Datacenter** ]。
   1. 按一下 [ **建立**]。
1. 在 [管理] 索引標籤上，啟用從 [關閉] 到 [**開啟**] Azure Active Directory 區段下的 [**使用 AAD 認證登入] (預覽) **的選項。
1. 請確定 [身分識別] 區段下的 [ **系統指派的受控識別** ] 設定為 [ **開啟**]。 當您啟用使用 Azure AD 認證登入之後，應該會自動發生此動作。
1. 請流覽建立虛擬機器的其餘體驗。 在此預覽期間，您將必須建立 VM 的系統管理員使用者名稱和密碼。

![使用 Azure AD 認證登入建立 VM](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-login-with-azure-ad.png)

> [!NOTE]
> 若要使用您的 Azure AD 認證登入 VM，您必須先設定 VM 的角色指派，如下列其中一節所述。

### <a name="using-the-azure-cloud-shell-experience-to-enable-azure-ad-login"></a>使用 Azure Cloud Shell 體驗來啟用 Azure AD 登入

Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 Cloud Shell 中已預先安裝和設定共用 Azure 工具，以便您搭配自己的帳戶使用。 只要選取 [複製] 按鈕即可複製程式碼，將它貼到 Cloud Shell 中，然後按 Enter 鍵加以執行。 以下有幾種開啟 Cloud Shell 的方式：

選取程式碼區塊右上角的 [試試看]。
在您的瀏覽器中開啟 Cloud Shell。
選取 [Azure 入口網站](https://portal.azure.com)右上角功能表上的 [Cloud Shell] 按鈕。

如果您選擇在本機安裝和使用 CLI，本文會要求您執行 Azure CLI 2.0.31 版版或更新版本。 執行 az --version 以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI](/cli/azure/install-azure-cli)一文。

1. 使用 [az group create](/cli/azure/group#az-group-create) 來建立資源群組。 
1. 使用支援的區域中支援的散發套件建立具有 [az vm create](/cli/azure/vm#az-vm-create) 的 vm。 
1. 安裝 Azure AD 登入 VM 擴充功能。 

下列範例會將名為 myVM 的 VM （其使用 Win2019Datacenter）部署到 southcentralus 區域中名為 myResourceGroup 的資源群組。 在下列範例中，您可以視需要提供自己的資源群組和 VM 名稱。

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
> 您必須先在虛擬機器上啟用系統指派的受控識別，才能安裝 Azure AD 登入 VM 擴充功能。

建立虛擬機器和支援資源需要幾分鐘的時間。

最後，安裝 Azure AD 登入 VM 擴充功能，以啟用 Windows VM Azure AD 登入。 VM 擴充功能是小型的應用程式，可在「Azure 虛擬機器」上提供部署後設定及自動化工作。 使用 [az vm extension](/cli/azure/vm/extension#az-vm-extension-set) 設定，在 myResourceGroup 資源群組中名為 MYVM 的 vm 上安裝 AADLoginForWindows 擴充功能：

> [!NOTE]
> 您可以在現有的 Windows Server 2019 或 Windows 10 1809 和更新版本的 VM 上安裝 AADLoginForWindows 延伸模組，以啟用 Azure AD 驗證。 AZ CLI 的範例如下所示。

```AzureCLI
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory \
    --name AADLoginForWindows \
    --resource-group myResourceGroup \
    --vm-name myVM
```

`provisioningState`一旦在 `Succeeded` VM 上安裝擴充功能之後，就會顯示的。

## <a name="configure-role-assignments-for-the-vm"></a>設定 VM 的角色指派

現在您已建立 VM，您必須設定 Azure RBAC 原則，以決定誰可以登入 VM。 有兩個 Azure 角色用來授權 VM 登入：

- **虛擬機器系統管理員登**入：已指派此角色的使用者可以使用系統管理員許可權登入 Azure 虛擬機器。
- **虛擬機器使用者登入**：被指派此角色的使用者能夠以一般使用者權限登入 Azure 虛擬機器。

> [!NOTE]
> 若要允許使用者透過 RDP 登入 VM，您必須指派虛擬機器系統管理員登入或虛擬機器使用者登入角色。 具有為 VM 指派擁有者或參與者角色的 Azure 使用者不會自動擁有透過 RDP 登入 VM 的許可權。 這是為了在控制虛擬機器的一組人與可存取虛擬機器的人員集合之間提供經過審核的分隔。

您可以透過多種方式來設定 VM 的角色指派：

- 使用 Azure AD 入口網站體驗
- 使用 Azure Cloud Shell 體驗

### <a name="using-azure-ad-portal-experience"></a>使用 Azure AD 入口網站體驗

若要為 Azure AD 啟用的 Windows Server 2019 Datacenter Vm 設定角色指派：

1. 流覽至特定的虛擬機器總覽頁面
1. 從功能表選項中選取 [**存取控制] (IAM) **
1. 選取 [ **新增**]、[ **新增角色指派** ] 以開啟 [新增角色指派] 窗格。
1. 在 [ **角色** ] 下拉式清單中，選取角色，例如 **虛擬機器系統管理員登** 入或 **虛擬機器使用者登**入。
1. 在 [ **選取** ] 欄位中，選取使用者、群組、服務主體或受控識別。 如果在清單中未看到安全性主體，您可以在 [選取]**** 方塊中輸入，以在目錄中搜尋顯示名稱、電子郵件地址和物件識別碼。
1. 選取 [ **儲存**] 以指派角色。

在幾分鐘之後，即會在所選範圍中指派安全性主體的角色。

![將角色指派給將存取 VM 的使用者](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-access-control-assign-role.png)

### <a name="using-the-azure-cloud-shell-experience"></a>使用 Azure Cloud Shell 體驗

下列範例會使用 [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) 將 [虛擬機器系統管理員登入] 角色指派給您目前 Azure 使用者的 VM。 作用中 Azure 帳戶的使用者名稱可透過 [az account show](/cli/azure/account#az-account-show) 來取得，而範圍會設定為在上一個步驟中使用 [az vm show](/cli/azure/vm#az-vm-show) 建立的 VM。 您也可以在資源群組或訂用帳戶層級指派範圍，並套用一般的 Azure RBAC 繼承許可權。 如需詳細資訊，請參閱 [使用 Azure Active Directory Authentication 登入 Azure 中的 Linux 虛擬機器](../../virtual-machines/linux/login-using-aad.md)。

```   AzureCLI
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> 如果您的 AAD 網域和登入使用者名稱網域不符，您必須指定使用者帳戶的物件識別碼 `--assignee-object-id` ，而不只是的使用者名稱 `--assignee` 。 您可以使用 [az ad user list](/cli/azure/ad/user#az-ad-user-list) 取得使用者帳戶的物件識別碼。

如需如何使用 Azure RBAC 來管理 Azure 訂用帳戶資源存取權的詳細資訊，請參閱下列文章：

- [使用 Azure CLI 新增或移除 Azure 角色指派](../../role-based-access-control/role-assignments-cli.md)
- [使用 Azure 入口網站新增或移除 Azure 角色指派](../../role-based-access-control/role-assignments-portal.md)
- [使用 Azure PowerShell 新增或移除 Azure 角色指派](../../role-based-access-control/role-assignments-powershell.md)。

## <a name="using-conditional-access"></a>使用條件式存取

您可以先強制執行條件式存取原則，例如多重要素驗證或使用者登入風險檢查，然後才會授權存取 Azure 中使用 Azure AD 登入啟用的 Windows Vm。 若要套用條件式存取原則，您必須從 [雲端應用程式] 或 [動作指派] 選項選取 [Azure Windows VM 登入] 應用程式，然後使用 [登入風險] 作為條件，並（或）要求多重要素驗證作為授與存取控制。 

> [!NOTE]
> 如果您使用「需要多重要素驗證」作為授與存取權以要求存取「Azure Windows VM 登入」應用程式，則您必須在用戶端提供多重要素驗證宣告，以在 Azure 中起始對目標 Windows VM 的 RDP 會話。 在 Windows 10 用戶端上達成此目標的唯一方法，是使用 Windows Hello 企業版 PIN 或生物識別驗證搭配 RDP 用戶端。 在 Windows 10 1809 版中，已將生物識別驗證的支援新增至 RDP 用戶端。 使用 Windows Hello 企業版驗證的遠端桌面僅適用于使用憑證信任模型的部署，且目前無法用於金鑰信任模型。

> [!WARNING]
> VM 登入不支援每位使用者啟用/強制執行的 Azure Multi-Factor Authentication。

## <a name="log-in-using-azure-ad-credentials-to-a-windows-vm"></a>使用 Azure AD 認證登入 Windows VM

> [!IMPORTANT]
> 已加入 Azure AD 的 Vm 的遠端連線，只允許來自 Azure AD 註冊的 Windows 10 電腦 (最小必要組建 20H1) 或加入至與 VM **相同** 目錄 Azure AD 加入或混合式 Azure AD。 此外，若要使用 Azure AD 認證的 RDP，則使用者必須屬於這兩個 Azure 角色的其中一個：虛擬機器系統管理員登入或虛擬機器使用者登入。 如果使用 Windows 10 PC 註冊的 Azure AD，您必須以 AzureAD\UPN 格式輸入認證 (例如 AzureAD\john@contoso.com) 。 目前，Azure 防禦無法用來搭配 AADLoginForWindows 延伸模組使用 Azure Active Directory authentication 來登入;僅支援直接 RDP。

使用 Azure AD 登入 Windows Server 2019 虛擬機器： 

1. 流覽至已使用 Azure AD 登入啟用之虛擬機器的 [總覽] 頁面。
1. 選取 [連線 **]** 以開啟 [連線到虛擬機器] 分頁。
1. 選取 [下載 RDP 檔案]。
1. 選取 [ **開啟** ] 以啟動遠端桌面連線用戶端。
1. 選取 **[連線]** 以啟動 Windows 登入對話方塊。
1. 使用您的 Azure AD 認證登入。

您現在已使用指派的角色許可權登入 Windows Server 2019 Azure 虛擬機器，例如 VM 使用者或 VM 系統管理員。 

> [!NOTE]
> 您可以儲存。本機電腦上的 RDP 檔案，可啟動虛擬機器的未來遠端桌面連線，而不需要流覽至 Azure 入口網站中的虛擬機器總覽頁面，並使用 [連線] 選項。

## <a name="troubleshoot"></a>疑難排解

### <a name="troubleshoot-deployment-issues"></a>為部署問題進行疑難排解

AADLoginForWindows 擴充功能必須成功安裝，VM 才能完成 Azure AD 聯結程式。 如果 VM 擴充功能無法正確安裝，請執行下列步驟。

1. 使用本機系統管理員帳戶從 RDP 連線到 VM，並檢查 CommandExecuti'n  
   
   C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.ActiveDirectory.AADLoginForWindows\0.3.1.0. 

   > [!NOTE]
   > 如果延伸模組在初始失敗之後重新開機，則會將具有部署錯誤的記錄檔儲存為 CommandExecution_YYYYMMDDHHMMSSSSS .log。 "
1. 在 VM 上開啟 PowerShell 命令提示字元，並針對在 Azure 主機上執行的 Instance Metadata Service (IMDS) 端點，確認這些查詢會傳回：

   | 要執行的命令 | 預期的輸出 |
   | --- | --- |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/instance?api-version=2017-08-01"` | 更正 Azure VM 的相關資訊 |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/info?api-version=2018-02-01"` | 與 Azure 訂用帳戶相關聯的有效租使用者識別碼 |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/oauth2/token?resource=urn:ms-drs:enterpriseregistration.windows.net&api-version=2018-02-01"` | 針對指派給此 VM 的受控識別，Azure Active Directory 所簽發的有效存取權杖 |

   > [!NOTE]
   > 您可以使用類似的工具來解碼存取權杖 [http://calebb.net/](http://calebb.net/) 。 確認存取權杖中的 "appid" 符合指派給 VM 的受控識別。

1. 使用命令列確認可從 VM 存取所需的端點：
   
   - 捲曲 HTTPs： \/ /login.microsoftonline.com/-D –
   - 捲曲 HTTPs： \/ /login.microsoftonline.com/ `<TenantID>` /-D –

   > [!NOTE]
   > `<TenantID>`以與 Azure 訂用帳戶相關聯的 Azure AD 租使用者識別碼取代。

   - 捲曲 HTTPs： \/ /enterpriseregistration.windows.net/-D-
   - 捲曲 HTTPs： \/ /device.login.microsoftonline.com/-D-
   - 捲曲 HTTPs： \/ /pas.windows.net/-D-

1. 您可以藉由執行來查看裝置狀態 `dsregcmd /status` 。 目標是讓裝置狀態顯示為 `AzureAdJoined : YES` 。

   > [!NOTE]
   > 在 [事件檢視器] 中，會在使用者裝置 Registration\Admin 記錄檔下捕獲 Azure AD 聯結活動。

如果 AADLoginForWindows 延伸模組因為特定的錯誤碼而失敗，您可以執行下列步驟：

#### <a name="issue-1-aadloginforwindows-extension-fails-to-install-with-terminal-error-code-1007-and-exit-code--2145648574"></a>問題1： AADLoginForWindows 延伸模組安裝失敗，終端機錯誤碼為 ' 1007 '，結束代碼：-2145648574。

此結束代碼會轉譯為 DSREG_E_MSI_TENANTID_UNAVAILABLE，因為擴充功能無法查詢 Azure AD 的租使用者資訊。

1. 確認 Azure VM 可以從 Instance Metadata Service 取出 TenantID。

   - 以本機系統管理員身分從 RDP 連線至 VM，並透過從 VM 上提高許可權的命令列執行下列命令，以確認端點會傳回有效的租使用者識別碼：
      
      - 捲曲-H 中繼資料： true http://169.254.169.254/metadata/identity/info?api-version=2018-02-01

1. VM 系統管理員會嘗試安裝 AADLoginForWindows 延伸模組，但系統指派的受控識別尚未先啟用 VM。 流覽至 VM 的身分識別 blade。 在 [系統指派] 索引標籤中，確認狀態已切換為 [開啟]。

#### <a name="issue-2-aadloginforwindows-extension-fails-to-install-with-exit-code--2145648607"></a>問題2： AADLoginForWindows 延伸模組無法安裝，結束代碼：-2145648607

此結束代碼會轉譯為 DSREG_AUTOJOIN_DISC_FAILED，因為擴充功能無法連線到 `https://enterpriseregistration.windows.net` 端點。

1. 使用命令列確認可從 VM 存取必要的端點：

   - 捲曲 HTTPs： \/ /login.microsoftonline.com/-D –
   - 捲曲 HTTPs： \/ /login.microsoftonline.com/ `<TenantID>` /-D –
   
   > [!NOTE]
   > `<TenantID>`以與 Azure 訂用帳戶相關聯的 Azure AD 租使用者識別碼取代。 如果您需要尋找租使用者識別碼，可以將滑鼠停留在您的帳戶名稱上以取得目錄/租使用者識別碼，或選取 Azure 入口網站中 > 目錄識別碼 Azure Active Directory > 屬性。

   - 捲曲 HTTPs： \/ /enterpriseregistration.windows.net/-D-
   - 捲曲 HTTPs： \/ /device.login.microsoftonline.com/-D-
   - 捲曲 HTTPs： \/ /pas.windows.net/-D-

1. 如果有任何命令失敗，並出現「無法解析主機」 `<URL>` ，請嘗試執行此命令，以判斷 VM 正在使用的 DNS 伺服器。
   
   `nslookup <URL>`

   > [!NOTE] 
   > 取代 `<URL>` 為端點所使用的完整功能變數名稱，例如 "login.microsoftonline.com"。

1. 接下來，請查看指定公用 DNS 伺服器是否允許命令成功：

   `nslookup <URL> 208.67.222.222`

1. 如有必要，請變更指派給 Azure VM 所屬網路安全性群組的 DNS 伺服器。

#### <a name="issue-3-aadloginforwindows-extension-fails-to-install-with-exit-code-51"></a>問題3： AADLoginForWindows 延伸模組安裝失敗，結束代碼：51

結束代碼51會轉譯為「此延伸模組在 VM 的作業系統上不受支援」。

在公開預覽版本中，AADLoginForWindows 延伸模組僅適用于 Windows Server 2019 或 Windows 10 (組建1809或更新版本) 。 請確定支援的 Windows 版本。 如果不支援 Windows 組建，請將 VM 延伸模組卸載。

### <a name="troubleshoot-sign-in-issues"></a>對登入問題進行疑難排解

當您嘗試搭配 Azure AD 認證使用 RDP 時，常見的錯誤包括沒有任何已指派的 Azure 角色、未經授權的用戶端或2FA 登入方法。 使用下列資訊來修正這些問題。

您可以藉由執行來查看裝置和 SSO 狀態 `dsregcmd /status` 。 目標是讓裝置狀態顯示為 `AzureAdJoined : YES` 並 `SSO State` 顯示 `AzureAdPrt : YES` 。

此外，使用 Azure AD 帳戶的 RDP 登入，會在事件檢視器中的 AAD\Operational 事件記錄檔下捕獲。

#### <a name="azure-role-not-assigned"></a>未指派 Azure 角色

如果您在起始 VM 的遠端桌面連線時看到下列錯誤訊息： 

- 您的帳戶已設定為防止您使用此裝置。 如需詳細資訊，請洽詢您的系統管理員

![您的帳戶已設定為防止您使用此裝置。](./media/howto-vm-sign-in-azure-ad-windows/rbac-role-not-assigned.png)

確認您已為 VM [設定 AZURE RBAC 原則](../../virtual-machines/linux/login-using-aad.md) ，以授與使用者虛擬機器系統管理員登入或虛擬機器使用者登入角色：
 
#### <a name="unauthorized-client"></a>未經授權的用戶端

如果您在起始 VM 的遠端桌面連線時看到下列錯誤訊息： 

- 您的認證無法運作

![您的認證無法運作](./media/howto-vm-sign-in-azure-ad-windows/your-credentials-did-not-work.png)

確認您用來起始遠端桌面連線的 Windows 10 電腦是已 Azure AD 加入的電腦，或加入您 VM 所在之相同 Azure AD 目錄的混合式 Azure AD。 如需裝置身分識別的詳細資訊，請參閱文章 [是什麼是裝置身分識別](./overview.md)。

> [!NOTE]
> Windows 10 Build 20H1 新增了 Azure AD 已註冊電腦的支援，以起始對您 VM 的 RDP 連線。 使用註冊的 Azure AD (未 Azure AD 加入或混合式 Azure AD 加入的) PC 作為 RDP 用戶端，以起始與您 VM 的連線時，您必須以 AzureAD\UPn (的格式輸入認證，例如 AzureAD\john@contoso.com) 。

此外，也請確認 AADLoginForWindows 延伸模組在 Azure AD 聯結完成後尚未卸載。
 
#### <a name="mfa-sign-in-method-required"></a>需要 MFA 登入方法

如果您在起始 VM 的遠端桌面連線時看到下列錯誤訊息： 

- 您嘗試使用的登入方法不允許。 請嘗試不同的登入方法，或洽詢您的系統管理員。

![您嘗試使用的登入方法不允許。](./media/howto-vm-sign-in-azure-ad-windows/mfa-sign-in-method-required.png)

如果您已設定條件式存取原則，要求 (MFA) 的多重要素驗證，則在存取資源之前，您必須確定使用強式驗證方法（例如 Windows Hello）來啟動與您 VM 之遠端桌面連線的 Windows 10 電腦登入。 如果您的遠端桌面連線未使用強式驗證方法，您將會看到先前的錯誤。

如果您尚未部署 Windows Hello 企業版，且目前無法使用此選項，您可以設定條件式存取原則，從需要 MFA 的雲端應用程式清單中排除「Azure Windows VM 登入」應用程式，以排除 MFA 需求。 若要深入瞭解 Windows Hello 企業版，請參閱 [Windows Hello 企業版總覽](/windows/security/identity-protection/hello-for-business/hello-identity-verification)。

> [!NOTE]
> 針對數個版本，Windows 10 支援使用 RDP 進行 Windows Hello 企業版 PIN 驗證，不過 Windows 10 1809 版中已新增以 RDP 進行生物特徵辨識驗證的支援。 在 RDP 期間使用 Windows Hello 企業版驗證僅適用于使用憑證信任模型的部署，且目前無法用於金鑰信任模型。
 
## <a name="preview-feedback"></a>預覽意見反應

請分享您對此預覽功能的意見反應，或在 [Azure AD 意見反應論壇](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)上使用它來報告問題。

## <a name="next-steps"></a>下一步

如需有關 Azure Active Directory 的詳細資訊，請參閱[什麼是 Azure Active Directory](../fundamentals/active-directory-whatis.md)
