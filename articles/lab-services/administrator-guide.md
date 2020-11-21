---
title: Azure 實驗室服務 - 管理員指南 |Microsoft Docs
description: 本指南可協助系統管理員使用 Azure 實驗室服務來建立和管理實驗室帳戶。
ms.topic: article
ms.date: 10/20/2020
ms.openlocfilehash: 08d2fea719ad67f666ea9da09721dc3f7ab54768
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024631"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure 實驗室服務 - 管理員指南
資訊技術 (IT) 管理大學雲端資源的系統管理員，通常負責設定學校的實驗室帳戶。 在設定實驗室帳戶之後，系統管理員或教育人員會建立帳戶內所含的實驗室。 本文提供相關 Azure 資源的高階總覽，以及建立這些資源的指引。

![實驗室帳戶中 Azure 資源的高階視圖圖表。](./media/administrator-guide/high-level-view.png)

- 實驗室是裝載在 Azure 實驗室服務所擁有的 Azure 訂用帳戶內。
- 實驗室帳戶、共用映射庫和映射版本都裝載在您的訂用帳戶內。
- 您可以將您的實驗室帳戶和共用映像庫放在相同的資源群組中。 在此圖中，其位於不同的資源群組。

如需架構的詳細資訊，請參閱 [實驗室架構基本](./classroom-labs-fundamentals.md)概念。

## <a name="subscription"></a>訂用帳戶
您的大學可能有一或多個 Azure 訂用帳戶。 您可以使用訂用帳戶來管理其中使用的所有 Azure 資源和服務的計費和安全性，包括實驗室帳戶。

實驗室帳戶和其訂用帳戶之間的關聯性很重要，因為：

- 帳單是透過包含實驗室帳戶的訂用帳戶來回報。
- 您可以授與訂用帳戶 Azure Active Directory (Azure AD) 租使用者存取 Azure 實驗室服務的許可權。 您可以將使用者新增為實驗室帳戶擁有者或參與者，或是實驗室建立者或實驗室擁有者。

系統會在 Azure 實驗室服務所擁有的訂用帳戶中，為您管理及裝載實驗室和其虛擬機器 (Vm) 。

## <a name="resource-group"></a>資源群組
訂用帳戶包含一或多個資源群組。 資源群組是用來建立 Azure 資源的邏輯分組，以在相同的解決方案中一起使用。  

當您建立實驗室帳戶時，您必須設定包含該實驗室帳戶的資源群組。 

