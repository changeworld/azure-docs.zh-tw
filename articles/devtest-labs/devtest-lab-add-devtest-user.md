---
title: 在 Azure DevTest Labs 中新增擁有者和使用者 | Microsoft Docs
description: 使用 Azure 入口網站或 PowerShell 在 Azure DevTest Labs 中新增擁有者和使用者
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 6d6af68cd663e88af90d690375a4d45c538aad1d
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92330184"
---
# <a name="add-owners-and-users-in-azure-devtest-labs"></a>在 Azure DevTest Labs 中新增擁有者和使用者
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/How-to-set-security-in-your-DevTest-Lab/player]
> 
> 

Azure DevTest Labs 中的存取權是由 [AZURE RBAC)  (azure 角色型存取控制 ](../role-based-access-control/overview.md)所控制。 使用 Azure RBAC，您可以將小組內的職責區隔為 *角色* ，您只授與使用者執行其作業所需的存取權數量。 其中三個 Azure 角色為 *擁有*者、 *DevTest Labs 使用者*和 *參與者*。 在本文中，您將瞭解可在三個主要 Azure 角色中執行的動作。 從中您將會了解如何透過入口網站和透過 PowerShell 指令碼將使用者新增至實驗室，以及如何在訂用帳戶層級新增使用者。

## <a name="actions-that-can-be-performed-in-each-role"></a>可在每個角色執行的動作
您可以對使用者指派三個主要角色︰

* 擁有者
* DevTest Labs 使用者
* 參與者

下表說明使用者可在每個角色中執行的動作︰

| **此角色中的使用者可以執行的動作** | **DevTest Labs 使用者** | **擁有者** | **參與者** |
| --- | --- | --- | --- |
| **實驗室工作** | | | |
| 將使用者新增至實驗室 |否 |是 |否 |
| 更新成本設定 |否 |是 |是 |
| **VM 的基本工作** | | | |
| 新增和移除自訂映像 |否 |是 |是 |
| 新增、更新和刪除公式 |是 |是 |是 |
| 將 Azure Marketplace 映像加入允許清單 |否 |是 |是 |
| **VM 工作** | | | |
| 建立 VM |是 |是 |是 |
| 啟動、停止和刪除 VM |僅限使用者所建立的 VM |是 |是 |
| 更新 VM 原則 |否 |是 |是 |
| 在 VM 中新增/移除資料磁碟 |僅限使用者所建立的 VM |是 |是 |
| **構件工作** | | | |
| 新增和移除構件儲存機制 |否 |是 |是 |
| 套用構件 |是 |是 |是 |

> [!NOTE]
> 當使用者建立 VM 時，該使用者會自動指派給所建立 VM 的 **擁有者** 角色。
> 
> 

