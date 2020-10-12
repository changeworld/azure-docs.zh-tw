---
title: Windows 虛擬桌面常見問題-Azure
description: Windows 虛擬桌面的常見問題和最佳作法。
author: Heidilohr
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 058c5778c116a9e8368049bf30046aa6b7634163
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88121114"
---
# <a name="windows-virtual-desktop-faq"></a>Windows 虛擬桌面常見問題集

本文將回答常見問題，並說明 Windows 虛擬桌面的最佳作法。

## <a name="what-are-the-minimum-admin-permissions-i-need-to-manage-objects"></a>管理物件所需的最低系統管理員許可權為何？

如果您想要建立主機集區和其他物件，您必須在您正在使用的訂用帳戶或資源群組上，將「參與者」角色指派給您。

您必須將應用程式群組的「使用者存取權管理員」角色指派給您，才能將應用程式群組發佈給使用者或使用者群組。

若要限制系統管理員只能管理使用者會話，例如將訊息傳送給使用者、登出使用者等等，您可以建立自訂角色。 例如：

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

當使用者指派給應用程式群組時，服務會執行簡單的 Azure 角色指派。 因此，使用者的 Azure Active Directory (AD) 和應用程式群組的 Azure AD 必須位於相同的位置。 所有服務物件（例如主機集區、應用程式群組和工作區）也必須位於與使用者相同的 Azure AD。

您可以在不同的 Azure AD 中建立虛擬機器 (Vm) ，只要將 Active Directory 與相同虛擬網路 Azure AD VNET (中的使用者) 同步即可。

## <a name="what-are-location-restrictions"></a>什麼是位置限制？

所有服務資源都有與其相關聯的位置。 主機集區的位置可決定要將主機集區的服務中繼資料儲存在哪一個地理位置。 沒有主機集區的應用程式群組不存在。 如果您將應用程式新增至 RemoteApp 應用程式群組，您也需要工作階段主機來判斷 [開始] 功能表應用程式。 針對任何應用程式群組動作，您也需要在主機集區上有相關的資料存取權。 為了確保資料不會在多個位置之間傳輸，應用程式群組的位置應該與主機集區的位置相同。

工作區也必須位於與其應用程式群組相同的位置。 每次更新工作區時，相關的應用程式群組會隨之更新。 和應用程式群組一樣，此服務會要求所有工作區都與在相同位置中建立的應用程式群組相關聯。

## <a name="how-do-you-expand-an-objects-properties-in-powershell"></a>如何在 PowerShell 中擴充物件的屬性？

當您執行 PowerShell Cmdlet 時，您只會看到資源名稱和位置。

例如：

```powershell
Get-AzWvdHostPool -Name 0224hp -ResourceGroupName 0224rg

Location Name   Type
-------- ----   ----
westus   0224hp Microsoft.DesktopVirtualization/hostpools
```

若要查看資源的所有屬性，請將 `format-list` 或新增 `fl` 至 Cmdlet 的結尾。

例如：

```powershell
Get-AzWvdHostPool -Name 0224hp -ResourceGroupName 0224rg |fl
```

若要查看特定屬性，請在或之後新增特定屬性名稱 `format-list` `fl` 。

例如：

```powershell
Get-AzWvdHostPool -Name demohp -ResourceGroupName 0414rg |fl CustomRdpProperty

CustomRdpProperty : audiocapturemode:i:0;audiomode:i:0;drivestoredirect:s:;redirectclipboard:i:1;redirectcomports:i:0;redirectprinters:i:1;redirectsmartcards:i:1;screen modeid:i:2;
```

## <a name="does-windows-virtual-desktop-support-guest-users"></a>Windows 虛擬桌面是否支援來賓使用者？

Windows 虛擬桌面不支援 Azure AD 來賓使用者帳戶。 例如，假設一組來賓使用者的每位使用者 Microsoft 365 E3、每位使用者的 Windows E3，或在自己的公司中贏得 VDA 授權，但在不同公司的 Azure AD 中是來賓使用者。 另一家公司則會在 Azure AD 中管理來賓使用者的使用者物件，Active Directory 像是本機帳戶。

