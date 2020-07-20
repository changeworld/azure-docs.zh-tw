---
title: 如何將其他擁有者新增至 Azure 實驗室服務中的實驗室
description: 本文會說明系統管理員如何將使用者新增為 Azure 實驗室服務中的實驗室的擁有者。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 6671a3070dae672769eecf59d614d3b75455ef5a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445860"
---
# <a name="how-to-add-additional-owners-to-an-existing-lab-in-azure-lab-services"></a>如何在 Azure 實驗室服務中將其他擁有者新增至現有的實驗室
本文將告訴您，身為系統管理員，您可以如何將其他擁有者新增至現有的實驗室。

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>將使用者新增至實驗室帳戶的 [讀者] 角色
若要將使用者新增為現有實驗室的其他擁有者，您必須先授與使用者實驗室帳戶的 [**讀取**] 許可權。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取左側功能表上的 [所有服務]。 搜尋 [**實驗室服務**]，然後選取它。
3. 從清單中選取您的**實驗室帳戶**。 
2. 在 [**實驗室帳戶] 頁面**上，選取左側功能表上的 **[存取控制（IAM）** ]。 
2. 在 [**存取控制（IAM）** ] 頁面上，選取工具列上的 [**新增**]，然後選取 [**新增角色指派**]。

    ![實驗室帳戶的角色指派 ](./media/how-to-add-user-lab-owner/lab-account-access-control-page.png)
3. 在 [新增角色指派] 頁面中，執行下列步驟： 
    1. 選取**角色**的 [**讀取**者]。 
    2. 選取使用者。 
    3. 選取 [儲存]。 

        ![將使用者新增至實驗室帳戶的 [讀者] 角色 ](./media/how-to-add-user-lab-owner/reader-lab-account.png)

## <a name="add-user-to-the-owner-role-for-the-lab"></a>將使用者新增至實驗室的擁有者角色

1. 回到 [**實驗室帳戶**] 頁面上，選取左側功能表上的 [**所有實驗室**]。
2. 選取您想要將使用者新增為擁有者的**實驗室**。 
    
    ![選取實驗室 ](./media/how-to-add-user-lab-owner/select-lab.png)    
3. 在 [**實驗室**] 頁面上，選取左側功能表上的 **[存取控制（IAM）** ]。
4. 在 [**存取控制（IAM）** ] 頁面上，選取工具列上的 [**新增**]，然後選取 [**新增角色指派**]。
5. 在 [新增角色指派] 頁面中，執行下列步驟： 
    1. 選取**角色**的 [**擁有**者]。 
    2. 選取使用者。 
    3. 選取 [儲存]。 

## <a name="next-steps"></a>後續步驟
確認使用者在登入[實驗室服務入口網站](https://labs.azure.com)時看到實驗室。