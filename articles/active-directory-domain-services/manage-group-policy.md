---
title: 在 Azure AD Domain Services 中建立和管理群組原則 |Microsoft Docs
description: 瞭解如何編輯內建的群組原則物件（Gpo），並在 Azure Active Directory Domain Services 受控網域中建立您自己的自訂原則。
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: d9738d3abfdf30e133ae241c497823be349d25da
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86040073"
---
# <a name="administer-group-policy-in-an-azure-active-directory-domain-services-managed-domain"></a>管理 Azure Active Directory Domain Services 受控網域中的群組原則

Azure Active Directory Domain Services （Azure AD DS）中使用者和電腦物件的設定通常是使用群組原則物件（Gpo）進行管理。 Azure AD DS 包含*AADDC Users*和*AADDC 電腦*容器的內建 gpo。 您可以自訂這些內建 Gpo，視您的環境需求設定群組原則。 *AZURE AD DC 系統管理員*群組的成員具有 Azure AD DS 網域中的群組原則系統管理許可權，也可以建立自訂 gpo 和組織單位（ou）。 如需群組原則及其運作方式的詳細資訊，請參閱[群組原則總覽][group-policy-overview]。

在混合式環境中，內部部署 AD DS 環境中設定的群組原則不會同步處理至 Azure AD DS。 若要定義 Azure AD DS 中的使用者或電腦的設定，請編輯其中一個預設 Gpo，或建立自訂 GPO。

本文說明如何安裝群組原則管理工具，然後編輯內建 Gpo 並建立自訂 Gpo。

## <a name="before-you-begin"></a>開始之前

若要完成本文章，您需要下列資源和權限：

* 有效的 Azure 訂用帳戶。
    * 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
    * 如有需要，請完成教學課程，以[建立及設定 Azure Active Directory Domain Services 受控網域][create-azure-ad-ds-instance]。
* 已加入 Azure AD DS 受控網域的 Windows Server 管理 VM。
    * 如有需要，請完成教學課程以[建立 Windows SERVER VM，並將它加入受控網域][create-join-windows-vm]。
* 屬於您 Azure AD 租用戶中 Azure AD DC 系統管理員** 群組成員的使用者帳戶。

> [!NOTE]
> 您可以藉由將新的範本複製到管理工作站來使用群組原則系統管理範本。 將*admx*檔案複製到中 `%SYSTEMROOT%\PolicyDefinitions` ，並將地區設定特定的*adml*檔案複製到 `%SYSTEMROOT%\PolicyDefinitions\[Language-CountryRegion]` ，其中 `Language-CountryRegion` 符合*adml*檔案的語言和區域。
>
> 例如，將*adml*檔案的英文版本複製到 `\en-us` 資料夾中。
>
> 或者，您也可以在隸屬于受控網域的網域控制站上，集中儲存群組原則系統管理範本。 如需詳細資訊，請參閱[如何在 Windows 中建立和管理群組原則系統管理範本的中央存放區](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra)。

## <a name="install-group-policy-management-tools"></a>安裝群組原則管理工具

若要建立和設定群組原則物件（Gpo），您需要安裝群組原則管理工具。 這些工具可以在 Windows Server 中安裝為功能。 如需有關如何在 Windows 用戶端上安裝系統管理工具的詳細資訊，請參閱 install[遠端伺服器管理工具（RSAT）][install-rsat]。

1. 登入您的管理 VM。 如需如何使用 Azure 入口網站進行連線的步驟，請參閱連線[到 Windows SERVER VM][connect-windows-server-vm]。
1. 當您登入 VM 時，預設應該會開啟 [伺服器管理員]****。 如果沒有，請在 [開始]**** 功能表上，選取 [伺服器管理員]****。
1. 在 [伺服器管理員]**** 視窗的 [儀表板]** 窗格內，選取 [新增角色及功能]****。
1. 在 [新增角色及功能精靈]**** 的 [開始之前]** 頁面上，選取 [下一步]****。
1. 針對 [安裝類型]**，保持勾選 [角色型或功能型安裝]**** 選項，然後選取 [下一步]****。
1. 在 [伺服器選擇]**** 頁面上，從伺服器集區中選擇目前的 VM，例如 myvm.aaddscontoso.com**，然後選取 [下一步]****。
1. 在 [伺服器角色]**** 頁面上，按 [下一步]****。
1. 在 [功能]**** 頁面上，選取 [群組原則管理]**** 功能。

    ![從 [功能] 頁面安裝 [群組原則管理]](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)