您無法使用自己的授權取得協力廠商的權益。 此外，Windows 虛擬桌面目前不支援 (MSA) 的 Microsoft 帳戶。

## <a name="why-dont-i-see-the-client-ip-address-in-the-wvdconnections-table"></a>為什麼我在 WVDConnections 資料表中看不到用戶端 IP 位址？

我們目前沒有可靠的方法可收集網頁用戶端的 IP 位址，因此我們不會在資料表中包含該值。

## <a name="how-does-windows-virtual-desktop-handle-backups"></a>Windows 虛擬桌面如何處理備份？

Azure 中有多個選項可處理備份。 您可以使用 Azure 備份、Site Recovery 和快照集。

## <a name="does-windows-virtual-desktop-support-third-party-collaboration-apps"></a>Windows 虛擬桌面是否支援協力廠商共同作業應用程式？

Windows 虛擬桌面目前已針對小組進行優化。 Microsoft 目前不支援縮放等協力廠商共同作業應用程式。 協力廠商組織負責為其客戶提供相容性指導方針。 Windows 虛擬桌面也不支援商務用 Skype。

## <a name="can-i-change-from-pooled-to-personal-host-pools"></a>我可以從共用區變更為個人主機集區嗎？

建立主機集區之後，就無法變更其類型。 不過，您可以將您註冊的任何 Vm 移至不同類型的主機集區。

## <a name="whats-the-largest-profile-size-fslogix-can-handle"></a>FSLogix 可以處理的最大配置檔案大小為何？

FSLogix 中的限制或配額取決於用來儲存使用者設定檔 VHD (X) 檔案的儲存網狀架構。

下表提供 FSLogix 設定檔所需的任何資源支援每個使用者的範例。 視每個設定檔上的使用者、應用程式和活動而定，需求可能會有很大的差異。

| 資源 | 需求 |
|---|---|
| 穩定狀態 IOPS | 10 |
| 登入/登出 IOPS | 50 |

此表格中的範例是單一使用者，但可以用來估計您環境中的使用者總數需求。 例如，您需要大約 1000 IOPS 給100使用者，以及在登入和登出期間大約 5000 IOPS。

## <a name="is-there-a-scale-limit-for-host-pools-created-in-the-azure-portal"></a>Azure 入口網站中建立的主機集區是否有調整規模限制？

這些因素可能會影響主機集區的調整限制：

- Azure 範本的限制為800個物件。 若要深入瞭解，請參閱 [Azure 訂用帳戶和服務限制、配額和條件約束](../azure-resource-manager/management/azure-subscription-service-limits.md#template-limits)。 每個 VM 也會建立大約六個物件，這表示您可以在每次執行範本時建立大約132個 Vm。

- 您可以針對每個區域和每個訂用帳戶建立的核心數目有一些限制。 例如，如果您有 Enterprise 合約訂用帳戶，您可以建立350個核心。 您必須將350除以每個 VM 的預設核心數目或您自己的核心限制，以判斷每次執行範本時可以建立多少 Vm。 若要深入瞭解，請參閱 [虛擬機器限制-Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machines-limits---azure-resource-manager)。

- VM 的首碼名稱和 Vm 數目少於15個字元。 若要深入瞭解，請參閱 [Azure 資源的命名規則和限制](../azure-resource-manager/management/resource-name-rules.md#microsoftcompute)。

## <a name="can-i-manage-windows-virtual-desktop-environments-with-azure-lighthouse"></a>我可以使用 Azure Lighthouse 來管理 Windows 虛擬桌面環境嗎？

Azure Lighthouse 不完全支援管理 Windows 虛擬桌面環境。 因為 Lighthouse 目前不支援跨 Azure AD 租使用者使用者管理，所以 Lighthouse 客戶仍然需要登入客戶用來管理使用者的 Azure AD。

您也無法使用 CSP 沙箱訂閱搭配 Windows 虛擬桌面服務。 若要深入瞭解，請參閱 [整合沙箱帳戶](/partner-center/develop/set-up-api-access-in-partner-center#integration-sandbox-account)。

最後，如果您從 CSP 擁有者帳戶啟用資源提供者，CSP 客戶帳戶將無法修改資源提供者。
