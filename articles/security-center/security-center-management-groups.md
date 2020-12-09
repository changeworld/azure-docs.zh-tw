---
title: 取得 Azure 資訊安全中心的全租用戶可見性 | Microsoft Docs
description: 本文說明如何將原則套用至連結至 Azure Active Directory 租使用者的所有訂用帳戶，以大規模管理您的安全性狀況。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b85c0e93-9982-48ad-b23f-53b367f22b10
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/07/2020
ms.author: memildin
ms.openlocfilehash: d03177e3224bbd3f53320871efc6a0d6b3ea479d
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96922700"
---
# <a name="organize-management-groups-subscriptions-and-tenant-wide-visibility"></a>組織管理群組、訂用帳戶及全租使用者的可見度

本文說明如何將安全性原則套用至連結至您 Azure Active Directory 租使用者的所有 Azure 訂用帳戶，以大規模管理您組織的安全性狀況。

若要瞭解 Azure AD 租使用者中註冊之所有訂用帳戶的安全性狀態，必須在根管理群組上指派具有足夠讀取權限的 Azure 角色。


## <a name="organize-your-subscriptions-into-management-groups"></a>將訂用帳戶組織成管理群組

### <a name="introduction-to-management-groups"></a>管理群組簡介

Azure 管理群組可讓您有效管理訂用帳戶群組的存取、原則和報告，並藉由對根管理群組執行動作來有效管理整個 Azure 資產。 您可以將訂用帳戶組織到管理群組中，並將控管原則套用至管理群組。 管理群組內的所有訂用帳戶都會自動繼承套用到管理群組的原則。 

每個 Azure AD 租使用者都有一個名為 **根管理群組** 的最上層管理群組。 這個根管理群組會建置於階層內，讓所有的管理群組和訂用帳戶摺疊於其中。 此群組可讓全域原則和 Azure 角色指派套用於目錄層級。 

