---
title: 建立 Windows 虛擬桌面秋季2019主機集區 PowerShell-Azure
description: 如何使用 PowerShell Cmdlet 在 Windows 虛擬桌面中建立主機集區。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 552a3fdd20d67d81fe0c6a81a965fd3231da643d
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86527637"
---
# <a name="create-a-host-pool-with-powershell"></a>使用 PowerShell 建立主機集區

>[!IMPORTANT]
>此內容適用於不支援 Azure Resource Manager Windows 虛擬桌面物件的 2019 年秋季版本。 如果您嘗試管理 2020 年春季版更新中引進的 Azure Resource Manager Windows 虛擬桌面物件，請參閱[這篇文章](../create-host-pools-powershell.md)。

主機集區是 Windows 虛擬桌面租用戶環境中一或多個相同虛擬機器的集合。 每個主機集區都可以包含一個應用程式群組，而使用者可如同在實體桌面上與其互動。

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>使用 PowerShell 用戶端建立主機集區

首先，[下載並匯入 Windows 虛擬桌面的 PowerShell 模組](/powershell/windows-virtual-desktop/overview/)，以在您的 PowerShell 工作階段中使用 (如果您還沒這麼做的話)。

執行下列 Cmdlet 來登入 Windows 虛擬桌面環境

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

接下來，執行這個 Cmdlet，在您的 Windows 虛擬桌面租用戶中建立新的主機集區：

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

執行下一個 Cmdlet 以建立註冊權杖來授權工作階段主機加入主機集區，並將其儲存到本機電腦上的新檔案。 您可使用 -ExpirationHours 參數指定註冊權杖的有效期長短。

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token | Out-File -FilePath <PathToRegFile>
```

然後執行此 Cmdlet，以將 Azure Active Directory 使用者新增至主機集區的預設桌面應用程式群組。

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

**Add-RdsAppGroupUser** Cmdlet 不支援新增安全性群組，而且一次只會將一位使用者新增至應用程式群組。 如果您想要將多個使用者新增至應用程式群組，請使用適當的使用者主體名稱重新執行 Cmdlet。

執行下列 Cmdlet，以將註冊權杖匯出為變數，稍後用以[將虛擬機器註冊至 Windows 虛擬桌面主機集區](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool)。

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>建立主機集區的虛擬機器

現在即可建立能夠加入 Windows 虛擬桌面主機集區的 Azure 虛擬機器。

您有多種方式可建立虛擬機器：

- [從 Azure 資源庫映像建立虛擬機器](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [從受控映像建立虛擬機器](../../virtual-machines/windows/create-vm-generalized-managed.md)
- [從非受控映像建立虛擬機器](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

>[!NOTE]
>如果要使用 Windows 7 作為主機 OS 來部署虛擬機器，則建立和部署程序會略有不同。 如需詳細資料，請參閱[在 Windows 虛擬桌面上部署 Windows 7 虛擬機器](deploy-windows-7-virtual-machine.md)。

建立工作階段主機虛擬機器之後，[將 Windows 授權套用至工作階段主機 VM](../apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm)，不用支付其他授權費用即可執行 Windows 或 Windows Server 虛擬機器。

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>準備虛擬機器以安裝 Windows 虛擬桌面代理程式

您必須先執行下列工作來備妥虛擬機器，才能安裝 Windows 虛擬桌面代理程式，並向 Windows 虛擬桌面主機集區註冊虛擬機器：

- 電腦必須加入網域。 這可讓進入的 Windows 虛擬桌面使用者能從其 Azure Active Directory 帳戶對應至其 Active Directory 帳戶，以成功存取虛擬機器。
- 如果虛擬機器執行的是 Windows Server 作業系統，則必須安裝遠端桌面工作階段主機 (RDSH) 角色。 RDSH 角色可供正確安裝 Windows 虛擬桌面代理程式。

為成功加入網域，請在每部虛擬機器上執行下列作業：

1. 使用建立虛擬機器時所提供的認證來[連線到虛擬機器](../../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)。
2. 在虛擬機器上，啟動 [控制台]，然後選取 [系統]。
3. 依序選取 [電腦名稱]、[變更設定] 以及 [變更...]。
4. 選取 [網域]，然後輸入虛擬網路上的 Active Directory 網域。
5. 使用具有加入網域電腦權限的網域帳戶進行驗證。

    >[!NOTE]
    > 如果您要將 VM 加入 Azure Active Directory Domain Services (Azure AD DS) 環境，請確定您的網域加入使用者也是 [AAD DC 系統管理員群組](../../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group)的成員。

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>向 Windows 虛擬桌面主機集區註冊虛擬機器

向 Windows 虛擬桌面主機集區註冊虛擬機器，就像安裝 Windows 虛擬桌面代理程式一樣簡單。

請在每部虛擬機器上執行下列作業，以註冊 Windows 虛擬桌面代理程式：

1. 使用建立虛擬機器時所提供的認證來[連線到虛擬機器](../../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)。
2. 下載並安裝 Windows 虛擬桌面代理程式。
   - 下載 [Windows 虛擬桌面代理程式](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv)。
   - 在下載的安裝程式上按一下滑鼠右鍵，選取 [內容]，選取 [解除封鎖]，然後選取 [確定]。 這可讓您的系統信任安裝程式。
   - 執行安裝程式。 當安裝程式要求提供註冊權杖時，請輸入從 **Export-RdsRegistrationInfo** Cmdlet 取得的值。
3. 下載並安裝 Windows 虛擬桌面代理程式開機載入器。
   - 下載 [Windows 虛擬桌面代理程式開機載入器](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH)。
   - 在下載的安裝程式上按一下滑鼠右鍵，選取 [內容]，選取 [解除封鎖]，然後選取 [確定]。 這可讓您的系統信任安裝程式。
   - 執行安裝程式。

>[!IMPORTANT]
>為了保護您在 Azure 中的 Windows 虛擬桌面環境，建議您不要在 VM 上開啟輸入連接埠 3389。 Windows 虛擬桌面不需要開啟輸入連接埠 3389 讓使用者存取主機集區的 VM。 如果您為了要進行疑難排解而必須開啟連接埠 3389，建議您使用 [Just-In-Time VM 存取](../../security-center/security-center-just-in-time.md)。

## <a name="next-steps"></a>後續步驟

建立好主機集區之後，即可在其中填入 RemoteApp。 若要深入了解如何管理 Windows 虛擬桌面中的應用程式，請參閱「管理應用程式群組」教學課程。

> [!div class="nextstepaction"]
> [管理應用程式群組教學課程](../manage-app-groups.md)
