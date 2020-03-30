---
title: Azure 應用商店雲合作夥伴門戶中的虛擬機器 SKU 選項卡
description: 介紹用來在 Azure Marketplace 中建立虛擬機器供應項目的 SKU 索引標籤。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: 49f1de5128325b2884ea76b010727be45f1b195d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288881"
---
# <a name="virtual-machine-skus-tab"></a>虛擬機器 SKU 索引標籤

可使用 [新增供應項目]**** 頁面的 [SKU]**** 索引標籤，建立一個或一個以上的 SKU，並建立 SKU 與新供應項目之間的關聯性。  不同的 SKU 可依功能集、VM 映像類型、輸送量或延展性、帳單模式或其他特性，讓解決方案彼此互有差異。


## <a name="create-a-sku"></a>建立 SKU

新供應項目起初並無任何關聯的 SKU，因此請按一下 [新 SKU]**** 來建立一個 SKU。

![虛擬機器的 [新增供應項目] 索引標籤上的 [新 SKU] 按鈕](./media/publishvm_005.png)

<br/>

此時會顯示 [新 SKU]**** 對話方塊。  請輸入新 SKU 的識別碼，然後按一下 [確定]****。 （有關識別碼命名約定，請參閱下文。 **SKU**選項卡現在將顯示可用於編輯的欄位。    欄位名稱上附加星號 (*) 表示為必填欄位。

<!-- TD: This tab has been updated, now has "Old Pricing" and "Simplified Currency Pricing" sections"! -->

![虛擬機器"新優惠"表單上的 SKU 選項卡](./media/publishvm_006.png)

下表會說明這些欄位的用途、內容和格式。  必要欄位由星號 (*) 標示。

<!-- TD: I took a new screenshot, and the fields differ somewhat from description in the VM Pub Guide.  Needs review. -->

