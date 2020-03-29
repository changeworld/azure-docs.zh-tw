---
title: 檢視 Azure DevTest Labs 中的每月估計實驗室成本趨勢
description: 本文提供有關如何在 Azure DevTest 實驗室中跟蹤實驗室成本（每月估計成本趨勢圖表）的資訊。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 1f46fdc5-d917-46e3-a1ea-f6dd41212ba4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 2e4fe55fac5edf73e16df05bd38cc2712a94377d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721722"
---
# <a name="track-costs-associated-with-a-lab-in-azure-devtest-labs"></a>跟蹤與 Azure 開發人員測試實驗室中的實驗室關聯的成本
本文提供有關如何跟蹤實驗室成本的資訊。 它演示如何查看實驗室當前日曆月的估計成本。 本文還介紹了如何在實驗室中查看每個資源的每月至今成本。

## <a name="view-the-monthly-estimated-lab-cost-trend"></a>查看每月估計的實驗室成本趨勢 
在本節中，您將瞭解如何使用**每月估計成本趨勢**圖表查看當前日曆月迄今為止的估計成本以及當前日曆月的預計月末成本。 您還可以瞭解如何通過設置支出目標和閾值來管理實驗室成本，當達到這些目標和閾值時，觸發 DevTest Labs 將結果報告給您。

