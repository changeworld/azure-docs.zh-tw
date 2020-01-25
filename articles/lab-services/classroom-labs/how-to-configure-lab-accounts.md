---
title: 在 Azure 實驗室服務中設定實驗室帳戶 |Microsoft Docs
description: 本文說明如何在 Azure 實驗室服務中建立實驗室帳戶、查看所有實驗室帳戶，或刪除實驗室帳戶。
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
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 597bbfdc35c556b5c75968084c01ac222fc468f4
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718096"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>在 Azure 實驗室服務中設定實驗室帳戶 
在 Azure 實驗室服務中，實驗室帳戶是適用于受控實驗室類型的容器，例如教室實驗室。 系統管理員會使用 Azure 實驗室服務來設定實驗室帳戶，並向可以在帳戶中建立實驗室的實驗室擁有者提供存取權。 本文說明如何建立實驗室帳戶、檢視所有實驗室帳戶，或刪除實驗室帳戶。

## <a name="connect-with-a-peer-virtual-network"></a>連線至對等虛擬網路
若要將虛擬網路作為對等網路連線至實驗室的虛擬網路，請遵循下列步驟：

1. 在 [**實驗室帳戶**] 頁面上，選取左側功能表上的 [**實驗室**設定]。

    ![實驗室設定頁面](../media/how-to-manage-lab-accounts/labs-configuration-page.png) 
1. 針對 [**對等虛擬網路**]，選取 [**啟用**] 或 [**停用**] 預設值為**Disabled**。 若要啟用對等虛擬網路，請執行下列步驟： 
    1. 選取 [啟用]。
    2. 從下拉式清單中選取**VNet** 。 
3. 在工具列上選取 [儲存]。 

在此帳戶中建立的實驗室會連線到選取的虛擬網路。 他們可以存取所選虛擬網路中的資源。 如需詳細資訊，請參閱[將您的實驗室網路與 Azure 實驗室服務中的對等虛擬網路](how-to-connect-peer-virtual-network.md)連線。

當您選取虛擬網路作為 [**對等虛擬網路**] 欄位時，會停用 [**允許實驗室建立者選擇實驗室位置**] 選項。 這是因為實驗室帳戶中的實驗室必須與實驗室帳戶位在相同的區域中，才能與對等虛擬網路中的資源連線。 

## <a name="allow-lab-creator-to-pick-location-for-the-lab"></a>允許實驗室建立者挑選實驗室的位置
您可以遵循下列步驟，讓實驗室建立者在與實驗室帳戶位置不同的位置中建立實驗室： 

1. 在 [**實驗室帳戶**] 頁面上，選取左側功能表上的 [**實驗室**設定]。
2. 針對 [**允許實驗室建立者選擇實驗室位置**]，如果您希望實驗室建立者能夠選取實驗室的位置，請選取 [**已啟用**]。 如果已停用，則會在實驗室帳戶所在的相同位置自動建立實驗室。 
    
    當您選取虛擬網路作為 [**對等虛擬網路**] 欄位時，會停用此欄位。 這是因為實驗室帳戶中的實驗室必須與實驗室帳戶位在相同的區域中，才能存取對等虛擬網路中的資源。 
1. 在工具列上選取 [儲存]。 

    ![設定實驗室位置設定](../media/how-to-manage-lab-accounts/labs-configuration-page-lab-location.png)


## <a name="specify-an-address-range-for-vms-in-the-lab"></a>為實驗室中的 Vm 指定位址範圍
下列程式中的步驟可為實驗室中的 Vm 指定位址範圍。 如果您更新您先前指定的範圍，修改過的位址範圍僅適用于在進行變更之後所建立的 Vm。 

當您指定應記住的位址範圍時，以下是一些限制。 

- 前置詞必須小於或等於23。 
- 如果虛擬網路是對等互連至實驗室帳戶，則提供的位址範圍不能與對等互連虛擬網路中的位址範圍重迭。