|  **領域**       |     **描述**                                                          |
|  ---------       |     ---------------                                                          |
|  *SKU 設定*   |    |
| **SKU 識別碼\***       | 此 SKU 的識別碼。  此名稱的長度上限為 50 個字元，由小寫英數字元或連字號 (-) 組成，但不能以連字號結尾。  發佈供應項目之後，即無法進行修改。  |
|  *SKU 詳細資料*   |  |
| **標題\***        | 要在市集中顯示的供應項目易記名稱。 長度上限為 50 個字元。 |
| **總結\***      | 要在市集中顯示的供應項目精簡說明。 長度上限是 100 個字元。 |
| **描述\***  | 進一步詳盡說明該供應項目的介紹文字。  <!-- TD: max len/guidance? 3k characters -->  |
| **隱藏此 SKU\*** | 表明是否要在市集中向客戶顯示該 SKU。  如果您只想要透過解決方案範本來提供此 SKU，而非以單獨購買的方式供應，則可隱藏此 SKU。  若是要進行初始測試，或是供應項目屬於暫時性或季節性，此功能也相當實用。 |
| **雲可用性\*** | 決定該 SKU 應在何種雲端上供應。  預設為 Azure 公用版本。  Microsoft Azure Government 是政府社群雲端，US 聯邦政府、州政府、本地政府或部落政府機構，以及通過認證的合作夥伴，皆擁有此雲端的控管權限。  如需有關政府雲端的詳細資訊，請參閱[歡迎使用 Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) (英文)。 |
| **這是私人 SKU 嗎？\*** | 表明 SKU 為私人或公用。 預設值為 [否]**** (公用)。  如需詳細資訊，請參閱[私人 SKU](../../cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md)。 |
| **國家/地區可用性\*** | 決定可在哪些國家或全球地區購買此 SKU。 請至少選取一個地區/國家。 <!-- TD: Is this parameter an AMP visibility control or a contractual one, or both? --> |  
|  *定價*   |  |
| **許可證模型\***| 使用標準化帳單模式。  如果選取 [Usage-based monthly billed SKU]\(依用量每月計費的 SKU\)****，則會開啟一個 Accordion 區段，可在此指定每核心價格的詳細資料，以及是否要提供免費試用期。  也可已在此區段內將價格排程匯出與匯入至 Excel。 有關詳細資訊，請參閱[Azure 應用商店中的計費選項](../../billing-options-azure-marketplace.md)。 | 
|  *VM 映像*   |  |
| **作業系統系列\*** | 表明此解決方案虛擬機器採用的是 Windows 架構還是 Linux 架構。 |
| **選取作業系統類型** | 特定廠商或指定的作業系統版本。 |
| **作業系統易記名稱\*** | 要向客戶顯示的作業系統名稱。  |
| **推薦的 VM 大小\*** | 允許從標準化清單選取最多六種建議的虛擬機器大小。  此清單將傳遞給 Azure 門戶和 Microsoft 市場。  此清單中的第一個 VM 大小有效（對於該客戶訂閱、區域、區域等）設置為該潛在客戶的預設值。  使用者可以將此大小更改為與解決方案映射相容的任何大小。 | 
| **開啟連接埠**| 要對 SKU 開啟的連接埠，以及支援的通訊協定。  這些設定必須符合您幫解決方案虛擬機器網路所設定的虛擬網路。 這些設定會在虛擬機器部署期間生效。 不過，您可在發佈 SKU 後修改連接埠設定。 如需詳細資訊，請參閱[如何使用 Azure 入口網站開啟虛擬機器的連接埠](https://docs.microsoft.com/azure/virtual-machines/windows/nsg-quickstart-portal)。 <br/>下列的預設網路對應會新增至所有虛擬機器。 &emsp; Windows：3389 -> 3389 TCP, 5986 -> 5986 TCP；&emsp; Linux：22 -> 22, TCP (SSH)。 |
| **磁碟版本**  | 依磁碟版本號碼和磁碟 URL 指定的關聯解決方案虛擬機器。 磁碟版本必須採用[語意版本](https://semver.org/)格式：`<major>.<minor>.<patch>`。  URL 是針對作業系統 VHD 建立的共用存取簽章 URI。  雖然每個 SKU 最多可加入八個磁碟版本，但在 Azure Marketplace 中僅會顯示 SKU 的最高磁碟版本號碼。 僅可透過 API 查看其他版本。  <!--TD: Add more specific link to API --> <br/> 可至 [新的資料磁碟]**** Accordion 區段，在虛擬機器上附加最多 15 個資料磁碟。  一旦您發佈具有虛擬機器版本和關聯資料磁碟的 SKU，即無法修改此設定。  若新增更多虛擬機器版本至 SKU，則新增的版本所支援的資料磁碟數目必須相同。 <br/> 如果您尚未建立以 Azure 為基礎的 VM 映像，則可稍後再更新此欄位。  如需建立關聯虛擬機器資源的相關資訊，請參閱[建立虛擬機器技術資產](./cpp-create-technical-assets.md) (英文)。  
|  |  |

<!-- TD: The CPP UX warning msg indicates that underscores are also supported in these SKU IDs. I suspect this might be true for other identifiers. --> 

<br/> 按一下 [儲存]**** 以儲存您的進度。 在下一個索引標籤中，請指定供應項目是否支援[試用產品](./cpp-test-drive-tab.md)。


## <a name="additional-pricing-considerations"></a>其他價格考量

上述的價格模式為基本說明。  價格模式目前正在變更，且可能會受到地區稅務法規和 Microsoft 價格政策等因素所影響。 

### <a name="new-core-sizes-added-on-722019"></a>2019 年 7 月 2 日新增的核心尺寸

VM 發佈商于 2019 年 7 月 2 日收到新 Azure 虛擬機器大小（基於內核數）新價格的通知。  新價格適用于核心尺寸 10、44、48、60、120、208 和 416。  對於現有 VM，這些內核的大小可根據當前價格自動計算新的價格。  出版商必須到 2019 年 8 月 1 日才能查看附加價格並進行任何所需的更改。  在此日期之後，如果發行者尚未重新發佈，則這些新內核大小的自動計算價格將生效。


### <a name="simplified-currency-pricing"></a>簡化的貨幣價格

從 2018 年 9 月 1 日開始，入口網站會新增一個 [Simplified Currency Pricing]\(簡化的貨幣價格\)**** 新區段。 Microsoft 目前正在讓價格更好預測，並從您的全球客戶收集意見，藉此精簡 Azure Marketplace 業務流程。 在這個簡化過程中，會減少向您的客戶開立發票時所用的貨幣種類。  如需詳細資訊，請參閱[更新 Azure Marketplace 中的現有虛擬機器供應項目](./cpp-update-existing-offer.md)。


### <a name="additional-information-on-taxes-and-prices"></a>稅金與價格的其他相關資訊

* 微軟將一些國家/地區歸類為*納稅匯出國*。  在這些國家/地區，Microsoft 向客戶徵稅，然後向政府支付（匯入）稅款。  在其他國家/地區，合作夥伴通常負責向客戶徵稅並向政府納稅。 如果您選擇在後一國家/地區銷售，您必須有能力計算和支付當地稅款。  <!-- TD: Find a good reference on taxing policies. The best I found was in the UWP section: https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps -->
* 一旦供應項目上線後，價格便則不可變更。 不過，您仍可以新增或移除支援的地區。 
* 除了排定的 SKU 費用外，Microsoft 還會向客戶收取標準 Azure 虛擬機器使用費用。
* 所有地區的價格會以設定價格當時所提供的費率，以當地幣值顯示。  <!-- TD: Meaning? - Offer created, published, other? -->
* 要單獨設置每個區域的價格，請匯出定價試算表，應用自訂定價，然後導入。 


## <a name="next-steps"></a>後續步驟

或者，如果您支援此功能，則可以指定["試駕"](./cpp-test-drive-tab.md)資訊;否則，您可以為您的產品/[服務提供市場](./cpp-marketplace-tab.md)資料。
