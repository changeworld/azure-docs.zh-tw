---
title: 更新 Azure 應用商店中的現有 VM 產品/服務
description: 說明如何更新 Azure Marketplace 中的現有虛擬機器供應項目。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: dsindona
ms.openlocfilehash: fe1397d7ad00a2eb6e9fe04d8bfa50e49839fe34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288779"
---
# <a name="update-an-existing-vm-offer-on-azure-marketplace"></a>更新 Azure Marketplace 中的現有虛擬機器供應項目

本文將逐步說明在 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)更新虛擬機器 (VM) 供應項目並重新發佈的各個層面。 

需要更新供應項目的幾個常見原因如下：

-  將新的 VM 映像版本新增至現有的 SKU
-  變更可取得 SKU 的區域
-  新增 SKU
-  更新供應項目或個別 SKU 的市集中繼資料
-  更新隨用隨付供應項目的價格

為了協助您修改這些項目，入口網站提供了 **「比較」** 與 **「記錄」** 的功能。  

>[!Note]
>雲解決方案供應商 （CSP） 合作夥伴管道加入宣告現已可用。  有關通過 Microsoft CSP 合作夥伴管道行銷產品/服務的更多資訊[，請參閱雲解決方案供應商](../../cloud-solution-providers.md)。

## <a name="unpermitted-changes-to-vm-offer-or-sku"></a>不允許對虛擬機器供應項目或 SKU 進行的變更

供應項目在 Azure Marketplace 中上線後，便無法修改虛擬機器供應項目或 SKU 的某些屬性，主要為：

-  供應項目的**供應項目識別碼**和**發行者識別碼**
-  現有 SKU 的 **SKU** 識別碼
-  現有 SKU 的資料磁碟計數
-  現有 SKU 的計費/授權模式變更
-  提高已發佈的 SKU 價格


## <a name="common-update-operations"></a>一般更新作業

雖然在 VM 上有各式各樣的特性可供變更，以下幾種作業較為常見。

### <a name="update-the-vm-image-version-for-a-sku"></a>更新 SKU 的 VM 映像版本

常見於會定期更新安全性修補程式、額外功能等項目的 VM 映像。  在這類情況下，建議透過以下步驟更新您 SKU 所參考的 VM 映像：

1.  登入 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)。

2.  在 [所有供應項目]**** 下方，尋找您要更新的供應項目。

3.  在 [SKU]**** 索引標籤中，按一下與 VM 映像關聯的 SKU，即可執行更新。

4.  在 [磁碟版本]**** 下方，按一下 [+新增磁碟版本]****，即可新增 VM 映像。

5.  提供新的 VM 映像**磁碟版本**。 磁碟版本必須遵循[語意版本](https://semver.org/)格式。 版本格式應該是 X.Y.Z，其中 X、Y 和 Z 是整數。 請確認您提供的新版本是否大於所有先前的版本，否則在重新發佈新版本後，不會顯示於入口網站或 Azure Marketplace。

6.  對於 [OS VHD URL] ****，請輸入為作業系統 VHD 建立的[共用存取簽章 (SAS) URI](./cpp-get-sas-uri.md)。 

    > [!WARNING] 
    > 無法變更不同 SKU 版本之間的資料磁碟計數。 如果先前的版本已設定資料磁碟，則此新版本也必須擁有相同數量的資料磁碟。

7.  按一下 [發佈]**** 啟動工作流程，將新的 VM 版本發佈至 Azure Marketplace。


### <a name="change-region-availability-of-a-sku"></a>變更 SKU 的區域可用性

經過一段時間，您可能想要在更多區域提供您的供應項目/SKU。  或者，您可能想要在特定區域停止支援供應項目/SKU。
若要修改可用性，請採取下列步驟：

1.  登入 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)。

2.  在 **"所有優惠**"下找到您要更新的優惠。

3.  在 [SKU]**** 索引標籤中，按一下想要修改可用性的 SKU。

4.  按一下 [國家/區域可用性]**** 欄位之下的 [選取國家 (地區)]**** 按鈕。

5.  在區域可用性快顯視窗中，新增或移除此 SKU 的區域。

6.  按一下 [發佈]**** 啟動發佈工作流程，即可更新 SKU 的區域可用性。

如果在新的區域中提供 SKU，您將可透過 [匯出價格資料]**** 功能來指定該特定地區的價格。 如果您加回之前曾經可用的區域，會無法更新其價格，因為不允許變更價格。


### <a name="add-a-new-sku"></a>新增 SKU