若要檢視「每月估計成本趨勢」圖表，請遵循下列步驟︰ 

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 選取 [所有服務]****，然後從清單中選取 [DevTest Labs]****。
3. 從實驗室清單中選取您的實驗室。  
4. 選擇左側功能表上的 **"配置"和"策略**"。  
4. 在左側功能表的"**成本跟蹤**"部分中選擇**成本趨勢**。 以下螢幕截圖顯示了成本圖表的示例。 
   
    ![成本圖表](./media/devtest-lab-configure-cost-management/graph.png)

    **估計成本** 值是到目前行事曆月份為止累計的預估成本。 **預計成本** 是目前行事曆月份整個月的估計成本，是以前五天的實驗室成本來計算。

    成本金額會無條件進位到下一個整數。 例如： 

   * 5.01 會無條件進位到 6 
   * 5.50 會無條件進位到 6
   * 5.99 會無條件進位到 6

     如圖表上方所述，您在圖表中看到的成本預設是使用[隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p/)供應項目費率的估計** 成本。 您也可以[管理實驗室的成本目標](#managing-cost-targets-for-your-lab)，來設定在圖表中顯示您自己的費用目標。

     以下成本*不包括*在成本計算中：

   * 目前不支援 CSP 和 Dreamspark 訂用帳戶，因為 Azure DevTest Labs 使用 [Azure 計費 API](../cost-management-billing/manage/usage-rate-card-overview.md) 來計算實驗室成本，而 Azure 計費 API 並不支援 CSP 或 Dreamspark 訂用帳戶。
   * 您的供應項目費率。 目前，您無法使用您與 Microsoft 或 Microsoft 合作夥伴協議的供應項目費率 (顯示於您的訂用帳戶下方)。 只會使用隨用隨付費率。
   * 您的稅率
   * 您的折扣
   * 您的帳單貨幣。 目前，實驗室成本只會以美元貨幣顯示。

### <a name="managing-cost-targets-for-your-lab"></a>管理您實驗室的成本目標
DevTest Labs 可讓您更輕鬆地管理您實驗室中的成本，方法是設定費用目標，以便您可在「每月估計成本趨勢」圖表中進行檢視。 當觸達指定的目標目標或閾值上限時，DevTest Labs 也可以傳送通知給您。 

1. 在 **"成本趨勢"** 頁上，選擇 **"管理目標**"。

    ![[管理目標] 按鈕](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)
2. 在 **"管理目標**"頁上，指定支出目標和閾值。 您也可以設定要在成本趨勢圖表上報告每個選取的閾值，還是透過 webhook 通知來報告每個選取的閾值。

    ![[管理目標] 窗格](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - 選擇您需要追蹤成本目標的時間週期。
      - **每月**：會追蹤每個月的成本目標。
      - **固定**：跟蹤您在開始日期和結束日期中指定的日期範圍的成本目標。 通常，這些值表示專案計劃運行的時間。
   - 指定**目標成本**。 例如，您計畫在定義的時間段內在此實驗室上花費多少。
   - 選取要啟用還是停用您需要報告的任何閾值 – 以 25% 增量計算 – 最高為您指定之**目標成本**的 125%。
      - **通知**：當符合此閾值時，您指定的 webhook URL 就會通知您。
      - **圖表上的繪圖**：當達到此閾值時，結果將繪製在您可以查看的成本趨勢圖上，如查看每月估計成本趨勢圖表中所述。
   - 如果您選擇在符合閾值時 [通知]****，就必須指定 webhook URL。 在成本整合區域中，選取 [按一下這裡可新增整合]****。 在"配置通知"窗格中輸入**Webhook URL，** 然後選擇 **"確定**"。

       ![[設定通知] 窗格](./media/devtest-lab-configure-cost-management/configure-notification.png)

     - 如果您指定 [通知]****，就必須定義 webhook URL。
     - 同樣地，如果您定義 webhook URL，就必須將 [成本閾值] 窗格中的 [通知]**** 設定為 [開啟]****。
     - 在這裡輸入 webhook 之前，您必須先建立 webhook。  

       如需 webhook 的詳細資訊，請參閱[建立 webhook 或 API Azure 函式](../azure-functions/functions-create-a-web-hook-or-api-function.md)。 

## <a name="view-cost-by-resource"></a>按資源查看成本 
實驗室中的每月成本趨勢功能允許您查看當前日曆月花費的時間。 它還根據您過去七天的支出，顯示到月底的支出預測。 為了説明您瞭解實驗室中的支出在較早時達到閾值的原因，可以使用**按資源顯示成本**的功能，顯示表中**每個資源的**每月至今成本。

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 選取 [所有服務]****，然後從清單中選取 [DevTest Labs]****。
3. 從實驗室清單中，選取所需的實驗室。  
4. 選擇左側功能表上的 **"配置"和"策略**"。
5. 在左側功能表的"**成本跟蹤**"部分中按資源選擇"**成本**"。 您將看到與實驗室關聯的每個資源關聯的成本。 

    ![依資源區分的成本](./media/devtest-lab-configure-cost-management/cost-by-resource.png)

此功能可説明您輕鬆識別成本最大的資源，以便可以採取措施減少實驗室支出。 例如，VM 的成本基於 VM 的大小。 VM 的大小越大，成本就越多。 您可以輕鬆地找到 VM 和擁有者的大小，以便您可以與 VM 擁有者交談，以瞭解為什麼需要此類 VM 大小，以及是否有機會減小該大小。

[自動關閉策略](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy)通過在一天的特定時間關閉實驗室 VM 來説明您降低成本。 但是，實驗室使用者可以退出宣告關閉策略，這會增加運行 VM 的成本。 您可以在表中選擇 VM 以查看 VM 是否已被退出宣告自動關閉策略。 如果是這種情況，您可以與 VM 擁有者交談，以查找為什麼 VM 已被退出宣告策略。
 
## <a name="next-steps"></a>後續步驟
以下是接下來要嘗試的一些事項：

* [定義實驗室原則](devtest-lab-set-lab-policy.md) - 了解如何設定用來管理您實驗室及其 VM 使用方式的各種原則。 
* [建立自訂映像](devtest-lab-create-template.md) - 當您建立 VM 時，您要指定一個基本映像，它可以是自訂映像或 Marketplace 映像。 本文會示範如何從 VHD 檔案建立自訂的映像。
* [設定 Marketplace 映像](devtest-lab-configure-marketplace-images.md) - DevTest Labs 支援根據 Azure Marketplace 映像建立 VM。 本文會示範在實驗室中建立 VM 時，如何指定可以使用哪些 Azure Marketplace 映像 (如果有的話)。
* [在實驗室中建立 VM](devtest-lab-add-vm.md) - 示範如何從基本映像 (自訂或 Marketplace) 建立 VM，以及如何使用 VM 中的構件。

