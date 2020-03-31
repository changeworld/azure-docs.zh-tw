---
title: 使用 Azure 活動目錄登錄到 Azure 中的 Windows 虛擬機器（預覽）
description: Azure AD 登錄到運行 Windows 的 Azure VM
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
ms.openlocfilehash: 88ae3c45126403161e35ec46e5ccc2666c3edb55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050067"
---
# <a name="sign-in-to-windows-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>使用 Azure 活動目錄身份驗證登錄到 Azure 中的 Windows 虛擬機器（預覽）

組織現在可以對其運行**Windows Server 2019 資料中心版**或**Windows 10 1809**及更高版本的 Azure 虛擬機器 （VM） 使用 Azure 活動目錄 （AD） 身份驗證。 使用 Azure AD 對 VM 進行身份驗證為您提供了集中控制和強制實施策略的方法。 使用 Azure 基於角色的存取控制 （RBAC） 和 Azure AD 條件訪問等工具，您可以控制誰可以訪問 VM。 本文介紹如何創建和配置 Windows Server 2019 VM 以使用 Azure AD 身份驗證。

|     |
| --- |
| Azure Windows VM 的 Azure AD 登錄是 Azure 活動目錄的公共預覽功能。 有關預覽的詳細資訊，請參閱 Microsoft [Azure 預覽的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

使用 Azure AD 身份驗證登錄到 Azure 中的 Windows VM 有許多好處，包括：

- 使用通常使用的相同聯合或託管 Azure AD 憑據。
- 不再需要管理本地管理員帳戶。
- Azure RBAC 允許您根據需要授予對 VM 的適當存取權限，並在不再需要 VM 時將其刪除。
- 在允許訪問 VM 之前，Azure AD 條件訪問可以強制實施其他要求，例如： 
   - Multi-Factor Authentication
   - 登錄風險檢查
- 自動和縮放作為 VDI 部署一部分的 Azure Windows VM 的 Azure Windows VM 加入。

> [!NOTE]
> 啟用此功能後，Azure 中的 Windows VM 將加入 Azure AD。 不能將其加入其他域，如本地 AD 或 Azure AD DS。 如果需要這樣做，則需要通過卸載擴展斷開 VM 與 Azure AD 租戶的連接。

## <a name="requirements"></a>需求

### <a name="supported-azure-regions-and-windows-distributions"></a>受支援的 Azure 區域和 Windows 發佈

在此功能的預覽期間，當前支援以下 Windows 發行版本：

- Windows Server 2019 Datacenter
- 視窗 10 1809 及更高版本

> [!IMPORTANT]
> 僅允許連接到 Azure AD 的 VM 的遠端連線從 Azure AD 聯接的 Windows 10 電腦或與 VM**加入的同一**目錄的混合 Azure AD。 

以下是目前在這項功能的預覽期間支援的 Azure 區域：

- 所有 Azure 全域區域

> [!IMPORTANT]
> 要使用此預覽功能，只需在受支援的 Windows 發佈區和受支援的 Azure 區域中部署。 Azure 政府或主權雲當前不支援此功能。

### <a name="network-requirements"></a>網路需求

要為 Azure 中的 Windows VM 啟用 Azure AD 身份驗證，需要確保 VM 網路設定允許通過 TCP 埠 443 對以下終結點進行出站訪問：

- HTTPs：\//enterpriseregistration.windows.net
- https:\//login.microsoftonline.com
- HTTPs：\//device.login.microsoftonline.com
- HTTPs：\//pas.windows.net

## <a name="enabling-azure-ad-login-in-for-windows-vm-in-azure"></a>在 Azure 中為 Windows VM 啟用 Azure AD 登錄

要在 Azure 中使用 Windows VM 的 Azure AD 登錄，需要首先為 Windows VM 啟用 Azure AD 登錄選項，然後需要為有權登錄到 VM 的使用者配置 RBAC 角色指派。
您可以通過多種方式為 Windows VM 啟用 Azure AD 登錄：

- 創建 Windows VM 時使用 Azure 門戶體驗
- 在創建 Windows VM**或針對現有 Windows VM**時使用 Azure 雲殼體驗

### <a name="using-azure-portal-create-vm-experience-to-enable-azure-ad-login"></a>使用 Azure 門戶創建 VM 體驗以啟用 Azure AD 登錄

您可以為 Windows 伺服器 2019 資料中心或 Windows 10 1809 和更高版本的 VM 映射啟用 Azure AD 登錄。 

要在 Azure 中使用 Azure AD 登錄創建 Windows 伺服器 2019 資料中心 VM，請執行： 

1. 使用有權訪問創建 VM 的帳戶登錄到[Azure 門戶](https://portal.azure.com)，然後選擇 **" 創建資源**"。
1. 在搜索應用商店搜索欄中鍵入**Windows 伺服器**。
   1. 按一下**Windows 伺服器**並從"選擇軟體計畫下拉清單"中選擇 Windows 伺服器**2019 資料中心**。
   1. 按一下"**創建**"。
1. 在"管理"選項卡上，啟用在 Azure 活動目錄部分"從關閉到**打開****"下使用 AAD 憑據（預覽）登錄**的選項。
1. 確保"標識"部分下**分配的系統託管標識**設置為**On**。 啟用使用 Azure AD 憑據登錄後，將自動執行此操作。
1. 體驗創建虛擬機器的其他體驗。 在此預覽期間，您必須為 VM 創建管理員使用者名和密碼。

![使用 Azure AD 憑據登錄創建 VM](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-login-with-azure-ad.png)

> [!NOTE]
> 為了使用 Azure AD 憑據登錄到 VM，首先需要配置 VM 的角色指派，如下節之一所述。

### <a name="using-the-azure-cloud-shell-experience-to-enable-azure-ad-login"></a>使用 Azure 雲外殼體驗啟用 Azure AD 登錄

Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 Cloud Shell 中已預先安裝和設定共用 Azure 工具，以便您搭配自己的帳戶使用。 只要選取 [複製] 按鈕即可複製程式碼，將它貼到 Cloud Shell 中，然後按 Enter 鍵加以執行。 以下有幾種開啟 Cloud Shell 的方式：

選取程式碼區塊右上角的 [試試看]  。
在您的瀏覽器中開啟 Cloud Shell。
選擇[Azure 門戶](https://portal.azure.com)右上角功能表上的雲外殼按鈕。

如果選擇在本地安裝和使用 CLI，則本文要求您運行 Azure CLI 版本 2.0.31 或更高版本。 執行 az --version 以尋找版本。 如果需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

1. 使用 [az group create](/cli/azure/group#az-group-create) 來建立資源群組。 
1. 使用受支援的區域中的受支援分發創建具有[az vm](/cli/azure/vm#az-vm-create)的 VM。 
1. 安裝 Azure AD 登錄 VM 擴展。 

下面的示例將使用 Win2019Datacenter 的名為 myVM 的 VM 部署到位於中南部區功能變數名稱為 myResourceGroup 的資源組中。 在下列範例中，您可以視需要提供自己的資源群組和 VM 名稱。

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
> 在安裝 Azure AD 登錄 VM 擴展之前，需要在虛擬機器上啟用系統分配的託管標識。

建立虛擬機器和支援資源需要幾分鐘的時間。

最後，安裝 Azure AD 登錄 VM 擴展，以啟用 Windows VM 的 Azure AD 登錄名。 VM 擴充功能是小型的應用程式，可在「Azure 虛擬機器」上提供部署後設定及自動化工作。 使用[az vm 擴展](/cli/azure/vm/extension#az-vm-extension-set)集在 myResourceGroup 資源組中名為 myVM 的 VM 上安裝 AADLoginForWindows 擴展：

> [!NOTE]
> 您可以在現有的 Windows 伺服器 2019 或 Windows 10 1809 和更高版本的 VM 上安裝 AADLoginForWindows 擴展，以啟用它進行 Azure AD 身份驗證。 下面顯示了 AZ CLI 的示例。

```AzureCLI
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory \
    --name AADLoginForWindows \
    --resource-group myResourceGroup \
    --vm-name myVM
```

在`provisioningState` `Succeeded` VM 上安裝擴展後，將顯示 。

## <a name="configure-role-assignments-for-the-vm"></a>設定 VM 的角色指派

創建 VM 後，需要配置 Azure RBAC 策略以確定誰可以登錄到 VM。 有兩個 RBAC 角色可用來授權 VM 登入：

- **虛擬機器管理員登錄**：分配此角色的使用者可以登錄到具有管理員許可權的 Azure 虛擬機器。
- **虛擬機器使用者登入**：被指派此角色的使用者能夠以一般使用者權限登入 Azure 虛擬機器。

> [!NOTE]
> 要允許使用者通過 RDP 登錄到 VM，必須分配虛擬機器管理員登錄或虛擬機器使用者登錄角色。 具有為 VM 分配的擁有者或參與者角色的 Azure 使用者不會自動具有通過 RDP 登錄到 VM 的許可權。 這是為控制虛擬機器的一組人員與可以訪問虛擬機器的人員集提供審核分離。

您可以通過多種方式為 VM 配置角色指派：

- 使用 Azure AD 門戶體驗
- 使用 Azure 雲外殼體驗

### <a name="using-azure-ad-portal-experience"></a>使用 Azure AD 門戶體驗

要為啟用 Azure AD 的 Windows 伺服器 2019 資料中心 VM 配置角色指派，請執行以下任務：

1. 導航到特定的虛擬機器概述頁面
1. 從功能表選項中選擇**存取控制 （IAM）**
1. 選擇 **"添加****"，添加角色指派**以打開"添加角色指派"窗格。
1. 在**角色**下拉清單中，選擇**虛擬機器管理員登錄**或**虛擬機器使用者登錄**等角色。
1. 在 **"選擇"** 欄位中，選擇使用者、組、服務主體或託管標識。 如果在清單中未看到安全性主體，您可以在 [選取]**** 方塊中輸入，以在目錄中搜尋顯示名稱、電子郵件地址和物件識別碼。
1. 選擇 **"保存**"以分配角色。

在幾分鐘之後，即會在所選範圍中指派安全性主體的角色。

![將角色指派給將訪問 VM 的使用者](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-access-control-assign-role.png)

### <a name="using-the-azure-cloud-shell-experience"></a>使用 Azure 雲外殼體驗

下列範例會使用 [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) 將 [虛擬機器系統管理員登入] 角色指派給您目前 Azure 使用者的 VM。 作用中 Azure 帳戶的使用者名稱可透過 [az account show](/cli/azure/account#az-account-show) 來取得，而範圍會設定為在上一個步驟中使用 [az vm show](/cli/azure/vm#az-vm-show) 建立的 VM。 範圍也可指派於資源群組或訂用帳戶層級上，並套用一般 RBAC 繼承權限。 有關詳細資訊，請參閱[基於角色的訪問控制項](../../virtual-machines/linux/login-using-aad.md)。

```   AzureCLI
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> 如果您的 AAD 域和登錄使用者名域不匹配，則必須使用 指定使用者帳戶的物件`--assignee-object-id`ID，而不僅僅是 的`--assignee`使用者名。 您可以使用 [az ad user list](/cli/azure/ad/user#az-ad-user-list) 取得使用者帳戶的物件識別碼。

有關如何使用 RBAC 管理對 Azure 訂閱資源的訪問的詳細資訊，請參閱以下文章：

- [使用 RBAC 與 Azure CLI 管理對 Azure 資源的存取](/azure/role-based-access-control/role-assignments-cli)
- [使用 RBAC 和 Azure 入口網站管理 Azure 資源的存取權](/azure/role-based-access-control/role-assignments-portal)
- [使用 RBAC 和 Azure PowerShell 管理對 Azure 資源的訪問](/azure/role-based-access-control/role-assignments-powershell)。

## <a name="using-conditional-access"></a>使用條件訪問

在授權訪問 Azure 中啟用 Azure AD 登錄的 Windows VM 之前，可以強制實施條件訪問策略，如多重要素驗證或使用者登錄風險檢查。 要應用條件訪問策略，必須從雲應用或操作分配選項中選擇"Azure Windows VM 登錄"應用，然後將登錄風險用作條件，和/或需要多重要素驗證作為授予存取控制。 

> [!NOTE]
> 如果使用"需要多重要素驗證"作為請求訪問"Azure Windows VM 登錄"應用的授予訪問控制項，則必須提供多重要素驗證聲明，作為將 RDP 會話啟動到目標 Windows 的用戶端的一部分Azure 中的 VM。 在 Windows 10 用戶端上實現此目的的唯一方法是使用 Windows Hello 業務 PIN 或與 RDP 用戶端進行生物識別身份驗證。 在 Windows 10 版本 1809 中，對生物識別身份驗證的支援已添加到 RDP 用戶端。 使用 Windows Hello 業務身份驗證的遠端桌面僅適用于使用證書信任模型且當前不適用於金鑰信任模型的部署。

## <a name="log-in-using-azure-ad-credentials-to-a-windows-vm"></a>使用 Azure AD 憑據登錄到 Windows VM

> [!IMPORTANT]
> 僅允許連接到 Azure AD 的 VM 的遠端連線從 Azure AD 聯接的 Windows 10 電腦或與 VM**加入的同一**目錄的混合 Azure AD。 此外，對於使用 Azure AD 憑據的 RDP，使用者必須屬於兩個 RBAC 角色之一，即虛擬機器管理員登錄或虛擬機器使用者登錄。 此時，Azure 堡壘不能用於使用具有 AADLoginForWindows 副檔名的 Azure 活動目錄身份驗證登錄。 僅支援直接 RDP。

要使用 Azure AD 登錄到 Windows 伺服器 2019 虛擬機器，請執行： 

1. 導航到已啟用 Azure AD 登錄的虛擬機器的概覽頁。
1. 選擇 **"連接**"以打開"連接到虛擬機器"邊欄選項卡。
1. 選擇**下載 RDP 檔**。
1. 選擇 **"打開"** 以啟動遠端桌面連線用戶端。
1. 選擇 **"連接**"以啟動 Windows 登錄對話方塊。
1. 使用 Azure AD 憑據登錄。

現在，您已登錄到 Windows Server 2019 Azure 虛擬機器，具有已分配的角色許可權，例如 VM 使用者或 VM 管理員。 

> [!NOTE]
> 可以保存 。RDP 檔在電腦上的本地啟動將來的遠端桌面連線到虛擬機器，而不必導航到 Azure 門戶中的虛擬機器概覽頁面並使用連接選項。

## <a name="troubleshoot"></a>疑難排解

### <a name="troubleshoot-deployment-issues"></a>為部署問題進行疑難排解

AADLoginForWindows 擴展必須成功安裝才能使 VM 完成 Azure AD 加入過程。 如果 VM 擴展無法正確安裝，請執行以下步驟。

1. RDP 使用本地管理員帳戶到 VM，並在下檢查 CommandExecuti'n.log  
   
   C：_WindowsAzure_Logs_Plugs_微軟.Azure.ActiveDirectory.AADloginForWindows\0.3.1.0。 

   > [!NOTE]
   > 如果擴展在初始失敗後重新開機，則具有部署錯誤的日誌將保存為 CommandExecution_YYYYMMDDHHMMSSSSS.log。 "
1. 在 VM 上打開命令提示，並針對在 Azure 主機上運行的實例中繼資料服務 （IMDS） 終結點驗證這些查詢：

   | 要運行的命令 | 預期的輸出 |
   | --- | --- |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/instance?api-version=2017-08-01"` | 有關 Azure VM 的正確資訊 |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/info?api-version=2018-02-01"` | 與 Azure 訂閱關聯的有效租戶 ID |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/oauth2/token?resource=urn:ms-drs:enterpriseregistration.windows.net&api-version=2018-02-01"` | Azure 活動目錄為分配給此 VM 的託管標識頒發的有效訪問權杖 |

   > [!NOTE]
   > 訪問權杖可以使用 像 的工具解碼[http://calebb.net/](http://calebb.net/)。 驗證訪問權杖中的"appid"與分配給 VM 的託管標識匹配。

1. 確保使用命令列可從 VM 訪問所需的終結點：
   
   - 捲曲 HTTPs：\//login.microsoftonline.com/ -D |
   - 捲曲 HTTPs：\/`<TenantID>`/login.microsoftonline.com/ / -D |

   > [!NOTE]
   > 替換為`<TenantID>`與 Azure 訂閱關聯的 Azure AD 租戶 ID。

   - 捲曲 HTTPs：\//enterpriseregistration.windows.net/ -D -
   - 捲曲 HTTPs：\//device.login.microsoftonline.com/ -D -
   - 捲曲 HTTPs：\//pas.windows.net/ -D -

1. 可以通過運行`dsregcmd /status`來查看設備狀態。 目標是將設備狀態顯示為`AzureAdJoined : YES`。

   > [!NOTE]
   > Azure AD 聯接活動在"使用者設備註冊"和管理日誌下的事件檢視器中捕獲。

如果 AADLoginForWindows 擴展在某些錯誤代碼下失敗，您可以執行以下步驟：

#### <a name="issue-1-aadloginforwindows-extension-fails-to-install-with-terminal-error-code-1007-and-exit-code--2145648574"></a>問題 1：AADLoginForWindows 擴展無法安裝終端錯誤代碼"1007"和結束代碼：-2145648574。

此結束代碼轉換為DSREG_E_MSI_TENANTID_UNAVAILABLE，因為擴展無法查詢 Azure AD 租戶資訊。

1. 驗證 Azure VM 可以從實例中繼資料服務中檢索租戶 ID。

   - RDP 作為本地管理員向 VM 返回 VM，並通過從 VM 上的提升命令列運行此命令來驗證終結點返回有效的租戶 ID：
      
      - 捲曲 -H 中繼資料：truehttp://169.254.169.254/metadata/identity/info?api-version=2018-02-01

1. VM 管理員嘗試安裝 AADLoginForWindows 擴展，但分配了託管標識的系統尚未首先啟用 VM。 導航到 VM 的標識邊欄選項卡。 從"系統分配"選項卡中，驗證狀態切換為"打開"。

#### <a name="issue-2-aadloginforwindows-extension-fails-to-install-with-exit-code--2145648607"></a>問題 2： AADLoginForWindows 擴展無法安裝結束代碼： -2145648607

此結束代碼轉換為DSREG_AUTOJOIN_DISC_FAILED因為擴展無法訪問`https://enterpriseregistration.windows.net`終結點。

1. 使用命令列從 VM 可訪問所需的終結點：

   - 捲曲 HTTPs：\//login.microsoftonline.com/ -D |
   - 捲曲 HTTPs：\/`<TenantID>`/login.microsoftonline.com/ / -D |
   
   > [!NOTE]
   > 替換為`<TenantID>`與 Azure 訂閱關聯的 Azure AD 租戶 ID。 如果需要查找租戶 ID，可以將滑鼠懸停在帳戶名稱上以獲取目錄/租戶 ID，或者選擇 Azure 門戶中>屬性>目錄 ID 中的 Azure 活動目錄。

   - 捲曲 HTTPs：\//enterpriseregistration.windows.net/ -D -
   - 捲曲 HTTPs：\//device.login.microsoftonline.com/ -D -
   - 捲曲 HTTPs：\//pas.windows.net/ -D -

1. 如果任何命令在"無法解析主機`<URL>`"時失敗，請嘗試運行此命令以確定 VM 正在使用的 DNS 伺服器。
   
   `nslookup <URL>`

   > [!NOTE] 
   > 替換為`<URL>`終結點使用的完全限定的功能變數名稱，如"login.microsoftonline.com"。

1. 接下來，查看指定公共 DNS 伺服器是否允許命令成功：

   `nslookup <URL> 208.67.222.222`

1. 如有必要，更改分配給 Azure VM 所屬的網路安全性群組的 DNS 伺服器。

#### <a name="issue-3-aadloginforwindows-extension-fails-to-install-with-exit-code-51"></a>問題 3：AADLoginForWindows 擴展無法使用結束代碼安裝：51

結束代碼 51 轉換為"VM 的作業系統不支援此擴展"。

在公共預覽版上，AADLoginForWindows 擴展僅用於安裝在 Windows 伺服器 2019 或 Windows 10（生成 1809 或更高版本）。 確保支援 Windows 版本。 如果不支援 Windows 生成，請卸載 VM 擴展。

### <a name="troubleshoot-sign-in-issues"></a>對登入問題進行疑難排解

當您嘗試使用 Azure AD 憑據 RDP 時，某些常見錯誤包括未分配 RBAC 角色、未經授權的用戶端或所需的 2FA 登錄方法。 使用以下資訊更正這些問題。

可以通過運行`dsregcmd /status`來查看設備和 SSO 狀態。 目標是使設備狀態顯示為`AzureAdJoined : YES`和。 `SSO State` `AzureAdPrt : YES`

此外，使用 Azure AD 帳戶的 RDP 登錄在 AAD_操作事件日誌下的事件檢視器中捕獲。

#### <a name="rbac-role-not-assigned"></a>未指派 RBAC 角色

如果在啟動與 VM 的遠端桌面連線時看到以下錯誤訊息： 

- 您的帳戶配置為阻止您使用此設備。 有關詳細資訊，請與系統管理員聯繫

![您的帳戶配置為阻止您使用此設備。](./media/howto-vm-sign-in-azure-ad-windows/rbac-role-not-assigned.png)

驗證您是否為授予使用者的虛擬機器管理員登錄或虛擬機器使用者登錄角色授予的 VM 配置了[RBAC 策略](../../virtual-machines/linux/login-using-aad.md)：
 
#### <a name="unauthorized-client"></a>未經授權的用戶端

如果在啟動與 VM 的遠端桌面連線時看到以下錯誤訊息： 

- 您的憑據不起作用

![您的憑據不起作用](./media/howto-vm-sign-in-azure-ad-windows/your-credentials-did-not-work.png)

驗證用於啟動遠端桌面連線的 Windows 10 電腦是 Azure AD 加入的，還是將混合 Azure AD 連接到 VM 加入的同一 Azure AD 目錄。 有關設備標識的詳細資訊，請參閱文章["什麼是設備標識](/azure/active-directory/devices/overview)"。

> [!NOTE]
> Windows 10 20H1，將添加對 Azure AD 註冊 PC 的支援，以啟動到 VM 的遠端桌面連線。 加入 Windows 預覽體驗程式，試用此功能並探索 Windows 10 的新功能。

此外，在 Azure AD 聯接完成後，驗證 AADLoginForWindows 擴展尚未卸載。
 
#### <a name="mfa-sign-in-method-required"></a>所需的 MFA 登錄方法

如果在啟動與 VM 的遠端桌面連線時看到以下錯誤訊息： 

- 不允許使用登錄方法。 請嘗試其他登錄方法或與系統管理員聯繫。

![不允許使用登錄方法。](./media/howto-vm-sign-in-azure-ad-windows/mfa-sign-in-method-required.png)

如果已配置了條件訪問策略，該策略在可以訪問資源之前需要多重要素驗證 （MFA），則需要確保啟動遠端桌面連線到 VM 的 Windows 10 電腦使用強身份驗證方法，如 Windows Hello。 如果不對遠端桌面連線使用增強式驗證方法，您將看到以前的錯誤。

如果您尚未部署適用于企業的 Windows Hello，並且現在不是選項，則可以通過配置條件訪問策略（將"Azure Windows VM 登錄"應用從需要 MFA 的雲應用清單中排除 MFA 要求來排除 MFA 要求。 要瞭解有關適用于企業的 Windows Hello 的詳細資訊，請參閱[Windows Hello 業務概述](/windows/security/identity-protection/hello-for-business/hello-identity-verification)。

> [!NOTE]
> Windows 10 支援多個版本的 Windows Hello 與 RDP 的業務 PIN 身份驗證，但 Windows 10 版本 1809 中添加了對 RDP 生物識別身份驗證的支援。 在 RDP 期間使用適用于業務身份驗證的 Windows Hello 僅適用于使用證書信任模型且當前不適用於金鑰信任模型的部署。
 
## <a name="preview-feedback"></a>預覽意見反應

在[Azure AD 回饋論壇](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)上，使用預覽功能或報告問題的回饋。

## <a name="next-steps"></a>後續步驟

如需有關 Azure Active Directory 的詳細資訊，請參閱[什麼是 Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)
