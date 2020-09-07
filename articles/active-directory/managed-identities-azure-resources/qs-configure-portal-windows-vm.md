---
title: 使用 Azure 入口網站設定受控識別 - Azure AD
description: 使用 Azure 入口網站在 Azure VM 上設定 Azure 資源的受控識別適用的逐步指示。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/10/2018
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ed19b9dae179c3e4fe89a9ce28df4ad0cd080af
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89263207"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-vm-using-the-azure-portal"></a>使用 Azure 入口網站在虛擬機器上設定 Azure 資源的受控識別

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure 資源受控識別會在 Azure Active Directory 中為 Azure 服務提供自動受控識別。 您可以使用此身分識別來向任何支援 Azure AD 驗證的服務進行驗證，不需要任何您程式碼中的認證。 

在本文中，您會了解如何使用 Azure 入口網站，為 Azure 虛擬機器 (VM) 啟用和停用系統與使用者指派的受控識別。 

## <a name="prerequisites"></a>先決條件

- 如果您不熟悉 Azure 資源的受控識別，請參閱[概觀一節](overview.md)。
- 如果您還沒有 Azure 帳戶，請先[註冊免費帳戶](https://azure.microsoft.com/free/)，再繼續進行。

## <a name="system-assigned-managed-identity"></a>系統指派的受控識別

在本節中，您會了解如何使用 Azure 入口網站，為虛擬機器啟用和停用系統指派的受控識別。

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-vm"></a>在建立虛擬機器時啟用系統指派的受身分控識別

若要於 VM 建立期間在 VM 上啟用系統指派的受控識別，您的帳戶需要[虛擬機器參與者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)角色指派。  不需要其他 Azure AD 目錄角色指派。

- 請在 [身分識別]**** 區段的 [管理]**** 索引標籤之下，將 [受控服務識別]**** 切換至 [開啟]****。  

![在建立 VM 時啟用系統指派的身分識別](./media/msi-qs-configure-portal-windows-vm/enable-system-assigned-identity-vm-creation.png)

請參閱下列快速入門來建立 VM： 

- [使用 Azure 入口網站建立 Windows 虛擬機器](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine) 
- [使用 Azure 入口網站建立 Linux 虛擬機器](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)


### <a name="enable-system-assigned-managed-identity-on-an-existing-vm"></a>在現有虛擬機器上啟用系統指派的受身分控識別

若要在原先佈建的 VM 上啟用系統指派的受控識別，您的帳戶需要[虛擬機器參與者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)角色指派。  不需要其他 Azure AD 目錄角色指派。

1. 使用與包含虛擬機器的 Azure 訂用帳戶相關聯的帳戶，登入 [Azure 入口網站](https://portal.azure.com)。

2. 瀏覽至所需的虛擬機器，然後選取 [身分識別]****。

3. 在 [系統指派]****、[狀態]**** 底下，選取 [開啟]****，然後按一下 [儲存]****：

   ![組態頁面螢幕擷取畫面](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

### <a name="remove-system-assigned-managed-identity-from-a-vm"></a>從虛擬機器移除系統指派的受控識別

若要從 VM 中移除系統指派的受控識別，您的帳戶需要[虛擬機器參與者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)角色指派。  不需要其他 Azure AD 目錄角色指派。

如果您的虛擬機器不再需要系統指派的受控識別：

1. 使用與包含虛擬機器的 Azure 訂用帳戶相關聯的帳戶，登入 [Azure 入口網站](https://portal.azure.com)。 

2. 瀏覽至所需的虛擬機器，然後選取 [身分識別]****。

3. 在 [系統指派]****、[狀態]**** 底下，選取 [關閉]****，然後按一下 [儲存]****：

   ![組態頁面螢幕擷取畫面](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)

## <a name="user-assigned-managed-identity"></a>使用者指派的受控識別

 在本節中，您會了解如何使用 Azure 入口網站在虛擬機器中新增和移除使用者指派的受控識別。

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-vm"></a>在建立虛擬機器時指派使用者指派的身分識別

若要將使用者指派的身分識別指派給 VM，您的帳戶需要[虛擬機器參與者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)和[受控識別操作者](../../role-based-access-control/built-in-roles.md#managed-identity-operator)角色指派。 不需要其他 Azure AD 目錄角色指派。

目前，Azure 入口網站不支援在建立虛擬機器時指派使用者指派的受控識別。 相反地，請參閱下列其中一篇虛擬機器建立快速入門文章來先建立虛擬機器，然後繼續進行下一節，以了解如何對虛擬機器指派使用者指派的受控識別：

- [使用 Azure 入口網站建立 Windows 虛擬機器](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [使用 Azure 入口網站建立 Linux 虛擬機器](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-vm"></a>將使用者指派的受控識別指派至現有虛擬機器

若要將使用者指派的身分識別指派給 VM，您的帳戶需要[虛擬機器參與者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)和[受控識別操作者](../../role-based-access-control/built-in-roles.md#managed-identity-operator)角色指派。 不需要其他 Azure AD 目錄角色指派。

1. 使用與包含虛擬機器的 Azure 訂用帳戶相關聯的帳戶，登入 [Azure 入口網站](https://portal.azure.com)。
2. 瀏覽至所需 VM，然後依序按一下 [身分識別]****、[使用者指派]**** 和 [\+新增]****。

   ![將使用者指派的受控識別新增至虛擬機器](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot1.png)

3. 按一下想要新增到虛擬機器的使用者指派身分識別，然後按一下 [新增]****。

    ![將使用者指派的受控識別新增至虛擬機器](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-vm"></a>從虛擬機器移除使用者指派的受控識別

若要從 VM 中移除使用者指派的身分識別，您的帳戶需要[虛擬機器參與者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)角色指派。 不需要其他 Azure AD 目錄角色指派。

1. 使用與包含虛擬機器的 Azure 訂用帳戶相關聯的帳戶，登入 [Azure 入口網站](https://portal.azure.com)。
2. 瀏覽至所需的虛擬機器，按一下 [身分識別]****、[使用者指派]**** 和想要刪除的使用者指派受控識別名稱，然後按一下 [移除]**** (按一下 [確認] 窗格中的 [是]****)。

   ![從虛擬機器移除使用者指派的受控識別](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vm-screenshot.png)

## <a name="next-steps"></a>後續步驟

- 使用 Azure 入口網站，提供 Azure VM 的受控識別[存取權給另一個 Azure 資源](howto-assign-access-portal.md)。