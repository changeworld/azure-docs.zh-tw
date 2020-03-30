---
title: 在 Azure AD 域服務中創建和管理群組原則 |微軟文檔
description: 瞭解如何編輯內建群組策略物件 （GPO） 並在 Azure 活動目錄域服務託管域中創建自己的自訂策略。
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: iainfou
ms.openlocfilehash: bce71355eef19ec3cc85525033274f57b1a3e0b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78946415"
---
# <a name="administer-group-policy-in-an-azure-ad-domain-services-managed-domain"></a>在 Azure AD 域服務託管域中管理群組原則

Azure 活動目錄域服務 （Azure AD DS） 中的使用者和電腦物件的設置通常使用群組原則物件 （GPO） 進行管理。 Azure AD DS 包括適用于*AddC 使用者和 AADDC* *電腦*容器的內置 GPO。 您可以自訂這些內置 GPO，以便根據環境需要配置群組原則。 *Azure AD DC 管理員*組的成員在 Azure AD DS 域中具有群組原則管理許可權，還可以創建自訂 GPO 和組織單位 （CO）。 有關什麼是群組原則及其工作原理的詳細資訊，請參閱[群組原則概述][group-policy-overview]。

在混合環境中，在本地 AD DS 環境中配置的群組原則不會同步到 Azure AD DS。 要為 Azure AD DS 中的使用者或電腦定義配置設置，請編輯其中一個預設 GPO 或創建自訂 GPO。

本文介紹如何安裝群組原則管理工具，然後編輯內置 GPO 並創建自訂 GPO。

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>開始之前

要完成本文，您需要以下資源和特權：

* 有效的 Azure 訂用帳戶。
    * 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
    * 如果需要，請完成創建[和配置 Azure 活動目錄域服務實例][create-azure-ad-ds-instance]的教程。
* 加入到 Azure AD DS 託管域的 Windows 伺服器管理 VM。
    * 如果需要，請完成[創建 Windows 伺服器 VM 的教程並將其加入託管域][create-join-windows-vm]。
* 屬於您 Azure AD 租用戶中 Azure AD DC 系統管理員** 群組成員的使用者帳戶。

> [!NOTE]
> 通過將新範本複製到管理工作站，可以使用群組原則管理範本。 將 *.admx*檔`%SYSTEMROOT%\PolicyDefinitions`複製到 並複製特定于地區設定的 *.adml* `%SYSTEMROOT%\PolicyDefinitions\[Language-CountryRegion]`檔`Language-CountryRegion`，其中與 *.adml*檔的語言和區域匹配。
>
> 例如，將 *.adml*檔的英文、美國版本複製到`\en-us`資料夾中。
>
> 或者，可以將群組原則管理樣板集中存儲在屬於 Azure AD DS 託管域的網域控制站上。 有關詳細資訊，請參閱如何在[Windows 中創建和管理群組原則管理範本的中央存儲](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra)。

## <a name="install-group-policy-management-tools"></a>安裝群組原則管理工具

要創建和配置群組原則物件 （GPO），您需要安裝群組原則管理工具。 這些工具可以作為功能安裝在 Windows 伺服器中。 有關如何在 Windows 用戶端上安裝管理工具的詳細資訊，請參閱安裝[遠端伺服器管理工具 （RSAT）。][install-rsat]

1. 登錄到管理 VM。 有關如何使用 Azure 門戶進行連接的步驟，請參閱[連接到 Windows 伺服器 VM][connect-windows-server-vm]。
1. 當您登入 VM 時，預設應該會開啟 [伺服器管理員]****。 如果沒有，請在 [開始]**** 功能表上，選取 [伺服器管理員]****。
1. 在 [伺服器管理員]**** 視窗的 [儀表板]** 窗格內，選取 [新增角色及功能]****。
1. 在 [新增角色及功能精靈]**** 的 [開始之前]** 頁面上，選取 [下一步]****。
1. 針對 [安裝類型]**，保持勾選 [角色型或功能型安裝]**** 選項，然後選取 [下一步]****。
1. 在 **"伺服器選擇**"頁上，從伺服器池中選擇當前 VM，如*myvm.aaddscontoso.com，* 然後選擇 **"下一步**"。
1. 在 [伺服器角色]**** 頁面上，按 [下一步]****。
1. 在 [功能]**** 頁面上，選取 [群組原則管理]**** 功能。

    ![從"功能"頁安裝"群組原則管理"](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)

