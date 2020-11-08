---
title: 如何為您的 Azure 應用程式供應專案建立方案
description: 瞭解如何在合作夥伴中心中建立 Azure 應用程式供應專案的方案。
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: fd5ee9085cf716128c5b3ae073f963c76d2bd17a
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2020
ms.locfileid: "94370004"
---
# <a name="how-to-create-plans-for-your-azure-application-offer"></a>如何為您的 Azure 應用程式供應專案建立方案

透過 Microsoft 商業 marketplace 銷售的優惠必須至少有一個方案，才能在您的商業市場中列出您的供應專案。 您可以在相同的供應專案內使用不同的選項來建立各種不同的方案。 這些方案 (有時稱為 Sku) 在方案類型 ( _解決方案範本_ 或 _受控應用程式_ ) 、營收或物件方面可能不同。 如需方案的一般指引，請參閱 [商業 marketplace 供應專案的方案和定價](plans-pricing.md)。

## <a name="create-a-plan"></a>建立方案

1. 在 [ **計畫總覽** ] 索引標籤頂端附近，選取 [ **+ 建立新方案** ]。
1. 在出現的對話方塊中，于 [ **方案識別碼** ] 方塊中輸入唯一的方案識別碼。 客戶將可在產品 URL 中看到此識別碼。 最多可使用50個小寫英數位元、連字號或底線。 選取 [ **建立** ] 之後，就無法修改方案識別碼。
1. 在 [ **方案名稱** ] 方塊中，輸入此方案的唯一名稱。 當您決定要在您的供應專案內選取哪些方案時，客戶會看到此名稱。 最多使用50個字元。
1. 選取 [建立]  。

## <a name="define-the-plan-setup"></a>定義計畫設定

[ **方案設定** ] 索引標籤可讓您設定方案的類型、是否重複使用另一個方案的技術設定，以及方案應該在哪些 Azure 區域中使用。 您在此索引標籤上的答案將會影響在此方案的其他索引標籤上所顯示的欄位。

### <a name="select-the-plan-type"></a>選取方案類型

- 從計畫清單的 [ **類型** ] 中，選取 [ **方案範本** ] 或 [ **受控應用程式** ]。

**解決方案範本** 方案完全由客戶所管理。 **受控應用程式** 方案可讓發行者代表客戶管理應用程式。 如需這兩種方案類型的詳細資訊，請參閱 [方案類型](plan-azure-application-offer.md#types-of-plans)。

### <a name="re-use-technical-configuration-optional"></a> (選用) 重複使用技術設定

如果您已在此供應專案中建立多個相同類型的方案，而且兩者之間的技術設定相同，則可以重複使用另一個方案中的技術設定。 這個方案發佈之後即無法變更此設定。

#### <a name="to-re-use-technical-configuration"></a>重複使用技術設定

1. 選取 [ **此方案重複使用相同類型的另一個方案中的技術** 設定] 核取方塊。
1. 在出現的清單中，選取您要的基本方案。

> [!NOTE]
> 當您重複使用另一個方案中的套件時，此方案中的整個 [ **技術** 設定] 索引標籤就會消失。 其他方案的技術設定詳細資料，包括您在未來所做的任何更新，也會用於此方案。

### <a name="select-azure-regions-clouds"></a>選取 Azure 區域 (雲端) 

您的方案必須至少在一個 Azure 區域中提供。 在您的方案發佈並可在特定 Azure 區域中使用之後，您就無法從供應專案中移除該區域。

#### <a name="azure-global-region"></a>Azure 全球區域

預設會選取 [ **Azure Global** ] 核取方塊。 這可讓您的方案可供所有具有商業 marketplace 整合的 Azure 全球區域中的客戶使用。 針對受管理的應用程式方案，您可以選取想要讓方案可供使用的市場。

若要從此區域移除您的供應專案，請清除 [ **Azure 全球** ] 核取方塊。

#### <a name="azure-government-region"></a>Azure Government 區域

此區域提供美國聯邦、州、地方或部落實體的客戶控制存取權，以及可提供服務的合作夥伴。 身為發佈者的您必須負責任何合規性控制、安全性措施和最佳做法。 Azure Government 會使用實際隔離的資料中心和網路 (僅限位於美國地區)。

Azure Government 服務會處理受限於特定政府法規和需求的資料。 例如，FedRAMP、NIST 800.171 (DIB)、ITAR、IRS 1075、DoD L4 和 CJIS。 若要告知這些程式的認證，您可以提供最多 100 個描述這些認證的連結。 這些可以是直接指向程式清單的連結，或是指向您自己網站上相關合規性描述的連結。 只有 Azure Government 客戶可以看到這些連結。

##### <a name="to-select-the-azure-government-region"></a>選取 Azure Government 區域

1. 選取 [ **Azure Government** ] 核取方塊。
1. 在 [ **Azure Government 認證** ] 下，選取 [ **+ 新增認證] ([最大 100])** 。
1. 在出現的方塊中，提供認證的名稱和連結。
1. 若要新增另一個認證，請重複步驟2和3。

選取 [ **儲存草稿** ]，然後繼續進行下一個索引標籤：方案清單。

## <a name="define-the-plan-listing"></a>定義計畫清單

[ **計畫清單** ] 索引標籤可讓您設定方案的清單詳細資料。 此索引標籤會顯示特定的資訊，以顯示相同供應專案中方案之間的差異。 您可以定義方案名稱、摘要和描述，以便讓它們出現在商業 marketplace 中。

1. 在 [ **方案名稱** ] 方塊中，您稍早為此方案提供的名稱會出現在這裡。 您可以隨時變更。 此名稱會在商業 marketplace 中顯示為供應專案的軟體方案標題，且限制為100個字元。
1. 在 [ **方案摘要** ] 方塊中，提供方案的簡短摘要， (不是供應專案) 。 此摘要限制為 100 個字元。
1. 在 [ **計畫描述** ] 方塊中，說明如何讓此軟體方案成為唯一的，以及與您供應專案中的其他方案有何差異。 請不要描述供應項目，而只描述方案。 此描述最多可包含2000個字元。
1. 選取 [儲存草稿] 後再繼續。

## <a name="next-steps"></a>後續步驟

請執行下列其中一項：

- 如果您要設定解決方案範本方案，請移至 [ [設定方案範本方案](create-new-azure-apps-offer-solution.md)]。
- 如果您正在設定受管理的應用程式方案，請移至 [設定受控應用程式方案](create-new-azure-apps-offer-managed.md)。
