---
title: 更新市場優惠 |Azure 應用商店
description: 使用 Cloud Partner 入口網站更新 Azure 和 AppSource Marketplace 上的供應項目
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: dsindona
ms.openlocfilehash: 101369a050770be3acd9534cef6229037fe1c366
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288507"
---
# <a name="update-azure-marketplace-and-appsource-offers"></a>更新 Azure Marketplace 和 AppSource 的供應項目

供應項目發佈之後，您可以對它套用各種更新。  [Cloud Partner 入口網站](https://cloudpartner.azure.com/)可協助您正確地修改供應項目的屬性，包括：

-  新增新的虛擬機器 (VM) 映像或套件版本到現有的 SKU
-  變更可取得 SKU 的區域
-  新增 SKU
-  針對供應項目或 SKU 更新 Marketplace 中繼資料 
-  更新隨用隨付供應項目的定價

該入口網站也具有功能，例如比較功能和檢視供應項目功能歷程記錄的功能，可協助您管理變更。  修改供應項目或 SKU 之後，必須重新發行才能反映變更。  本文會引導您完成更新市集供應項目的各種層面。

## <a name="unpermitted-changes-to-an-offersku"></a>不允許對供應項目/SKU 進行的變更

供應項目或 SKU 的某些屬性，一旦在市集上發佈就無法修改。  在入口網站的 [編輯器]**** 索引標籤中停用對應的欄位，例如：  

- 供應項目識別碼以及發行者識別碼
- SKU 識別碼 
- 現有 SKU 的資料磁碟計數
- 現有 SKU 的計費/授權模型變更
- 版本標記，例如：`1.0.1`


## <a name="common-update-operations"></a>一般更新作業

下列各節說明如何執行大部分的更新作業。  這些作業不適用於所有的供應項目類型。  您必須登入 Cloud Partner 入口網站才能開始任何一項作業。


### <a name="update-offer-contacts"></a>更新供應項目連絡人

使用下列步驟來更新供應項目的支援連絡人。
1. 在 [所有供應項目]**** 頁面中選取供應項目。
2. 選擇 **"連絡人"** 選項卡。更新連絡人。
3. 選取 [儲存]**** 按鈕。
4. 選取 [發佈]**** 即可啟動發佈程序。


### <a name="change-regions-an-offer-or-sku-is-available-in"></a>變更可取得供應項目或 SKU 的區域

經過一段時間，您可能想要在更多區域提供您的供應項目/SKU。
或者，您可能想要在特定區域停止支援供應項目/SKU。
若要實作這些變更，請依照下列步驟執行。

1. 在 [所有供應項目]**** 中，尋找您要更新的供應項目。

針對 Azure Marketplace 供應項目：

1. 選擇**SKU**選項卡。 選擇要修改的 SKU。
1. 按一下 [國家/區域可用性]**** 欄位之下的 [選取國家 (地區)]**** 按鈕。
1. 在 [區域可用性] 對話方塊中，新增或移除此 SKU 的區域。

針對 AppSource 供應項目：

1. 選取 [電子店面詳細資料]**** 索引標籤。
1. 在 [支援的國家/地區]**** 標籤旁邊，按一下 [支援的國家/地區]****。 
1. 在 [支援的國家/地區] 對話方塊中，新增或移除此供應項目的區域。

針對任何一個市集：

1. 按一下 [發佈]**** 即可啟動發佈程序。 

如果在新的區域中提供 SKU，您可透過 [匯出價格資料]**** 功能來指定該特定地區的價格。 如果您加回之前曾經可用的區域，會無法更新其價格，因為不允許變更價格。


### <a name="add-a-new-sku"></a>新增 SKU 

若要讓您現有的供應項目可使用新的 SKU，請使用下列步驟：

1. 在 [所有供應項目]**** 頁面中尋找供應項目。
3. 在 [SKU]**** 表單之下，按一下 [新增 SKU]**** 並在快顯視窗中提供 [SKU 識別碼]****。
4. 依照[發行虛擬機器供應項目](../virtual-machine/cpp-publish-offer.md)中詳述的剩餘步驟執行。
5. 按一下 [發佈]**** 即可啟動發佈程序。


### <a name="update-offer-marketplace-assets"></a>更新供應項目市集資產

您可能有需要更新市場文本和圖像資產的方案，例如公司徽標、產品/服務說明等。使用以下步驟更新這些資產。

1. 在 [所有供應項目]**** 頁面中尋找您的供應項目。 
2. 選取 [市集]**** 索引標籤，然後依照供應項目的*市集索引標籤*主題中的指示操作。
3. 按一下 [發佈]**** 即可啟動發佈程序。


### <a name="update-pricing-on-published-offers"></a>更新已發佈供應項目的價格

發佈隨用隨付的供應項目後，就無法提高現有 SKU 的價格。  相反的，可在相同的供應項目下建立 SKU、刪除舊的 SKU，然後再重新發佈您的供應項目。 您可以降低先前發佈的供應項目價格。 降低您的供應項目價格：

1. 選取您要降低價格的 SKU。
2. 您必須使用原本使用的相同機制設定更優惠的價格：直接在入口網站 UI 中或使用匯入/匯出試算表。
3. 按一下 [儲存]****。
4. 按一下 [發佈]**** 即可啟動發佈程序。

一旦供應項目在市集上架，其定價就會顯示給新客戶，而且所有新客戶都只需要支付新的降價後價格。  至於現有的客戶，降低後的價格可追溯到降價開始生效時的帳單週期開始時間。  如果客戶在降價時已遭收取費用，則會在下一個帳單週期收到退款單，以補償降價後的價格。


## <a name="compare-feature"></a>比較功能

當您變更已發佈的供應項目時，可使用「比較」** 功能來稽核變更。 若要利用這項功能：

1. 在編輯程序中的任何時間點，您都可以按一下您供應項目 [編輯器]**** 索引標籤的 [比較]**** 按鈕。
2. 與市集供應項目相比，比較視窗會顯示此供應項目的已儲存變更的並排版本。 

![[編輯器] 索引標籤中的 [比較供應項目] 按鈕](./media/offer-compare-button.png)


## <a name="history-of-publishing-actions"></a>發佈動作的歷程記錄

若要檢視發佈活動記錄，請在 Cloud Partner 入口網站左邊的垂直功能表列中，選取 [記錄]**** 索引標籤。  [記錄] 頁面提供了數個特性的彈性篩選，並支援資料行排序。  每個發佈事件都有時間戳記。  如需詳細資訊，請參閱 [[稽核記錄] 頁面](../portal-tour/cpp-history-page.md)。


## <a name="next-steps"></a>後續步驟

您也可以使用 Cloud Partner 入口網站來[刪除已發佈的 SKU 或供應項目](./cpp-delete-offer.md)。
