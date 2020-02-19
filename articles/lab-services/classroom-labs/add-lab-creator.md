---
title: 將使用者新增為 Azure 實驗室服務中的實驗室建立者
description: 本文說明如何在 Azure 實驗室服務中，將使用者新增至實驗室帳戶的實驗室建立者角色。 實驗室建立者可以在此實驗室帳戶中建立實驗室。
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
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444767"
---
# <a name="add-lab-creators-to-a-lab-account-in-azure-lab-services"></a>將實驗室建立者新增至 Azure 實驗室服務中的實驗室帳戶
本文說明如何將使用者當做實驗室建立者新增至 Azure 實驗室服務中的實驗室帳戶。 然後這些用途可以在實驗室帳戶中建立教室實驗室。 

## <a name="add-microsoft-user-account-to-lab-creator-role"></a>將 Microsoft 使用者帳戶新增至實驗室建立者角色
若要在實驗室帳戶中設定教室實驗室，使用者必須是實驗室帳戶中的 [實驗室建立者] 角色的成員。 您用來建立實驗室帳戶的帳戶會自動新增至此角色。 如果您打算使用相同的使用者帳戶建立教室實驗室，您可以略過此步驟。 若要使用其他使用者帳戶來建立教室實驗室，請執行下列步驟： 

若要提供授課者為其班級建立實驗室的權限，請將他們新增至 [實驗室建立者] 角色：

1. 在 [實驗室帳戶] 頁面上，選取 [存取控制] \(IAM\)，然後按一下工具列上的 [+新增角色指派]。 

    ![[存取控制] -> [新增角色指派] 按鈕](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. 在 [新增角色指派] 頁面上，針對 [角色] 選取 [實驗室建立者]，選取您要新增到實驗室建立者角色的使用者，然後選取 [儲存]。 

    ![新增實驗室建立者](../media/tutorial-setup-lab-account/add-lab-creator.png)

    > [!NOTE]
    > 如果您要將非 Microsoft 帳戶的使用者新增為實驗室建立者，請參閱[將非 Microsoft 帳戶的使用者新增為實驗室建立者](#add-a-non-microsoft-account-user-as-a-lab-creator)一節。 

## <a name="add-a-non-microsoft-account-user-as-a-lab-creator"></a>將非 Microsoft 帳戶的使用者新增為實驗室建立者
若要將使用者新增為實驗室建立者，請使用其電子郵件帳戶。 可能會使用下列類型的電子郵件帳戶：

- 您的大學 Office 365 Azure Active Directory （AAD）所提供的電子郵件帳戶。 
- Microsoft 電子郵件帳戶，例如 `@outlook.com`、`@hotmail.com`、`@msn.com`或 `@live.com`。
- 非 Microsoft 的電子郵件帳戶，例如 Yahoo 或 Google 提供的帳戶。 不過，這些類型的帳戶必須與 Microsoft 帳戶連結。
- GitHub 帳戶。 此帳戶必須與 Microsoft 帳戶連結。

### <a name="using-a-non-microsoft-email-account"></a>使用非 Microsoft 電子郵件帳戶
實驗室建立者/講師可以使用非 Microsoft 的電子郵件帳戶來註冊並登入教室實驗室。  不過，登入實驗室服務入口網站時，講師必須先建立連結到其非 Microsoft 電子郵件地址的 Microsoft 帳戶。

許多講師可能已經有 Microsoft 帳戶連結到其非 Microsoft 的電子郵件地址。 例如，如果講師已將其電子郵件地址與 Microsoft 的其他產品或服務（例如 Office、Skype、OneDrive 或 Windows）搭配使用，則會有 Microsoft 帳戶。  

當講師登入實驗室服務入口網站時，系統會提示他們輸入其電子郵件地址和密碼。 如果講師嘗試使用未連結 Microsoft 帳戶的非 Microsoft 帳戶進行登入，講師將會收到下列錯誤訊息： 

![錯誤訊息](../media/how-to-configure-student-usage/cant-find-account.png)

若要註冊 Microsoft 帳戶，講師應前往[http://signup.live.com](http://signup.live.com)。  


### <a name="using-a-github-account"></a>使用 GitHub 帳戶
講師也可以使用現有的 GitHub 帳戶來註冊並登入教室實驗室。 如果講師已連結至其 GitHub 帳戶的 Microsoft 帳戶，則他們可以登入並提供其密碼，如上一節所示。 如果他們尚未將其 GitHub 帳戶連結到 Microsoft 帳戶，他們應該選取 [登**入選項**]：

![登入選項連結](../media/how-to-configure-student-usage/signin-options.png)

在 [登**入選項**] 頁面上，選取 [**使用 GitHub 登入**]。

![使用 GitHub 連結進行登入](../media/how-to-configure-student-usage/signin-github.png)

最後，系統會提示他們建立連結至其 GitHub 帳戶的 Microsoft 帳戶。 當講師選取 **[下一步]** 時，就會自動發生。  然後，講師會立即登入並聯機到教室實驗室。


## <a name="next-steps"></a>後續步驟
查看下列文章：

- [以實驗室擁有者身分建立及管理實驗室](how-to-manage-classroom-labs.md)
- [以實驗室擁有者身分設定及發佈範本](how-to-create-manage-template.md)
- [以實驗室擁有者身分設定及控制實驗室的使用方式](how-to-configure-student-usage.md)
- [以實驗室使用者的身分存取教室實驗室](how-to-use-classroom-lab.md)
