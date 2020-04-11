---
title: Azure 實驗室服務 - 管理員指南 |微軟文件
description: 本指南可説明使用 Azure 實驗室服務創建和管理實驗室帳戶的管理員。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2019
ms.author: spelluru
ms.openlocfilehash: 7ce7ef15f0bf13182e4799fb640e83136d0d4695
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115031"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure 實驗室服務 - 管理員指南
管理大學雲資源的資訊技術 (IT) 管理員通常負責為其學校設置實驗室帳戶。 設置實驗室帳戶后,管理員或教育工作者將創建實驗室帳戶中包含的教室實驗室。 本文提供了所涉及的 Azure 資源的高級概述以及創建這些資源的指導。

![實驗室帳戶中 Azure 資源的進階檢視](../media/administrator-guide/high-level-view.png)

- 課堂實驗室託管在 Azure 實驗室服務擁有的 Azure 訂閱中。
- 實驗室帳戶、共用圖像庫和映射版本託管在訂閱中。
- 可以將實驗室帳戶和共用圖像庫放在同一資源組中。 在此圖中,它們位於不同的資源組中。 

## <a name="subscription"></a>訂用帳戶
您的大學具有一個或多個 Azure 訂閱。 訂閱用於管理在其中使用的所有 Azure 資源、服務(包括實驗室帳戶)的計費和安全。

實驗室帳戶與其訂閱之間的關係很重要,因為:

- 計費通過包含實驗室帳戶的訂閱報告。
- 您可以向訂閱中的 Azure 活動目錄 (AD) 租戶中的使用者授予對 Azure 實驗室服務的許可權。 您可以將使用者添加為實驗室帳戶擁有者、參與者、課堂實驗室建立者或教室實驗室擁有者。

課堂實驗室及其虛擬機 (VM) 在 Azure 實驗室服務擁有的訂閱中為您管理和託管。

## <a name="resource-group"></a>資源群組
訂閱包含一個或多個資源組。 資源組用於創建在同一解決方案中一起使用的 Azure 資源的邏輯分組。  

創建實驗室帳戶時,必須配置包含實驗室帳戶的資源組。 

