---
title: 教學課程 - 在 Azure AD Domain Services 中建立複本集 | Microsoft Docs
description: 了解如何在 Azure 入口網站中建立和使用複本集，以使用 Azure AD Domain Services 進行服務復原
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: iainfou
ms.openlocfilehash: 6f166cdcb5f3764d7b264fdb4ebc082ece4c798b
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/15/2020
ms.locfileid: "88245089"
---
# <a name="tutorial-create-and-use-replica-sets-for-resiliency-or-geolocation-in-azure-active-directory-domain-services-preview"></a>教學課程：針對復原或地理位置在 Azure Active Directory Domain Services (預覽) 中建立和使用複本集

要改善 Azure Active Directory Domain Services (Azure AD DS) 受控網域的復原，或部署到接近您應用程式的其他地理位置，您可以使用「複本集」。 每個 Azure AD DS 受控網域命名空間，例如 aaddscontoso.com，都會包含一個初始複本集。 在其他 Azure 區域中建立其他複本集的能力，可為受控網域提供地理復原功能。

您可以將複本集新增至任何支援 Azure AD DS 之 Azure 區域中的任何對等互連虛擬網路。

複本集是 Azure AD Domain Services 中的公開預覽功能。 請留意預覽版中提供的功能支援可能有所差異。 如需預覽的詳細資訊，請參閱 [Azure Active Directory 預覽 SLA](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 了解虛擬網路需求
> * 建立複本集
> * 刪除複本集

如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)再開始。

## <a name="prerequisites"></a>必要條件

若要完成此教學課程，您需要下列資源和權限：

* 有效的 Azure 訂用帳戶。
    * 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 已在您的 Azure AD 租用戶中使用 Azure Resource Manager 部署模型建立 Azure Active Directory Domain Services 受控網域並進行設定。
    * 如有需要，請[建立並設定 Azure Active Directory Domain Services 受控網域][tutorial-create-instance]。

    > [!IMPORTANT]
    > 使用傳統部署模型建立的受控網域無法使用複本集。 您至少也必須使用受控網域的 *Enterprise* SKU。 如有需要，請[變更受控網域的 SKU][howto-change-sku]。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

在本教學課程中，您會使用 Azure 入口網站來建立和設定管理複本集。 若要開始使用，請先登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="networking-considerations"></a>網路考量

裝載複本集的虛擬網路必須能夠彼此通訊。 依賴 Azure AD DS 的應用程式和服務也需要對裝載複本集的虛擬網路進行網路連線。 必須在所有虛擬網路之間設定 Azure 虛擬網路對等互連，才能建立完整的網狀網路。 這些對等互連可讓您在複本集之間進行有效的內部網站複寫。

您必須先檢閱下列 Azure 虛擬網路需求，才能使用 Azure AD DS 中的複本集：

* 避免 IP 位址空間重疊，以允許虛擬網路進行對等互連和路由。
* 建立具有足夠 IP 位址的子網路，以支援您的案例。
* 請確定 Azure AD DS 有自己的子網路。 請勿與應用程式 VM 和服務共用此虛擬網路子網路。
* 對等互連虛擬網路「不可」轉移。

> [!TIP]
> 在 Azure 入口網站中建立複本集時，系統會為您建立虛擬網路之間的網路對等互連。
>
> 如有需要，您可以在 Azure 入口網站中新增複本集時，建立虛擬網路和子網路。 或者，您可以為複本集選擇目的地區域中的現有虛擬網路資源，並讓系統自動建立對等互連 (如果對等互連不存在)。

## <a name="create-a-replica-set"></a>建立複本集

當您建立受控網域 (例如 aaddscontoso.com) 時，會建立初始複本集。 其他複本集會共用相同的命名空間和組態。 Azure AD DS 的變更，包括組態、使用者識別和認證、群組、群組原則物件、電腦物件和其他變更，會使用 AD DS 複寫套用至受控網域中的所有複本集。

在本教學課程中，您會在與初始 Azure AD DS 複本集不同的 Azure 區域中建立其他複本集。

若要建立其他複本集，請完成下列步驟：

1. 在 Azure 入口網站中，搜尋並選取 [Azure AD Domain Services]。
1. 選擇您的受控網域，例如 aaddscontoso.com。
1. 選取左側的 [複本集 (預覽)]。 每個受控網域都會在選取的區域中包含一個初始複本集，如下列範例螢幕擷取畫面所示：

    ![在 Azure 入口網站中檢視和新增複本集的範例螢幕擷取畫面](./media/tutorial-create-replica-set/replica-set-list.png)

    若要建立其他複本集，請選取 [+ 新增]。

1. 在「新增複本集」視窗中選取目的地區域，例如「美國東部」。

    選取目的地區域中的虛擬網路，例如 vnet-eastus，然後選擇子網路，例如 aadds-subnet。 如有需要，請選擇 [建立新的] 以在目的地區域中新增虛擬網路，然後選擇 [管理] 建立 Azure AD DS 的子網路。

    如果這些項目不存在，則系統會在您現有的受控網域虛擬網路和目的地虛擬網路之間自動建立 Azure 虛擬網路對等互連。

    下列範例螢幕擷取畫面顯示在「美國東部」中建立新複本集的流程：

    ![在 Azure 入口網站中建立本集的範例螢幕擷取畫面](./media/tutorial-create-replica-set/create-replica-set.png)

1. 在準備就緒時，選取 [儲存]。

建立複本集的流程需要一些時間，因為這些資源是在目的地區域中建立的。 受控網域本身接著會使用 AD DS 複寫進行複寫。

系統繼續部署時，複本集會回報為「佈建中」，如下列範例螢幕擷取畫面所示。 完成時，複本集會顯示為「執行中」。

![Azure 入口網站中複本集部署狀態的範例螢幕擷取畫面](./media/tutorial-create-replica-set/replica-set-provisioning.png)

## <a name="delete-a-replica-set"></a>刪除複本集

受控網域目前限制為四個複本，分別是初始複本集，以及三個額外的複本集。 如果不再需要複本集，或要在另一個區域中建立複本集，可以刪除不需要的複本集。

> [!IMPORTANT]
> 您無法刪除受控網域中的最後一個複本集。

若要刪除複本集，請完成下列步驟：

1. 在 Azure 入口網站中，搜尋並選取 [Azure AD Domain Services]。
1. 選擇您的受控網域，例如 aaddscontoso.com。
1. 選取左側的 [複本集 (預覽)]。 從複本集清單中，選取您想要刪除之複本集旁的 **...** 捷徑功能表。
1. 從捷徑功能表中選取 [刪除]，然後確認您想要刪除複本集。

> [!NOTE]
> 刪除複本集可能是十分耗時的作業。

如果不再需要複本集所使用的虛擬網路或對等互連，您也可以刪除這些資源。 刪除之前，請確定其他區域中的其他應用程式資源都不需要網路連線。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 設定虛擬網路對等互連
> * 在不同的地理區域中建立複本集
> * 刪除複本集

如需詳細的概念資訊，請了解複本集在 Azure AD DS 中的運作方式。

> [!div class="nextstepaction"]
> [複本集概念和功能][concepts-replica-sets]

<!-- INTERNAL LINKS -->
[replica-sets]: concepts-replica-sets.md
[tutorial-create-instance]: tutorial-create-instance-advanced.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[howto-change-sku]: change-sku.md
[concepts-replica-sets]: concepts-replica-sets.md
