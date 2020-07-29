---
title: Azure 實驗室服務的成本管理指南
description: 瞭解查看實驗室服務成本的不同方式。
author: rbest
ms.author: rbest
ms.date: 06/26/2020
ms.topic: article
ms.openlocfilehash: fbbaf4a3646260fc09467e214b82fd0213415635
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445299"
---
# <a name="cost-management-for-azure-lab-services"></a>Azure 實驗室服務的成本管理

成本管理可以細分成兩個不同的區域：成本估計和成本分析。  成本估計會在設定實驗室時進行，以確保實驗室的初始結構會符合預期的預算。  成本分析通常會在月底發生，以分析成本並判斷下個月的必要動作。

## <a name="estimating-the-lab-costs"></a>預估實驗室成本

每個實驗室儀表板都有**成本 & 計費**區段，其會針對該月份的實驗室成本進行粗略估計。  成本估計會以每小時估計成本的最大使用者數目來匯總小時使用量。  若要取得最精確的估計值，請設定實驗室，包括[排程](how-to-create-schedules.md)，而儀表板會反映估計的成本。  

此預估可能不是所有可能的成本，有幾個資源不會包含在內。  範本準備成本不會納入成本預估。  建立範本所需的時間量可能會有很大的差異。 執行範本的成本與每小時的整體實驗室成本相同。 任何[共用映射庫](how-to-use-shared-image-gallery.md)成本都不會包含在實驗室儀表板中，因為資源庫可在多個實驗室之間共用。  最後，當實驗室建立者啟動電腦時所產生的時數，會從此估計中排除。

> [!div class="mx-imgBorder"]
> ![儀表板成本預估](./media/cost-management-guide/dashboard-cost-estimation.png)

## <a name="analyzing-previous-months-usage"></a>分析先前的月份使用量

成本分析是用來審查先前的月份使用量，以協助判斷實驗室的任何調整。  在訂用帳戶[成本分析](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis)中可找到過去成本的細目。  在 [Azure 入口網站中，您可以在上方搜尋欄位中輸入「訂閱」，然後選取 [訂用帳戶] 選項。  

> [!div class="mx-imgBorder"]
> ![訂用帳戶搜尋](./media/cost-management-guide/subscription-search.png)

選取要檢查的特定訂用帳戶。

> [!div class="mx-imgBorder"]
> ![選取訂用帳戶](./media/cost-management-guide/subscription-select.png)

 在左側窗格中，選取 [**成本管理**] 底下的 [成本分析]。

 > [!div class="mx-imgBorder"]
> ![訂用帳戶成本分析](./media/cost-management-guide/subscription-cost-analysis.png)

此儀表板可讓您深入分析成本，包括依排程匯出至不同檔案類型的功能。  成本管理有許多功能可取得詳細資訊，請參閱[成本管理計費總覽](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview)

依資源類型篩選： `microsoft.labservices/labaccounts` 只會顯示與實驗室服務相關聯的成本。

## <a name="understanding-the-usage"></a>瞭解使用方式

以下是成本分析的範例。

> [!div class="mx-imgBorder"]
> ![訂用帳戶成本分析](./media/cost-management-guide/cost-analysis.png)

根據預設，有六個數據行：資源、資源類型、位置、資源組名、標記、成本。  [資源] 資料行包含實驗室帳戶、實驗室名稱和 VM 的相關資訊。  具有實驗室帳戶/實驗室名稱/預設值的資料列是實驗室的成本，可以在第二個和第三個數據列上看到。  使用的 Vm 在實驗室帳戶/實驗室名稱/預設/VM 名稱底下會有成本。  在此範例中，新增第一個具有第二列（以 "aaalab/dockerlab" 為開頭）的資料列，將會在 "aaalab" 實驗室帳戶中提供實驗室 "dockerlab" 的總成本。

若要取得共用映射資源庫的資訊，請將資源類型變更為 `Microsoft.Compute/Galleries` ，這樣會為您提供映射庫的整體成本。  共用映射資源庫可能不會顯示在成本中，視圖庫的儲存位置而定。

> [!NOTE]
> 共用映射資源庫已連線至實驗室帳戶。  這表示多個實驗室可以使用相同的映射。

## <a name="separating-costs"></a>分隔成本

有些大學使用實驗室帳戶和資源群組，做為分隔不同類別的方式。  每個類別都有自己的實驗室帳戶和資源群組。 在 [成本分析] 窗格中，根據具有適當資源組名的資源組名新增篩選，而只有該類別的成本才會顯示。  這可讓您在查看成本時，在不同類別之間進行更清楚的略圖。  成本分析的[排程匯出](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data)功能允許在個別檔案中下載每個類別的成本。

## <a name="managing-costs"></a>管理成本

視類別的類型而定，有一些方法可以管理成本，以減少 Vm 在未使用電腦的情況下執行。

### <a name="auto-shutdown-on-disconnect"></a>中斷連線時自動關閉

在建立實驗室時，實驗室擁有者可以將實驗室中的 Vm 設定為在與[VM 的 RDP 連線中斷連線時關閉](how-to-enable-shutdown-disconnect.md)。  此設定可減少學生中斷連線但忘記停止 VM 的案例。

### <a name="quota-vs-scheduled-time"></a>配額與排定的時間

瞭解[配額時間](classroom-labs-concepts.md#quota)與[排定的時間](classroom-labs-concepts.md#schedules)，可讓實驗室擁有者設定實驗室，使其更符合教授和學生的需求。  排定的時間是一段時間，讓所有學生 Vm 都已啟動並可供連線。  當所有學生都有自己的 VM，且在一天中的一段時間內，這種情況下會使用「一般排程」來進行。  缺點是所有的學生 Vm 都已啟動，而且會產生成本，即使學生未登入 VM 也一樣。  配額時間是配置給每個學生的時間，他們可以自行決定使用，而且通常用於獨立研究。 在學生啟動 VM 之前，Vm 不會啟動。  

實驗室可能會使用配額時間、排程時間或兩者的組合。 如果類別不需要排程的時間，則只使用配額時間來最有效地使用 Vm。

### <a name="scheduled-event---stop-only"></a>已排程事件-僅停止

在排程中，您可以新增 [僅停止] 事件種類，這會在特定時間停止所有電腦。  某些實驗室擁有者已設定每日午夜停止事件，以減少學生忘記關閉所使用 VM 的成本和配額使用量。  這種事件的缺點是，即使學生使用 VM，所有 Vm 也會關閉。

### <a name="other-costs-related-to-labs"></a>與實驗室相關的其他成本 

不會將成本納入實驗室服務，而是可以系結至實驗室服務。  共用映射資源庫可以連接到實驗室，但不會顯示在實驗室服務成本下，而且會有成本。  為了協助降低整體成本，您應該從資源庫中移除任何未使用的映射，因為映射具有繼承儲存成本。  當移除實驗室時，實驗室可以透過虛擬網路（VNet）連接到其他 Azure 資源，您應該移除 VNet 和其他資源。

## <a name="conclusion"></a>結論

希望上述資訊能讓您更清楚瞭解使用成本，以及如何使用工具提供來減少多餘成本。
