---
title: 變更 Azure AD Domain Services 的 SKU |Microsoft Docs
description: 瞭解如果您的業務需求變更，如何使用 Azure AD Domain Services 受控網域的 SKU 層
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 320bd87aa78d26cee44c48f27365febd1dd426ff
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620285"
---
# <a name="change-the-sku-for-an-existing-azure-active-directory-domain-services-managed-domain"></a>變更現有 Azure Active Directory Domain Services 受控網域的 SKU

在 Azure Active Directory Domain Services (Azure AD DS) 中，可用的效能和功能是以 SKU 類型為基礎。 這些功能差異包括備份頻率或單向輸出樹系信任的最大數目。

當您建立受控網域時，您會選取一個 SKU，而您可以在部署受控網域之後，在您的業務需求變更時，相應增加或減少 Sku。 商務需求的變更可能包括需要更頻繁的備份或建立額外的樹系信任。 如需不同 Sku 的限制和定價的詳細資訊，請參閱 [AZURE AD DS SKU 概念][concepts-sku] 和 [Azure AD ds 定價][pricing] 頁面。

本文說明如何使用 Azure 入口網站來變更現有 Azure AD DS 受控網域的 SKU。

## <a name="before-you-begin"></a>開始之前

若要完成本文章，您需要下列資源和權限：

* 有效的 Azure 訂用帳戶。
    * 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
    * 如有需要，請完成教學課程以 [建立和設定受控網域][create-azure-ad-ds-instance]。

## <a name="sku-change-limitations"></a>SKU 變更限制

部署受控網域之後，您可以將 Sku 變更為相應增加或減少。 不過，如果您使用資源樹系，並已建立從 Azure AD DS 到內部部署 AD DS 環境的單向輸出樹系信任，則 SKU 變更作業有一些限制。 *Premium* 和 *Enterprise* sku 會定義您可建立之信任數目的限制。 您無法變更為具有低於目前設定之最大限制的 SKU。

例如：

* 如果您已在 *Premium* sku 上建立兩個樹系信任，則無法變更為 *標準* sku。 *標準* SKU 不支援樹系信任。
* 或者，如果您已在 *Premium* sku 上建立了七個信任，您將無法變更為 *企業* SKU。 *企業* SKU 最多可支援五個信任。

如需這些限制的詳細資訊，請參閱 [AZURE AD DS SKU 的功能和限制][concepts-sku]。

## <a name="select-a-new-sku"></a>選取新的 SKU

若要使用 Azure 入口網站變更受控網域的 SKU，請完成下列步驟：

1. 在 Azure 入口網站頂端，搜尋並選取 [ **Azure AD Domain Services**]。 從清單中選擇您的受控網域，例如 *aaddscontoso.com*。
1. 在 Azure AD DS] 頁面左側的功能表中，選取 [ **設定] > SKU**。

    ![在 Azure 入口網站中，為您的 Azure AD DS 受控網域選取 SKU 功能表選項](media/change-sku/overview-change-sku.png)

1. 從下拉式功能表中，選取您要用於受控網域的 SKU。 如果您有資源樹系，則無法選取 *標準* SKU，因為樹系信任只適用于 *企業* SKU 或更高版本。

    從下拉式功能表中選擇您要的 SKU，然後選取 [ **儲存**]。

    ![從 Azure 入口網站的下拉式功能表中選擇所需的 SKU](media/change-sku/change-sku-selection.png)

變更 SKU 類型可能需要一或兩分鐘的時間。

## <a name="next-steps"></a>後續步驟

如果您有資源樹系，且想要在 SKU 變更後建立其他信任，請參閱 [在 AZURE AD DS 中建立內部部署網域的輸出樹系信任][create-trust]。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus
[create-trust]: tutorial-create-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
