---
title: 規劃您的 Avere vFXT 系統 - Azure
description: 規劃最適合您需求的 Avere vFXT for Azure 叢集。 在進入 Azure Marketplace 或建立虛擬機器之前，請先瞭解問題。
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: 9f852ec056f3252005bba0bc142c05a9cfbe6c20
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342394"
---
# <a name="plan-your-avere-vfxt-system"></a>規劃您的 Avere vFXT 系統

本文說明如何規劃新的 Avere vFXT for Azure 叢集，並根據您的需求適當地調整其大小。

前往 Azure Marketplace 或建立任何 Vm 之前，請考慮下列詳細資料：

* 叢集將如何與其他 Azure 資源互動？
* 叢集元素應該位於私人網路和子網中的何處？
* 您將使用哪種類型的後端儲存體，以及叢集存取它的方式為何？
* 您的叢集節點需要哪些功能才能支援您的工作流程？

繼續閱讀以深入了解。

## <a name="learn-the-components-of-the-system"></a>瞭解系統的元件

當您開始規劃時，瞭解 Avere vFXT for Azure 系統的元件可能會很有説明。

* 叢集節點-叢集是由設定為叢集節點的三部或多部 Vm 所組成。 更多節點提供系統更高的輸送量和較大的快取。

* 快取-快取容量會在叢集節點之間平均分配。 當您建立叢集時，請設定每個節點的快取大小;加入的節點大小會變成總快取大小。

* 叢集控制器-叢集控制器是與叢集節點位於相同子網內的額外 VM。 需要控制器才能建立叢集和進行中的管理工作。

* 後端儲存體-您想要快取的資料會長期儲存在硬體儲存體系統或 Azure Blob 容器中。 您可以在建立 Avere vFXT for Azure 叢集之後新增存放裝置，或者，如果使用 Blob 儲存體，您可以在建立叢集時新增及設定容器。

* 用戶端-使用快取檔案的用戶端電腦會使用虛擬檔案路徑連接到叢集，而不是直接存取儲存體系統。  (詳細資訊，請參閱 [裝載 Avere vFXT](avere-vfxt-mount-clients.md)叢集。 ) 

## <a name="subscription-resource-group-and-network-infrastructure"></a>訂用帳戶、資源群組和網路基礎結構

請考慮部署 Avere vFXT for Azure 元素的位置。 下圖顯示 Avere vFXT for Azure 元件可能的排列方式：

![本圖顯示一個子網路內的叢集控制器和叢集 VM。 子網路界限周圍是 Vnet 界限。 Vnet 內是一個代表儲存體服務端點的六邊形；它會以虛線箭號連線至 Vnet 外部的 Blob 儲存體。](media/avere-vfxt-components-option.png)

規劃 Avere vFXT 叢集的網路基礎結構時，請遵循下列指導方針：

* 針對每個 Avere vFXT for Azure 部署建立新的訂用帳戶。 管理此訂用帳戶中的所有元件。

  針對每個部署使用新訂用帳戶的優點包括：
  * 追蹤成本更簡單 - 在一個訂用帳戶中檢視和稽核所有資源、基礎結構及計算循環的成本。
  * 清除更容易 - 您可以在完成專案後移除整個訂用帳戶。
  * 方便分割資源配額-在單一訂用帳戶中隔離 Avere vFXT 用戶端和叢集，以防止其他重要工作負載進行可能的資源節流。 這種分隔可在為高效能運算工作流程提供大量用戶端時，避免發生衝突。

* 找出接近 vFXT 叢集的用戶端計算系統。 後端儲存體可能更加遙遠。  

* 同時找出 vFXT 叢集和叢集控制器 VM，特別是：

  * 在相同的虛擬網路中
  * 在相同的資源群組中
  * 使用相同的儲存體帳戶
  
  叢集建立範本會在大部分情況下處理這種設定。

* 叢集必須位於自己的子網中，以避免 IP 位址與用戶端或其他計算資源發生衝突。

