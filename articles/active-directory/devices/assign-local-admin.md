---
title: 如何在已加入 Azure AD 的裝置上管理本機系統管理員
description: 了解如何將 Azure 角色指派給 Windows 裝置的本機系統管理員群組。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: f705150f927a08b5ca2f91b702ee0853766ac23a
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96511112"
---
# <a name="how-to-manage-the-local-administrators-group-on-azure-ad-joined-devices"></a>如何管理已加入 Azure AD 的裝置上的本機系統管理員群組

若要管理 Windows 裝置，您必須是本機系統管理員群組的成員。 在 Azure Active Directory (Azure AD) 加入程序中，Azure AD 會在裝置上更新此群組的成員資格。 您可以自訂成員資格更新，以符合自己的商務需求。 舉例來說，如果您想要讓技術服務人員能夠在裝置上執行需要系統管理員權限的工作，成員資格更新就有其效益。

本文說明本機系統管理員成員資格更新的運作方式，以及如何在 Azure AD 聯結期間進行自訂。 本文內容不適用於 **混合式 Azure AD 加入** 的裝置。

## <a name="how-it-works"></a>運作方式

當您使用 Azure AD 聯結將 Windows 裝置與 Azure AD 連線時，Azure AD 會將下列安全性主體新增至裝置上的本機系統管理員群組：

- Azure AD 全域管理員角色
- Azure AD 裝置管理員角色 
- 執行 Azure AD Join 的使用者   

藉由將 Azure AD 角色新增至本機系統管理員群組，您可以直接更新能夠隨時在 Azure AD 中管理裝置的使用者，而無須對裝置進行任何修改。 目前，您無法將群組指派給系統管理員角色。
Azure AD 也會將 Azure AD 裝置管理員角色新增至本機系統管理員群組，以支援最低權限 (PoLP) 的準則。 除了全域管理員以外，您也可以讓 *僅* 被指派裝置管理員角色的使用者管理裝置。 

## <a name="manage-the-global-administrators-role"></a>管理全域管理員角色

若要檢視及更新全域管理員角色的成員資格，請參閱：

- [檢視 Azure Active Directory 中的所有系統管理員角色成員](../roles/manage-roles-portal.md)
- [在 Azure Active Directory 中將使用者指派給系統管理員角色](../fundamentals/active-directory-users-assign-role-azure-portal.md)


## <a name="manage-the-device-administrator-role"></a>管理裝置管理員角色 

在 Azure 入口網站中，您可以在 [裝置] 頁面上管理裝置管理員角色。 若要開啟 [裝置] 頁面：

1. 以全域管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋並選取 [Azure Active Directory]  。
1. 在 [管理] 區段中，按一下 [裝置]。
1. 在 [裝置] 頁面上，按一下 [裝置設定]。

若要修改裝置管理員角色，請設定 **加入 Azure AD 的裝置上的其他本機系統管理員**。  

![其他本機系統管理員](./media/assign-local-admin/10.png)

>[!NOTE]
> 此選項需要 Azure AD Premium 租用戶。 

裝置管理員會指派給所有加入 Azure AD 的裝置。 您無法將裝置管理員的範圍設定為一組特定的裝置。 更新裝置管理員角色不一定會對受影響的使用者產生直接的影響。 在使用者已登入的裝置上，執行下列 *兩個* 動作時，會發生許可權提升：

- 針對 Azure AD，已超過4小時，可使用適當的許可權來發出新的主要重新整理權杖。 
- 使用者登出並重新登入，而不是鎖定/解除鎖定，以重新整理其設定檔。

>[!NOTE]
> 上述動作不適用於先前尚未登入相關裝置的使用者。 在此情況下，系統管理員許可權會在使用者第一次登入裝置之後立即套用。 

## <a name="manage-administrator-privileges-using-azure-ad-groups-preview"></a>使用 Azure AD 群組 (預覽版來管理系統管理員許可權) 

>[!NOTE]
> 此功能目前為預覽狀態。


從 Windows 10 2004 更新開始，您可以使用 Azure AD 群組，以 [受限的群組](/windows/client-management/mdm/policy-csp-restrictedgroups) MDM 原則來管理 Azure AD 已加入裝置上的系統管理員許可權。 此原則可讓您將個別使用者或 Azure AD 群組指派給已加入 Azure AD 的裝置上的本機系統管理員群組，讓您可以針對不同的裝置群組設定不同的系統管理員。 

