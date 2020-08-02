---
title: Windows 虛擬桌面常見問題-Azure
description: Windows 虛擬桌面的常見問題和最佳作法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 6867d24d84f6dfb51b2ca7b86ec882102b96552b
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87504410"
---
# <a name="windows-virtual-desktop-faq"></a>Windows 虛擬桌面常見問題集

本文提供常見問題的解答，並說明 Windows 虛擬桌面的最佳做法。

## <a name="what-are-the-minimum-admin-permissions-i-need-to-manage-objects"></a>管理物件所需的最低管理員許可權為何？

如果您想要建立主機集區和其他物件，您必須在您所使用的訂用帳戶或資源群組上，獲指派參與者角色。

您必須獲指派應用程式群組的「使用者存取系統管理員」角色，才能將應用程式群組發佈給使用者或使用者群組。

若要限制系統管理員只能管理使用者會話，例如傳送訊息給使用者、登出使用者等等，您可以建立自訂角色。 例如： 

```powershell
"actions": [
"Microsoft.Resources/deployments/operations/read",
"Microsoft.Resources/tags/read",
"Microsoft.Authorization/roleAssignments/read",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/read",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/write",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/write",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/delete"
],
"notActions": [],
"dataActions": [],
"notDataActions": []
}
```

## <a name="does-windows-virtual-desktop-support-split-azure-active-directory-models"></a>Windows 虛擬桌面支援分割 Azure Active Directory 模型嗎？

將使用者指派給應用程式群組時，服務會執行簡單的 Azure 角色指派。 因此，使用者的 Azure Active Directory （AD）和應用程式群組的 Azure AD 必須位於相同的位置。 所有服務物件（例如主機集區、應用程式群組和工作區）也必須與使用者位於相同的 Azure AD。

您可以在不同的 Azure AD 中建立虛擬機器（Vm），只要您在相同的虛擬網路（VNET）中，將 Active Directory 與使用者的 Azure AD 同步。

Azure 燈塔不完全支援管理 Windows 虛擬桌面環境。 由於燈塔目前不支援跨 Azure AD 的租使用者使用者管理，因此燈塔客戶仍然需要登入客戶用來管理使用者的 Azure AD。

## <a name="what-are-location-restrictions"></a>什麼是位置限制？

所有服務資源都有與其相關聯的位置。 主機集區的位置會決定要將主機集區的服務中繼資料儲存在哪一個地理位置。 在沒有主機集區的情況下，應用程式群組不存在。 如果您將應用程式新增至 RemoteApp 應用程式群組，您也需要工作階段主機來決定 [開始] 功能表應用程式。 針對任何應用程式群組動作，您也必須在主機集區上有相關的資料存取權。 為了確保資料不會在多個位置之間傳輸，應用程式群組的位置應該與主機集區的位置相同。

工作區也必須位於與其應用程式群組相同的位置。 每當工作區更新時，相關的應用程式群組會隨之更新。 就像應用程式群組一樣，服務會要求所有工作區都與相同位置中建立的應用程式群組相關聯。

## <a name="how-do-you-expand-an-objects-properties-in-powershell"></a>如何在 PowerShell 中展開物件的屬性？

當您執行 PowerShell Cmdlet 時，您只會看到資源名稱和位置。

例如：

```powershell
Get-AzWvdHostPool -Name 0224hp -ResourceGroupName 0224rg

Location Name   Type
-------- ----   ----
westus   0224hp Microsoft.DesktopVirtualization/hostpools
```

若要查看所有資源的屬性，請將 `format-list` 或新增 `fl` 至 Cmdlet 的結尾。

例如：

```powershell
Get-AzWvdHostPool -Name 0224hp -ResourceGroupName 0224rg |fl
```

若要查看特定屬性，請在或後面加入特定的屬性名稱 `format-list` `fl` 。

例如：

```powershell
Get-AzWvdHostPool -Name demohp -ResourceGroupName 0414rg |fl CustomRdpProperty

CustomRdpProperty : audiocapturemode:i:0;audiomode:i:0;drivestoredirect:s:;redirectclipboard:i:1;redirectcomports:i:0;redirectprinters:i:1;redirectsmartcards:i:1;screen modeid:i:2;
```

