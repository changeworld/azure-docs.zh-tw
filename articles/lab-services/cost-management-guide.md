---
title: Azure 實驗室服務的成本管理指南
description: 瞭解用來查看實驗室服務成本的不同方式。
author: rbest
ms.author: rbest
ms.date: 08/16/2020
ms.topic: article
ms.openlocfilehash: 0aaa454df05cd8981b314abe238163caced7864c
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88604595"
---
# <a name="cost-management-for-azure-lab-services"></a>Azure 實驗室服務的成本管理

成本管理可分成兩個不同的區域：成本估計和成本分析。  設定實驗室時，會發生成本估計，以確保實驗室的初始結構符合預期的預算。  成本分析通常會在月底發生，以分析成本並判斷下個月的必要動作。

## <a name="estimate-the-lab-costs"></a>估計實驗室成本

每個實驗室儀表板都有 **成本 & 計費** 區段，其會針對該月的實驗室成本進行粗略估計。  成本預估會以每小時預估成本的最大使用者數目，來匯總小時的使用量。  若要取得最精確的評估設定實驗室（包括 [排程](how-to-create-schedules.md)），儀表板會反映預估成本。  

這種估計值可能不是所有可能的成本，有幾個不包含的資源。  範本準備成本不會納入成本預估中。  建立範本所需的時間量可能會有很大的差異。 執行範本的成本與每小時的整體實驗室成本相同。 任何 [共用映射庫](how-to-use-shared-image-gallery.md) 成本都不會包含在 lab 儀表板中，因為您可以在多個實驗室之間共用資源庫。  最後，當實驗室建立者啟動電腦時所產生的時數會排除在此預估中。

> [!div class="mx-imgBorder"]
> ![儀表板成本估計](./media/cost-management-guide/dashboard-cost-estimation.png)

## <a name="analyze-previous-months-usage"></a>分析先前的月份使用量

成本分析是用來查看前幾個月的使用量，以協助判斷實驗室的任何調整。  您可以在訂用帳戶 [成本分析](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis)中找到過去的成本細目。  在 [Azure 入口網站] 中，您可以在 [上方搜尋] 欄位中輸入「訂用帳戶」，然後選取 [訂閱] 選項。  

> [!div class="mx-imgBorder"]
> ![訂用帳戶搜尋](./media/cost-management-guide/subscription-search.png)

選取要檢查的特定訂用帳戶。

> [!div class="mx-imgBorder"]
> ![訂用帳戶選取專案](./media/cost-management-guide/subscription-select.png)

 在 [ **成本管理**] 下的左窗格中，選取 [成本分析]。

> [!div class="mx-imgBorder"]
> ![訂用帳戶成本分析](./media/cost-management-guide/subscription-cost-analysis.png)

此儀表板會允許深入的成本分析，包括依排程匯出至不同檔案類型的能力。  成本管理有許多功能可提供詳細資訊，請參閱 [成本管理計費總覽](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview)

依資源類型篩選： `microsoft.labservices/labaccounts` 只會顯示與實驗室服務相關聯的成本。

## <a name="understand-the-usage"></a>瞭解使用量

以下是成本分析的範例。

> [!div class="mx-imgBorder"]
> ![訂用帳戶成本分析](./media/cost-management-guide/cost-analysis.png)

預設有六個數據行：資源、資源類型、位置、資源組名、標籤、成本。  資源資料行包含實驗室帳戶、實驗室名稱和 VM 的相關資訊。  具有實驗室帳戶/實驗室名稱/預設值的資料列是實驗室的成本，可在第二和第三個數據列上看到。  使用的 Vm 會在實驗室帳戶/實驗室名稱/預設/VM 名稱下產生費用。  在此範例中，新增第一個具有第二個數據列（開頭為 "aaalab/dockerlab"）的第一個資料列會提供 "aaalab" 實驗室帳戶中實驗室 "dockerlab" 的總成本。

若要取得共用映射資源庫的資訊，請將資源類型變更為 `Microsoft.Compute/Galleries` ，以提供映射庫的整體成本。  共用映射資源庫可能不會顯示在成本中，視資源庫的儲存位置而定。

> [!NOTE]
> 共用映射庫已連線到實驗室帳戶。  這表示多個實驗室可以使用相同的映射。

## <a name="separating-costs"></a>分隔成本

有些大學使用實驗室帳戶和資源群組，做為分隔不同類別的方式。  每個類別都有自己的實驗室帳戶和資源群組。 在 [成本分析] 窗格中，根據具有適當資源組名的資源組名來新增篩選，而且只會顯示該類別的成本。  這可讓您在查看成本時，在不同類別之間進行更清楚的略圖。  成本分析的 [排程匯出](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data) 功能可讓您在個別的檔案中下載每個類別的成本。

## <a name="managing-costs"></a>管理成本

根據類別的類型而定，有一些方法可管理成本，以降低 Vm 在沒有使用電腦的學生的情況下執行。

### <a name="maximize-cost-control-with-auto-shutdown-settings"></a>使用自動關機設定將成本控制最大化

主動自動關機成本控制功能可讓您避免在實驗室內浪費虛擬機器使用時數。 下列三個自動關機和中斷連線功能的組合，會攔截使用者不小心讓虛擬機器執行的大部分情況：

> [!div class="mx-imgBorder"]
> ![訂用帳戶成本分析](./media/cost-management-guide/auto-shutdown-disconnect.png)

這些設定可在實驗室帳戶層級和實驗室層級進行設定。 如果設定是在實驗室帳戶層級啟用，則會套用至實驗室帳戶中的所有實驗室。 針對所有新的實驗室帳戶，預設會開啟這些設定。 

