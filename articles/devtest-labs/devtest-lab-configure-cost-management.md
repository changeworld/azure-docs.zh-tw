---
title: 檢視 Azure DevTest Labs 中的每月估計實驗室成本趨勢
description: 本文提供的資訊說明如何在 Azure DevTest Labs 中追蹤實驗室 (每月估計成本趨勢圖表) 的成本。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 6a2a9bef9e54ef7deda123aad34cf0c576fd158f
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98892332"
---
# <a name="track-costs-associated-with-a-lab-in-azure-devtest-labs"></a>在 Azure DevTest Labs 中追蹤與實驗室相關聯的成本
本文提供如何追蹤實驗室成本的相關資訊。 它會示範如何查看實驗室目前行事曆月份的預估成本趨勢。 本文也會說明如何在實驗室中查看每個資源的月對日成本。

## <a name="view-the-monthly-estimated-lab-cost-trend"></a>查看每月預估的實驗室成本趨勢 
在本節中，您會瞭解如何使用 **每月估計成本趨勢** 圖來查看目前行事曆月份的預估成本和預估當月成本，以及目前行事曆月份的預估結束日期。 您也會瞭解如何藉由設定消費目標和閾值來管理實驗室成本，並在達到此閾值時觸發 DevTest Labs 以向您報告結果。

