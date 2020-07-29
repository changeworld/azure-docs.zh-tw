---
title: Azure AD Domain Services 的限域同步處理 |Microsoft Docs
description: 瞭解如何使用 Azure 入口網站來設定從 Azure AD 到 Azure Active Directory Domain Services 受控網域的範圍同步處理
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/24/2020
ms.author: iainfou
ms.openlocfilehash: 97c561b2ae919799343abe844b57239d04c26d90
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87283124"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services-using-the-azure-portal"></a>使用 Azure 入口網站，設定從 Azure AD 到 Azure Active Directory Domain Services 的範圍同步處理

為了提供驗證服務，Azure Active Directory Domain Services （Azure AD DS）會同步處理 Azure AD 的使用者和群組。 在混合式環境中，內部部署 Active Directory Domain Services （AD DS）環境中的使用者和群組可以先使用 Azure AD Connect 同步處理 Azure AD，然後同步處理到 Azure AD DS 受控網域。

根據預設，Azure AD 目錄中的所有使用者和群組都會同步處理至受控網域。 如果您有特定需求，您可以改為選擇只同步處理一組已定義的使用者。

本文說明如何設定限域同步處理，然後使用 Azure 入口網站來變更或停用一組範圍的使用者。 您也可以[使用 PowerShell 來完成這些步驟][scoped-sync-powershell]。

## <a name="before-you-begin"></a>開始之前

若要完成本文章，您需要下列資源和權限：

* 有效的 Azure 訂用帳戶。
    * 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
    * 如有需要，請完成教學課程，以[建立及設定 Azure Active Directory Domain Services 受控網域][tutorial-create-instance]。
* 您需要 Azure AD 租使用者中的*全域系統管理員*許可權，才能變更 Azure AD DS 同步處理範圍。

## <a name="scoped-synchronization-overview"></a>限域同步處理總覽

根據預設，Azure AD 目錄中的所有使用者和群組都會同步處理至受控網域。 如果只有少數幾個使用者需要存取受控網域，您只能同步處理那些使用者帳戶。 此限域同步處理是以群組為基礎。 當您設定以群組為基礎的範圍同步處理時，只有屬於您指定之群組的使用者帳戶會同步處理至受控網域。 嵌套的群組不會同步處理，只有您選取的特定群組。

當您建立受控網域或部署之後，您可以變更同步處理範圍。 您現在也可以變更現有受控網域上的同步處理範圍，而不需要重新建立。

若要深入瞭解同步處理常式，請參閱[瞭解 Azure AD Domain Services 中的同步][concepts-sync]。

> [!WARNING]
> 變更同步處理的範圍會導致受控網域重新同步處理所有資料。 您必須考量下列事項：
>
>  * 當您變更受控網域的同步處理範圍時，會進行完整重新同步處理。
>  * 系統會刪除受控網域中不再需要的物件。 系統會在受控網域中建立新物件。

## <a name="enable-scoped-synchronization"></a>啟用限域同步處理

若要在 Azure 入口網站中啟用限域同步處理，請完成下列步驟：

1. 在 [Azure 入口網站中，搜尋並選取 [ **Azure AD Domain Services**]。 選擇您的受控網域，例如 aaddscontoso.com。
1. 從左側功能表中選取 [**同步**處理]。
1. 針對 [*同步處理類型*]，選取 [**範圍**]。
1. 選擇 [**選取群組**]，然後搜尋並選擇要新增的群組。
1. 進行所有變更時，請選取 **[儲存同步處理範圍**]。

變更同步處理的範圍會導致受控網域重新同步處理所有資料。 系統會刪除受控網域中不再需要的物件，而重新同步處理可能需要一些時間才能完成。

## <a name="modify-scoped-synchronization"></a>修改限域同步處理

若要修改使用者應該同步處理至受控網域的群組清單，請完成下列步驟：

1. 在 [Azure 入口網站中，搜尋並選取 [ **Azure AD Domain Services**]。 選擇您的受控網域，例如 aaddscontoso.com。
1. 從左側功能表中選取 [**同步**處理]。
1. 若要新增群組，請選擇頂端的 [ **+ 選取群組**]，然後選擇要新增的群組。
1. 若要從同步處理範圍中移除群組，請從目前同步處理的群組清單中選取它，然後選擇 [**移除群組**]。
1. 進行所有變更時，請選取 **[儲存同步處理範圍**]。

變更同步處理的範圍會導致受控網域重新同步處理所有資料。 系統會刪除受控網域中不再需要的物件，而重新同步處理可能需要一些時間才能完成。

## <a name="disable-scoped-synchronization"></a>停用範圍同步處理

若要針對受控網域停用以群組為基礎的範圍同步處理，請完成下列步驟：

1. 在 [Azure 入口網站中，搜尋並選取 [ **Azure AD Domain Services**]。 選擇您的受控網域，例如 aaddscontoso.com。
1. 從左側功能表中選取 [**同步**處理]。
1. 將*同步處理類型*從 [**範圍**] 變更為 [**全部**]，然後選取 [**儲存同步處理範圍**]。

變更同步處理的範圍會導致受控網域重新同步處理所有資料。 系統會刪除受控網域中不再需要的物件，而重新同步處理可能需要一些時間才能完成。

## <a name="next-steps"></a>後續步驟

若要深入瞭解同步處理常式，請參閱[瞭解 Azure AD Domain Services 中的同步][concepts-sync]。

<!-- INTERNAL LINKS -->
[scoped-sync-powershell]: powershell-scoped-synchronization.md
[concepts-sync]: synchronization.md
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md