1. 在 [確認]**** 頁面上，選取 [安裝]****。 安裝群組原則管理工具可能需要一兩分鐘。
1. 完成功能安裝時，請選取 [關閉]**** 以結束 [新增角色及功能]**** 精靈。

## <a name="open-the-group-policy-management-console-and-edit-an-object"></a>打開群組原則管理主控台並編輯物件

Azure AD DS 託管域中的使用者和電腦存在預設群組原則物件 （GPO）。 通過從上一部分安裝群組原則管理功能，讓我們查看和編輯現有的 GPO。 在下一節中，您將創建自訂 GPO。

> [!NOTE]
> 若要在 Azure AD DS 託管域中管理群組原則，必須登錄到*屬於 AAD DC 管理員*組的使用者帳戶。

1. 在"開始"螢幕中，選擇 **"管理工具**"。 將顯示可用管理工具的清單，包括上一節中安裝的**群組原則管理**。
1. 要打開群組原則管理主控台 （GPMC），請選擇**群組原則管理**。

    ![群組原則管理主控台打開，準備編輯群組原則物件](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

Azure AD DS 託管域中有兩個內建群組策略物件 （GPO）， 一個用於*AADDC 電腦*容器，一個用於*AADDC 使用者*容器。 您可以自訂這些 GPO 以根據需要在 Azure AD DS 託管域中配置群組原則。

1. 在**群組原則管理**主控台中，展開**林：aaddscontoso.com**節點。 接下來，展開**域**節點。

    *AADDC 電腦*和*AADDC 使用者*存在兩個內置容器。 每個容器都應用了預設 GPO。

    ![內置 GPO 應用於預設的"AddC 電腦"和"AddC 使用者"容器](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)

1. 可以自訂這些內置 GPO 以在 Azure AD DS 託管域上配置特定的群組原則。 右選擇 GPO 之一，如*AADDC 電腦 GPO，* 然後選擇 **"編輯..."**

    ![選擇"編輯"內置 GPO 之一的選項](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)

1. 將打開群組原則管理編輯器工具，以便自訂 GPO，例如*帳戶原則*：

    ![自訂 GPO 可根據需要配置設置](./media/active-directory-domain-services-admin-guide/gp-editor.png)

    完成後，選擇 **"檔>保存**以保存策略。 預設情況下，電腦每 90 分鐘刷新一次群組原則，並應用所做的更改。

## <a name="create-a-custom-group-policy-object"></a>創建自訂群組策略物件

要對類似的策略設置進行分組，您通常會創建其他 GPO，而不是在單個預設 GPO 中應用所有必需的設置。 使用 Azure AD DS，您可以創建或導入自己的自訂群組策略物件，並將它們連結到自訂 OU。 如果需要首先創建自訂 OU，請參閱[在 Azure AD DS 託管域中創建自訂 OU。](create-ou.md)

1. 在 **"群組原則管理**"主控台中，選擇自訂群組織單位 （OU），如*MyCustomOU*。 右選擇 OU 並**在此域中選擇"在此域中創建 GPO"，然後在此處連結它...**

    ![在群組原則管理主控台中創建自訂 GPO](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)

1. 為新 GPO 指定名稱，如 *"我的自訂 GPO"，* 然後選擇 **"確定**"。 您可以選擇將此自訂 GPO 基於現有的 GPO 和一群組原則選項。

    ![為新自訂 GPO 指定名稱](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)

1. 自訂 GPO 將創建並連結到自訂 OU。 現在要配置策略設置，請右選擇自訂 GPO 並選擇 **"編輯..."**

    ![選擇"編輯"自訂 GPO 的選項](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)

1. 組**策略管理編輯器**將打開，以便自訂 GPO：

    ![自訂 GPO 可根據需要配置設置](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)

    完成後，選擇 **"檔>保存**以保存策略。 預設情況下，電腦每 90 分鐘刷新一次群組原則，並應用所做的更改。

## <a name="next-steps"></a>後續步驟

有關可以使用群組原則管理主控台配置的可用群組原則設置的詳細資訊，請參閱[使用群組原則首選項項][group-policy-console]。

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