建立[共用圖像庫](#shared-image-gallery)時也需要資源組。 您可以選擇將實驗室帳戶和共用圖像庫放在兩個單獨的資源組中,如果您計劃跨不同解決方案共用圖像庫,則這是典型的。 或者,您可以選擇將它們放在同一資源組中。

創建實驗室帳戶時,可以同時自動創建和附加共用圖像庫。  此選項會導致在單獨的資源組中創建實驗室帳戶和共用映射庫。 在使用本教學中描述的步驟時,您將看到此行為:[在建立實驗室帳戶時設定分享映射庫](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation)。 本文頂部的圖像也使用此配置。 

我們建議預先投入時間來規劃資源組的結構,因為創建實驗室帳戶或共用圖像庫的資源組后*無法*更改它。 如果需要更改這些資源的資源組,則需要刪除並重新創建實驗室帳戶和\或共用圖像庫。

## <a name="lab-account"></a>實驗室帳戶
實驗室帳戶用作一個或多個教室實驗室的容器。 開始使用 Azure 實驗室服務時,通常只擁有一個實驗室帳戶。 隨著實驗室使用量的擴展,您以後可能會選擇創建更多實驗室帳戶。

以下清單突出顯示了多個實驗室帳戶可能是有益的方案:

- **跨課堂實驗室管理不同的原則要求** 
    
    設定實驗室帳戶時,將設置適用於實驗室帳戶下*所有*教室實驗室的策略,例如:
    - 具有教室實驗室可以訪問的共享資源的 Azure 虛擬網路。 例如,您可能有一組需要訪問虛擬網路中的共享數據集的教室實驗室。
    - 教室實驗室可用於創建 VM 的虛擬機器 (VM) 映射。 例如,您可能有一組教室實驗室需要訪問用於 Linux 應用商店映像[的數據科學 VM。](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu) 
    
    如果您有具有彼此獨特策略要求的教室實驗室,則創建單獨的實驗室帳戶以單獨管理這些教室實驗室可能是有益的。

- **依實驗室帳戶劃分單獨的預算**
  
    您可能需要更明確地劃分預算,而不是通過單個實驗室帳戶報告所有課堂實驗室成本。 例如,您可以為大學數學系、計算機科學系等創建實驗室帳戶,以跨部門劃分預算。  然後,您可以使用[Azure 成本管理](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview)查看每個實驗室帳戶的成本。
    
- **將試驗實驗室與活動生產實驗室隔離開來**
  
    您可能希望為實驗室帳戶進行策略更改,而不會影響活動和生產實驗室。 在此類方案中,為試驗目的創建單獨的實驗室帳戶允許您隔離更改。 

## <a name="classroom-lab"></a>課堂實驗室
課堂實驗室包含虛擬機 (VM),每個虛擬機器都分配給一個學生。 通常,您可以預期:

- 每節課有一個教室實驗室。
- 每學期創建一組新的課堂實驗室(或針對每次課程提供的時間框架)。 通常,對於具有相同圖像需求的類,應使用[共用圖像庫](#shared-image-gallery)在實驗室和學期之間重用圖像。

在確定如何構建課堂實驗室時,請考慮以下幾點:

- **教室實驗室中的所有 VM 都部署與已發布的映像相同的映像**

    因此,如果您有一個需要同時發佈不同實驗室圖像的類,則必須為每個教室創建單獨的教室實驗室。
  
- **使用配額在實驗室級別設置,並應用於實驗室內的所有使用者**
    
    要為使用者設置不同的配額,必須創建單獨的教室實驗室。 但是,在設置配額后,可以向特定使用者添加更多小時。
  
- **啟動或關閉計劃設定在實驗室等級,並應用於實驗室內的所有 VM**

    與前一點類似,如果需要為使用者設置不同的計劃,則需要創建單獨的教室實驗室。 

## <a name="shared-image-gallery"></a>共用影像庫
共用圖像庫附加到實驗室帳戶,並用作存儲圖像的中央存儲庫。 當教育者選擇從教室實驗室的範本虛擬機器 (VM) 匯出時,圖像將保存在庫中。 每次教育者更改範本 VM 和匯出時,都會保存映射的新版本,同時維護以前的版本。

教師可以在創建新的教室實驗室時從共用圖像庫中發佈圖像版本。 儘管庫可以存儲圖像的多個版本,但教育工作者只能在實驗室創建期間選擇最新版本。

共用圖像庫是一種可選資源,只需幾個教室實驗室,您就不需要立即使用。 但是,使用共用圖像庫有許多好處,在擴展到擁有更多教室實驗室時非常有用:

- **讓您可以儲存及管理樣本 VM 映像的版本**

    從公共應用商店庫中創建自定義映射或對映射進行更改(軟體、配置等)非常有用。  例如,教育工作者通常要求安裝不同的軟體\工具。 範本 VM 映像的不同版本可以匯出到共用映射庫,而不是要求學生自行手動安裝這些先決條件。 然後,在創建新的教室實驗室時,可以使用這些圖像版本。
- **支援跨教室實驗室共用和重用範本 VM 映像**

    您可以保存和重用映射,這樣每次創建新的教室實驗室時都不必從頭開始配置映射。 例如,如果提供多個類需要相同的映射,則只需創建一次此映射並將其匯出到共用圖像庫,即可跨教室實驗室共用。
- **以複製檔案時使用可用性**

    當您從教室實驗室儲存到分享影像庫時,您的影像將自動複製到[同一地理位置中的其他區域](https://azure.microsoft.com/global-infrastructure/regions/)。 如果區域出現中斷,則將映射發佈到教室實驗室不會受到影響,因為可以使用來自另一個區域的圖像副本。  從多個副本發布 VM 也有助於提高性能。

要對共享映像進行邏輯群組,您有幾個選項:

- 創建多個共用圖像庫。 每個實驗室帳戶只能連接到一個共用圖像庫,因此此選項還需要創建多個實驗室帳戶。
- 或者,您可以使用由多個實驗室帳戶共用的單個共用圖像庫。 在這種情況下,每個實驗室帳戶只能啟用適用於它所包含的教室實驗室的圖像。

## <a name="naming"></a>命名
開始使用 Azure 實驗室服務時,我們建議您為資源組、實驗室帳戶、教室實驗室和共用映射庫建立命名約定。 雖然您建立的命名約定將針對組織的需求而獨一無二,但下表概述了一般準則。

| 資源類型 | 角色 | 建議模式 | 範例 |
| ------------- | ---- | ----------------- | -------- | 
| 資源群組 | 包含一個或多個實驗室帳戶和一個或多個分享影像庫 | \<組織短名稱\>-\<\>環境 -rg<ul><li>**組織短名稱**識別資源組支援的組織名稱</li><li>**環境**識別資源的環境,如試驗或生產</li><li>**Rg**代表資源類型:資源組。</li></ul> | 孔索大學實驗室-rg<br/>康托索大學實驗室-飛行員-rg<br/>孔索大學實驗室-prod-rg |
| 實驗室帳戶 | 包含一個或多個實驗室 | \<組織短名稱\>-\<\>環境 -la<ul><li>**組織短名稱**識別資源組支援的組織名稱</li><li>**環境**識別資源的環境,如試驗或生產</li><li>**La**代表資源類型:實驗室帳戶。</li></ul> | 孔索大學實驗室-拉<br/>數學計算實驗室-拉<br/>科學實驗室-飛行員-拉<br/>科學實驗室-prod-la |
| 課堂實驗室 | 包含一個或多個 VM |\<\>\<\>類別名稱時間-\<的-字格\><ul><li>**類名稱**標識實驗室支援的類的名稱。</li><li>**時間範圍**標識課程的提供時間範圍。</li>**教育標識符**標識擁有實驗室的教育工作者。</li></ul> | CS1234-秋天2019-約翰多<br/>CS1234-彈簧2019-約翰多 | 
| 共用影像庫 | 包含一個或多個 VM 映像版本 | \<組織短名稱\>庫 | 孔索大學實驗室 |

有關命名其他 Azure 資源的詳細資訊,請參閱[為 Azure 資源命名約定](/azure/architecture/best-practices/naming-conventions)。

## <a name="regionslocations"></a>區域\位置

設置 Azure 實驗室服務的資源時,需要提供承載資源的資料中心的區域(或位置)。 以下是有關區域如何影響設置實驗室所涉及的每個資源的詳細資訊。

### <a name="resource-group"></a>資源群組

該區域指定存儲有關資源組資訊的數據中心。 資源組中包含的 Azure 資源可以位於與其父資源不同的區域中。

### <a name="lab-account"></a>實驗室帳戶

實驗室帳戶的位置指示此資源存在的區域。  

### <a name="classroom-lab"></a>課堂實驗室
    
教室實驗室所在的位置因以下因素而異:

  - **實驗室帳戶與虛擬網路 (VNet) 對等**
  
    實驗室帳戶位於同一區域時,可以使用[VNet 對等](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network)互連。  當實驗室帳戶與 VNet 對等時,教室實驗室將自動創建在與實驗室帳戶和 VNet 相同的區域中。

    > [!NOTE]
    > 當實驗室帳戶與 VNet 對等時,將禁用**允許實驗室建立者選擇實驗室位置**的設置。 有關此設定的其他資訊,請參閱文章:[允許實驗室建立者選擇實驗室的位置](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location)。
    
  - **沒有 VNet 是對等的 ***,並且***不允許實驗室建立者選擇實驗室位置**
  
    當**沒有**VNet 與實驗室帳戶對等 *,並且*[實驗室建立者**不允許**選擇實驗室位置](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location)時,教室實驗室將自動在具有可用 VM 容量的區域創建。  具體而言,Azure 實驗室服務查找[與實驗室帳戶位於同一地理位置的區域](https://azure.microsoft.com/global-infrastructure/regions)的可用性。

  - **沒有 VNet 對等 ***,實驗室***建立者可以選擇實驗室位置**
       
    當**沒有**VNet 對等和[實驗室建立者可以選擇實驗室位置](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location)時,實驗室建立者可以選擇的位置將基於可用容量。

> [!NOTE]
> 為了幫助確保區域有足夠的 VM 容量,請務必首先通過實驗室帳戶或創建實驗室時請求容量。

常規規則是將資源的區域設置為最接近其用戶的區域。 對於課堂實驗室,這意味著創建離學生最近的教室實驗室。 對於學生遍佈世界各地的在線課程,您需要使用最佳判斷力創建位於中心位置的教室實驗室。 或者,根據學生區域將班級拆分為多個教室實驗室。

### <a name="shared-image-gallery"></a>共用影像庫

該區域指示在將第一個圖像版本自動複製到目標區域之前存儲第一個圖像版本的源區域。

## <a name="vm-sizing"></a>VM 大小調整
當管理員或實驗室建立者創建教室實驗室時,他們可以根據教室的需要從以下 VM 大小中進行選擇。 請記住,可用的計算大小取決於您的實驗室帳戶所在的區域:

| 大小 | 規格 | 數列 | 建議使用 |
| ---- | ----- | ------ | ------------- |
| 小型| <ul><li>2 個核心</li><li>3.5 GB 記憶體</li> | [Standard_A2_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | 此大小最適合命令列、打開 Web 瀏覽器、低流量 Web 伺服器、中小型資料庫。 |
| 中 | <ul><li>4 核心</li><li>7 GB 記憶體</li> | [Standard_A4_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | 此大小最適合關係資料庫、記憶體緩存和分析。 |
| 中階(嵌入虛擬化) | <ul><li>4 核心</li><li>16 GB RAM</li></ul> | [Standard_D4s_v3](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#dsv3-series) | 此大小最適合關係資料庫、記憶體緩存和分析。  此大小還支援嵌套虛擬化。 |
| 大型 | <ul><li>8 核心</li><li>32 GB 記憶體</li></ul>  | [Standard_DC8_v2](https://docs.microsoft.com/azure/virtual-machines/dcv2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | 此大小最適合需要更快 CPU、更好的本地磁碟性能、大型資料庫和大型記憶體緩存的應用程式。  此大小還支援嵌套虛擬化。 |
| 小型 GPU(視覺化) | <ul><li>6 核心</li><li>56 GB 記憶體</li>  | [Standard_NV6](https://docs.microsoft.com/azure/virtual-machines/nv-series) | 此大小最適合使用 OpenGL 和 DirectX 等框架進行遠端視覺化、流式傳輸、遊戲和編碼。 |
| 小型 GPU(計算) | <ul><li>6 核心</li><li>56 GB 記憶體</li></ul>  | [Standard_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) |此尺寸最適合電腦密集型應用,如人工智慧和深度學習。 |
| 中型 GPU(視覺化) | <ul><li>12 核心</li><li>112 GB 記憶體</li></ul>  | [Standard_NC12](https://docs.microsoft.com/azure/virtual-machines/nc-series) | 此大小最適合使用 OpenGL 和 DirectX 等框架進行遠端視覺化、流式傳輸、遊戲和編碼。 |

## <a name="manage-identity"></a>管理識別
使用[Azure 基於角色的存取控制](https://docs.microsoft.com/azure/role-based-access-control/overview),可以分配以下角色以授予對實驗室帳戶和教室實驗室的存取許可權:

- **實驗室帳戶擁有者**

    建立實驗室帳戶的管理員將自動新增到實驗室**帳戶的擁有者角色**。  設定**擁有者角色的管理員**可以:
     - 更改實驗室帳戶的設置。
     - 授予其他管理員作為擁有者或貢獻者對實驗室帳戶的訪問許可權。 
     - 使教育工作者能夠作為建立者、擁有者或貢獻者訪問教室實驗室。
     - 在實驗室帳戶中創建和管理所有教室實驗室。

- **實驗室帳戶參與者**

    配置了 **「參與者」** 角色的管理員可以:
    - 更改實驗室帳戶的設置。
    - 創建和管理實驗室帳戶中的所有教室實驗室。
    
    但是,*它們不能*允許其他使用者訪問實驗室帳戶或教室實驗室。

- **教室教室建立者**

    要在實驗室帳戶中創建教室實驗室,教育者必須是**實驗室建立者**角色的成員。  當教師創建教室實驗室時,他們將自動添加為實驗室的擁有者。  請參閱有關如何[將使用者添加到**實驗室建立者**角色的](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role)教程。 

- **課堂實驗室擁有者\貢獻者**
  
    當教室實驗室是實驗室**的擁有者**或**參與者**角色的成員時,教師可以查看和更改教室實驗室的設置;他們還必須是實驗室帳戶的**讀取器**角色的成員。

    實驗室**的所有者**角色和**參與者**角色之間的一個關鍵區別是,參與者*無法*授予其他使用者管理實驗室的訪問許可權 - 只有擁有者才能授予其他使用者管理實驗室的訪問許可權。
    
    此外,教育者*不能*創建新的教室實驗室,除非他們也是**實驗室建立者**角色的成員。

- **共用影像庫**
    
    將共用圖像庫附加到實驗室帳戶時,實驗室帳戶擁有者、參與者和實驗室建立者\擁有者\貢獻者將自動獲得在庫中查看和保存圖像的訪問許可權。 

以下是一些説明分配角色的提示:
   - 通常,只有管理員應該是實驗室帳戶**的擁有者**或**參與者**角色的成員;您可能有多個擁有者\貢獻者。

   - 使教育工作者能夠創建新的課堂實驗室並管理他們創建的實驗室;您只需分配對**實驗室建立者**角色的訪問許可權。
   
   - 使教育者能夠管理特定的課堂實驗室,*但不能*使教師能夠創建新的實驗室;您應該為他們將管理的每個教室實驗室分配對 **「擁有者**」或 **「參與者」** 角色的訪問。  例如,您可能希望允許教授和助教共同擁有教室實驗室。  請參閱有關如何[將用戶作為所有者添加到教室實驗室的](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-add-user-lab-owner)指南。

## <a name="pricing"></a>定價

### <a name="azure-lab-services"></a>Azure 實驗室服務
Azure 實驗室服務的定價在以下文章中介紹[:Azure 實驗室服務定價](https://azure.microsoft.com/pricing/details/lab-services/)。

如果您計劃使用它存儲和管理映射版本,則還需要考慮共用映射庫的定價。 

### <a name="shared-image-gallery"></a>共用影像庫
創建共用圖像庫並將其附加到實驗室帳戶是免費的。 在將圖像版本保存到庫中之前,不會產生成本。 通常,使用共用映射庫的定價可以忽略不計,但瞭解如何計算它非常重要,因為它未包含在 Azure 實驗室服務的定價中。  

#### <a name="storage-charges"></a>存儲費用
要儲存映射版本,共用映射庫使用標準 HDD 管理的磁碟。 使用的 HDD 託管磁碟的大小取決於要儲存的圖像版本的大小。 請參考以下文章以檢視定價:[託管磁碟定價](https://azure.microsoft.com/pricing/details/managed-disks/)。


#### <a name="replication-and-network-egress-charges"></a>複製和網路出口費用
使用教室實驗室的範本虛擬機器 (VM) 儲存映像版本時,Azure 實驗室服務首先將其儲存在來源區域中,然後自動將源映像版本複製到一個或多個目標區域。 請務必注意,Azure 實驗室服務會自動將源映像版本複製到教室實驗室所在的[地理位置中的所有目標區域](https://azure.microsoft.com/global-infrastructure/regions/)。 例如,如果您的教室實驗室位於美國地理中,則圖像版本將複製到美國境內存在的八個區域中的每個區域。

當圖像版本從源區域複製到其他目標區域時,將發生網路出口費用。 所收取的金額基於圖像數據最初從源區域向外傳輸時的圖像版本的大小。  有關定價詳細資訊,請參閱以下文章:[頻寬定價詳細資訊](https://azure.microsoft.com/pricing/details/bandwidth/)。

[教育解決方案](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3)客戶可以免除支付出口費用。 與您的客戶經理交談以瞭解更多資訊。  有關詳細資訊,請參閱鏈接文檔中的**常見問題**部分,特別是"學術客戶存在哪些數據傳輸計劃以及如何獲得資格?"

#### <a name="pricing-example"></a>定價範例
為了回顧上述定價,讓我們看一下將範本 VM 映射保存到共用映射庫的示例。 假設以下專案:

- 您有一個自定義 VM 映射。
- 您保存映像的兩個版本。
- 您的實驗室位於美國,共有八個區域。
- 每個映射版本的大小為 32 GB;因此,硬碟管理的磁碟價格為每月 1.54 美元。

總成本估計為:

映像數 = 版本數 + 複本數 = 託管磁碟價格

此範例中,成本為:

1 個自訂映像 (32 GB) x 2 個版本 x 8 個美國區域 x $1.54 = 每月 24.64 美元

#### <a name="cost-management"></a>成本管理
對於實驗室帳戶管理員來說,通過定期從庫中刪除不需要的圖像版本來管理成本非常重要。 

不應刪除對特定區域的複製,以降低成本(此選項存在於共用映射庫中)。 複製更改可能會對 Azure 實驗室服務從保存在共用映射庫中的圖像發佈 VM 的能力產生不利影響。

## <a name="next-steps"></a>後續步驟
有關創建實驗室帳戶和實驗室的分步說明,請參閱本教程:[設置指南](tutorial-setup-lab-account.md)
