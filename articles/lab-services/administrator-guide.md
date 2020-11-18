---
title: Azure 實驗室服務 - 管理員指南 |Microsoft Docs
description: 本指南可協助使用 Azure 實驗室服務建立及管理實驗室帳戶的管理員。
ms.topic: article
ms.date: 10/20/2020
ms.openlocfilehash: b1fadc58926b00c75ab888dad86e45b181059a38
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659840"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure 實驗室服務 - 管理員指南
管理大學雲端資源的資訊技術 (IT) 管理員通常負責為其學校設定實驗室帳戶。 設定實驗室帳戶之後，系統管理員或教育人員會建立實驗室帳戶內所含的實驗室。 本文提供相關 Azure 資源的高階概述以及建立概述的指引。

![實驗室帳戶中 Azure 資源的高階觀點](./media/administrator-guide/high-level-view.png)

- 實驗室是裝載在 Azure 實驗室服務所擁有的 Azure 訂用帳戶內。
- 實驗室帳戶、共用映像庫、映像版本是在您的訂用帳戶下託管。
- 您可以將您的實驗室帳戶和共用映像庫放在相同的資源群組中。 在此圖中，其位於不同的資源群組。

如需架構的詳細資訊，請參閱文章：[實驗室架構基本](./classroom-labs-fundamentals.md)概念

## <a name="subscription"></a>訂用帳戶
您的大學有一或多個 Azure 訂用帳戶。 訂用帳戶是用來管理在帳戶中所使用所有 Azure 資源/服務的計費和安全性，包括實驗室帳戶。

實驗室帳戶和其訂用帳戶之間的關聯性很重要，因為：

- 帳單是透過包含實驗室帳戶的訂用帳戶來回報。
- 您可以為訂用帳戶的 Azure Active Directory (AD) 租用戶提供 Azure 實驗室服務的存取權。 您可以將使用者新增為實驗室帳戶 owner\contributor、實驗室建立者或實驗室擁有者。

系統會在 Azure 實驗室服務所擁有的訂用帳戶中，為您管理及裝載實驗室和其虛擬機器 (Vm) 。

## <a name="resource-group"></a>資源群組
訂用帳戶包含一或多個資源群組。 資源群組是用來建立 Azure 資源的邏輯分組，以在相同的解決方案中一起使用。  

當您建立實驗室帳戶時，您必須設定包含該實驗室帳戶的資源群組。 

