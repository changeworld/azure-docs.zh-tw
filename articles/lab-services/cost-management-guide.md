---
title: Azure 實驗室服務的成本管理指南
description: 瞭解用來查看實驗室服務成本的不同方式。
author: rbest
ms.author: rbest
ms.date: 08/16/2020
ms.topic: article
ms.openlocfilehash: 98e04ba6bb1310935c4893a3616dfd68c2e99a55
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88797627"
---
# <a name="cost-management-for-azure-lab-services"></a>Azure 實驗室服務的成本管理

針對 Azure 實驗室服務，成本管理可分成兩個不同的區域：成本估計和成本分析。 當您要設定實驗室以確定實驗室的初始結構符合預期的預算時，就會發生成本估計。 成本分析通常會在月底發生，以判斷下個月所需的動作。

## <a name="estimate-the-lab-costs"></a>估計實驗室成本

每個實驗室儀表板都有 **成本 & 計費** 區段，其會針對該月的實驗室成本進行粗略估計。 成本預估會以每小時預估成本的最大使用者數目，來匯總小時的使用量。 若要取得最精確的估計值，請設定實驗室，包括 [排程](how-to-create-schedules.md)。 儀表板會反映估計成本。 

此預估值可能不會顯示所有可能的成本。 其中不包含一些資源：

- 範本準備成本。 建立範本所需的時間量會有很大的差異。 執行範本的成本與每小時的整體實驗室成本相同。 
- 任何 [共用映射資源庫](how-to-use-shared-image-gallery.md) 的成本，因為可以跨多個實驗室共用資源庫。 
- 實驗室建立者啟動虛擬機器 (VM) 時所產生的時數。

> [!div class="mx-imgBorder"]
> ![顯示儀表板成本估計值的螢幕擷取畫面。](./media/cost-management-guide/dashboard-cost-estimation.png)

## <a name="analyze-the-previous-months-usage"></a>分析上個月的使用量

成本分析是用來查看上個月的使用量，以協助您判斷實驗室的任何調整。 您可以在訂用帳戶 [成本分析](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis)中找到過去成本的明細。 在 [Azure 入口網站] 中，您可以在 [搜尋] 方塊 **中輸入訂** 用帳戶，然後選取 [ **訂閱** ] 選項。 

> [!div class="mx-imgBorder"]
> ![顯示 [搜尋] 方塊和 [訂閱] 選項的螢幕擷取畫面。](./media/cost-management-guide/subscription-search.png)

選取您要檢查的特定訂用帳戶。

> [!div class="mx-imgBorder"]
> ![顯示訂用帳戶選取專案的螢幕擷取畫面。](./media/cost-management-guide/subscription-select.png)

在 [**成本管理**] 下的左窗格中，選取 [**成本分析**]。

> [!div class="mx-imgBorder"]
> ![顯示圖表上訂用帳戶成本分析的螢幕擷取畫面。](./media/cost-management-guide/subscription-cost-analysis.png)

此儀表板可進行深入的成本分析，包括依排程匯出至不同檔案類型的能力。 如需詳細資訊，請參閱 [成本管理 + 計費總覽](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview)。

您可以依資源類型進行篩選。 使用 `microsoft.labservices/labaccounts` 將只會顯示與實驗室服務相關聯的成本。

## <a name="understand-the-usage"></a>瞭解使用量

下列螢幕擷取畫面是成本分析的範例。

> [!div class="mx-imgBorder"]
> ![顯示訂用帳戶之範例成本分析的螢幕擷取畫面。](./media/cost-management-guide/cost-analysis.png)

根據預設，有六個數據行： **資源**、 **資源類型**、 **位置**、 **資源組名**、 **標記**和 **成本**。 **資源**資料行包含實驗室帳戶、實驗室名稱和 VM 的相關資訊。 顯示實驗室帳戶、實驗室名稱和預設 (第二個數據列和第三個數據) 列的資料列是實驗室的成本。 使用的 Vm 會有成本，可供您看到顯示實驗室帳戶、實驗室名稱、預設值和 VM 名稱的資料列。 