>[!NOTE]
> 開始 Windows 10 20H2 更新時，建議使用 [本機使用者和群組](/windows/client-management/mdm/policy-csp-localusersandgroups) 原則，而不是限制的群組原則


目前，Intune 中沒有任何 UI 可用來管理這些原則，而且必須使用 [自訂 Oma-uri 設定](/mem/intune/configuration/custom-settings-windows-10)來設定它們。 使用其中一種原則的幾個考慮： 

- 透過原則新增 Azure AD 群組需要可透過執行群組 API 取得的群組 SID。 SID 是由群組 API 中的屬性所定義 `securityIdentifier` 。
- 強制使用受限制的群組原則時，會移除不在成員清單上之群組的目前成員。 因此，使用新的成員或群組來強制執行此原則，將會移除現有的系統管理員，也就是已加入裝置的使用者、裝置系統管理員角色，以及裝置的全域管理員角色。 若要避免移除現有的成員，您必須將它們設定為 [受限制的群組] 原則中成員清單的一部分。 如果您使用允許對群組成員資格進行累加式更新的本機使用者和群組原則，就會解決這項限制
- 使用這兩個原則的系統管理員許可權只會針對 Windows 10 裝置上的下列已知群組進行評估：系統管理員、使用者、來賓、Power Users、遠端桌面使用者和遠端系統管理使用者。 
- 使用 Azure AD 群組來管理本機系統管理員，並不適用于混合式 Azure AD 已加入或 Azure AD 註冊的裝置。
- 雖然受限制的群組原則在 Windows 10 2004 更新之前就已存在，但不支援將 Azure AD 群組作為裝置本機系統管理員群組的成員。 

## <a name="manage-regular-users"></a>管理一般使用者

根據預設，Azure AD 會將執行 Azure AD Join 的使用者新增至裝置的系統管理員群組。 如果您想要防止一般使用者成為本機系統管理員，您會有下列選項：

- [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot) - Windows Autopilot 可讓您選擇防止執行加入的主要使用者成為本機系統管理員。 您可以藉由[建立 Autopilot 設定檔](/intune/enrollment-autopilot#create-an-autopilot-deployment-profile)來完成這項作業。
- [大量註冊](/intune/windows-bulk-enroll) - 在大量註冊的內容中執行的 Azure AD Join，會在自動建立的使用者內容中執行。 在裝置加入後登入的使用者不會新增至系統管理員群組。   

## <a name="manually-elevate-a-user-on-a-device"></a>手動提高使用者在裝置上的權限 

除了使用 Azure AD Join 程序以外，您也可以手動提高一般使用者的權限，使其成為一個特定裝置的本機系統管理員。 若要執行此步驟，您必須已是本機系統管理員群組的成員。 

從 **Windows 10 1709** 版開始，您可以從 [ **設定-> 帳戶] > 其他使用者** 執行這項工作。 選取 [新增工作或學校使用者]，並且在 [使用者帳戶] 下輸入使用者的 UPN，然後選取 [帳戶類型] 下的 [管理員]  
 
此外，您也可以使用命令提示字元來新增使用者：

- 如果您的租用戶使用者是從內部部署 Active Directory 同步處理的，請使用 `net localgroup administrators /add "Contoso\username"`。
- 如果您的租用戶使用者是在 Azure AD 中建立的，請使用 `net localgroup administrators /add "AzureAD\UserUpn"`

## <a name="considerations"></a>考量 

您無法將群組指派給裝置管理員角色，而只能指派個別使用者。

裝置管理員會指派給所有加入 Azure AD 的裝置。 這些管理員無法以一組特定的裝置為範圍。

當您從裝置管理員角色中移除使用者時，這些使用者只要仍在裝置上處於登入狀態，就仍具有本機系統管理員權限。 發行新的主要重新整理權杖時，會在下次登入時撤銷此許可權。 這種撤銷與權限提高的類似，最多可能需要4小時的時間。

## <a name="next-steps"></a>後續步驟

- 若要取得在 Azure 入口網站中管理裝置的概觀，請參閱[使用 Azure 入口網站來管理裝置](device-management-azure-portal.md)
- 若要深入瞭解裝置型條件式存取，請參閱 [設定 Azure Active Directory 以裝置為基礎的條件式存取原則](../conditional-access/require-managed-devices.md)。