依照下列步驟，讓您現有的供應項目可使用新的 SKU： 

1.  登入 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)。

2.  在 **"所有優惠**"下找到您要更新的優惠。

3.  在 [SKU]**** 索引標籤下方，按一下 [新增 SKU]****，並在快顯視窗中提供 [SKU 識別碼]****。

4.  依照[將虛擬機器發佈至 Azure Marketplace](./cpp-publish-offer.md) (英文) 一文中的詳細說明，重新發佈虛擬機器。

5.  按一下 [發佈]**** 啟動工作流程，以發佈您的新 SKU。


### <a name="update-offer-marketplace-metadata"></a>更新供應項目市集中繼資料

使用下列步驟來更新與供應項目關聯的市集中繼資料，例如公司名稱、標誌等等： 

1.  登入 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)。

2.  在 **"所有優惠**"下找到您要更新的優惠。

3.  移至 [Marketplace]**** 索引標籤，然後依照[將虛擬機器發佈至 Azure Marketplace](./cpp-publish-offer.md) (英文) 一文中的說明，來變更中繼資料。

4.  按一下 [發佈]**** 啟動工作流程，以發佈您的變更。


### <a name="update-pricing-on-published-offers"></a>更新已發佈供應項目的價格

發佈隨用隨付的供應項目後，就無法直接提高 SKU 的價格。  （但是，您可以在同一產品/服務下創建新的 SKU，刪除舊的 SKU，然後為新客戶重新發佈產品/服務。 相反，您可以使用以下步驟降低已發佈產品/服務的價格：

1.  登入 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)。

2.  在 [所有供應項目]**** 下方，尋找您要更新的供應項目。

3.  按一下您要降低價格的 SKU。

4.  如果您已在 1x1 GUI 中設定價格，則可直接在 UI 中變更價格。 如果您是以匯入/匯出試算表的方式設定價格，則只能以匯入/匯出功能來降低價格。

3.  按一下 [儲存]****。

4.  按一下 [發佈]**** 啟動工作流程，以發佈您的變更。

降價後的新價格在網站上線後，新客戶即可看見。  新價格對新客戶有下列影響：

- 會以此新費率向客戶收費。 
- 至於現有的客戶，降低後的價格可追溯到降價開始生效時的帳單週期開始時間。
如果客戶在降價時已遭收取費用，則會在下一個帳單週期收到退款單，以補償降價後的價格。


<!-- TD: This has been implemented, need to change the SKU Tab topic to reflect and move this section there. -->
### <a name="simplified-currency-pricing"></a>簡化的貨幣價格

從 2018 年 9 月 1 日開始，入口網站會新增一個 [Simplified Currency Pricing]\(簡化的貨幣價格\)**** 新區段。 Microsoft 目前正在讓價格更好預測，並從您的全球客戶收集意見，藉此精簡 Azure Marketplace 業務流程。 在這個簡化過程中，會減少向您的客戶開立發票時所用的貨幣種類。

新的區段會以新貨幣來顯示價格。一旦所有客戶都改為使用這些新的結算貨幣，原始價格區段即會淘汰不用，而只會留下 [Simplified Currency Pricing]\(簡化的貨幣價格\) 區段。

請在 2018 年 11 月 1 日之前設定區域的新價格，設定之後結算貨幣即會變更。 未變更結算貨幣的區域無法提高價格。

> [!NOTE] 
> 若是使用 API 來發佈供應項目，則您會在 Offer JSON 中看到新區段。 此會標註為 `virtualMachinePricingV2` 或 `monthlyPricingV2`，視供應項目類型而定。 

如果您對此次變更有任何疑問，請聯絡 [Azure Marketplace 支援](../../support-azure-marketplace.md)。


## <a name="compare-feature"></a>比較功能

當您變更已發佈的供應項目時，可使用**比較**功能來稽核已完成的變更。 若要使用此功能：

1.  在編輯流程中的任何時間點，按一下您供應項目的 [比較]**** 按鈕。

    ![比較功能按鈕](./media/publishvm_037.png)


2.  並排檢視行銷資產與中繼資料。


## <a name="history-of-publishing-actions"></a>發佈動作的記錄

若要檢視發佈活動記錄，請在 Cloud Partner 入口網站左邊導覽功能表列中，按一下的 [記錄]**** 項目。 可在該處檢視您 Azure Marketplace 供應項目生命週期中發生的動作 (含時間戳記)。  
<!-- TD: Add after section authored: For more information, see [History page](../portal-tour/cpp-history-page.md). -->