在此範例中，新增第一個和第二個數據列 (start with **aaalab/dockerlab**) 會提供您 "aaalab" 實驗室帳戶中實驗室 "dockerlab" 的總成本。

若要取得映射庫的整體成本，請將資源類型變更為 `Microsoft.Compute/Galleries` 。 共用映射庫可能不會顯示在成本中，視資源庫的儲存位置而定。

> [!NOTE]
> 共用映射庫已連線到實驗室帳戶。 這表示多個實驗室可以使用相同的映射。

## <a name="separate-the-costs"></a>分隔成本

有些大學使用實驗室帳戶和資源群組做為分隔類別的方式。 每個類別都有自己的實驗室帳戶和資源群組。 

在 [成本分析] 窗格中，根據資源組名新增篩選，該類別具有適當的資源組名。 然後，只會顯示該類別的成本。 這可讓您在查看成本時，在類別之間進行更清楚的略圖。 您可以使用成本分析的 [排程匯出](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data) 功能，在個別的檔案中下載每個類別的成本。

## <a name="manage-costs"></a>管理成本

根據類別的類型而定，有一些方法可管理成本，以減少在沒有使用學生的情況下執行的 Vm 實例。

### <a name="automatic-shutdown-settings-for-cost-control"></a>成本控制的自動關機設定

自動關機功能可讓您避免在實驗室中浪費的 VM 使用時間。 下列設定會捕捉使用者不小心讓虛擬機器繼續執行的大部分案例：

> [!div class="mx-imgBorder"]
> ![顯示三個自動關機設定的螢幕擷取畫面。](./media/cost-management-guide/auto-shutdown-disconnect.png)

您可以在實驗室帳戶層級和實驗室層級設定這些設定。 如果您在實驗室帳戶層級啟用它們，則會套用至實驗室帳戶中的所有實驗室。 針對所有新的實驗室帳戶，預設會開啟這些設定。 

#### <a name="automatically-disconnect-users-from-virtual-machines-that-the-os-deems-idle"></a>自動中斷使用者與作業系統認為閒置的虛擬機器的連線

> [!NOTE]
> 這項設定僅適用于 Windows 虛擬機器。