建立[共用映像庫](#shared-image-gallery)時，也需要資源群組。 您可以選擇將您的實驗室帳戶和共用映像庫放在兩個不同的資源群組中，如果您打算讓不同的解決方案共用映像庫，這是常見的做法。 或者，您可以選擇將其放在相同資源群組中。

當您建立實驗室帳戶時，可以同時自動建立和連結共用映像庫。  這個做法會導致實驗室帳戶和共用映像庫在不同的資源群組中建立。 使用此教學課程所述的步驟時，您會看到這項行為：[在建立實驗室帳戶時設定共用映像庫](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation)。 本文開頭的映像也使用此設定。 

建議您事先投入時間來規劃您的資源群組結構，因為實驗室帳戶或共用映像庫的資源群組一旦建立之後，就「不」能變更。 如果您需要變更這些資源的資源群組，您必須刪除並重新建立實驗室帳戶和/或共用映像庫。

## <a name="lab-account"></a>實驗室帳戶

實驗室帳戶可作為一或多個實驗室的容器。 開始使用 Azure 實驗室服務時，通常只有一個實驗室帳戶。 當您的實驗室使用量規模改變時，您稍後可以選擇建立更多的實驗室帳戶。

以下清單重點介紹可能有益於多個實驗室帳戶的案例：

- **跨實驗室管理不同的原則需求**

    當您設定實驗室帳戶時，您會設定適用于實驗室帳戶下 *所有* 實驗室的原則，例如：
    - 具有實驗室可存取之共用資源的 Azure 虛擬網路。 例如，您可能有一組實驗室需要存取虛擬網路內的共用資料集。
    - 虛擬機器 (VM) 映射，可供實驗室用來建立 Vm。 例如，您可能有一組實驗室需要存取 Linux Marketplace 映射的 [資料科學 VM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) 。

    如果您的實驗室有彼此獨有的獨特原則需求，則建立個別的實驗室帳戶來個別管理這些實驗室可能會有説明。

- **依實驗室帳戶區分預算**
  
    您可能需要更清楚的預算，而不是透過單一實驗室帳戶報告所有實驗室成本。 例如，您可以為大學的數學系、資訊科學系等建立實驗室帳戶，以區隔各系的預算。  接著，您可以使用 [Azure 成本管理](../cost-management-billing/cost-management-billing-overview.md)檢視每個個別實驗室帳戶的成本。

- **隔離試驗實驗室和作用/生產實驗室**
  
    在某些情況下，您可能會想要變更實驗室帳戶的試驗原則，而不影響作用/生產實驗室。 在這種情況下，為試驗目的建立個別的實驗室帳戶，可讓您隔離變更。 

## <a name="lab"></a>實驗室

實驗室包含虛擬機器 (Vm) 分別指派給單一學生。  一般來說，您可以預期：

- 每個類別都有一個實驗室。
- 每半年度建立一組新的實驗室 (或針對每個時間範圍，) 提供您的類別。 通常具有相同映像需求的課程應該使用[共用映像庫](#shared-image-gallery)，在實驗室和學期之間重複使用映像。

判斷如何結構您的實驗室時，請考慮下列幾點：

- **實驗室中的所有 Vm 都會使用已發佈的相同映射進行部署**

    如此一來，如果您的類別需要同時發行不同的實驗室映射，則必須為每個實驗室建立個別的實驗室。
  
- **使用量配額是在實驗室層級設定，並套用至實驗室內的所有使用者**

    若要為使用者設定不同的配額，您必須建立個別的實驗室。 不過，在您設定配額之後，可以為特定使用者增加更多時數。
  
- **啟動或關閉排程是在實驗室層級設定，並套用至實驗室內的所有 VM**

    類似于上一個點，如果您需要為使用者設定不同的排程，您需要建立個別的實驗室。

根據預設，每個實驗室都有自己的虛擬網路。  如果您已啟用 vnet 對等互連，每個實驗室都會有自己的子網對等互連至指定的虛擬網路。

## <a name="shared-image-gallery"></a>共用映像庫

共用映像庫會連結至實驗室帳戶，並做為儲存映像的中央存放庫。 當教師選擇從實驗室的範本虛擬機器匯出 (VM) 時，映射會儲存在資源庫中。 每次教師變更範本 VM 並匯出時，都會儲存新版本的映像，同時維持舊版。

講師可以在建立新的實驗室時，從共用映射庫發佈映射版本。 雖然映像庫會儲存映像的多個版本，但教師只能在建立實驗室時選取最新版本。

共用映射庫是選擇性的資源，當您只需要幾個實驗室時，可能不會立即需要。 不過，使用「共用映射庫」有許多優點，可在您調整以擁有更多實驗室時很有説明：

- **可讓您儲存及管理範本 VM 映像的版本**

    對於建立自訂映像或是對公用 Marketplace 資源庫中的映像進行變更 (軟體、設定等等) 非常有用。  例如，教育工作者通常需要安裝不同的軟體/工具。 不同版本的範本 VM 映像可以匯出至共用映像庫，而不需要學生自行手動安裝這些必要條件。 然後，您可以在建立新的實驗室時使用這些映射版本。
- **允許跨實驗室 sharing\reuse 範本 VM 映射**

    您可以儲存並重複使用映射，如此一來，您就不必在每次建立新的實驗室時，從頭開始設定映射。 例如，如果提供需要相同映射的多個類別，則只需要建立一次此映射，並將其匯出至共用映射庫，讓它可以跨實驗室共用。
- **可透過複寫來確保映像可用性**

    當您從實驗室儲存至共用映射庫時，會自動將您的映射複寫到 [相同地理位置內的其他區域](https://azure.microsoft.com/global-infrastructure/regions/)。 如果區域發生中斷情況，則將映射發佈至您的實驗室並不會受到影響，因為可以使用來自另一個區域的映射複本。  從多個複本發佈 VM 也有助於提高效能。

若要將共用映像邏輯分組，有幾個做法：

- 建立多個共用映像庫。 每個實驗室帳戶只能與一個共用映像庫連線，因此，此做法也會需要您建立多個實驗室帳戶。
- 或者，您可以使用由多個實驗室帳戶共用的單一共用映像庫。 在此情況下，每個實驗室帳戶只能啟用適用于其包含之實驗室的映射。

## <a name="naming"></a>命名

當您開始使用 Azure 實驗室服務時，建議您建立資源群組、實驗室帳戶、實驗室和共用映射庫的命名慣例。 雖然您所建立的命名慣例是針對貴組織的需求而設，但下表概述了一般準則。

| 資源類型 | 角色 | 建議模式 | 範例 |
| ------------- | ---- | ----------------- | -------- | 
| 資源群組 | 包含一或多個實驗室帳戶，以及一或多個共用映像庫 | \<organization short name\>-\<environment\>-rg<ul><li>**組織簡短名稱** 指出資源群組支援的組織名稱</li><li>**環境** 指出資源的環境，例如試驗或生產</li><li>**rg** 代表資源類型：資源群組。</li></ul> | contosouniversitylabs-rg<br/>contosouniversitylabs-pilot-rg<br/>contosouniversitylabs-prod-rg |
| 實驗室帳戶 | 包含一或多個實驗室 | \<organization short name\>-\<environment\>-la<ul><li>**組織簡短名稱** 指出資源群組支援的組織名稱</li><li>**環境** 指出資源的環境，例如試驗或生產</li><li>**la** 代表資源類型：實驗室帳戶。</li></ul> | contosouniversitylabs-la<br/>mathdeptlabs-la<br/>sciencedeptlabs-pilot-la<br/>sciencedeptlabs-prod-la |
| 實驗室 | 包含一或多個 VM |\<class name\>-\<timeframe\>-\<educator identifier\><ul><li>**類別名稱** 指出實驗室支援的課程名稱。</li><li>**時間範圍** 指出提供課程的時間範圍。</li>**教師識別碼** 指出擁有實驗室的教師。</li></ul> | CS1234-fall2019-johndoe<br/>CS1234-spring2019-johndoe |
| 共用映像庫 | 包含一或多個 VM 映像版本 | \<organization short name\>gallery | contosouniversitylabsgallery |

如需有關命名其他 Azure 資源的詳細資訊，請參閱[適用於 Azure 資源的 命名慣例](/azure/architecture/best-practices/naming-conventions)。

## <a name="regionslocations"></a>區域/位置

設定 Azure 實驗室服務的資源時，您必須提供託管該資源的資料中心所在區域 (或位置)。 以下詳述在設定實驗室時，區域如何影響相關的每個資源。

### <a name="resource-group"></a>資源群組

區域會指定用於儲存資源群組相關資訊的資料中心。 資源群組包含的 Azure 資源可以位於與其父代不同的區域中。

### <a name="lab-account"></a>實驗室帳戶

實驗室帳戶的位置會指出此資源所在的區域。  

### <a name="lab"></a>實驗室

實驗室存在的位置會根據下列因素而有所不同：

  - **實驗室帳戶已與虛擬網路 (VNet) 對等互連**
  
    當實驗室帳戶與 VNet 位於相同區域時，可以[與 VNet 對等互連](./how-to-connect-peer-virtual-network.md)。  使用 VNet 對等互連實驗室帳戶時，實驗室會自動建立在與實驗室帳戶和 VNet 相同的區域中。

    > [!NOTE]
    > 當實驗室帳戶與 VNet 對等互連時時，會停用 [允許實驗室建立者挑選實驗室位置] 設定。 如需此設定的詳細資訊，請參閱此文章：[允許實驗室建立者挑選實驗室的位置](./allow-lab-creator-pick-lab-location.md)。
    
  - * * 沒有 VNet 是對等互連 **_，而_* 不允許實驗室建立者挑選實驗室 location_ *
  
    如果沒有具有實驗室帳戶 **的 VNet 對等互連**，*且* [**不** 允許實驗室建立者挑選實驗室位置](./allow-lab-creator-pick-lab-location.md)，則會自動在具有可用 VM 容量的區域中建立實驗室。  具體而言，Azure 實驗室服務會在[與實驗室帳戶位於相同地理位置的區域](https://azure.microsoft.com/global-infrastructure/regions)中尋找可用性。

  - * * 沒有 VNet 是對等互連 **_，而_* 允許實驗室建立者挑選實驗室 location_ *
       
    當「沒有」VNet 對等互連，而且 [允許實驗室建立者挑選實驗室位置](./allow-lab-creator-pick-lab-location.md)時，實驗室建立者可選取的位置會以可用容量為基礎。

> [!NOTE]
> 為了確保區域中有足夠的 VM 容量，請務必先透過實驗室帳戶要求容量或在建立實驗室時要求容量。

一般規則是將資源的區域設定為最接近其使用者的地區。 針對實驗室，這表示要建立最接近您學生的實驗室。 針對學生在全球各地的線上課程，您必須使用最佳判斷來建立集中位置的實驗室。 或者，根據您的學生區域，將類別分割成多個實驗室。

### <a name="shared-image-gallery"></a>共用映像庫

區域表示第一個映像版本儲存所在的來源區域，然後才會自動複寫至目標區域。

## <a name="vm-sizing"></a>VM 大小調整

當系統管理員或實驗室建立者建立實驗室時，他們可以根據其教室的需求選擇下列 VM 大小。 請記住，可用的計算大小取決於您的實驗室帳戶所在的區域：

| 大小 | 規格 | 數列 | 使用建議 |
| ---- | ----- | ------ | ------------- |
| 小型| <ul><li>2 核心</li><li>3.5 GB RAM</li> | [Standard_A2_v2](../virtual-machines/av2-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json) | 此大小最適合用於命令列、開啟網頁瀏覽器、低流量網頁伺服器、小型至中型資料庫。 |
| 中 | <ul><li>4 核心</li><li>7 GB RAM</li> | [Standard_A4_v2](../virtual-machines/av2-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json) | 此大小最適合用於關聯式資料庫、記憶體內部快取及分析。 |
| 中型 (巢狀虛擬化) | <ul><li>4 核心</li><li>16 GB RAM</li></ul> | [Standard_D4s_v3](../virtual-machines/dv3-dsv3-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json#dsv3-series) | 此大小最適合用於關聯式資料庫、記憶體內部快取及分析。
| 大型 | <ul><li>8 核心</li><li>16 GB RAM</li></ul>  | [Standard_A8_v2](../virtual-machines/av2-series.md) | 此大小最適合用於需要更快 CPU、更高本機磁碟效能、大型資料庫、大型記憶體快取的應用程式。  此大小也支援巢狀虛擬化。 |
| 大型 (巢狀虛擬化) | <ul><li>8 核心</li><li>32 GB RAM</li></ul>  | [Standard_D8s_v3](../virtual-machines/dv3-dsv3-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json#dsv3-series) | 此大小最適合用於需要更快 CPU、更高本機磁碟效能、大型資料庫、大型記憶體快取的應用程式。 |
| 小型 GPU (視覺化) | <ul><li>6 核心</li><li>56 GB RAM</li>  | [Standard_NV6](../virtual-machines/nv-series.md) | 此大小最適合使用 OpenGL 和 DirectX 這類架構的遠端視覺化、串流、遊戲、編碼。 |
| 小型 GPU (計算) | <ul><li>6 核心</li><li>56 GB RAM</li></ul>  | [Standard_NC6](../virtual-machines/nc-series.md) |此大小最適用於計算密集型應用程式，例如人工智慧及深度學習。 |
| 中型 GPU (視覺化) | <ul><li>12 核心</li><li>112 GB RAM</li></ul>  | [Standard_NV12](../virtual-machines/nv-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json) | 此大小最適合使用 OpenGL 和 DirectX 這類架構的遠端視覺化、串流、遊戲、編碼。 |

## <a name="manage-identity"></a>管理身分識別

使用 [azure 角色型存取控制 (AZURE RBAC) ](../role-based-access-control/overview.md)，您可以指派下列角色來授與實驗室帳戶和實驗室的存取權：

- **實驗室帳戶擁有者**

    建立實驗室帳戶的管理員會自動新增至實驗室帳戶的 **擁有者** 角色。  具有 **擁有者** 角色的管理員可以：
     - 變更實驗室帳戶的設定。
     - 授與其他管理員對實驗室帳戶的擁有者或參與者存取權。
     - 將實驗室的存取權授與作者、擁有者或參與者。
     - 在實驗室帳戶中建立及管理所有實驗室。

- **實驗室帳戶參與者**

    具有 **參與者** 角色的管理員可以：
    - 變更實驗室帳戶的設定。
    - 在實驗室帳戶中建立及管理所有實驗室。

    不過，他們 *無法* 將實驗室帳戶或實驗室的存取權授與其他使用者。

- **實驗室建立者**

    若要在實驗室帳戶中建立實驗室，教師必須是 **實驗室建立者** 角色的成員。  當教育工作者建立實驗室時，系統會自動將他們新增為實驗室的擁有者。  請參閱如何 [將使用者新增至 **實驗室建立者** 角色](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)的教學課程。 

- **實驗室 owner\contributor**
  
    當實驗室是實驗室的 **擁有** 者或 **參與者** 角色的成員時，可以查看並變更實驗室的設定;它們也必須是實驗室帳戶 **讀取** 者角色的成員。

    實驗室的 **擁有者** 和 **參與者** 角色主要差異在於，參與者「無法」授與其他使用者管理實驗室的存取權，只有擁有者能夠給予其他使用者管理實驗室的存取權。

    此外，如果教育工作者也是 **實驗室建立者** 角色的成員，則 *無法* 建立新的實驗室。

- **共用映像庫**

    當您將共用映像庫連結至實驗室帳戶時，實驗室帳戶擁有者/參與者和實驗室建立者/擁有者/參與者會自動獲得可以檢視和儲存映像庫中映像的存取權。

以下是協助指派角色的一些秘訣：
   - 一般而言，只有管理員應該是實驗室帳戶的 **擁有者** 或 **參與者** 角色的成員；您可能會有一個以上的擁有者/參與者。
   - 讓教師能夠建立新的實驗室，以及管理他們所建立的實驗室;您只需要將存取權指派給 **實驗室建立者** 角色。
   - 讓教師能夠管理特定的實驗室，但 *不* 能建立新的實驗室;您應針對每個要管理的實驗室，指派 **擁有** 者或 **參與者** 角色的存取權。  例如，您可能想要讓教授和教學助理共同擁有實驗室。  請參閱指南，以瞭解如何 [將使用者新增為實驗室的擁有](./how-to-add-user-lab-owner.md)者。

## <a name="pricing"></a>定價

### <a name="azure-lab-services"></a>Azure 實驗室服務

下列文章說明 Azure 實驗室服務的價格：[Azure 實驗室服務定價](https://azure.microsoft.com/pricing/details/lab-services/)。

如果您打算使用共用映像庫來儲存和管理映像版本，您也需要考慮其價格。 

### <a name="shared-image-gallery"></a>共用映像庫

建立共用映像庫並連結至您的實驗室帳戶是免費的。 在您將映像版本儲存至映像庫之前，不會產生費用。 一般而言，使用共用映像庫的價格相當低廉，但請務必瞭解其計算方式，因為共用映像庫不包含在 Azure 實驗室服務的價格中。  

#### <a name="storage-charges"></a>儲存體費用

共用映像庫會使用標準 HDD 受控磁碟來儲存映像版本。 所使用之 HDD 受控磁碟的大小取決於所儲存映像版本的大小。 若要瞭解價格，請參閱下列文章：[受控磁碟價格](https://azure.microsoft.com/pricing/details/managed-disks/)。

#### <a name="replication-and-network-egress-charges"></a>複寫和網路輸出費用

當您使用實驗室的範本虛擬機器 (VM) 來儲存映射版本時，Azure 實驗室服務會先將它儲存在來源區域，然後自動將來源映射版本複寫到一或多個目的地區域。 請務必注意，Azure 實驗室服務會自動將來源映射版本複製到實驗室所在 [地理位置內](https://azure.microsoft.com/global-infrastructure/regions/) 的所有目的地區域。 例如，如果您的實驗室位於美國地理位置，則會將映射版本複寫到每個存在於美國境內的八個區域

當映像版本從來源區域複寫到其他目標區域時，就會發生網路輸出費用。 當映像的資料開始從來源區域傳出，就會依據映像版本的大小開始計費。  如需價格詳細資料，請參閱下列文章：[頻寬定價詳細資料](https://azure.microsoft.com/pricing/details/bandwidth/)。

[教育解決方案](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3)的客戶可能可以免除輸出費用。 請與您的帳戶管理員洽談，以深入瞭解。  如需詳細資訊，請看連結文件中的 **參閱常見問題集** 一節，特別是「學術客戶有哪些資料傳輸方案，我要如何符合格？」的問題。

#### <a name="pricing-example"></a>價格範例

為了回顧上面所述的價格，讓我們來看一個將範本 VM 映像儲存到共用映像庫的範例。 假設以下案例：

- 您有一個自訂 VM 映像。
- 您要儲存兩個版本的映像。
- 您的實驗室位於美國，美國總共有八個區域。
- 每個映像版本的大小為 32 GB；因此，HDD 受控磁碟的價格是每月 $1.54。

預估總成本為：

映像數目 × 版本數目 × 複本數目 × 受控磁碟價格

在此範例中，成本是：

1 個自訂映像 (32 GB) x 2 個版本 x 8 個美國區域 x $1.54 = 每月 $24.64

#### <a name="cost-management"></a>成本管理

請務必讓實驗室帳戶管理員定期從映像庫中刪除不必要的映像版本，以管理成本。 

您不應該為了降低成本而刪除特定區域的複本 (共用映像庫中便有此選項)。 複本變更可能會對 Azure 實驗室服務造成負面影響，損及從儲存在共用映像庫中的映像發佈 VM 的能力。

## <a name="next-steps"></a>後續步驟

設定實驗室環境的一般步驟。

- [實驗室帳戶設定指南](account-setup-guide.md)
- [實驗室設定指南](setup-guide.md)
- [適用於實驗室的成本管理](cost-management-guide.md)
- [在小組內使用 Azure 實驗室服務](lab-services-within-teams-overview.md)