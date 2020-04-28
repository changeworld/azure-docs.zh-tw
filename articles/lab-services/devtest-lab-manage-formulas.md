---
title: 管理 Azure DevTest Labs 中的公式來建立 VM | Microsoft Docs
description: 本文說明如何從基礎 (自訂映像、Marketplace 映像或另一個公式) 或現有的 VM 建立公式。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 841dd95a-657f-4d80-ba26-59a9b5104fe4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2020
ms.author: spelluru
ms.openlocfilehash: ce980167cd88adfada1aa294aafa885184565799
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81641152"
---
# <a name="manage-azure-devtest-labs-formulas"></a>管理 Azure DevTest Labs 公式

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

本文說明如何從基礎 (自訂映像、Marketplace 映像或另一個公式) 或現有的 VM 建立公式。 本文也會引導您管理現有公式。

## <a name="create-a-formula"></a>建立公式
具有 DevTest Labs「使用者」 ** 權限的所有使用者，都可以使用公式作為基底來建立 VM。 有兩種方式可以建立公式： 

* 從基底開始 - 在您想要定義公式的所有特性時使用。
* 從現有實驗室 VM - 在您想要根據現有 VM 的設定建立公式時使用。

如需有關新增使用者和權限的詳細資訊，請參閱[在 Azure DevTest Labs 中新增擁有者和使用者](./devtest-lab-add-devtest-user.md)。

### <a name="create-a-formula-from-a-base"></a>從基底建立公式
下列步驟會引導您完成從自訂映像、Marketplace 映像或其他公式建立公式的程序。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 選取 [所有服務]****，然後從清單中選取 [DevTest Labs]****。

3. 從實驗室清單中，選取所需的實驗室。  

4. 在實驗室的頁面上，選取左側功能表上的 **[公式（可重複使用的基底）** ]。
5. 在 [公式]**** 頁面上，選取 [+新增]****。
   
    ![加入公式](./media/devtest-lab-create-formulas/add-formula.png)

6. 在 [**選擇基底**] 頁面上，選取您要從中建立公式的基底（自訂映射或 Marketplace 映射）。
7. 在 [建立公式]**** 頁面的 [基本設定]**** 索引標籤上，指定下列值：
   
    * **公式名稱** - 輸入公式的名稱。 當您建立 VM 時，這個值會在顯示在基本映像清單中。 輸入名稱時會立即驗證，如果無效，則會出現訊息指出有效名稱的需求。
    - 輸入公式的選擇性**描述**。 
    * **使用者名稱** - 輸入被授與系統管理員權限的使用者名稱。
    * **密碼** - 從下拉式清單中輸入或選取一個值，該值與您想用於指定使用者的密碼相關聯。 若要深入了解如何在金鑰保存庫中儲存祕密以及在建立實驗室資源時使用它們，請參閱[在 Azure Key Vault 中儲存祕密](devtest-lab-store-secrets-in-key-vault.md)。

        如果您想要使用 Azure Key Vault 的秘密，而不是使用密碼，請選取 [**使用儲存的秘密**]。 
    * **虛擬機器大小**-選取 [**變更大小**] 以變更 VM 的大小。 
    - **OS 磁片類型**-選取您想要使用的磁片類型（[標準 HDD]、[標準 SSD] 或 [進階 SSD]）。
    * **成品** - 選取 [新增或移除成品]**** 頁面，在其中選取並設定您想要新增到基底映像中的成品。 如需構件的詳細資訊，請參閱[建立 Azure DevTest Labs 虛擬機器的自訂構件](devtest-lab-artifact-author.md)。

        ![基本設定頁面](./media/devtest-lab-create-formulas/basic-settings.png)
8. 切換 [進階設定]**** 索引標籤，然後指定下列值：
    - **虛擬網路** - 若要變更虛擬網路，請選取 [變更 Vnet]****。 
    - **子網路** - 若要變更子網路，請選取 [變更子網路]****。 
    - **IP 位址組態** - 指定您想要公用、私人或共用 IP 位址。 如需共用 IP 位址的詳細資訊，請參閱[了解 Azure DevTest Labs 中的共用 IP 位址](./devtest-lab-shared-ip.md)。
    - **到期日期和時間**-您無法編輯此欄位。 
    - **允許宣告此機器** -「允許宣告」機器表示建立機器時不會指派擁有權。 取而代之的是，實驗室使用者將能夠在實驗室的頁面中，取得 (認領) 機器的擁有權。  

        ![基本設定頁面](./media/devtest-lab-create-formulas/advanced-settings.png)
8. 選取 [提交]**** 以建立公式。

9. 公式在建立之後會顯示在 [公式]**** 頁面的清單中。

### <a name="create-a-formula-from-a-vm"></a>從 VM 建立公式
下列步驟會引導您完成根據現有 VM 建立公式的程序。 

> [!NOTE]
> VM 必須是在 2016 年 3 月 30 日以後建立的，才能從該 VM 建立公式。 
> 
> 

1. 登入 [Azure 入口網站](https://go.microsoft.com/fwlink/p/?LinkID=525040)。
2. 選取 [所有服務]****，然後從清單中選取 [DevTest Labs]****。
3. 從實驗室清單中，選取所需的實驗室。  
4. 在實驗室的 [概觀] **** 頁面上，選取您想要從中建立公式的 VM。
   
    ![實驗室 VM](./media/devtest-lab-create-formulas/my-vms.png)
5. 在 VM 的頁面上，選取 [建立公式 (可重複使用的基底)]****。
   
    ![建立公式](./media/devtest-lab-create-formulas/create-formula-menu.png)
6. 在 [建立公式]**** 頁面上，輸入新公式的 [名稱]**** 和 [描述]****。
   
    ![建立公式頁面](./media/devtest-lab-create-formulas/create-formula-blade.png)
7. 選取 [確定] **** 以建立公式。

## <a name="modify-a-formula"></a>修改公式
若要修改公式，請遵循下列步驟︰

1. 登入 [Azure 入口網站](https://go.microsoft.com/fwlink/p/?LinkID=525040)。
2. 選取 [所有服務]****，然後從清單中選取 [DevTest Labs]****。
3. 從實驗室清單中，選取所需的實驗室。  
4. 在實驗室的頁面上，選取 [公式 (可重複使用的基底)] ****。
   
    ![公式功能表](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. 在 [Lab formulas] \(實驗室公式\)**** 頁面上，選取您想要修改的公式。
6. 在 [更新公式]**** 頁面上，進行所需的編輯，然後選取 [更新]****。

## <a name="delete-a-formula"></a>刪除公式
若要刪除公式，請遵循下列步驟︰

1. 登入 [Azure 入口網站](https://go.microsoft.com/fwlink/p/?LinkID=525040)。
2. 選取 [所有服務]****，然後從清單中選取 [DevTest Labs]****。
3. 從實驗室清單中，選取所需的實驗室。  
4. 在實驗室的 [設定]**** 頁面上，選取 [公式]****。
   
    ![公式功能表](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. 在 [Lab formulas] \(實驗室公式)**** 頁面上，選取您想要刪除之公式右邊的省略符號。
   
    ![公式功能表](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. 在公式的操作功能表上，選取 [刪除] ****。
   
    ![公式操作功能表](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. 在刪除確認對話方塊上，選取 [是] **** 。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>相關部落格文章
* [自訂映像或公式？](devtest-lab-faq.md#what-is-the-difference-between-a-custom-image-and-a-formula)

## <a name="next-steps"></a>後續步驟
建立要在建立 VM 時使用的公式之後，下一個步驟就是[將 VM 新增實驗室](devtest-lab-add-vm.md)。

