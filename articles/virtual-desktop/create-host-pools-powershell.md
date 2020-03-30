---
title: 創建 Windows 虛擬桌面主機池 PowerShell - Azure
description: 如何使用 PowerShell Cmdlet 在 Windows 虛擬桌面中創建主機池。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: b390c0beb20b7557294c18f889a0f41023513e2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246954"
---
# <a name="create-a-host-pool-with-powershell"></a>使用 PowerShell 建立主機集區

主機集區是 Windows 虛擬桌面租用戶環境中一或多個相同虛擬機器的集合。 每個主機集區都可以包含一個應用程式群組，而使用者可如同在實體桌面上與其互動。

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>使用 PowerShell 用戶端創建主機池

首先，[下載並匯入 Windows 虛擬桌面的 PowerShell 模組](/powershell/windows-virtual-desktop/overview/)，以在您的 PowerShell 工作階段中使用 (如果您還沒這麼做的話)。

運行以下 Cmdlet 以登錄到 Windows 虛擬桌面環境

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

接下來，運行此 Cmdlet 以在 Windows 虛擬桌面租戶中創建新的主機池：

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

運行下一個 Cmdlet 以創建註冊權杖，以授權工作階段主機加入主機池並將其保存到本地電腦上的新檔中。 您可以使用 -過期小時參數指定註冊權杖的有效期。

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token | Out-File -FilePath <PathToRegFile>
```

之後，運行此 Cmdlet 以將 Azure 活動目錄使用者添加到主機池的預設桌面應用組。

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

**Add-RdsAppGroupUser** Cmdlet 不支援添加安全性群組，並且一次只向應用組添加一個使用者。 如果要將多個使用者添加到應用組，請使用適當的使用者主體名稱重新運行 Cmdlet。

運行以下 Cmdlet 將註冊權杖匯出到變數，稍後將在[將虛擬機器註冊到 Windows 虛擬桌面主機池](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool)中使用該變數。

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>為主機池創建虛擬機器

現在，您可以創建可加入到 Windows 虛擬桌面主機池的 Azure 虛擬機器。

您可以通過多種方式創建虛擬機器：

- [從 Azure 庫映射創建虛擬機器](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [從託管映射創建虛擬機器](../virtual-machines/windows/create-vm-generalized-managed.md)
- [從非託管映射創建虛擬機器](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-user-image-data-disks)

>[!NOTE]
>如果要使用 Windows 7 作為主機作業系統部署虛擬機器，則創建和部署過程將略有不同。 有關詳細資訊，請參閱在[Windows 虛擬桌面上部署 Windows 7 虛擬機器](deploy-windows-7-virtual-machine.md)。

創建工作階段主機虛擬機器後，[將 Windows 許可證應用於工作階段主機 VM](./apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm)以運行 Windows 或 Windows Server 虛擬機器，而無需支付其他許可證。 

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>為 Windows 虛擬桌面代理安裝準備虛擬機器

在安裝 Windows 虛擬桌面代理並將虛擬機器註冊到 Windows 虛擬桌面主機池之前，您需要執行以下操作來準備虛擬機器：

- 您必須域加入電腦。 這允許傳入的 Windows 虛擬桌面使用者將其 Azure 活動目錄帳戶映射到其活動目錄帳戶，並成功允許訪問虛擬機器。
- 如果虛擬機器正在運行 Windows 伺服器作業系統，則必須安裝遠端桌面工作階段主機 （RDSH） 角色。 RDSH 角色允許 Windows 虛擬桌面代理正確安裝。

要成功加入域，在每個虛擬機器上執行以下操作：

1. 使用創建虛擬機器時提供的憑據[連接到虛擬機器](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)。
2. 在虛擬機器上，啟動**控制台**並選擇 **"系統**"。
3. 選擇**電腦名稱稱**，選擇 **"更改設置**"，然後選擇 **"更改..."**
4. 選擇 **"域**"，然後在虛擬網路上輸入活動目錄域。
5. 使用具有域加入電腦許可權的域帳戶進行身份驗證。

    >[!NOTE]
    > 如果您要將 VM 加入 Azure Active Directory Domain Services (Azure AD DS) 環境，請確定您的網域加入使用者也是 [AAD DC 系統管理員群組](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group)的成員。

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>將虛擬機器註冊到 Windows 虛擬桌面主機池

將虛擬機器註冊到 Windows 虛擬桌面主機池與安裝 Windows 虛擬桌面代理一樣簡單。

要註冊 Windows 虛擬桌面代理，請在每台虛擬機器上執行以下操作：

1. 使用創建虛擬機器時提供的憑據[連接到虛擬機器](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)。
2. 下載並安裝 Windows 虛擬桌面代理。
   - 下載[Windows 虛擬桌面代理](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv)。
   - 按右鍵下載的安裝程式，選擇 **"屬性****"，選擇"取消阻止"，** 然後選擇 **"確定**"。 這將允許您的系統信任安裝程式。
   - 執行安裝程式。 當安裝程式要求您提供註冊權杖時，輸入從**匯出 Rds註冊資訊**Cmdlet 獲得的值。
3. 下載並安裝 Windows 虛擬桌面代理引導載入程式。
   - 下載[Windows 虛擬桌面代理引導載入程式](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH)。
   - 按右鍵下載的安裝程式，選擇 **"屬性****"，選擇"取消阻止"，** 然後選擇 **"確定**"。 這將允許您的系統信任安裝程式。
   - 執行安裝程式。

>[!IMPORTANT]
>為了保護您在 Azure 中的 Windows 虛擬桌面環境，建議您不要在 VM 上開啟輸入連接埠 3389。 Windows 虛擬桌面不需要開啟輸入連接埠 3389 讓使用者存取主機集區的 VM。 如果您為了要進行疑難排解而必須開啟連接埠 3389，建議您使用 [Just-In-Time VM 存取](../security-center/security-center-just-in-time.md)。

## <a name="next-steps"></a>後續步驟

現在，您已經製作了一個主機池，您可以使用 RemoteApps 填充它。 若要深入了解如何管理 Windows 虛擬桌面中的應用程式，請參閱「管理應用程式群組」教學課程。

> [!div class="nextstepaction"]
> [管理應用程式群組教學課程](./manage-app-groups.md)