## <a name="add-an-owner-or-user-at-the-lab-level"></a>在實驗室層級新增擁有者或使用者
可透過 Azure 入口網站在實驗室層級新增擁有者和使用者。 使用者可以是具備有效 [Microsoft 帳戶 (MSA)](devtest-lab-faq.md#what-is-a-microsoft-account) 的外部使用者。
下列步驟會引導您進行在 Azure DevTest Labs 新增擁有者或使用者至實驗室的程序︰

1. 登入 [Azure 入口網站](https://go.microsoft.com/fwlink/p/?LinkID=525040)。
2. 選取 [ **所有服務**]，然後從清單中選取 [ **DevTest Labs** ]。
3. 從實驗室清單中，選取所需的實驗室。
4. 在實驗室的分頁上，選取 [設定 **與原則**]。 
5. 請在 [組態和原則]**** 頁面上，從左側功能表中選取 [存取控制 (IAM)]****。 
6. 在工具列上選取 [新增角色指派]**** 以將使用者新增至角色。
1. 在 [新增權限]**** 視窗中，執行下列動作： 
    1. 選取角色 (例如：DevTest Labs 使用者)。 [可在每個角色執行的動作](#actions-that-can-be-performed-in-each-role) 一節列出使用者可在擁有者、DevTest 使用者和參與者角色中執行的各種動作。
    2. 選取要新增至角色的使用者。 
    3. 選取 [儲存]。 
11. 當您返回 [使用者] **** 刀鋒視窗時，該使用者已新增。  

## <a name="add-an-external-user-to-a-lab-using-powershell"></a>使用 PowerShell 將外部使用者新增至實驗室

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

除了在 Azure 入口網站新增使用者，您還可以使用 PowerShell 指令碼將外部使用者新增至實驗室。 在下列範例中，修改 **Values to change** 註解底下的參數值。
您可以從 Azure 入口網站中的 [實驗室] 刀鋒視窗擷取 `subscriptionId`、`labResourceGroup` 及 `labName`。

> [!NOTE]
> 範例指令碼假設指定的使用者已做為來賓新增至 Active Directory 中，如果並非如此則會失敗。 若要將不在 Active Directory 中的使用者新增至實驗室，請如 [在實驗室層級新增擁有者或使用者](#add-an-owner-or-user-at-the-lab-level)一節所述使用 Azure 入口網站將使用者指派給角色。   
> 
> 

```azurepowershell
# Add an external user in DevTest Labs user role to a lab
# Ensure that guest users can be added to the Azure Active directory:
# https://azure.microsoft.com/documentation/articles/active-directory-create-users/#set-guest-user-access-policies

# Values to change
$subscriptionId = "<Enter Azure subscription ID here>"
$labResourceGroup = "<Enter lab's resource name here>"
$labName = "<Enter lab name here>"
$userDisplayName = "<Enter user's display name here>"

# Log into your Azure account
Connect-AzAccount

# Select the Azure subscription that contains the lab. 
# This step is optional if you have only one subscription.
Select-AzSubscription -SubscriptionId $subscriptionId

# Retrieve the user object
$adObject = Get-AzADUser -SearchString $userDisplayName

# Create the role assignment. 
$labId = ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
New-AzRoleAssignment -ObjectId $adObject.Id -RoleDefinitionName 'DevTest Labs User' -Scope $labId
```

## <a name="add-an-owner-or-user-at-the-subscription-level"></a>在訂用帳戶層級新增擁有者或使用者
在 Azure 中，Azure 權限會從父範圍傳播至子範圍。 因此，包含實驗室之 Azure 訂用帳戶的擁有者會自動成為這些實驗室的擁有者。 他們也擁有實驗室使用者和 Azure DevTest Labs 服務所建立的 VM 和其他資源。 

您可以在 [Azure 入口網站](https://go.microsoft.com/fwlink/p/?LinkID=525040)中透過實驗室的刀鋒視窗對實驗室新增其他擁有者。 不過，所新增擁有者的管理範圍小於訂用帳戶擁有者的範圍。 例如，新增的擁有者沒有 DevTest Labs 服務在訂用帳戶中所建立之某些資源的完整存取權。 

若要對 Azure 訂用帳戶新增擁有者，請遵循下列步驟︰

1. 登入 [Azure 入口網站](https://go.microsoft.com/fwlink/p/?LinkID=525040)。
2. 選取 [所有服務]****，然後從清單中選取 [訂用帳戶]****。
3. 選取所需的訂用帳戶。
4. 選取 [存取] **** 圖示。 
   
    ![存取使用者](./media/devtest-lab-add-devtest-user/access-users.png)
5. 在 [ **使用者** ] 分頁上，選取 [ **新增**]。
   
    ![新增使用者](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)
6. 在 [選取角色]**** 刀鋒視窗中，選取 [擁有者]****。
7. 在 [新增使用者] **** 刀鋒視窗上，輸入您想要新增為擁有者之使用者的電子郵件地址或名稱。 如果找不到該使用者，您會收到錯誤訊息來說明問題。 如果有找到使用者，該使用者會列在 [使用者] **** 文字方塊底下。
8. 選取找到的使用者名稱。
9. 選取 [選取]  。
10. 選取 [確定]****，以關閉 [新增存取]**** 刀鋒視窗。
11. 當您返回 [使用者] **** 刀鋒視窗時，該使用者已新增為擁有者。 此使用者現在是在此訂用帳戶下所建立全部實驗室的擁有者，因而能夠執行擁有者工作。 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