* 使用叢集建立範本來建立叢集所需的大部分基礎結構資源，包括資源群組、虛擬網路、子網和儲存體帳戶。

  如果您想要使用已經存在的資源，請確定其符合此表中的需求。

  | 資源 | 使用現有的？ | 需求 |
  |----------|-----------|----------|
  | 資源群組 | 是，如果是空的 | 必須是空的|
  | 儲存體帳戶 | **是** ，如果在建立叢集之後連線到現有的 Blob 容器 <br/>  如果在叢集建立期間建立新的 Blob 容器，則為**否** | 現有的 Blob 容器必須為空白 <br/> &nbsp; |
  | 虛擬網路 | 是 | 如果建立新的 Azure Blob 容器，則必須包含儲存體服務端點 |
  | 子網路 | 是 | 不可包含其他資源 |

## <a name="ip-address-requirements"></a>IP 位址需求

請確定您叢集子網路的 IP 位址範圍夠大，足以支援叢集。

Avere vFXT 叢集會使用下列 IP 位址：

* 一個叢集管理 IP 位址。 此位址可以視需要從節點移至叢集中的節點，以便永遠可供使用。 使用此位址連接到 Avere 主控台設定工具。
* 對於每個叢集節點：
  * 至少有一個面向用戶端的 IP 位址   (所有面向用戶端的位址都是由叢集的 *vserver*來管理，視需要在節點之間移動 IP 位址。 ) 
  * 一個用於叢集通訊的 IP 位址
  * 一個執行個體 IP 位址 (指派給 VM)

如果您使用 Azure Blob 儲存體，它也可能需要來自您叢集虛擬網路的 IP 位址：  

* Azure Blob 儲存體帳戶必須至少有五個 IP 位址。 如果您在與叢集相同的虛擬網路中找到 Blob 儲存體，請記住這項需求。
* 如果您使用叢集虛擬網路外的 Azure Blob 儲存體，請在虛擬網路內建立儲存體服務端點。 端點不會使用 IP 位址。

您可以選擇在叢集的不同資源群組中，找出網路資源和 Blob 儲存體 (如果使用的話)。

## <a name="vfxt-node-size"></a>vFXT 節點大小

當作叢集節點的 VM 可決定您快取的要求輸送量和儲存體容量。 <!-- The instance type offered has been chosen for its memory, processor, and local storage characteristics. You can choose from two instance types, with different memory, processor, and local storage characteristics. -->

每個 vFXT 節點都將相同。 也就是說，如果您建立一個三節點的叢集，您將會有三個相同類型和大小的 VM。

| 執行個體類型 | vCPU | 記憶體  | 本機 SSD 儲存體  | 最大資料磁碟 | 取消快取的磁碟輸送量 | NIC (計數) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_E32s_v3 | 32  | 256 GiB | 512 GiB  | 32 | 51,200 IOPS <br/> 768 MBps | 16,000 MBps (8)  |

每個節點的磁碟快取都可以設定，而且範圍可以從 1000 GB 到 8000 GB。 針對 Standard_E32s_v3 節點，建議使用的快取大小為每個節點 4 TB。

如需這些 Vm 的詳細資訊，請參閱 Microsoft Azure 檔： [記憶體優化的虛擬機器大小](../virtual-machines/sizes-memory.md)

## <a name="account-quota"></a>帳戶配額

請確定您的訂用帳戶具有執行 Avere vFXT 叢集，以及所使用之任何計算或用戶端系統的容量。 如需詳細資訊，請閱讀 [vFXT 叢集配額](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)。

## <a name="back-end-data-storage"></a>後端資料儲存體

後端儲存體系統會將檔案提供給叢集的快取，也會從快取接收變更的資料。 決定您的工作集將長期儲存在新的 Blob 容器中，還是儲存在現有的儲存體系統中， (雲端或硬體) 。 這些後端儲存體系統稱為 *核心檔*系統。

### <a name="hardware-core-filers"></a>硬體核心檔

在建立叢集之後，將硬體存放裝置系統新增至 vFXT 叢集。 您可以使用各種常見的硬體系統，包括內部部署系統，只要可以從叢集的子網連線到存放裝置系統即可。

如需有關如何將現有的儲存體系統新增至 Avere vFXT 叢集的詳細指示，請閱讀[設定儲存體](avere-vfxt-add-storage.md)。

### <a name="cloud-core-filers"></a>雲端核心檔