若要檢視「每月估計成本趨勢」圖表，請遵循下列步驟︰ 

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [所有服務]，然後從清單中選取 [DevTest Labs]。
3. 從實驗室清單中選取您的實驗室。  
4. 選取左側功能表上的 [設定 **與原則** ]。  
4. 在左側功能表的 [**成本追蹤**] 區段中，選取 [**成本趨勢**]。 下列螢幕擷取畫面顯示成本圖表的範例。 
   
    ![成本圖表](./media/devtest-lab-configure-cost-management/graph.png)

    **估計成本** 值是到目前行事曆月份為止累計的預估成本。 **預計成本** 是目前行事曆月份整個月的估計成本，是以前五天的實驗室成本來計算。

    成本金額會無條件進位到下一個整數。 例如： 

   * 5.01 會無條件進位到 6 
   * 5.50 會無條件進位到 6
   * 5.99 會無條件進位到 6

     如圖表上方所述，您在圖表中看到的成本預設是使用[隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p/)供應項目費率的估計成本。 您也可以[管理實驗室的成本目標](#managing-cost-targets-for-your-lab)，來設定在圖表中顯示您自己的費用目標。

     成本計算 *不* 包含下列成本：

   * 目前不支援 CSP 和 Dreamspark 訂用帳戶，因為 Azure DevTest Labs 使用 Azure 計費 API 來計算實驗室成本，而 Azure 計費 API 並不支援 CSP 或 Dreamspark 訂用帳戶。
   * 您的供應項目費率。 目前，您無法使用您與 Microsoft 或 Microsoft 合作夥伴協議的供應項目費率 (顯示於您的訂用帳戶下方)。 只會使用隨用隨付費率。
   * 您的稅率
   * 您的折扣
   * 您的帳單貨幣。 目前，實驗室成本只會以美元貨幣顯示。

### <a name="managing-cost-targets-for-your-lab"></a>管理您實驗室的成本目標
DevTest Labs 可讓您更輕鬆地管理您實驗室中的成本，方法是設定費用目標，以便您可在「每月估計成本趨勢」圖表中進行檢視。 當觸達指定的目標目標或閾值上限時，DevTest Labs 也可以傳送通知給您。 

1. 在 [ **成本趨勢** ] 頁面上，選取 [ **管理目標**]。

    ![[管理目標] 按鈕](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)
2. 在 [ **管理目標** ] 頁面上，指定消費目標和臨界值。 您也可以設定要在成本趨勢圖表上報告每個選取的閾值，還是透過 webhook 通知來報告每個選取的閾值。

    ![[管理目標] 窗格](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - 選擇您需要追蹤成本目標的時間週期。
      - **每月**：會追蹤每個月的成本目標。
      - 已 **修正**：系統會針對您在開始和結束日期中指定的日期範圍追蹤成本目標。 一般而言，這些值代表您的專案排程執行的時間長度。
   - 指定 **目標成本**。 例如，您計畫在您定義的時間週期內，花費在此實驗室的程度。
   - 選取要啟用還是停用您需要報告的任何閾值 – 以 25% 增量計算 – 最高為您指定之 **目標成本** 的 125%。
      - **通知**：當符合此閾值時，您指定的 webhook URL 就會通知您。
      - **圖表** 圖：當符合此閾值時，會在您可以查看的成本趨勢圖上繪製結果，如查看每月估計成本趨勢圖所述。
   - 如果您選擇在符合閾值時 [通知]，就必須指定 webhook URL。 在成本整合區域中，選取 [按一下這裡可新增整合]。 在 [設定通知] 窗格中輸入 **WEBHOOK URL** ，然後選取 **[確定]**。

       ![[設定通知] 窗格](./media/devtest-lab-configure-cost-management/configure-notification-new.png)

     - 如果您指定 [通知]，就必須定義 webhook URL。
     - 同樣地，如果您定義 webhook URL，就必須將 [成本閾值] 窗格中的 [通知] 設定為 [開啟]。
     - 在這裡輸入 webhook 之前，您必須先建立 webhook。  

       如需 webhook 的詳細資訊，請參閱[建立 webhook 或 API Azure 函式](../azure-functions/functions-bindings-http-webhook.md)。 

## <a name="view-cost-by-resource"></a>依資源查看成本 
實驗室中的每月成本趨勢功能可讓您查看目前行事曆月份花費多少時間。 它也會根據您在過去七天內的費用，顯示在月底之前的消費預測。 為了協助您瞭解實驗室中的消費為何提早達成閾值，您可以使用 [ **依資源** 區分的成本] 功能，以顯示資料表中 **每個資源的每** 個月迄今成本。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [所有服務]，然後從清單中選取 [DevTest Labs]。
3. 從實驗室清單中，選取所需的實驗室。  
4. 選取左側功能表上的 [設定 **與原則** ]。
5. 在左側功能表的 [**成本追蹤**] 區段中，選取 [**依資源的成本**]。 您會看到與實驗室相關聯之每個資源的相關成本。 

    ![依資源區分的成本](./media/devtest-lab-configure-cost-management/cost-by-resource.png)

這項功能可協助您輕鬆地識別成本最高的資源，讓您可以採取動作來減少實驗室費用。 例如，VM 的成本是以 VM 的大小為基礎。 VM 的大小愈大，成本就愈高。 您可以輕鬆地找到 VM 和擁有者的大小，以便與 VM 擁有者討論，以瞭解為何需要這類 VM 大小，以及是否有機會降低大小。

[自動關機原則](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy) 可協助您在一天的特定時間關閉實驗室 vm，以降低成本。 不過，實驗室使用者可以退出宣告關閉原則，這會增加執行 VM 的成本。 您可以選取資料表中的 VM，以查看它是否已退出宣告自動關機原則。 如果是這種情況，您可以與 VM 擁有者討論，以找出 VM 退出宣告原則的原因。
 
## <a name="next-steps"></a>後續步驟
以下是接下來要嘗試的一些事項：

* [定義實驗室原則](devtest-lab-set-lab-policy.md) - 了解如何設定用來管理您實驗室及其 VM 使用方式的各種原則。 
* [建立自訂映像](devtest-lab-create-template.md) - 當您建立 VM 時，您要指定一個基本映像，它可以是自訂映像或 Marketplace 映像。 本文會示範如何從 VHD 檔案建立自訂的映像。
* [設定 Marketplace 映像](devtest-lab-configure-marketplace-images.md) - DevTest Labs 支援根據 Azure Marketplace 映像建立 VM。 本文會示範在實驗室中建立 VM 時，如何指定可以使用哪些 Azure Marketplace 映像 (如果有的話)。
* [在實驗室中建立 VM](devtest-lab-add-vm.md) - 示範如何從基本映像 (自訂或 Marketplace) 建立 VM，以及如何使用 VM 中的構件。