#### <a name="details-about-auto-shutdown-settings"></a>自動關機設定的詳細資料

* 自動中斷使用者與 OS 認為閒置 (Windows) 的虛擬機器連線。

    > [!NOTE]
    > 這項設定僅適用于 Windows 虛擬機器。

    開啟此設定時，當 Windows 作業系統認為會話閒置 (包括範本虛擬機器) 時，使用者就會與實驗室中的任何電腦中斷連線。 [WINDOWS OS 的閒置定義](https://docs.microsoft.com/windows/win32/taskschd/task-idle-conditions#detecting-the-idle-state) 會使用兩個準則： 

    * 使用者缺席-沒有鍵盤或滑鼠輸入。
    * 缺少資源耗用量–所有處理器和所有磁片都已閒置一段時間

    使用者在中斷連線之前，會在虛擬機器內看到如下的訊息： 

    > [!div class="mx-imgBorder"]
    > ![訂用帳戶成本分析](./media/cost-management-guide/idle-timer-expired.png)
    
    當使用者中斷連線時，虛擬機器仍在執行中。 如果使用者透過登入重新連接到虛擬機器，則在中斷連線之前開啟或未儲存工作的 windows 或檔案仍會存在。 在此狀態下，由於虛擬機器正在執行中，它仍會計算為使用中，並產生累算成本。 
    
    若要自動關閉已中斷連線的閒置 Windows 虛擬機器， **當虛擬機器閒置** 時，請使用 [中斷連線使用者] 的組合，並 **在使用者中斷連線設定時關閉虛擬機器** 。

    例如，如果您依照下列方式設定設定：
    
    * 當虛擬機器閒置時，中斷使用者連線-偵測到閒置狀態的15分鐘。
    * 當使用者中斷連線時，將虛擬機器關機至5分鐘。
    
    Windows 虛擬機器將會在使用者停止使用後的20分鐘內自動關機。 
    
    > [!div class="mx-imgBorder"]
    > ![訂用帳戶成本分析](./media/cost-management-guide/vm-idle-diagram.png)
* 當使用者中斷 (Windows & Linux) 的連線時，自動關閉虛擬機器。
    
    這項設定同時支援 Windows 和 Linux 虛擬機器。 開啟此設定時，將會在下列情況進行自動關機：
    
    * 若為 Windows，遠端桌面 (RDP) 連線已中斷連接。
    * 若為 Linux，SSH 連線已中斷連接。
    
    > [!NOTE]
    > 只支援 [特定的 linux 散發套件和版本](https://docs.microsoft.com/azure/virtual-machines/extensions/diagnostics-linux#supported-linux-distributions) 。
    
    您可以指定虛擬機器在自動關機之前，應等候使用者重新連線的時間長度。 
* 自動關閉已啟動但使用者未連接的虛擬機器。
     
    在實驗室內，使用者可能會啟動虛擬機器，但永遠不會連接到該虛擬機器。 例如：
    
    * 實驗室中的排程會啟動類別會話的所有虛擬機器，但有些學生不會顯示，也不會連接到其電腦。  
    * 使用者啟動虛擬機器，但忘記連接。 
    
    [當使用者未連線時關閉虛擬機器] 設定會攔截這些情況並自動關閉虛擬機器。  
    
如需有關如何在中斷連線時設定和啟用 Vm 自動關機的詳細資訊，請參閱下列文章：

* [設定實驗室帳戶的 Vm 自動關機](how-to-configure-lab-accounts.md)
* [設定實驗室的 Vm 自動關機](how-to-enable-shutdown-disconnect.md)

### <a name="quota-vs-scheduled-time"></a>配額與排程時間

瞭解 [配額時間](classroom-labs-concepts.md#quota) 與 [排程時間](classroom-labs-concepts.md#schedules) ，可讓實驗室擁有者設定實驗室，使其更符合教授和學生的需求。  排程時間是一種設定的時間，其中所有學生 Vm 都已啟動且可供連接。  通常會在所有學生都有自己的 VM 時使用，而且會在一天內的設定時間遵循教授的指示，例如課堂時數。  缺點是所有的學生 Vm 都會啟動並累積成本，即使學生未登入 VM 也是一樣。  配額時間是配置給每位學生的時間，可自行決定使用，且通常用於獨立研究。 除非學生啟動 VM，否則 Vm 不會啟動。  

實驗室可能會使用配額時間、排程時間或兩者的組合。 如果類別不需要排程的時間，則只能使用配額時間來充分發揮 Vm 的使用效率。

### <a name="scheduled-event---stop-only"></a>排定的事件-僅停止

在排程中，您可以新增停止的事件種類，這會在特定時間停止所有電腦。  某些實驗室擁有者已在午夜設定每天停止活動，以在學生忘記關閉其使用的 VM 時降低成本和配額使用量。  這種事件種類的缺點是，即使學生使用 VM，也會關閉所有 Vm。

### <a name="other-costs-related-to-labs"></a>與實驗室相關的其他成本 

有一些成本不會累積到實驗室服務中，但可以系結至實驗室服務。  共用映射庫可連接至實驗室，但不會顯示在實驗室服務成本之下，而且會產生費用。  為了協助降低整體成本，您應該從資源庫移除任何未使用的映射，因為映射會繼承儲存體成本。  實驗室可透過虛擬網路連線至其他 Azure 資源 (VNet) 在移除實驗室時，您應該移除 VNet 和其他資源。

## <a name="conclusion"></a>結論

希望上述資訊可讓您更瞭解使用量成本，以及如何使用工具來降低超額成本。
