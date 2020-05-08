---
title: 建立 Windows 虛擬桌面主機集區 PowerShell-Azure
description: 如何使用 PowerShell Cmdlet 在 Windows 虛擬桌面中建立主機集區。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 474eb4f5247aeb77edce0ebfde1611bf2deef493
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2020
ms.locfileid: "82930398"
---
# <a name="create-a-host-pool-with-powershell"></a>使用 PowerShell 建立主機集區

>[!IMPORTANT]
>此內容適用于具有 Azure Resource Manager Windows 虛擬桌面物件的春季2020更新。 如果您使用的是 Windows 虛擬桌面不含 Azure Resource Manager 物件的2019版，請參閱[這篇文章](./virtual-desktop-fall-2019/create-host-pools-powershell-2019.md)。
>
> Windows 虛擬桌面春季2020更新目前為公開預覽狀態。 此預覽版本是在沒有服務等級協定的情況下提供，不建議針對生產環境工作負載使用。 可能不支援特定功能，或可能已經限制功能。 
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

主機集區是 Windows 虛擬桌面租用戶環境中一或多個相同虛擬機器的集合。 每個主機集區都可以與多個 RemoteApp 群組、一個桌面應用程式群組，以及多個工作階段主機相關聯。

## <a name="prerequisites"></a>Prerequisites

本文假設您已遵循[設定 PowerShell 模組](powershell-module.md)中的指示。

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>使用您的 PowerShell 用戶端建立主機集區

執行下列 Cmdlet 來登入 Windows 虛擬桌面環境：

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -HostPoolType <Pooled|Personal> -LoadBalancerType <BreadthFirst|DepthFirst|Persistent> -Location <region> -DesktopAppGroupName <appgroupname> 
```

此 Cmdlet 會建立主機集區、工作區和桌面應用程式群組。 此外，它會將桌面應用程式群組註冊至工作區。 您可以使用此 Cmdlet 來建立工作區，或使用現有的工作區。 

執行下一個 Cmdlet 來建立註冊權杖，以授權工作階段主機加入主機集區，並將它儲存到本機電腦上的新檔案。 您可以使用-ExpirationHours 參數指定註冊權杖有效的時間長度。

>[!NOTE]
>權杖的到期日不能少於一小時，也不能超過一個月。 如果您在該限制外設定 *-ExpirationTime* ，此 Cmdlet 將不會建立權杖。

```powershell
New-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> -ExpirationTime $((get-date).ToUniversalTime().AddDays(1).ToString('yyyy-MM-ddTHH:mm:ss.fffffffZ'))
```

例如，如果您想要建立在兩個小時內過期的權杖，請執行此 Cmdlet： 

```powershell
New-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> -ExpirationTime $((get-date).ToUniversalTime().AddHours(2).ToString('yyyy-MM-ddTHH:mm:ss.fffffffZ')) 
```

之後，執行這個指令程式可將 Azure Active Directory 使用者新增至主機集區的預設桌面應用程式群組。

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+"-DAG"> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups' 
```

執行此下一個 Cmdlet，將 Azure Active Directory 使用者群組新增至主機集區的預設桌面應用程式群組：

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+“-DAG”> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

執行下列 Cmdlet，將註冊權杖匯出至變數，稍後將在向[Windows 虛擬桌面主機集區註冊虛擬機器](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool)中使用。

```powershell
$token = Get-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> 
```

## <a name="create-virtual-machines-for-the-host-pool"></a>建立主機集區的虛擬機器

現在您可以建立可聯結至 Windows 虛擬桌面主機集區的 Azure 虛擬機器。

您可以使用多種方式來建立虛擬機器：

- [從 Azure 資源庫映射建立虛擬機器](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [從受控映射建立虛擬機器](../virtual-machines/windows/create-vm-generalized-managed.md)
- [從非受控映射建立虛擬機器](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-user-image-data-disks)

>[!NOTE]
>如果您要使用 Windows 7 作為主機 OS 來部署虛擬機器，建立和部署程式將會稍有不同。 如需詳細資訊，請參閱[在 Windows 虛擬桌面上部署 Windows 7 虛擬機器](./virtual-desktop-fall-2019/deploy-windows-7-virtual-machine.md)。

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
   - 執行安裝程式。 當安裝程式要求您提供註冊權杖時，請輸入您從**RdsRegistrationInfo** Cmdlet 取得的值。
3. 下載並安裝 Windows 虛擬桌面代理程式開機載入器。
   - 下載[Windows 虛擬桌面代理程式](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH)開機載入器。
   - 執行安裝程式。

>[!IMPORTANT]
>為了保護您在 Azure 中的 Windows 虛擬桌面環境，建議您不要在 VM 上開啟輸入連接埠 3389。 Windows 虛擬桌面不需要開啟輸入連接埠 3389 讓使用者存取主機集區的 VM。 如果您為了要進行疑難排解而必須開啟連接埠 3389，建議您使用 [Just-In-Time VM 存取](../security-center/security-center-just-in-time.md)。 我們也建議您不要將 Vm 指派給公用 IP。

## <a name="next-steps"></a>後續步驟

既然您已建立主機集區，您可以在其中填入 Remoteapp。 若要深入了解如何管理 Windows 虛擬桌面中的應用程式，請參閱「管理應用程式群組」教學課程。

> [!div class="nextstepaction"]
> [管理應用程式群組教學課程](./manage-app-groups.md)