1. 在 [確認]**** 頁面上，選取 [安裝]****。 安裝群組原則管理工具可能需要一或兩分鐘的時間。
1. 完成功能安裝時，請選取 [關閉]**** 以結束 [新增角色及功能]**** 精靈。

## <a name="open-the-group-policy-management-console-and-edit-an-object"></a>開啟群組原則管理主控台並編輯物件

受控網域中的使用者和電腦都有預設的群組原則物件（Gpo）。 在上一節安裝群組原則管理功能之後，讓我們來看看並編輯現有的 GPO。 在下一節中，您會建立自訂 GPO。

> [!NOTE]
> 若要管理受控網域中的群組原則，您必須登入屬於*AAD DC 系統管理員*群組成員的使用者帳戶。

1. 從 [開始] 畫面中，選取 [系統**管理工具**]。 隨即會顯示可用的管理工具清單，包括上一節中所安裝的**群組原則管理**。
1. 若要開啟群組原則管理主控台（GPMC），請選擇 [**群組原則管理**]。

    ![群組原則管理主控台開啟 [準備編輯群組原則物件]](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

受控網域中有兩個內建的群組原則物件（Gpo）：一個用於*AADDC 電腦*容器，另一個用於*AADDC 使用者*容器。 您可以視需要自訂這些 Gpo 來設定您的受控網域中的群組原則。

1. 在 [**群組原則管理**] 主控台中，展開 [**樹系： aaddscontoso.com** ] 節點。 接下來，展開 [**網域**] 節點。

    有兩個內建容器適用于*AADDC 電腦*和*AADDC 使用者*。 這些容器都會套用預設 GPO。

    ![適用于預設「AADDC 電腦」和「AADDC 使用者」容器的內建 Gpo](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)

1. 您可以自訂這些內建 Gpo，以在您的受控網域上設定特定的群組原則。 以滑鼠右鍵選取其中一個 Gpo，例如 [ *AADDC 電腦 GPO*]，然後選擇 [**編輯 ...**]。

    ![選擇 [編輯] 其中一個內建 Gpo 的選項](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)

1. [群組原則管理編輯器] 工具隨即開啟，讓您自訂 GPO，例如*帳戶原則*：

    ![自訂 GPO 以視需要進行設定](./media/active-directory-domain-services-admin-guide/gp-editor.png)

    完成時，選擇 [檔案] **> 儲存**] 以儲存原則。 電腦預設會每隔90分鐘重新整理群組原則，並套用您所做的變更。

## <a name="create-a-custom-group-policy-object"></a>建立自訂群組原則物件

若要將類似的原則設定分組，您通常會建立額外的 Gpo，而不是在單一預設 GPO 中套用所有必要的設定。 有了 Azure AD DS，您就可以建立或匯入自己的自訂群組策略物件，並將其連結至自訂 OU。 如果您需要先建立自訂 OU，請參閱[在受控網域中建立自訂 ou](create-ou.md)。

1. 在 [**群組原則管理**] 主控台中，選取您的自訂群組織單位（OU），例如*MyCustomOU*。 以滑鼠右鍵選取 OU，然後選擇 [**在這個網域中建立 GPO 並連結到**...]：

    ![在群組原則管理主控台中建立自訂 GPO](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)

1. 指定新 GPO 的名稱，例如 [我的*自訂 Gpo*]，然後選取 **[確定]**。 您可以選擇性地根據現有的 GPO 和原則選項群組來建立此自訂 GPO 的基礎。

    ![為新的自訂 GPO 指定名稱](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)

1. 自訂 GPO 隨即建立，並連結至您的自訂 OU。 若要立即設定原則設定，請以滑鼠右鍵選取自訂 GPO，然後選擇 [**編輯 ...**]：

    ![選擇 [編輯] 自訂 GPO 的選項](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)

1. 隨即開啟**群組原則管理編輯器**，讓您自訂 GPO：

    ![自訂 GPO 以視需要進行設定](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)

    完成時，選擇 [檔案] **> 儲存**] 以儲存原則。 電腦預設會每隔90分鐘重新整理群組原則，並套用您所做的變更。

## <a name="next-steps"></a>後續步驟

如需可使用群組原則管理主控台設定之可用群組原則設定的詳細資訊，請參閱使用[群組原則喜好設定專案][group-policy-console]。

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
