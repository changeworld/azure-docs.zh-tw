---
title: 在 Azure Marketplace 上設定虛擬機器供應專案屬性
description: 瞭解如何在 Azure Marketplace 上設定虛擬機器供應專案屬性。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: 281553782774c31ec8cfaf614542fd739c4d3dd9
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629524"
---
# <a name="how-to-configure-virtual-machine-offer-properties"></a>如何設定虛擬機器供應專案屬性

**在 [內容] 頁面上** (從合作夥伴中心) 的左側導覽功能表中選取，您可以定義用來將虛擬機器分組 (VM 的類別) 、您的應用程式版本，以及支援您的供應專案的法律合約。

## <a name="select-a-category"></a>選取類別目錄

選取類別和子類別，以將您的供應專案放在適當的 Azure Marketplace 搜尋區域中。 請務必在供應專案描述中稍後描述您的供應專案如何支援這些類別。

- 選取主要類別。
- 若要在次要)  (新增第二個選擇性類別，請選取 [ **+ 類別** ] 連結。
- 針對主要和/或次要類別，最多選取兩個子類別。 如果沒有任何子類別適用于您的供應專案，請選取 [ **不適用** ]。 使用 Ctrl + 按一下來選取第二個子類別。

請參閱 [供應專案清單最佳做法](gtm-offer-listing-best-practices.md)中的類別和子類別的完整清單。 虛擬機器供應專案一律會出現在 Azure Marketplace 的 [ **計算** ] 類別之下。

## <a name="provide-terms-and-conditions"></a>提供條款及條件

在 [ **法律** ] 下，提供供應專案的條款及條件。 您有兩個選擇：

- [使用標準合約搭配選擇性的修正](#use-the-standard-contract)
- [使用您自己的條款及條件](#use-your-own-terms-and-conditions)

若要深入瞭解標準合約和選擇性的修訂，請參閱 [Microsoft 商業 marketplace 的標準合約](standard-contract.md)。 您可以下載 [標準合約](https://go.microsoft.com/fwlink/?linkid=2041178) PDF (確定您的快顯封鎖程式) 。

### <a name="use-the-standard-contract"></a>使用標準合約

為了簡化客戶的採購程式，並降低軟體廠商的法律複雜性，Microsoft 提供了一種標準合約，可供您在商業市集中提供供應專案使用。 當您在標準合約下提供您的軟體時，客戶只需要讀取並接受一次，您就不需要建立自訂條款及條件。

1. 選取 [ **使用 Microsoft 的商業 marketplace 標準合約** ] 核取方塊。

   ![說明 [使用 Microsoft 的商業 marketplace 標準合約] 核取方塊。](partner-center-portal/media/use-standard-contract.png)

1. 在 **確認** 對話方塊中，選取 [ **接受** ]。 視畫面的大小而定，您可能必須向上移動才能看到它。
1. 選取 [儲存草稿] 後再繼續。

   > [!NOTE]
   > 使用商用 marketplace 的標準合約發佈供應專案之後，您就無法使用自己的自訂條款及條件。 您可以使用選擇性的修訂或您自己的條款及條件，在標準合約下提供您的解決方案。

#### <a name="add-amendments-to-the-standard-contract-optional"></a>將修訂新增至標準合約 (選擇性) 

有兩種可用的改正： *通用* 和 *自訂* 。

##### <a name="add-universal-amendment-terms"></a>新增通用修訂條款

在 [ **Microsoft 商業 marketplace 標準合約] 的 [通用修訂條款** ] 方塊中，輸入您的通用修訂條款。 您可以在此方塊中輸入不限數目的字元。 客戶在探索和購買流程期間，會於 AppSource、Azure Marketplace 和/或 Azure 入口網站看到這些條款。

##### <a name="add-one-or-more-custom-amendments"></a>新增一或多個自訂修正

1. 在 **Microsoft 商業 marketplace 標準合約的自訂修訂條款** 底下，選取 [ **新增自訂修訂條款] ([最大 10])** 連結。
2. 在 box 中輸入您的 **自訂修訂條款** 。
3. 在方塊中輸入 **租使用者識別碼** 。 只有與您為這些自訂條款指定的租使用者識別碼相關聯的客戶，才會在 Azure 入口網站的供應專案購買流程中看到這些客戶。

   > [!TIP]
   > 租使用者識別碼會識別您在 Azure 中的客戶。 您可以要求客戶提供此識別碼，並藉由前往 [**https://portal.azure.com**](https://portal.azure.com)  >  **Azure Active Directory**  >  **屬性** 來尋找。 目錄識別碼值是租使用者識別碼 (例如 `50c464d3-4930-494c-963c-1e951d15360e`) 。 您也可以在[我的 Microsoft Azure 和 Office 365 租用戶識別碼是什麼？](https://www.whatismytenantid.com/) (英文) 中，使用其網域名稱 URL 來查詢客戶的組織租用戶識別碼。

4. （選擇性）輸入租使用者識別碼的易記 **描述** 。 此描述可協助您識別您以修訂為目標的客戶。
5. 若要新增另一個租使用者識別碼，請選取 [ **新增客戶的租使用者識別碼 (最大 10)** ] 連結，然後重複步驟3和4。 您最多可以新增20個租使用者識別碼。
6. 若要新增其他修訂期限，請重複步驟1到5。 您可以為每個供應項目最多提供 10 個自訂增修條款。
7. 選取 [儲存草稿] 後再繼續。

### <a name="use-your-own-terms-and-conditions"></a>使用您自己的條款及條件

您可以選擇提供您自己的條款及條件，而不是標準合約。 客戶必須先接受這些條款，才能試用您的供應項目。

1. 在 [ **法律** ] 下，清除 [ **使用 Microsoft 的商業 marketplace 標準合約** ] 核取方塊。
1. 在 [ **條款及條件** ] 方塊中，輸入最多10000個字元的文字。

   > [!NOTE]
   > 如果您需要較長的描述，請輸入指向可在何處找到您的條款及條件的單一網址。 這將會向客戶顯示為作用中連結。

1. 選取 [ **儲存草稿** ]，然後繼續前往左側導覽功能表中的下一個索引標籤， **提供清單** 。

## <a name="next-steps"></a>後續步驟

- [設定 VM 供應項目清單](azure-vm-create-listing.md)
