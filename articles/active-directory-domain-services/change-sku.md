---
title: 更改 Azure AD 網域服務的 SKU |微軟文件
description: 瞭解在業務需求發生變化時,如何存取 Azure AD 網域服務託管域的 SKU 層
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/31/2020
ms.author: iainfou
ms.openlocfilehash: 32f8f157abaf5076911c3908a83be4a644e09656
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655590"
---
# <a name="change-the-sku-for-an-existing-azure-ad-domain-services-managed-domain"></a>變更現有 Azure AD 網域服務託管網域的 SKU

在 Azure 活動目錄域服務 (Azure AD DS) 中,可用性能和功能基於 SKU 類型。 這些功能差異包括備份頻率或單向出站林信任的最大數量(當前處於預覽中)。 創建託管域時選擇 SKU,並且可以在部署託管域後,隨著業務需求的變化而向上或向下切換 SKU。 業務需求的變化可能包括需要更頻繁的備份或創建其他林信任。 有關不同 SKU 的限制和定價的詳細資訊,請參閱 Azure [AD DS SKU 概念][concepts-sku]和[Azure AD DS 定價][pricing]頁。

本文演示如何使用 Azure 門戶更改現有 Azure AD DS 託管域的 SKU。

## <a name="before-you-begin"></a>開始之前

要完成本文,您需要以下資源和特權:

* 有效的 Azure 訂用帳戶。
    * 如果沒有 Azure 訂閱,[請建立帳號](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
    * 如果需要,請完成創建[和配置 Azure 活動目錄域服務實例][create-azure-ad-ds-instance]的教程。

## <a name="sku-change-limitations"></a>SKU 變更限制

在部署 Azure AD DS 託管域後,可以向上或向下更改 SKU。 但是,如果使用資源林(當前處於預覽狀態),並且創建了從 Azure AD DS 到本地 AD DS 環境的單向出站林信任,則 SKU 更改操作存在一些限制。 *高級**和企業*SKU 定義了可創建的信任數量的限制。 不能更改為最大限制低於當前配置的 SKU。

例如：

* 如果在*先進*SKU 上建立了兩個林信任,則無法向下更改為*標準*SKU。 *標準*SKU 不支援林信任。
* 或者,如果您在*高級*SKU 上創建了七個信任,則無法向下更改為*企業*SKU。 *企業*SKU 最多支援五個信任。

有關這些限制的詳細資訊,請參閱 Azure [AD DS SKU 功能和限制][concepts-sku]。

## <a name="select-a-new-sku"></a>選擇新的 SKU

要使用 Azure 門戶更改 Azure AD DS 託管域的 SKU,請使用以下步驟完成:

1. 在 Azure 門戶的頂部,搜尋並選擇**Azure AD 網域服務**。 從清單中選擇託管域,如*aaddscontoso.com*。
1. 在 Azure AD DS 頁面左側的選單中,選擇 **「設定」> SKU**。

    ![在 Azure 門戶中選擇 Azure AD DS 託管域的 SKU 選單選項](media/change-sku/overview-change-sku.png)

1. 在下拉菜單中,為 Azure AD DS 託管域選擇所需的 SKU。 如果您有資源林,則無法選擇*標準*SKU,因為林信任僅在*企業*SKU 或更高版本上可用。

    從下拉菜單中選擇您想要的 SKU,然後選擇 **「保存**」。

    ![從 Azure 門戶的下拉選單中選擇所需的 SKU](media/change-sku/change-sku-selection.png)

更改 SKU 類型可能需要一兩分鐘。

## <a name="next-steps"></a>後續步驟

如果您有資源林,並希望在 SKU 更改後建立其他信任,請參閱[在 Azure AD DS(預覽)中建立對本地域的出站林信任][create-trust]。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus
[create-trust]: tutorial-create-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
