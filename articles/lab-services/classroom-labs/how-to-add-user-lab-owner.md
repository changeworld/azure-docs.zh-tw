---
title: 如何向 Azure 實驗室服務中的實驗室添加其他擁有者
description: 本文介紹管理員如何將使用者作為擁有者添加到 Azure 實驗室服務中的實驗室。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 9302cadf0b900c9faac382bad86b51cea7d828fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443511"
---
# <a name="how-to-add-additional-owners-to-an-existing-lab-in-azure-lab-services"></a>如何向 Azure 實驗室服務中的現有實驗室添加其他擁有者
本文介紹作為管理員如何向現有實驗室添加其他擁有者。

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>將使用者添加到實驗室帳戶的讀取器角色
要將使用者作為其他擁有者添加到現有實驗室，必須首先授予使用者對實驗室帳戶的**讀取**許可權。

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 選取左側功能表上的 [所有服務]****。 搜索**實驗室服務**，然後選擇它。
3. 從清單中選擇您的**實驗室帳戶**。 
2. 在 **"實驗室帳戶"頁上**，選擇左側功能表上**的存取控制 （IAM）。** 
2. 在 **"存取控制 （IAM）"** 頁上，選擇"在工具列上**添加**"，然後選擇 **"添加角色指派**"。

    ![實驗室帳戶的角色指派 ](../media/how-to-add-user-lab-owner/lab-account-access-control-page.png)
3. 在"**添加角色指派"** 頁上，執行以下步驟： 
    1. 為**角色**選擇 **"讀取器**"。 
    2. 選取使用者。 
    3. 選取 [儲存]****。 

        ![將使用者添加到實驗室帳戶的讀取器角色 ](../media/how-to-add-user-lab-owner/reader-lab-account.png)

## <a name="add-user-to-the-owner-role-for-the-lab"></a>將使用者添加到實驗室的擁有者角色

1. 返回 **"實驗室帳戶"** 頁面，選擇左側功能表上**的所有實驗室**。
2. 選擇要將使用者添加為擁有者的**實驗室**。 
    
    ![選取實驗室 ](../media/how-to-add-user-lab-owner/select-lab.png)    
3. 在 **"實驗室"** 頁上，選擇左側功能表上**的存取控制 （IAM）。**
4. 在 **"存取控制 （IAM）"** 頁上，選擇"在工具列上**添加**"，然後選擇 **"添加角色指派**"。
5. 在"**添加角色指派"** 頁上，執行以下步驟： 
    1. 為**角色**選擇**擁有者**。 
    2. 選取使用者。 
    3. 選取 [儲存]****。 

## <a name="next-steps"></a>後續步驟
確認使用者在登錄到[實驗室服務門戶](https://labs.azure.com)時看到實驗室。