當您建立 [共用映射庫](#shared-image-gallery)時，也需要資源群組。 您可以將實驗室帳戶和共用映射資源庫放在相同的資源群組中，或放在兩個不同的資源群組中。 如果您打算在不同的解決方案之間共用映射庫，您可能會想要採用第二種方法。

當您建立實驗室帳戶時，可以同時自動建立和連結共用映像庫。  這個做法會導致實驗室帳戶和共用映像庫在不同的資源群組中建立。 當您遵循在 [實驗室帳戶建立教學課程中設定共用映射庫](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation) 中所述的步驟時，您會看到此行為。 本文開頭的映射會使用此設定。 

建議您提前投入時間來規劃資源群組的結構，因為建立之後，就 *無法* 變更實驗室帳戶或共用映射庫資源群組。 如果您需要變更這些資源的資源群組，您必須刪除並重新建立您的實驗室帳戶或共用映射庫。

## <a name="lab-account"></a>實驗室帳戶

實驗室帳戶可作為一或多個實驗室的容器。 當您開始使用 Azure 實驗室服務時，最常見的是使用單一實驗室帳戶。 當您的實驗室使用方式擴大時，您可以選擇稍後再建立更多實驗室帳戶。

下列清單強調一個以上的實驗室帳戶可能有所説明的案例：

- **跨實驗室管理不同的原則需求**

    當您設定實驗室帳戶時，您會設定適用于實驗室帳戶下 *所有* 實驗室的原則，例如：
    - 具有實驗室可存取之共用資源的 Azure 虛擬網路。 例如，您可能有一組實驗室需要存取虛擬網路內的共用資料集。
    - 實驗室可以用來建立 Vm 的虛擬機器映射。 例如，您可能有一組實驗室需要存取 Linux Azure Marketplace 映射的 [資料科學 VM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) 。

    如果您的每個實驗室都有獨特的原則需求，則建立個別的實驗室帳戶來個別管理每個實驗室可能會很有説明。

- **將個別的預算指派給每個實驗室帳戶**
  
    您可能需要更清楚分配預算，而不是透過單一實驗室帳戶報告所有實驗室成本。 例如，您可以為大學的數學部門、電腦科學部門等建立不同的實驗室帳戶，以將預算分散至各部門。  然後，您可以使用 [Azure 成本管理](../cost-management-billing/cost-management-billing-overview.md)來查看每個個別實驗室帳戶的成本。

- **從主動式或生產實驗室隔離試驗實驗室**
  
    在某些情況下，您可能會想要試驗實驗室帳戶的原則變更，而不會影響作用中或生產環境的實驗室。 在這種情況下，為試驗目的建立個別的實驗室帳戶，可讓您隔離變更。 

## <a name="lab"></a>實驗室

實驗室包含每個指派給單一學生的 Vm。  一般來說，您可以預期：

- 每個類別都有一個實驗室。
- 針對您所使用的每半年度、季或其他學術系統，建立一組新的實驗室。 對於需要使用相同映射的類別，您應該使用 [共用映射庫](#shared-image-gallery)。 如此一來，您可以在實驗室和學術期間重複使用映射。

當您在決定如何結構您的實驗室時，請考慮下列各點：

- **實驗室中的所有 Vm 都會使用已發佈的相同映射進行部署**

    因此，如果您的類別需要同時發行不同的實驗室映射，則必須為每個映射建立個別的實驗室。
  
- **使用量配額是在實驗室層級設定，並適用于實驗室中的所有使用者**

    若要為使用者設定不同的配額，您必須建立個別的實驗室。 不過，您可以在設定配額之後，將更多的小時新增至特定使用者。
  
- **啟動或關閉排程是在實驗室層級設定，並套用至實驗室內的所有 VM**

    類似于配額設定，如果您需要為使用者設定不同的排程，您需要為每個排程建立個別的實驗室。

根據預設，每個實驗室都有自己的虛擬網路。  如果您已啟用虛擬網路對等互連，每個實驗室都會有它自己的子網對等互連，且具有指定的虛擬網路。

## <a name="shared-image-gallery"></a>共用映像庫

共用映像庫會連結至實驗室帳戶，並做為儲存映像的中央存放庫。 當教師選擇從實驗室的範本 VM 匯出映射時，映射會儲存在資源庫中。 每次當教師對範本 VM 進行變更並將其匯出時，會儲存新版本的映射，並維護先前的版本。

講師可以在建立新的實驗室時，從共用映射庫發佈映射版本。 雖然資源庫會儲存多個版本的映射，但教師只能在實驗室建立期間選取最新版本。

共用映射庫服務是選擇性的資源，如果您只是要開始幾個實驗室，您可能不需要立即使用。 但是，當您擴大至其他實驗室時，共用映射庫可提供許多優點：

- **您可以儲存和管理範本 VM 映射的版本**

    建立自訂映射或變更 (軟體、設定，以及從公用 Azure Marketplace 圖庫) 至映射，是很有用的。  例如，教育工作者通常需要安裝不同的軟體或工具。 不同版本的範本 VM 映射可匯出至共用映射庫，而不需要學生自行手動安裝這些必要條件。 然後，您可以在建立新的實驗室時使用這些映射版本。

- **您可以跨實驗室共用和重複使用範本 VM 映射**

    您可以儲存並重複使用映射，如此一來，您就不必在每次建立新的實驗室時從頭進行設定。 例如，如果多個類別需要使用相同的映射，您可以建立一次，並將它匯出至共用映射庫，讓它可以跨實驗室共用。

- **透過自動複寫確保映射可用性**

    當您將影像從實驗室儲存至共用映射庫時，它會自動複寫到 [相同地理位置內的其他區域](https://azure.microsoft.com/global-infrastructure/regions/)。 如果區域中斷，將映射發佈至您的實驗室不會受到影響，因為可以使用來自另一個區域的映射複本。  從多個複本發佈 VM 也有助於提高效能。

若要以邏輯方式分組共用映射，您可以執行下列其中一項：

- 建立多個共用映像庫。 每個實驗室帳戶只能連接到一個共用映射庫，因此此選項也會要求您建立多個實驗室帳戶。
- 使用多個實驗室帳戶所共用的單一共用映射庫。 在此情況下，每個實驗室帳戶只能啟用該帳戶中適用于實驗室的映射。

## <a name="naming"></a>命名

當您開始使用 Azure 實驗室服務時，建議您建立資源群組、實驗室帳戶、實驗室和共用映射庫的命名慣例。 雖然您所建立的命名慣例對貴組織的需求是唯一的，但下表提供一般指導方針：

| 資源類型 | 角色 | 建議模式 | 範例 |
| ------------- | ---- | ----------------- | -------- | 
| 資源群組 | 包含一或多個實驗室帳戶，以及一或多個共用映像庫 | \<organization short name\>-\<environment\>-rg<ul><li>**組織簡短名稱** 可識別資源群組支援的組織名稱。</li><li>**環境** 會識別資源的環境，例如 *試驗* 或 *生產* 環境。</li><li>**Rg** 代表資源類型 *資源群組*。</li></ul> | contosouniversitylabs-rg<br/>contosouniversitylabs-pilot-rg<br/>contosouniversitylabs-prod-rg |
| 實驗室帳戶 | 包含一或多個實驗室 | \<organization short name\>-\<environment\>-la<ul><li>**組織簡短名稱** 可識別資源群組支援的組織名稱。</li><li>**環境** 會識別資源的環境，例如 *試驗* 或 *生產* 環境。</li><li>**La** 代表資源類型 *實驗室帳戶*。</li></ul> | contosouniversitylabs-la<br/>mathdeptlabs-la<br/>sciencedeptlabs-pilot-la<br/>sciencedeptlabs-prod-la |
| 實驗室 | 包含一或多個 VM |\<class name\>-\<timeframe\>-\<educator identifier\><ul><li>**類別名稱** 會識別實驗室支援的類別名稱。</li><li>**時間範圍** 指出提供課程的時間範圍。</li>**教育工作者識別碼** 會識別擁有實驗室的教師。</li></ul> | CS1234-fall2019-johndoe<br/>CS1234-spring2019-johndoe |
| 共用映像庫 | 包含一或多個 VM 映像版本 | \<organization short name\>gallery | contosouniversitylabsgallery |

如需有關命名其他 Azure 資源的詳細資訊，請參閱 [Azure 資源的命名慣例](/azure/architecture/best-practices/naming-conventions)。

## <a name="regionslocations"></a>區域/位置

當您設定 Azure 實驗室服務資源時，您必須提供將裝載資源之資料中心的區域或位置。 下一節說明區域或位置可能會如何影響與設定實驗室相關的每個資源。

### <a name="resource-group"></a>資源群組

區域會指定儲存資源群組相關資訊的資料中心。 包含在資源群組內的 Azure 資源可以與其父系位於不同的區域。

### <a name="lab-account"></a>實驗室帳戶

實驗室帳戶的位置會指出資源存在的區域。  

### <a name="lab"></a>實驗室

實驗室存在的位置會根據下列因素而有所不同：

  - **實驗室帳戶是使用虛擬網路對等互連的**
  
    當實驗室帳戶位於相同區域時，您可以 [對等互連虛擬網路](./how-to-connect-peer-virtual-network.md) 。  使用虛擬網路對等互連實驗室帳戶時，實驗室會自動建立在與實驗室帳戶和虛擬網路相同的區域中。

    > [!NOTE]
    > 當實驗室帳戶與虛擬網路對等互連時，會停用 [ **允許實驗室建立者挑選實驗室位置** ] 設定。 如需詳細資訊，請參閱[允許實驗室建立者挑選實驗室的位置](./allow-lab-creator-pick-lab-location.md)。
    
  - **未對等互連任何虛擬網路 *，且* 不允許實驗室建立者挑選實驗室位置**
  
    若未使用實驗室帳戶對等互連 *任何* 虛擬網路，且 [實驗室建立者 *不允許* 挑選實驗室位置](./allow-lab-creator-pick-lab-location.md)，則會自動在具有可用 VM 容量的區域中建立實驗室。  具體而言，Azure 實驗室服務會在[與實驗室帳戶位於相同地理位置的區域](https://azure.microsoft.com/global-infrastructure/regions)中尋找可用性。

  - **未對等互連任何虛擬網路 *，並* 允許實驗室建立者挑選實驗室位置**
       
    若 *未對等互連任何* 虛擬網路，且 [*允許* 實驗室建立者挑選實驗室位置](./allow-lab-creator-pick-lab-location.md)，實驗室建立者可以選取的位置會視可用容量而定。

> [!NOTE]
> 若要協助確保區域具有足夠的 VM 容量，請務必在建立實驗室時，先透過實驗室帳戶要求容量。

一般規則是將資源的區域設定為最接近其使用者的區域。 針對實驗室，這表示要建立最接近您學生的實驗室。 針對學生位於世界各地的線上課程，請使用您的最佳判斷來建立集中位置的實驗室。 或者，您可以根據您的學生區域，將某個類別分割成多個實驗室。

## <a name="vm-sizing"></a>VM 大小調整

當系統管理員或實驗室建立者建立實驗室時，他們可以選擇各種不同的 VM 大小，視其教室的需求而定。 請記住，計算大小可用性取決於您的實驗室帳戶所在的區域。

| 大小 | 規格 | 數列 | 使用建議 |
| ---- | ----- | ------ | ------------- |
| 小型| <ul><li>2個 &nbsp; 核心</li><li>3.5 gb (GB) RAM</li> | [Standard_A2_v2](../virtual-machines/av2-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json) | 最適合命令列、開啟網頁瀏覽器、低流量網頁伺服器、小型至中型資料庫。 |
| 中 | <ul><li>4 &nbsp; 核心</li><li>7 &nbsp; GB &nbsp; RAM</li> | [Standard_A4_v2](../virtual-machines/av2-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json) | 最適合關係資料庫、記憶體內部快取及分析。 |
| 中型 (嵌套虛擬化)  | <ul><li>4 &nbsp; 核心</li><li>16 &nbsp; GB &nbsp; RAM</li></ul> | [Standard_D4s_v3](../virtual-machines/dv3-dsv3-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json#dsv3-series) | 最適合關係資料庫、記憶體內部快取及分析。
| 大型 | <ul><li>8個 &nbsp; 核心</li><li>16 &nbsp; GB &nbsp; RAM</li></ul>  | [Standard_A8_v2](../virtual-machines/av2-series.md) | 最適合需要更快的 Cpu、更佳的本機磁片效能、大型資料庫、大型記憶體快取的應用程式。  此大小也支援巢狀虛擬化。 |
| 大型 (的嵌套虛擬化)  | <ul><li>8個 &nbsp; 核心</li><li>32 &nbsp; GB &nbsp; RAM</li></ul>  | [Standard_D8s_v3](../virtual-machines/dv3-dsv3-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json#dsv3-series) | 最適合需要更快的 Cpu、更佳的本機磁片效能、大型資料庫、大型記憶體快取的應用程式。 |
| 小型 GPU (視覺效果)  | <ul><li>6個 &nbsp; 核心</li><li>56 &nbsp; GB &nbsp; RAM</li>  | [Standard_NV6](../virtual-machines/nv-series.md) | 最適合使用 OpenGL 和 DirectX 等架構的遠端視覺效果、串流、遊戲及編碼。 |
| 小型 GPU (計算) | <ul><li>6個 &nbsp; 核心</li><li>56 &nbsp; GB &nbsp; RAM</li></ul>  | [Standard_NC6](../virtual-machines/nc-series.md) |最適合需要大量電腦的應用程式，例如 AI 和深度學習。 |
| 中型 GPU (視覺效果)  | <ul><li>12個 &nbsp; 核心</li><li>112 &nbsp; GB &nbsp; RAM</li></ul>  | [Standard_NV12](../virtual-machines/nv-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json) | 最適合使用 OpenGL 和 DirectX 等架構的遠端視覺效果、串流、遊戲及編碼。 |

## <a name="manage-identity"></a>管理身分識別

藉由使用 [Azure 角色型存取控制 (RBAC) ](../role-based-access-control/overview.md) 來存取實驗室帳戶和實驗室，您可以指派下列角色：

- 實驗室帳戶 **擁有** 者

    系統會自動將實驗室帳戶擁有者角色指派給建立實驗室帳戶的系統管理員。 擁有者角色可以：
     - 變更實驗室帳戶設定。
     - 將實驗室帳戶的存取權授與其他系統管理員，以做為擁有者或參與者。
     - 以建立者、擁有者或參與者的身分，授與教師存取實驗室的許可權。
     - 在實驗室帳戶中建立及管理所有實驗室。

- 實驗室帳戶 **參與者**

    獲指派參與者角色的系統管理員可以：
    - 變更實驗室帳戶設定。
    - 在實驗室帳戶中建立及管理所有實驗室。

    不過，參與者 *無法* 將實驗室帳戶或實驗室的存取權授與其他使用者。

- **實驗室建立者**

    若要在實驗室帳戶中建立實驗室，教師必須是實驗室建立者角色的成員。  建立實驗室的教師會自動新增為實驗室擁有者。 如需詳細資訊，請參閱 [將使用者新增至實驗室建立者角色](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)。 

- 實驗室 **擁有** 者或 **參與者**
  
    實驗室擁有者或參與者角色的教育者可以查看和變更實驗室的設定。 此人員也必須是實驗室帳戶讀取者角色的成員。

    實驗室擁有者和參與者角色之間的主要差異在於，只有擁有者可以授與其他使用者管理實驗室的存取權。 參與者 *無法* 授與其他使用者管理實驗室的存取權。

- **共用映像庫**

    當您將共用映射庫連結到實驗室帳戶時，實驗室帳戶擁有者和參與者和實驗室建立者、實驗室擁有者和實驗室參與者都會自動獲得存取權，以在資源庫中查看和儲存影像。

當您指派角色時，它有助於遵循下列秘訣：

   - 一般來說，只有系統管理員才應該是實驗室帳戶擁有者或參與者角色的成員。 實驗室帳戶可能有一個以上的擁有者或參與者。
   - 若要讓教師能夠建立新的實驗室並管理他們所建立的實驗室，您只需要將實驗室建立者角色指派給他們。
   - 若要讓教師能夠管理特定的實驗室，但 *不* 能建立新的實驗室，請將其所管理之每個實驗室的擁有者或參與者角色指派給他們。 例如，您可能想要讓教授和教學助理共同擁有實驗室。 如需詳細資訊，請參閱 [將擁有者新增至實驗室](./how-to-add-user-lab-owner.md)。

## <a name="pricing"></a>定價

### <a name="azure-lab-services"></a>Azure 實驗室服務

若要瞭解定價，請參閱 [Azure 實驗室服務定價](https://azure.microsoft.com/pricing/details/lab-services/)。


### <a name="shared-image-gallery"></a>共用映像庫

如果您打算使用共用映射庫來儲存和管理映射版本，您也需要考慮共用映射庫服務的定價。 

建立共用映像庫並連結至您的實驗室帳戶是免費的。 在您將映射版本儲存至資源庫之前，不會產生任何費用。 使用共用映射庫的定價通常相當微不足道，但請務必瞭解其計算方式，因為它不包含在 Azure 實驗室服務的定價中。  

#### <a name="storage-charges"></a>儲存體費用

為了儲存映射版本，共用映射庫會使用標準硬碟 (HDD) 受控磁片。 使用 HDD 受控磁片的大小取決於所儲存映射版本的大小。 若要瞭解定價，請參閱 [受控磁片定價](https://azure.microsoft.com/pricing/details/managed-disks/)。

#### <a name="replication-and-network-egress-charges"></a>複寫和網路輸出費用

當您使用實驗室範本 VM 儲存映射版本時，Azure 實驗室服務會先將它儲存在來源區域，然後自動將來源映射版本複寫到一或多個目的地區域。 

請務必注意，Azure 實驗室服務會自動將來源映射版本複製到實驗室所在 [地理位置內的所有目的地區域](https://azure.microsoft.com/global-infrastructure/regions/) 。 例如，如果您的實驗室位於美國地理位置，則會將映射版本複寫到每個存在於美國的八個區域中。

當映像版本從來源區域複寫到其他目標區域時，就會發生網路輸出費用。 當映像的資料開始從來源區域傳出，就會依據映像版本的大小開始計費。  如需定價詳細資料，請參閱 [頻寬定價詳細資料](https://azure.microsoft.com/pricing/details/bandwidth/)。

針對 [教育解決方案](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) 客戶，可能會放棄出口費用。 若要深入瞭解，請洽詢您的帳戶管理員。 

如需詳細資訊，請參閱 < 適用于學術客戶的資料傳輸程式有哪些，以及如何符合資格？ 在 [ [教育機構方案](https://azure.microsoft.com/pricing/details/bandwidth/) ] 頁面的 [常見問題] 區段中。

#### <a name="pricing-example"></a>價格範例

讓我們看看將範本 VM 映射儲存至共用映射庫的成本範例。 假設以下案例：

- 您有一個自訂 VM 映像。
- 您要儲存兩個版本的映像。
- 您的實驗室在美國，總共有八個區域。
- 每個映像版本的大小為 32 GB；因此，HDD 受控磁碟的價格是每月 $1.54。

每月的總成本預估為：

* *&times; &times; 複本 &times; 受控磁片價格 = 每月總成本數目的映射數目*

在此範例中，成本是：

* 1個自訂映射 (32 GB) &times; 2 &times; 個版本8美國區域 &times; $1.54 = $24.64/月

> [!NOTE]
> 上述的計算僅供範例之用。 其中涵蓋與使用共用映射庫相關聯的儲存體成本， *不包含輸出* 成本。 如需儲存體的實際定價，請參閱 [受控磁碟定價](https://azure.microsoft.com/en-us/pricing/details/managed-disks/)。

#### <a name="cost-management"></a>成本管理

實驗室帳戶管理員必須定期從資源庫刪除不必要的映射版本，以管理成本。 

請勿將複寫刪除到特定區域，以降低成本，不過這個選項存在於共用映射庫中。 複寫變更可能會對 Azure 實驗室服務從儲存在共用映射庫中的映射發佈 Vm 的能力造成負面影響。

## <a name="next-steps"></a>下一步

如需設定和管理實驗室的詳細資訊，請參閱：

- [實驗室帳戶設定指南](account-setup-guide.md)  
- [實驗室設定指南](setup-guide.md)  
- [適用於實驗室的成本管理](cost-management-guide.md)  
- [在小組中使用 Azure 實驗室服務](lab-services-within-teams-overview.md)
