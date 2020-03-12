---
title: 建立 Windows 虛擬桌面主機集區 PowerShell-Azure
description: 如何使用 PowerShell Cmdlet 在 Windows 虛擬桌面中建立主機集區。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0ab876a871037908981c95a962f53e1936e31905
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128152"
---
# <a name="create-a-host-pool-with-powershell"></a>使用 PowerShell 建立主機集區

主機集區是 Windows 虛擬桌面租用戶環境中一或多個相同虛擬機器的集合。 每個主機集區都可以包含一個應用程式群組，而使用者可如同在實體桌面上與其互動。

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>使用您的 PowerShell 用戶端建立主機集區

首先，[下載並匯入 Windows 虛擬桌面的 PowerShell 模組](/powershell/windows-virtual-desktop/overview/)，以在您的 PowerShell 工作階段中使用 (如果您還沒這麼做的話)。

執行下列 Cmdlet 以登入 Windows 虛擬桌面環境

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

接下來，執行這個指令程式，在您的 Windows 虛擬桌面租使用者中建立新的主機集區：

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

執行下一個 Cmdlet 來建立註冊權杖，以授權工作階段主機加入主機集區，並將它儲存到本機電腦上的新檔案。 您可以使用-ExpirationHours 參數指定註冊權杖有效的時間長度。

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token | Out-File -FilePath <PathToRegFile>
```

之後，執行這個指令程式可將 Azure Active Directory 使用者新增至主機集區的預設桌面應用程式群組。

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

**RdsAppGroupUser**指令程式不支援新增安全性群組，而且一次只會將一位使用者新增到應用程式群組。 如果您想要將多個使用者新增至應用程式群組，請使用適當的使用者主體名稱重新執行 Cmdlet。

執行下列 Cmdlet，將註冊權杖匯出至變數，稍後將在向[Windows 虛擬桌面主機集區註冊虛擬機器](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool)中使用。

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>建立主機集區的虛擬機器

現在您可以建立可聯結至 Windows 虛擬桌面主機集區的 Azure 虛擬機器。

您可以使用多種方式來建立虛擬機器：

- [從 Azure 資源庫映射建立虛擬機器](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [從受控映射建立虛擬機器](../virtual-machines/windows/create-vm-generalized-managed.md)
- [從非受控映射建立虛擬機器](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image.md)

>[!NOTE]
>如果您要使用 Windows 7 作為主機 OS 來部署虛擬機器，建立和部署程式將會稍有不同。 如需詳細資訊，請參閱[在 Windows 虛擬桌面上部署 Windows 7 虛擬機器](deploy-windows-7-virtual-machine.md)。

建立工作階段主機虛擬機器之後，請[將 Windows 授權套用至工作階段主機 VM](./apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm) ，以執行您的 Windows 或 windows Server 虛擬機器，而不需支付另一份授權。 

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>準備 Windows 虛擬桌面代理程式安裝的虛擬機器

您必須先執行下列動作來準備您的虛擬機器，才能安裝 Windows 虛擬桌面代理程式，並向您的 Windows 虛擬桌面主機集區註冊虛擬機器：

- 您必須將電腦加入網域。 這可讓傳入 Windows 虛擬桌面的使用者從其 Azure Active Directory 帳戶對應到其 Active Directory 帳戶，並成功地允許存取虛擬機器。
- 如果虛擬機器正在執行 Windows Server 作業系統，您必須安裝遠端桌面工作階段主機（RDSH）角色。 RDSH 角色可讓 Windows 虛擬桌面代理程式正確安裝。

若要成功加入網域，請在每部虛擬機器上執行下列操作：

1. 使用您在建立虛擬機器時提供的認證[連接到虛擬機器](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)。
2. 在虛擬機器上，啟動 [**控制台**]，然後選取 [**系統**]。
3. 選取 [**電腦名稱稱**]，選取 [**變更設定**]，然後選取 [**變更 ...** ]
4. 選取 [**網域**]，然後在虛擬網路上輸入 Active Directory 網域。
5. 使用具有網域加入電腦許可權的網域帳戶進行驗證。

    >[!NOTE]
    > 如果您要將 VM 加入 Azure Active Directory Domain Services (Azure AD DS) 環境，請確定您的網域加入使用者也是 [AAD DC 系統管理員群組](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group)的成員。

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>向 Windows 虛擬桌面主機集區註冊虛擬機器

將虛擬機器註冊到 Windows 虛擬桌面主機集區，就像安裝 Windows 虛擬桌面代理程式一樣簡單。

若要註冊 Windows 虛擬桌面代理程式，請在每部虛擬機器上執行下列動作：

1. 使用您在建立虛擬機器時提供的認證[連接到虛擬機器](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)。
2. 下載並安裝 Windows 虛擬桌面代理程式。
   - 下載[Windows 虛擬桌面代理程式](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv)。
   - 以滑鼠右鍵按一下下載的安裝程式，選取 [**屬性**]，選取 [**解除封鎖**]，然後選取 **[確定]** 。 這可讓您的系統信任安裝程式。
   - 執行安裝程式。 當安裝程式要求您提供註冊權杖時，請輸入您從**RdsRegistrationInfo** Cmdlet 取得的值。
3. 下載並安裝 Windows 虛擬桌面代理程式開機載入器。
   - 下載[Windows 虛擬桌面代理程式](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH)開機載入器。
   - 以滑鼠右鍵按一下下載的安裝程式，選取 [**屬性**]，選取 [**解除封鎖**]，然後選取 **[確定]** 。 這可讓您的系統信任安裝程式。
   - 執行安裝程式。

>[!IMPORTANT]
>為了保護您在 Azure 中的 Windows 虛擬桌面環境，建議您不要在 VM 上開啟輸入連接埠 3389。 Windows 虛擬桌面不需要開啟輸入連接埠 3389 讓使用者存取主機集區的 VM。 如果您為了要進行疑難排解而必須開啟連接埠 3389，建議您使用 [Just-In-Time VM 存取](../security-center/security-center-just-in-time.md)。

## <a name="next-steps"></a>後續步驟

既然您已建立主機集區，您可以在其中填入 Remoteapp。 若要深入了解如何管理 Windows 虛擬桌面中的應用程式，請參閱「管理應用程式群組」教學課程。

> [!div class="nextstepaction"]
> [管理應用程式群組教學課程](./manage-app-groups.md)
