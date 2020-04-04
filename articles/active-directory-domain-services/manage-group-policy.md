---
title: 在 Azure AD 網域服務建立及管理群組策略 |微軟文件
description: 瞭解如何編輯內建組策略物件 (GPO) 並在 Azure 活動目錄域服務託管域中創建自己的自定義策略。
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/09/2020
ms.author: iainfou
ms.openlocfilehash: 742d716ecdfff6ab67dedc281aa6134020f57add
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655041"
---
# <a name="administer-group-policy-in-an-azure-ad-domain-services-managed-domain"></a>Azure AD 網域服務託管網域中管理群組原則

Azure 活動目錄域服務 (Azure AD DS) 中的使用者和計算機物件的設置通常使用組策略物件 (GPO) 進行管理。 Azure AD DS 包括適用於*AddC 使用者和 AADDC* *計算機*容器的內建 GPO。 您可以自定義這些內建 GPO,以便根據環境需要配置組策略。 *Azure AD DC 管理員*組的成員在 Azure AD DS 網域中具有組策略管理許可權,還可以創建自訂 GPO 和組織單位 (CO)。 有關什麼是組策略及其工作原理的詳細資訊,請參閱[組策略概述][group-policy-overview]。

在混合環境中,在本地 ADDS 環境中配置的組策略不會同步到 Azure AD DS。 要為 Azure AD DS 中的使用者或電腦定義設定設定,請編輯其中一個預設 GPO 或創建自訂 GPO。

本文介紹如何安裝組策略管理工具,然後編輯內置 GPO 並創建自訂 GPO。

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>開始之前

要完成本文,您需要以下資源和特權:

* 有效的 Azure 訂用帳戶。
    * 如果沒有 Azure 訂閱,[請建立帳號](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
    * 如果需要,請完成創建[和配置 Azure 活動目錄域服務實例][create-azure-ad-ds-instance]的教程。
* 加入到 Azure AD DS 託管域的 Windows 伺服器管理 VM。
    * 如果需要,請完成[建立 Windows 伺服器 VM 的教學並將加入託管域][create-join-windows-vm]。
* 屬於您 Azure AD 租用戶中 Azure AD DC 系統管理員** 群組成員的使用者帳戶。

> [!NOTE]
> 通過將新範本複製到管理工作站,可以使用組策略管理範本。 將 *.admx*檔案`%SYSTEMROOT%\PolicyDefinitions`複製到並複製特定於區域設置的`Language-CountryRegion`*.adml*`%SYSTEMROOT%\PolicyDefinitions\[Language-CountryRegion]`檔 ,其中與 *.adml*檔的語言和區域匹配。
>
> 例如,將 *.adml*檔的英文、美國版本`\en-us`複製到 資料夾中。
>
> 或者,可以將組策略管理範本集中存儲在屬於 Azure AD DS 託管域的域控制器上。 有關詳細資訊,請參閱如何在[Windows 中建立和管理組原則管理範本的中央存儲](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra)。

## <a name="install-group-policy-management-tools"></a>安裝群組原則管理工具

要建立和設定群組策略物件 (GPO),您需要安裝組原則管理工具。 這些工具可以作為功能安裝在 Windows 伺服器中。 有關如何在 Windows 用戶端上安裝管理工具的詳細資訊,請參閱安裝[遠端伺服器管理工具 (RSAT)。][install-rsat]

1. 登錄到管理 VM。 有關如何使用 Azure 門戶進行連接的步驟,請參閱[連接到 Windows 伺服器 VM][connect-windows-server-vm]。
1. 當您登入 VM 時，預設應該會開啟 [伺服器管理員]****。 如果沒有，請在 [開始]**** 功能表上，選取 [伺服器管理員]****。
1. 在 [伺服器管理員]**** 視窗的 [儀表板]** 窗格內，選取 [新增角色及功能]****。
1. 在 [新增角色及功能精靈]**** 的 [開始之前]** 頁面上，選取 [下一步]****。
1. 針對 [安裝類型]**，保持勾選 [角色型或功能型安裝]**** 選項，然後選取 [下一步]****。
1. 在 **「伺服器選擇**」頁上,從伺服器池中選擇當前 VM,如*myvm.aaddscontoso.com,* 然後選擇 **「下一步**」 。
1. 在 [伺服器角色]**** 頁面上，按 [下一步]****。
1. 在 [功能]**** 頁面上，選取 [群組原則管理]**** 功能。

    ![從"功能"頁安裝"組策略管理"](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)

1. 在 [確認]**** 頁面上，選取 [安裝]****。 安裝組策略管理工具可能需要一兩分鐘。
1. 完成功能安裝時，請選取 [關閉]**** 以結束 [新增角色及功能]**** 精靈。

## <a name="open-the-group-policy-management-console-and-edit-an-object"></a>開啟群組原則管理主控台並編輯物件

Azure AD DS 託管域中的使用者和電腦存在預設組策略物件 (GPO)。 通過從上一部分安裝組策略管理功能,讓我們查看和編輯現有的 GPO。 在下一節中,您將創建自定義 GPO。

> [!NOTE]
> 若要在 Azure AD DS 託管域中管理組策略,必須登入*屬於 AAD DC 管理員*組的使用者帳戶。

1. 在"開始"螢幕中,選擇 **"管理工具**"。 將顯示可用管理工具的清單,包括上一個組中安裝的**群組原則管理**。
1. 要開啟群組原則管理主控台 (GPMC),請選擇**群組原則管理**。

    ![群組原則管理主控台開啟,準備編輯組原則物件](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

Azure AD DS 託管域中有兩個內建組策略物件 (GPO), 一個用於*AADDC 計算機*容器,一個用於*AADDC 使用者*容器。 您可以自定義這些 GPO 以根據需要在 Azure AD DS 託管域中設定組策略。

1. 在**組策略管理**控制台中,展開**林:aaddscontoso.com**節點。 接下來,展開**域**節點。

    *AADDC 計算機*和*AADDC 使用者*存在兩個內置容器。 每個容器都應用了預設 GPO。

    ![內建 GPO 應用程式於預設的「AddC 電腦」和「AddC 使用者」容器](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)

1. 可以自定義這些內置 GPO 以在 Azure AD DS 託管域上配置特定的組策略。 右選擇 GPO 之一,如*AADDC 計算機 GPO,* 然後選擇 **"編輯..."**

    ![選擇「編輯」內建 GPO 之一的選項](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)

1. 將開啟群組政策管理編輯器工具,以便自訂 GPO,例如*帳號政策*:

    ![自訂 GPO 可根據需要設定設定](./media/active-directory-domain-services-admin-guide/gp-editor.png)

    完成後,選擇 **「檔>儲存**以儲存策略。 默認情況下,計算機每 90 分鐘刷新一次組策略,並應用所做的更改。

## <a name="create-a-custom-group-policy-object"></a>建立自訂群組原則物件

要對類似的策略設置進行分組,您通常會創建其他 GPO,而不是在單個預設 GPO 中應用所有必需的設定。 使用 Azure AD DS,您可以創建或導入自己的自訂組策略物件,並將它們連結到自訂 OU。 如果需要首先創建自定義 OU,請參閱[在 Azure AD DS 託管域中創建自訂 OU。](create-ou.md)

1. 在 **'組原則管理**'toto),選擇自訂組織單位 (OU),如*MyCustomOU*。 右選擇 OU 並**在此網域選擇「在此網域建立 GPO」,然後在此處連結它...**

    ![在群組政策管理控制台中建立自訂 GPO](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)

1. 為新 GPO 指定名稱,如 *「我的自訂 GPO」。,* 然後選擇 **「確定**」。 您可以選擇將此自訂 GPO 基於現有的 GPO 和一組策略選項。

    ![為新自訂 GPO 指定名稱](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)

1. 自定義 GPO 將建立並連結到自訂 OU。 現在要配置策略設置,請右選擇自定義 GPO 並選擇 **"編輯..."**

    ![選擇「編輯」自訂 GPO 的選項](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)

1. 群組**政策管理編輯器**開啟,以便自訂 GPO:

    ![自訂 GPO 可根據需要設定設定](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)

    完成後,選擇 **「檔>儲存**以儲存策略。 默認情況下,計算機每 90 分鐘刷新一次組策略,並應用所做的更改。

## <a name="next-steps"></a>後續步驟

有關可以使用群組政策管理主控台設定的可用群組政策設定的詳細資訊,請參考[使用群組策略設定選項 。][group-policy-console]

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[group-policy-overview]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831791(v=ws.11)
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
[group-policy-console]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789194(v=ws.11)