1. 在 [**實驗室帳戶**] 頁面上，選取左側功能表上的 [**實驗室**設定]。
2. 針對 [**位址範圍**] 欄位，指定要在實驗室中建立之 vm 的位址範圍。 位址範圍應該是無類別網域間路由（CIDR）標記法（例如： 10.20.0.0/23）。 實驗室中的虛擬機器將會建立在此位址範圍內。
3. 在工具列上選取 [儲存]。 

    ![設定位址範圍](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>將使用者新增至實驗室建立者角色
若要在實驗室帳戶中設定教室實驗室，使用者必須是實驗室帳戶中的 [實驗室建立者] 角色的成員。 您用來建立實驗室帳戶的帳戶會自動新增至此角色。 如果您打算使用相同的使用者帳戶建立教室實驗室，您可以略過此步驟。 若要使用其他使用者帳戶來建立教室實驗室，請執行下列步驟： 

若要提供授課者為其班級建立實驗室的權限，請將他們新增至 [實驗室建立者] 角色：

1. 在 [實驗室帳戶] 頁面上，選取 [存取控制] \(IAM\)，然後按一下工具列上的 [+新增角色指派]。 

    ![[存取控制] -> [新增角色指派] 按鈕](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. 在 [新增角色指派] 頁面上，針對 [角色] 選取 [實驗室建立者]，選取您要新增到實驗室建立者角色的使用者，然後選取 [儲存]。 

    ![新增實驗室建立者](../media/tutorial-setup-lab-account/add-lab-creator.png)

    > [!NOTE]
    > 如果您要將非 Microsoft 帳戶的使用者新增為實驗室建立者，請參閱[將非 Microsoft 帳戶的使用者新增為實驗室建立者](#add-a-non-microsoft-account-user-as-a-lab-creator)一節。 

## <a name="specify-marketplace-images-available-to-lab-creators"></a>指定實驗室建立者可用的 Marketplace 映像
身為實驗室帳戶擁有者的您，可以指定實驗室建立者可用來在實驗室帳戶中建立實驗室的 Marketplace 映像。 

1. 選取左側功能表上的 [Marketplace 映像]。 根據預設，您會看到映像 (包括已啟用和停用) 的完整清單。 您可以從頂端的下拉式清單中選取 [僅限已啟用]/[僅限已停用] 選項來篩選清單，而僅檢視已啟用/已停用的映像。 
    
    ![Marketplace 映像頁面](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    在清單中顯示的是滿足下列條件的 Marketplace 映像：
        
    - 建立單一 VM。
    - 使用 Azure Resource Manager 來佈建 VM
    - 不需要購買額外的授權方案
2. 若要**停用**已啟用的 Marketplace 映像，請執行下列其中一個動作： 
    1. 選取最後一個資料行中的 [...] (省略符號)，然後選取 [停用映像]。 

        ![停用一個映像](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. 在清單中選取映像名稱前面的核取方塊，以選取清單中的一或多個映像，然後選取 [停用選取的映像]。 

        ![停用多個映像](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. 同樣地，若要**啟用** Marketplace 映像，請執行下列其中一個動作： 
    1. 選取最後一個資料行中的 [...] (省略符號)，然後選取 [啟用映像]。 
    2. 在清單中選取映像名稱前面的核取方塊，以選取清單中的一或多個映像，然後選取 [啟用選取的映像]。 

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

## <a name="automatic-shutdown-of-vms-on-disconnect"></a>在中斷連線時自動關閉 Vm
您可以在遠端桌面連線中斷連線之後，啟用或停用 Windows 實驗室 Vm （範本或學生）的自動關閉。 您也可以指定 Vm 在自動關機之前，應等候多久的時間才能重新連線。

![實驗室帳戶的自動關閉設定](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

此設定適用于在實驗室帳戶中建立的所有實驗室。 實驗室擁有者可以在實驗室層級覆寫此設定。 在實驗室帳戶對此設定進行的變更，只會影響在進行變更之後所建立的實驗室。

若要瞭解實驗室擁有者如何在實驗室層級進行這項設定，請參閱[這篇文章](how-to-enable-shutdown-disconnect.md)

## <a name="next-steps"></a>後續步驟
查看下列文章：

- [以實驗室擁有者身分建立及管理實驗室](how-to-manage-classroom-labs.md)
- [以實驗室擁有者身分設定及發佈範本](how-to-create-manage-template.md)
- [以實驗室擁有者身分設定及控制實驗室的使用方式](how-to-configure-student-usage.md)
- [以實驗室使用者的身分存取教室實驗室](how-to-use-classroom-lab.md)