當您執行下列任何動作時，即會自動建立根管理群組： 
- 開啟 [Azure 入口網站](https://portal.azure.com)中的 **管理群組**。
- 使用 API 呼叫建立管理群組。
- 透過 PowerShell 建立管理群組。 如需 PowerShell 指示，請參閱 [建立資源和組織管理的管理群組](../governance/management-groups/create-management-group-portal.md)。

不需要管理群組，就能將安全中心上線，但我們建議您至少建立一個管理群組，以便建立根管理群組。 在群組建立後，Azure AD 租用戶下的所有訂用帳戶都會連結至該群組。 


如需管理群組的詳細概觀，請參閱[使用 Azure 管理群組來組織資源](../governance/management-groups/overview.md)一文。

### <a name="view-and-create-management-groups-in-the-azure-portal"></a>在 Azure 入口網站中查看並建立管理群組

1. 在 [Azure 入口網站](https://portal.azure.com)中，使用頂端列中的 [搜尋] 方塊來尋找並開啟 **管理群組**。

    :::image type="content" source="./media/security-center-management-groups/open-management-groups-service.png" alt-text="存取您的管理群組":::

    管理群組的清單隨即出現。

1. 若要建立管理群組，請選取 [ **新增管理群組**]，輸入相關的詳細資料，然後選取 [ **儲存**]。

    :::image type="content" source="media/security-center-management-groups/add-management-group.png" alt-text="將管理群組新增至 Azure":::

    - [管理群組識別碼] 是用來在此管理群組上提交命令的目錄唯一識別碼。 此識別碼在建立後即無法編輯，因為整個 Azure 系統會使用它來識別此群組。 
    - [顯示名稱] 欄位是顯示在 Azure 入口網站內的名稱。 在建立管理群組時，不同的顯示名稱是選擇性欄位，並且可以隨時進行變更。  


### <a name="add-subscriptions-to-a-management-group"></a>將訂用帳戶新增至管理群組
您可以將訂用帳戶新增至已建立的管理群組。

1. 在 [ **管理群組**] 下，選取您訂用帳戶的管理群組。

    :::image type="content" source="./media/security-center-management-groups/management-group-subscriptions.png" alt-text="為您的訂用帳戶選取管理群組":::

1. 當群組的頁面開啟時，選取 [**詳細資料**]。

    :::image type="content" source="./media/security-center-management-groups/management-group-details-page.png" alt-text="開啟管理群組的詳細資料頁面":::

1. 從群組的詳細資料頁面中，選取 [ **新增訂閱**]，然後選取您的訂用帳戶，然後選取 [ **儲存**]。 重複直到您已新增範圍中的所有訂用帳戶為止。

    :::image type="content" source="./media/security-center-management-groups/management-group-add-subscriptions.png" alt-text="將訂用帳戶新增至管理群組":::
   > [!IMPORTANT]
   > 管理群組可同時包含訂用帳戶和子管理群組。 當您將 Azure 角色指派給使用者的父管理群組時，子管理群組的訂用帳戶會繼承存取權。 子系也會繼承在父管理群組上設定的原則。 


## <a name="grant-tenant-wide-permissions-to-yourself"></a>將整個租使用者的許可權授與您自己

具有「 **全域系統管理員** 」 Azure Active Directory 角色的使用者，可能會擁有整個租使用者的責任，但缺少 Azure 許可權可在 Azure 資訊安全中心中查看整個組織的資訊。 

> [!TIP]
> 如果您的組織使用 [Azure AD Privileged Identity Management (PIM) ](../active-directory/privileged-identity-management/pim-configure.md)或任何其他 PIM 工具來管理資源存取權，則全域管理員角色必須在進行這些變更的使用者上為使用中狀態。

若要指派您自己的租使用者層級許可權：

1. 在租使用者的根管理群組中，以不具指派的全域系統管理員使用者身分開啟資訊安全中心的 **[總覽** ] 頁面，並選取橫幅中的 **整個租使用者可見度** 連結。 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-banner.png" alt-text="啟用 Azure 資訊安全中心中的租使用者層級許可權":::

1. 選取要指派的新 Azure 角色。 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-form.png" alt-text="定義要指派給使用者之租使用者層級許可權的表單":::

    > [!TIP]
    > 一般而言，需具備安全性管理員角色，才能在根層級套用原則，而安全性讀取者就足以提供租用戶層級的可見性。 如需有關這些角色所授與權限的詳細資訊，請參閱[安全性管理員內建角色描述](../role-based-access-control/built-in-roles.md#security-admin)或[安全性讀取者內建角色描述](../role-based-access-control/built-in-roles.md#security-reader)。
    >
    > 如需這些角色特定的安全性中心差異，請參閱 [角色和允許的動作](security-center-permissions.md#roles-and-allowed-actions)中的資料表。

    您可以在租使用者的根管理群組層級上授與角色，來達成全組織的觀點。  

1. 登出 Azure 入口網站，然後再重新登入一次。

1. 提高存取權限後，請開啟或重新整理 Azure 資訊安全中心，以確認您可以檢視 Azure AD 租用戶下的所有訂用帳戶。 

## <a name="assign-azure-roles-to-other-users"></a>將 Azure 角色指派給其他使用者

### <a name="assign-azure-roles-to-users-through-the-azure-portal"></a>透過 Azure 入口網站將 Azure 角色指派給使用者： 
1. 登入 [Azure 入口網站](https://portal.azure.com)。 
1. 若要檢視管理群組，請在 Azure 主功能表下選取 [所有服務]，然後選取 [管理群組]。
1.  選取管理群組，然後選取 [ **詳細資料**]。

    :::image type="content" source="./media/security-center-management-groups/management-group-details.PNG" alt-text="管理群組詳細資料螢幕擷取畫面":::

1. 選取 [ **存取控制] (IAM)** 接著 **角色指派**。
1. 選取 [新增角色指派]。
1. 選取要指派的角色和使用者，然後選取 [ **儲存**]。  
   
   ![新增安全性讀取者角色螢幕擷取畫面](./media/security-center-management-groups/asc-security-reader.png)

### <a name="assign-azure-roles-to-users-with-powershell"></a>使用 PowerShell 將 Azure 角色指派給使用者： 
1. 安裝 [Azure PowerShell](/powershell/azure/install-az-ps)。
2. 執行下列命令： 

    ```azurepowershell
    # Login to Azure as a Global Administrator user
    Connect-AzAccount
    ```

3. 出現提示時，請使用全域管理員認證登入。 

    ![登入提示螢幕擷取畫面](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. 執行下列命令以授與讀者角色權限：

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. 若要移除角色，請使用下列命令： 

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

## <a name="remove-elevated-access"></a>移除已提高的存取權 
一旦將 Azure 角色指派給使用者之後，租使用者系統管理員就應該將自己從「使用者存取系統管理員」角色中移除。

1. 登入 [Azure 入口網站](https://portal.azure.com)或 [Azure Active Directory 系統管理中心](https://aad.portal.azure.com)。

2. 在導覽清單中，選取 **Azure Active Directory** 然後選取 [ **屬性**]。

3. 在 [ **Azure 資源的存取管理**] 下，將參數設定為 [ **否**]。

4. 若要儲存您的設定，請選取 [ **儲存**]。



## <a name="next-steps"></a>後續步驟
在本文中，您已了解如何取得 Azure 資訊安全中心的全租用戶可見性。 如需相關資訊，請參閱：

- [Azure 資訊安全中心的權限](security-center-permissions.md)