---
title: 在 Azure 實驗室服務中將使用者添加為實驗室建立者
description: 本文演示如何將使用者添加到 Azure 實驗室服務中的實驗室帳戶的實驗室建立者角色。 實驗室建立者可以在此實驗室帳戶中創建實驗室。
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
ms.openlocfilehash: 0538747ec639b3fab1a7b38193796d80a7736170
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444767"
---
# <a name="add-lab-creators-to-a-lab-account-in-azure-lab-services"></a>將實驗室建立者添加到 Azure 實驗室服務中的實驗室帳戶
本文介紹如何將使用者作為實驗室建立者添加到 Azure 實驗室服務中的實驗室帳戶。 這些用途可以在實驗室帳戶中創建教室實驗室。 

## <a name="add-microsoft-user-account-to-lab-creator-role"></a>將 Microsoft 使用者帳戶添加到實驗室建立者角色
若要在實驗室帳戶中設定教室實驗室，使用者必須是實驗室帳戶中的 [實驗室建立者]**** 角色的成員。 您用來建立實驗室帳戶的帳戶會自動新增至此角色。 如果您打算使用相同的使用者帳戶建立教室實驗室，您可以略過此步驟。 若要使用其他使用者帳戶來建立教室實驗室，請執行下列步驟： 

若要提供授課者為其班級建立實驗室的權限，請將他們新增至 [實驗室建立者]**** 角色：

1. 在 [實驗室帳戶]**** 頁面上，選取 [存取控制] \(IAM\)****，然後按一下工具列上的 [+新增角色指派]****。 

    ![[存取控制] -> [新增角色指派] 按鈕](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. 在 [新增角色指派]**** 頁面上，針對 [角色]**** 選取 [實驗室建立者]****，選取您要新增到實驗室建立者角色的使用者，然後選取 [儲存]****。 

    ![新增實驗室建立者](../media/tutorial-setup-lab-account/add-lab-creator.png)

    > [!NOTE]
    > 如果要將非 Microsoft 帳戶使用者添加為實驗室建立者，請參閱[將非 Microsoft 帳戶使用者添加為實驗室建立者](#add-a-non-microsoft-account-user-as-a-lab-creator)部分。 

## <a name="add-a-non-microsoft-account-user-as-a-lab-creator"></a>將非 Microsoft 帳戶使用者添加為實驗室建立者
要將使用者添加為實驗室建立者，請使用其電子郵件帳戶。 可以使用以下類型的電子郵件帳戶：

- 由大學 Office 365 Azure 活動目錄 （AAD） 提供的電子郵件帳戶。 
- 微軟電子郵件帳戶`@outlook.com`，如 、`@hotmail.com``@msn.com`或 。 `@live.com`
- 非微軟電子郵件帳戶，如雅虎或谷歌提供的電子郵件帳戶。 但是，這些類型的帳戶必須與 Microsoft 帳戶連結。
- GitHub 帳戶。 此帳戶必須與 Microsoft 帳戶連結。

### <a name="using-a-non-microsoft-email-account"></a>使用非 Microsoft 電子郵件帳戶
實驗室建立者/講師可以使用非 Microsoft 電子郵件帳戶註冊並登錄到教室實驗室。  但是，登錄到實驗室服務門戶要求教師首先創建連結到其非 Microsoft 電子郵件地址的 Microsoft 帳戶。

許多教師可能已經將 Microsoft 帳戶連結到其非 Microsoft 電子郵件地址。 例如，如果教師將電子郵件地址用於 Microsoft 的其他產品或服務（如 Office、Skype、OneDrive 或 Windows），則他們已擁有 Microsoft 帳戶。  

當教師登錄到實驗室服務門戶時，系統會提示他們輸入電子郵件地址和密碼。 如果講師嘗試使用未連結 Microsoft 帳戶的非 Microsoft 帳戶登錄，講師將收到以下錯誤訊息： 

![錯誤訊息](../media/how-to-configure-student-usage/cant-find-account.png)

要註冊 Microsoft 帳戶，講師應轉到[http://signup.live.com](http://signup.live.com)。  


### <a name="using-a-github-account"></a>使用 GitHub 帳戶
教師還可以使用現有的 GitHub 帳戶註冊並登錄到教室實驗室。 如果教師已經擁有連結到其 GitHub 帳戶的 Microsoft 帳戶，則可以登錄並提供其密碼，如上一節所示。 如果他們尚未將其 GitHub 帳戶連結到 Microsoft 帳戶，則應選擇**登錄選項**：

![登錄選項連結](../media/how-to-configure-student-usage/signin-options.png)

在 **"登錄選項**"頁上，選擇**使用 GitHub 登錄**。

![使用 GitHub 連結登錄](../media/how-to-configure-student-usage/signin-github.png)

最後，系統會提示他們創建連結到其 GitHub 帳戶的 Microsoft 帳戶。 當講師選擇 **"下一步**"時，它會自動發生。  然後，教師立即登錄並連接到教室實驗室。


## <a name="next-steps"></a>後續步驟
查看下列文章：

- [以實驗室擁有者身分建立及管理實驗室](how-to-manage-classroom-labs.md)
- [以實驗室擁有者身分設定及發佈範本](how-to-create-manage-template.md)
- [以實驗室擁有者身分設定及控制實驗室的使用方式](how-to-configure-student-usage.md)
- [以實驗室使用者的身分存取教室實驗室](how-to-use-classroom-lab.md)
