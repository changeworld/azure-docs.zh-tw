---
title: 雲解決方案供應商 |Azure 應用商店
description: 發佈商現在可以通過 Microsoft 雲解決方案供應商 （CSP） 合作夥伴管道銷售其產品/服務。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: dsindona
ms.openlocfilehash: 9d7cbdd5ad551ba48f08f62237d5384aa7998fb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281658"
---
# <a name="cloud-solution-providers"></a>雲端解決方案提供者

除了通過[Microsoft 網路店面](https://docs.microsoft.com/azure/marketplace/comparing-appsource-azure-marketplace)公開提供產品外，軟體產品還可以覆蓋雲解決方案供應商 （CSP） 計畫中合作夥伴服務的數百萬合格的 Microsoft 客戶。

發佈商根據加入宣告（新產品/服務）配置 CSP 計畫中的可用性產品/服務，使合作夥伴能夠銷售您的產品並為客戶創建捆綁解決方案。

發佈商負責向最終客戶提供中斷修復支援，並為 CSP 計畫和/或客戶中的合作夥伴提供聯繫支援的機制。 最佳做法是向 CSP 計畫中的合作夥伴提供使用者文檔、培訓和服務運行狀況/中斷通知（如適用），以便 CSP 計畫中的合作夥伴能夠處理來自客戶的第 1 層支援請求。  

以下優惠有資格被選擇由 CSP 計畫中的合作夥伴出售：

- 軟體即服務 （SaaS） 交易優惠
- 虛擬機器 (VM)
- 解決方案範本
- 受控應用程式

> [!NOTE]
> 預設情況下，CSP 程式中的合作夥伴會選擇容器和自帶許可證 （BYOL） VM SKU。

## <a name="how-to-configure-an-offering"></a>如何配置產品/服務

CSP 計畫加入宣告設置在合作夥伴中心或雲合作夥伴門戶提供創建體驗中配置。 [詳細瞭解不斷變化的發行者體驗](https://www.microsoftpartnercommunity.com/t5/Azure-Marketplace-and-AppSource/Cloud-Marketplace-In-Partner-Center/m-p/9738#M293)。

### <a name="partner-center-opt-in"></a>合作夥伴中心加入宣告

在合作夥伴中心，您可以在 CSP 轉銷商訪問模組下找到加入宣告體驗。

![CSP 轉銷商受眾](media/marketplace-publishers-guide/csp-reseller-audience.png)

在 CSP 轉銷商訪問模組中，您可以選擇三個選項：

- 選項一：CSP 計畫中的任何合作夥伴
- 選項二：我選擇的 CSP 計畫中的特定合作夥伴
- 備選方案三：CSP計畫中沒有合作夥伴

#### <a name="option-one-any-partner-in-the-csp-program"></a>選項一：CSP 計畫中的任何合作夥伴

![CSP 計畫中的任何合作夥伴](media/marketplace-publishers-guide/csp-reseller-option-one.png)

 通過選擇此選項，CSP 計畫中的所有合作夥伴都有資格將您的產品/服務轉售給客戶。

#### <a name="option-two-specific-partners-in-the-csp-program-i-select"></a>選項二：我選擇的 CSP 計畫中的特定合作夥伴

![我選擇的 CSP 計畫的特定合作夥伴](media/marketplace-publishers-guide/csp-reseller-option-two.png)

通過選擇此選項，您可以授權 CSP 計畫中的哪些合作夥伴有資格轉售您的產品/服務。

要授權合作夥伴，請按一下 **"選擇 CSP 合作夥伴**"，並顯示一個功能表，允許您按合作夥伴名稱或 CSP Azure 活動目錄 （AAD） 租戶 ID 進行搜索。

![選擇 CSP 功能表](media/marketplace-publishers-guide/csp-pop-up-module.png)

您可以應用搜索篩選器，如**國家/地區**、**能力**或**技能**。

![合作夥伴搜索的國家/地區、能力和技能篩選器](media/marketplace-publishers-guide/csp-add-resellers.png)

選擇合作夥伴清單後，選擇 **"添加**"。

![CSP 計畫中授權合作夥伴的示例清單](media/marketplace-publishers-guide/csp-add-resellers-details.png)

顯示所選合作夥伴清單的表將顯示在 CSP 轉銷商訪問台頁面上。

![CSP 轉銷商訪問台頁面上包含合作夥伴清單的表](media/marketplace-publishers-guide/csp-option-two-add-reseller.png)

選擇 **"保存草稿"** 以註冊更改。

如果此優惠未發佈，則需要發佈產品/服務以使其提供給所選合作夥伴。

>[!NOTE]
>如果您授權給定區域的 CSP 計畫中合作夥伴，他們可以將產品/服務出售給屬於該特定區域的任何客戶。 有關 CSP 優惠如何分類區域的詳細資訊，請參閱[雲解決方案供應商計畫區域市場和貨幣](https://docs.microsoft.com/partner-center/regional-authorization-overview)。

如果要更新已發佈的優惠的 CSP 清單，請添加其他合作夥伴並選擇**同步 CSP 訪問群體**。

如果您有一個已具有授權合作夥伴清單的要約，並且希望為另一個產品/服務使用相同的清單，請使用**導入/匯出**。 導航到具有 CSP 清單的要約，然後選擇 **"匯出 CSP"。** 該函數開發一個 .csv 檔，該檔可以導入到另一個產品/服務中。

#### <a name="option-three-no-partners-in-the-csp-program"></a>備選方案三：CSP計畫中沒有合作夥伴

![CSP 計畫中沒有合作夥伴](media/marketplace-publishers-guide/csp-reseller-option-three.png)

通過選擇此選項，您將從 CSP 計畫中選擇您的產品/服務。 您可以隨時更改此選擇。

### <a name="cloud-partner-portal-opt-in"></a>雲合作夥伴門戶加入宣告

在雲合作夥伴門戶中，加入宣告設置在"應用商店"或"網店"選項卡上。選擇 CSP 計畫中特定合作夥伴的能力僅在合作夥伴中心提供。

![CPP 中的 CSP 加入宣告經驗](media/marketplace-publishers-guide/csp-opt-in.png)

## <a name="deauthorize-partners-in-the-csp-program"></a>取消 CSP 計畫中的合作夥伴授權

如果您已授權 CSP 計畫中的合作夥伴，並且該合作夥伴已將產品轉售給其客戶，則不允許您取消該合作夥伴的授權。

如果 CSP 計畫中的合作夥伴沒有向客戶銷售您的產品，並且您希望在產品/服務發佈後刪除 CSP，請使用以下說明：

1. 轉到["支援請求"頁](https://aka.ms/marketplacepublishersupport)。 前幾個下拉式功能表會自動為您填寫。

   > [!NOTE]
   > 不要更改預填充的下拉式功能表選擇。

2. 要**選擇產品版本**，請選擇 **"即時產品/服務管理**"。
3. 對於**選擇最能描述問題的類別**，請選擇引用您的報價的類別。
4. 對於**選擇最能描述問題的問題**，請選擇 **"更新現有產品/服務**"。
5. 選擇 **"下一步**"以定向到 **"問題詳細資訊"頁面**，以瞭解有關您的問題的更多詳細資訊。
6. 使用**取消授權 CSP**作為問題標題並填寫所需的其餘部分。




## <a name="navigate-between-options"></a>在選項之間導航

使用此部分在三個 CSP 轉銷商選項之間導航。

### <a name="navigate-from-option-one-any-partner-in-the-csp-program"></a>從選項 1 導航：CSP 計畫中的任何合作夥伴

如果您的報價當前是**選項 1：CSP 計畫中的任何合作夥伴**，並且您希望導航到其他兩個選項之一，請使用以下說明創建請求：

1. 轉到["支援請求"頁](https://aka.ms/marketplacepublishersupport)。 前幾個下拉式功能表會自動為您填寫。

   > [!NOTE]
   > 不要更改預填充的下拉式功能表選擇。

2. 要**選擇產品版本**，請選擇 **"即時產品/服務管理**"。
3. 對於**選擇最能描述問題的類別**，請選擇引用您的報價的類別。
4. 對於**選擇最能描述問題的問題**，請選擇 **"更新現有產品/服務**"。
5. 選擇 **"下一步**"以定向到 **"問題詳細資訊"頁面**，以瞭解有關您的問題的更多詳細資訊。
6. 使用**取消授權 CSP**作為問題標題並填寫所需的其餘部分。

> [!NOTE]
> 如果您嘗試導航到選項 2，並且 CSP 計畫中的合作夥伴已將產品/服務轉售給其客戶，則該合作夥伴預設已位於您的授權合作夥伴清單中。  

### <a name="navigate-from-option-two-specific-partners-in-the-csp-program-i-select"></a>從選項 2 導航：我選擇的 CSP 計畫中的特定合作夥伴

如果您的報價當前是**選項 2：我選擇的 CSP 計畫中的特定合作夥伴**，您希望導航到**選項 1：CSP 計畫中的任何合作夥伴**，請使用以下說明創建請求：

1. 轉到["支援請求"頁](https://aka.ms/marketplacepublishersupport)。 前幾個下拉式功能表會自動為您填寫。

   > [!NOTE]
   > 不要更改預填充的下拉式功能表選擇。

2. 要**選擇產品版本**，請選擇 **"即時產品/服務管理**"。
3. 對於**選擇最能描述問題的類別**，請選擇引用您的報價的類別。
4. 對於**選擇最能描述問題的問題**，請選擇 **"更新現有產品/服務**"。
5. 選擇 **"下一步**"以定向到 **"問題詳細資訊"頁面**，以瞭解有關您的問題的更多詳細資訊。
6. 使用**取消授權 CSP**作為問題標題並填寫所需的其餘部分。

 如果您的報價目前是選項**2：我選擇的 CSP 計畫中的特定合作夥伴**，並且您希望導航到**選項 3：CSP 計畫中沒有合作夥伴**，則只有在您以前授權的 CSP 計畫中合作夥伴沒有將您的報價轉售給最終客戶時，您才能導航到該選項。 請使用以下說明創建請求：

1. 轉到["支援請求"頁](https://aka.ms/marketplacepublishersupport)。 前幾個下拉式功能表會自動為您填寫。

   > [!NOTE]
   > 不要更改預填充的下拉式功能表選擇。

2. 要**選擇產品版本**，請選擇 **"即時產品/服務管理**"。
3. 對於**選擇最能描述問題的類別**，請選擇引用您的報價的類別。
4. 對於**選擇最能描述問題的問題**，請選擇 **"更新現有產品/服務**"。
5. 選擇 **"下一步**"以定向到 **"問題詳細資訊"頁面**，以瞭解有關您的問題的更多詳細資訊。
6. 使用**取消授權 CSP**作為問題標題並填寫所需的其餘部分。

### <a name="navigate-from-option-3-no-partners-in-the-csp-program"></a>從選項 3 導航：CSP 計畫中沒有合作夥伴

如果您的報價當前是**選項 3：CSP 計畫中沒有合作夥伴**，您可以隨時導航到其他兩個選項之一。

## <a name="sharing-sales-and-support-materials-with-partners-in-the-csp-program"></a>與 CSP 計畫中的合作夥伴共用銷售和支援材料

為了説明雲解決方案供應商計畫中的合作夥伴最有效地代表您的產品並與您的組織互動，您必須提交可供轉銷商使用的銷售和支援材料。 這些資源不會暴露給市場網店中的客戶。

### <a name="partner-center-csp-channel"></a>合作夥伴中心 CSP 通道

如果您已加入宣告合作夥伴中心的 CSP 管道，則發佈商必須輸入一個 URL，該 URL 在產品/服務清單模組下承載相關行銷材料和將聯繫資訊引入 CSP 管道：

![合作夥伴中心 CSP 抵押品資訊](media/marketplace-publishers-guide/pc-csp-channel.png)

### <a name="cloud-partner-portal-csp-channel"></a>雲合作夥伴門戶 CSP 管道

如果您已加入宣告雲合作夥伴門戶中的 CSP 管道，則發行者必須輸入一個 URL，該 URL 承載相關的行銷材料並將聯繫資訊引入 CSP 管道：

![雲合作夥伴門戶 CSP 抵押品資訊](media/marketplace-publishers-guide/cpp-csp-information.png)

## <a name="next-steps"></a>後續步驟

訪問[Azure 應用商店和應用程式源發行者指南](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)。

要瞭解有關市場 GTM 服務的資訊，請參閱[上市服務](https://partner.microsoft.com/reach-customers/gtm)。

登錄到[合作夥伴中心](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv)或[雲合作夥伴門戶](https://cloudpartner.azure.com/)以創建和配置您的產品/服務。