Avere vFXT for Azure 系統可以使用空的 Blob 容器作為後端儲存體。 新增至叢集時，容器必須為空白-vFXT 系統必須能夠管理其物件存放區，而不需要保留現有的資料。

> [!TIP]
> 如果您想要使用 Azure Blob 儲存體作為後端，請在建立 vFXT 叢集時建立新的容器。 叢集建立範本可以建立及設定新的 Blob 容器，使其可以在叢集可供使用時立即使用。 稍後新增容器會更複雜。
>
> 請參閱[建立 Avere vFXT for Azure](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure)，以了解詳細資料。

將空的 Blob 儲存體容器新增為核心檔案管理工具之後，您可以透過叢集將資料複製到其中。 使用平行、多執行緒的複製機制。 請閱讀[將資料移動到 vFXT 叢集](avere-vfxt-data-ingest.md)以了解如何使用用戶端電腦和 Avere vFXT 快取，將資料有效率地複製到叢集的新容器中。

## <a name="cluster-access"></a>叢集存取

Avere vFXT for Azure 位於私人子網路，叢集並沒有公用 IP 位址。 您必須有一些方法可以存取私人子網，以進行叢集管理和用戶端連線。

存取選項包括：

* 跳躍主機-將公用 IP 位址指派給私人網路內的個別 VM，並使用它來建立叢集節點的 TLS 通道。

  > [!TIP]
  > 如果您在叢集控制器上設定公用 IP 位址，則此控制器可作為跳板主機。 如需詳細資訊，請參閱[作為跳板主機的叢集控制器](#cluster-controller-as-jump-host)。

* 虛擬私人網路 (VPN) -在 Azure 與公司網路中的私人網路之間設定點對站或站對站 VPN。

* Azure ExpressRoute - 透過 ExpressRoute 合作夥伴設定私人連線。

如需有關這些選項的詳細資訊，請參閱[關於網際網路通訊的 Azure 虛擬網路文件](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet)。

### <a name="cluster-controller-as-jump-host"></a>作為跳板主機的叢集控制器

如果您在叢集控制器上設定公用 IP 位址，您可以將其作為跳板主機，從私人子網路外連絡 Avere vFXT 叢集。 不過，由於控制器有修改叢集節點的存取權限，因此會產生輕微的安全性風險。

為了改善具有公用 IP 位址之控制器的安全性，部署腳本會自動建立網路安全性群組，以限制僅限埠22的輸入存取。 若要進一步保護系統，您可以鎖定 IP 來源位址範圍的存取，也就是只允許從要用來存取叢集的機器進行連線。

建立叢集時，您可以選擇是否要在叢集控制器上建立公用 IP 位址。

* 如果您建立 **新的虛擬網路** 或 **新的子網**，將會指派 **公用** IP 位址給叢集控制器。
* 如果您選取現有的虛擬網路和子網，則叢集控制器將只會有 **私人** IP 位址。

## <a name="vm-access-roles"></a>VM 存取角色

Azure 會使用 [azure 角色型存取控制 (AZURE RBAC) ](../role-based-access-control/index.yml) 來授權叢集 vm 執行某些工作。 例如，叢集控制器需要授權，才能建立及設定叢集節點 Vm。 叢集節點必須能夠將 IP 位址指派或重新指派給其他叢集節點。

Avere vFXT 的虛擬機器會使用兩個內建的 Azure 角色：

* 叢集控制器會使用內建的角色 [Avere 參與者](../role-based-access-control/built-in-roles.md#avere-contributor)。
* 叢集節點使用內建角色 [Avere 操作員](../role-based-access-control/built-in-roles.md#avere-operator)。

如果您需要自訂 Avere vFXT 元件的存取角色，您必須定義自己的角色，然後在建立時將其指派給 Vm。 您無法使用 Azure Marketplace 中的部署範本。 在 Azure 入口網站中開啟票證以查看 Microsoft 客戶服務及支援，如「 [取得系統協助](avere-vfxt-open-ticket.md)」中所述。

## <a name="next-steps"></a>後續步驟

[部署總覽](avere-vfxt-deploy-overview.md) 提供建立 Avere vFXT for Azure 系統，並讓它準備好提供資料所需之步驟的大圖片。