開啟 [ **當虛擬機器閒置時中斷使用者連線]** 設定時，當 Windows 作業系統認為會話閒置 (包括範本虛擬機器) 時，使用者就會與實驗室中的任何電腦中斷連線。 [WINDOWS OS 的 idle 定義](https://docs.microsoft.com/windows/win32/taskschd/task-idle-conditions#detecting-the-idle-state)會使用兩個準則： 

* 使用者缺席：沒有鍵盤或滑鼠輸入。
* 缺少資源耗用量：所有處理器和所有磁片都處於閒置狀態的特定時間百分比。

在 VM 中斷連線之前，使用者將會在 VM 中看到如下的訊息： 

> [!div class="mx-imgBorder"]
> ![顯示警告訊息的螢幕擷取畫面，指出會話已閒置超過其時間限制，且將會中斷連線。](./media/cost-management-guide/idle-timer-expired.png)
 
當使用者中斷連線時，虛擬機器仍在執行中。 如果使用者登入、開啟的 windows 或開啟或未儲存的檔案，但在中斷連線之前未儲存的檔案，則會繼續存在。 在此狀態下，由於虛擬機器正在執行中，它仍會計算為使用中，並產生累算成本。 
 
若要自動關閉已中斷連線的閒置 Windows 虛擬機器， **當虛擬機器閒置** 時，請使用 [中斷連線使用者] 的組合，並 **在使用者中斷連線設定時關閉虛擬機器** 。

例如，如果您依照下列方式設定設定：
 
* **當虛擬機器閒置時，中斷使用者**的連線：偵測到閒置狀態後的15分鐘。
* **當使用者中斷連線時，關閉虛擬機器**：使用者中斷連線後的5分鐘。
 
Windows 虛擬機器將會在使用者停止使用後的20分鐘內自動關機。 
 
> [!div class="mx-imgBorder"]
> ![說明設定組合的圖表，會導致自動 VM 關閉。](./media/cost-management-guide/vm-idle-diagram.png)

#### <a name="automatically-shut-down-virtual-machines-when-users-disconnect"></a>當使用者中斷連線時自動關閉虛擬機器
 
[ **當使用者中斷連線時關閉虛擬機器]** 設定同時支援 Windows 和 Linux 虛擬機器。 開啟此設定時，將會在下列情況進行自動關機：
 
* 針對 Windows， (RDP) 連線的遠端桌面已中斷連接。
* 若為 Linux，SSH 連線已中斷連接。
 
> [!NOTE]
> 只支援 [特定的 linux 散發套件和版本](https://docs.microsoft.com/azure/virtual-machines/extensions/diagnostics-linux#supported-linux-distributions) 。
 
您可以指定虛擬機器在自動關機之前，應等候使用者重新連線的時間長度。 

#### <a name="automatically-shut-down-virtual-machines-that-are-started-but-users-dont-connect"></a>自動關閉已啟動但使用者未連接的虛擬機器
 
在實驗室中，使用者可能會啟動虛擬機器，但永遠不會連接到該虛擬機器。 例如：
 
* 實驗室中的排程會啟動類別會話的所有虛擬機器，但有些學生不會顯示，也不會連接到其電腦。 
* 使用者啟動虛擬機器，但忘記連接。 
 
[ **當使用者未連線時關閉虛擬機器]** 設定會攔截這些情況並自動關閉虛擬機器。 
 
如需有關如何在中斷連線時設定和啟用 Vm 自動關機的詳細資訊，請參閱下列文章：

* [設定實驗室帳戶的 Vm 自動關機](how-to-configure-lab-accounts.md)
* [設定實驗室的 Vm 自動關機](how-to-enable-shutdown-disconnect.md)

### <a name="scheduled-time-vs-quota-time"></a>排程時間與配額時間

瞭解 [排程時間](classroom-labs-concepts.md#schedules) 和 [配額時間](classroom-labs-concepts.md#quota) 可協助您設定實驗室，以更符合教授和學生的需求。 

排程時間是一種設定的時間，其中所有學生 Vm 都已啟動且可供連接。 排程的時間通常是在所有學生都有自己的 Vm 時使用，並且在一天內的設定時間遵循教授的方向， (例如) 的課程。 缺點是所有的學生 Vm 都會啟動並累積成本，即使學生未登入 VM 也是一樣。 

配額時間是配置給每位學生以自行決定使用的時間，通常用於獨立研究。 除非學生啟動 VM，否則 Vm 不會啟動。 

實驗室可以使用配額時間或排程時間，或兩者的組合。 如果類別不需要排程的時間，則只能使用配額時間來充分發揮 Vm 的使用效率。

### <a name="scheduled-event-stop-only"></a>排定的事件：僅停止

在排程中，您可以新增僅停止的事件種類，以在特定時間停止所有電腦。 某些實驗室擁有者已在午夜設定每天停止活動，以降低當學生忘記關閉所使用 VM 時的成本和配額使用量。 這類事件的缺點是所有 Vm 將會關閉，即使學生使用 VM 也是一樣。

### <a name="other-costs-related-to-labs"></a>與實驗室相關的其他成本 

某些成本不會匯總到實驗室服務中，但可以系結至實驗室服務。 您可以將共用映射庫連線到實驗室，但不會顯示在實驗室服務成本之下，而且會有成本。 為了協助降低整體成本，您應該從資源庫移除任何未使用的映射，因為映射具有固有的儲存體成本。 

實驗室可以透過虛擬網路連接到其他 Azure 資源。 移除實驗室時，您應該移除虛擬網路和其他資源。

## <a name="conclusion"></a>結論

希望本文中的資訊可讓您更瞭解可協助您降低使用量成本的工具。