## <a name="does-windows-virtual-desktop-support-guest-users"></a>Windows 虛擬桌面是否支援來賓使用者？

Windows 虛擬桌面不支援 Azure AD 來賓使用者帳戶。 例如，假設一組來賓使用者擁有 Microsoft 365 E3 每位使用者、Windows E3 每位使用者，或在自己公司中贏得 VDA 授權，但在不同公司的 Azure AD 中是來賓使用者。 另一家公司則會在 Azure AD 和 Active Directory （例如本機帳戶）中管理來賓使用者的使用者物件。

您不能將自己的授權用於協力廠商的權益。 此外，Windows 虛擬桌面目前不支援 Microsoft 帳戶（MSA）。

## <a name="why-dont-i-see-the-client-ip-address-in-the-wvdconnections-table"></a>為什麼我在 WVDConnections 資料表中看不到用戶端 IP 位址？

我們目前沒有可靠的方法可收集 web 用戶端的 IP 位址，因此我們不會在資料表中包含該值。

## <a name="how-does-windows-virtual-desktop-handle-backups"></a>Windows 虛擬桌面如何處理備份？

Azure 中有多個選項可處理備份。 您可以使用 Azure 備份、Site Recovery 和快照集。

## <a name="does-windows-virtual-desktop-support-third-party-collaboration-apps"></a>Windows 虛擬桌面是否支援協力廠商協同作業應用程式？

Windows 虛擬桌面目前已針對小組進行優化。 Microsoft 目前不支援 Zoom 等協力廠商共同作業應用程式。 協力廠商組織會負責為其客戶提供相容性指導方針。 Windows 虛擬桌面也不支援商務用 Skype。

## <a name="can-i-change-from-pooled-to-personal-host-pools"></a>我可以從共用集區變更為個人主機集區嗎？

建立主機集區之後，就無法變更其類型。 不過，您可以將註冊到主機集區的任何 Vm 移至不同類型的主機集區。

## <a name="whats-the-largest-profile-size-fslogix-can-handle"></a>最大的配置檔案大小 FSLogix 可處理的內容為何？

FSLogix 中的限制或配額取決於用來儲存使用者設定檔 VHD （X）檔案的存放網狀架構。

下表提供 FSLogix 設定檔支援每個使用者所需資源的範例。 視每個設定檔的使用者、應用程式和活動而定，需求可能會有很大的差異。 

| 資源 | 需求 |
|---|---|
| 穩定狀態 IOPS | 10 |
| 登入/登出 IOPS | 50 |

此表格中的範例是單一使用者，但可用來估計您環境中的使用者總數需求。 例如，您需要100位使用者大約 1000 IOPS，以及在登入和登出期間的 5000 IOPS。

## <a name="is-there-a-scale-limit-for-host-pools-created-in-the-azure-portal"></a>在 Azure 入口網站中建立的主機集區是否有調整限制？

這些因素可能會影響主機集區的調整限制：

- Azure 範本的限制為800個物件。 若要深入瞭解，請參閱[Azure 訂用帳戶和服務限制、配額和條件約束](../azure-resource-manager/management/azure-subscription-service-limits.md#template-limits)。 每個 VM 也會建立大約六個物件，因此每次執行範本時，您都可以建立大約132的 Vm。

- 針對每個區域和每個訂用帳戶，您可以建立的核心數目有限制。 例如，如果您有 Enterprise 合約訂用帳戶，您可以建立350核心。 您必須將350除以每個 VM 的預設核心數目或您自己的核心限制，以決定每次執行範本時可以建立的 Vm 數目。 若要深入瞭解，請[虛擬機器限制-Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machines-limits---azure-resource-manager)。

- VM 首碼名稱和 Vm 數目少於15個字元。 若要深入瞭解，請參閱[Azure 資源的命名規則和限制](../azure-resource-manager/management/resource-name-rules.md#microsoftcompute)。