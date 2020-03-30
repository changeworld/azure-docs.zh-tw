---
title: 規劃您的 Avere vFXT 系統 - Azure
description: 說明在部署Avere vFXT for Azure 之前的規劃事項
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: cd0c74c8aa40b3e96716ef37aa27b08b5f6aece1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76547535"
---
# <a name="plan-your-avere-vfxt-system"></a>規劃您的 Avere vFXT 系統

本文介紹如何為 Azure 群集規劃新的 Avere vFXT，該群集根據您的需要定位和調整大小。

在去 Azure 應用商店或創建任何 VM 之前，請考慮以下詳細資訊：

* 群集將如何與其他 Azure 資源交互？
* 群集元素應位於私人網路絡和子網中的位置？
* 您將使用哪種類型的後端存儲，群集將如何訪問它？
* 叢集節點需要多大的強大功能來支援您的工作流？

繼續閱讀以深入了解。

## <a name="learn-the-components-of-the-system"></a>瞭解系統的元件

在開始規劃時，瞭解 Azure 系統的 Avere vFXT 的元件會很有説明。

* 叢集節點 - 群集由配置為叢集節點的三個或多個 VM 組成。 節點越多，系統輸送量就越高，緩存也更大。

* 緩存 - 緩存容量在叢集節點之間平均分配。 創建群集時設置每個節點的緩存大小;節點大小將添加為總緩存大小。

* 叢集控制器 - 叢集控制器是位於與叢集節點相同的子網內的其他 VM。 創建群集和持續管理工作需要控制器。

* 後端存儲 - 要緩存的資料長期存儲在硬體存儲系統或 Azure Blob 容器中。 可以為 Azure 群集創建 Avere vFXT 後添加存儲，或者通過使用 Blob 存儲可以在創建群集時添加和配置容器。

* 用戶端 - 使用快取檔案的用戶端電腦使用虛擬檔案路徑連接到群集，而不是直接存取存儲系統。 （在["Avere vFXT"群集中](avere-vfxt-mount-clients.md)閱讀更多內容。

## <a name="subscription-resource-group-and-network-infrastructure"></a>訂閱、資源組和網路基礎結構

請考慮部署 Avere vFXT for Azure 元素的位置。 下圖顯示 Avere vFXT for Azure 元件可能的排列方式：

![本圖顯示一個子網路內的叢集控制器和叢集 VM。 子網路界限周圍是 Vnet 界限。 Vnet 內是一個代表儲存體服務端點的六邊形；它會以虛線箭號連線至 Vnet 外部的 Blob 儲存體。](media/avere-vfxt-components-option.png)

規劃 Avere vFXT 群集的網路基礎結構時，請遵循以下準則：

* 為每個 Avere vFXT 創建用於 Azure 部署的新訂閱。 管理此訂閱中的所有元件。

  為每個部署使用新訂閱的好處包括：
  * 追蹤成本更簡單 - 在一個訂用帳戶中檢視和稽核所有資源、基礎結構及計算循環的成本。
  * 清除更容易 - 您可以在完成專案後移除整個訂用帳戶。
  * 方便劃分資源配額 - 將 Avere vFXT 用戶端和群集隔離到單個訂閱中，以保護其他關鍵工作負載免受可能的資源限制。 這種分離可防止在為高性能計算工作流中引入大量用戶端時發生衝突。

* 找出接近 vFXT 叢集的用戶端計算系統。 後端儲存體可能更加遙遠。  

* 一起查找 vFXT 群集和叢集控制器 VM - 具體來說，它們應該是：

  * 在同一虛擬網路中
  * 在同一資源組中
  * 使用相同的存儲帳戶
  
  在大多數情況下，群集創建範本處理此配置。

* 群集必須位於其自己的子網中，以避免與用戶端或其他計算資源發生 IP 位址衝突。

* 使用群集創建範本為群集創建所需的大多數基礎結構資源，包括資源組、虛擬網路、子網和存儲帳戶。

  如果要使用已經存在的資源，請確保它們滿足此表中的要求。

  | 資源 | 使用現有？ | 需求 |
  |----------|-----------|----------|
  | 資源群組 | 是，如果為空 | 必須為空|
  | 儲存體帳戶 | 如果群集創建後連接現有 Blob 容器，**則為** <br/>  如果在群集創建期間創建新 Blob 容器，**則否** | 現有 Blob 容器必須為空 <br/> &nbsp; |
  | 虛擬網路 | 是 | 如果創建新的 Azure Blob 容器，則必須包括存儲服務終結點 |
  | 子網路 | 是 | 無法包含其他資源 |

## <a name="ip-address-requirements"></a>IP 位址需求

請確定您叢集子網路的 IP 位址範圍夠大，足以支援叢集。

Avere vFXT 叢集會使用下列 IP 位址：

* 一個叢集管理 IP 位址。 此位址可以根據需要從群集中的節點移動到一個節點，以便始終可用。 使用此位址可連接到 Avere 控制台組態工具。
* 對於每個叢集節點：
  * 至少有一個面向用戶端的 IP 位址  （所有面向用戶端的位址都由群集的*vserver*管理，它可以根據需要在節點之間移動 IP 位址。
  * 一個用於叢集通訊的 IP 位址
  * 一個執行個體 IP 位址 (指派給 VM)

如果使用 Azure Blob 存儲，它可能還需要群集虛擬網路中的 IP 位址：  

* Azure Blob 儲存體帳戶必須至少有五個 IP 位址。 如果找到與群集相同的虛擬網路中的 Blob 存儲，請記住此要求。
* 如果使用群集虛擬網路外的 Azure Blob 存儲，請在虛擬網路內創建存儲服務終結點。 終結點不使用 IP 位址。

您可以選擇在叢集的不同資源群組中，找出網路資源和 Blob 儲存體 (如果使用的話)。

## <a name="vfxt-node-size"></a>vFXT 節點大小

當作叢集節點的 VM 可決定您快取的要求輸送量和儲存體容量。 <!-- The instance type offered has been chosen for its memory, processor, and local storage characteristics. You can choose from two instance types, with different memory, processor, and local storage characteristics. -->

每個 vFXT 節點都將相同。 也就是說，如果您建立一個三節點的叢集，您將會有三個相同類型和大小的 VM。

| 執行個體類型 | vCPU | 記憶體  | 本機 SSD 儲存體  | 最大資料磁碟 | 取消快取的磁碟輸送量 | NIC (計數) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_E32s_v3 | 32  | 256 GiB | 512 GB  | 32 | 51,200 IOPS <br/> 768 MBps | 16,000 MBps (8)  |

每個節點的磁碟快取都可以設定，而且範圍可以從 1000 GB 到 8000 GB。 每個節點 4 TB 是 Standard_E32s_v3節點的建議緩存大小。

有關這些 VM 的其他資訊，請閱讀 Microsoft Azure 文檔：[記憶體優化的虛擬機器大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)

## <a name="account-quota"></a>帳戶配額

請確定您的訂用帳戶具有執行 Avere vFXT 叢集，以及所使用之任何計算或用戶端系統的容量。 如需詳細資訊，請閱讀 [vFXT 叢集配額](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)。

## <a name="back-end-data-storage"></a>後端資料儲存體

後端存儲系統既向群集的緩存提供檔，也從緩存接收更改的資料。 決定您的工作集是長期存儲在新的 Blob 容器中還是存儲在現有存儲系統（雲或硬體）中。 這些後端存儲系統稱為*核心檔案伺服器*。

### <a name="hardware-core-filers"></a>硬體核心檔案伺服器

創建群集後，將硬體存儲系統添加到 vFXT 群集。 只要可以從群集的子網到達存儲系統，就可以使用各種流行的硬體系統，包括本地系統。

如需有關如何將現有的儲存體系統新增至 Avere vFXT 叢集的詳細指示，請閱讀[設定儲存體](avere-vfxt-add-storage.md)。

### <a name="cloud-core-filers"></a>雲核心檔案伺服器

Azure 系統的 Avere vFXT 可以使用空 Blob 容器進行後端存儲。 容器在添加到群集時必須為空 - vFXT 系統必須能夠管理其物件存儲，而無需保留現有資料。

> [!TIP]
> 如果要對後端使用 Azure Blob 存儲，請創建一個新容器作為創建 vFXT 群集的一部分。 群集創建範本可以創建和配置新的 Blob 容器，以便在群集可用後立即可以使用該容器。 以後添加容器會更加複雜。
>
> 請參閱[建立 Avere vFXT for Azure](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure)，以了解詳細資料。

將空 Blob 存儲容器添加為核心檔程式後，可以通過群集將資料複製到該容器。 使用並行的多執行緒複製機制。 請閱讀[將資料移動到 vFXT 叢集](avere-vfxt-data-ingest.md)以了解如何使用用戶端電腦和 Avere vFXT 快取，將資料有效率地複製到叢集的新容器中。

## <a name="cluster-access"></a>叢集存取

Avere vFXT for Azure 位於私人子網路，叢集並沒有公用 IP 位址。 您必須有某種方法來訪問群集管理和用戶端連接的專用子網。

存取選項包括：

* 跳板主機 - 將公用 IP 位址指派給私人網路中不同的 VM，並使用此位址建立 SSL 通道來連線到叢集節點。

  > [!TIP]
  > 如果您在叢集控制器上設定公用 IP 位址，則此控制器可作為跳板主機。 如需詳細資訊，請參閱[作為跳板主機的叢集控制器](#cluster-controller-as-jump-host)。

* 虛擬私人網路絡 （VPN） - 在 Azure 中的私人網路絡和商業網路之間配置點對點或網站到網站 VPN。

* Azure ExpressRoute - 透過 ExpressRoute 合作夥伴設定私人連線。

如需有關這些選項的詳細資訊，請參閱[關於網際網路通訊的 Azure 虛擬網路文件](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet)。

### <a name="cluster-controller-as-jump-host"></a>作為跳板主機的叢集控制器

如果您在叢集控制器上設定公用 IP 位址，您可以將其作為跳板主機，從私人子網路外連絡 Avere vFXT 叢集。 不過，由於控制器有修改叢集節點的存取權限，因此會產生輕微的安全性風險。

為了提高具有公共 IP 位址的控制器的安全性，部署腳本會自動創建一個網路安全性群組，該組僅限制對埠 22 的入站訪問。 若要進一步保護系統，您可以鎖定 IP 來源位址範圍的存取，也就是只允許從要用來存取叢集的機器進行連線。

建立叢集時，您可以選擇是否要在叢集控制器上建立公用 IP 位址。

* 如果創建新**的虛擬網路**或**新子網**，將為叢集控制器分配**公共**IP 位址。
* 如果選擇現有的虛擬網路和子網，叢集控制器將僅具有**專用**IP 位址。

## <a name="vm-access-roles"></a>VM 訪問角色

Azure 使用[基於角色的存取控制](../role-based-access-control/index.yml)（RBAC） 授權群集 VM 執行某些任務。 例如，叢集控制器需要授權才能創建和配置叢集節點 VM。 叢集節點需要能夠將 IP 位址分配給其他叢集節點或重新分配 IP 位址。

Avere vFXT 虛擬機器使用兩個內置 Azure 角色：

* 叢集控制器使用內置角色[Avere 參與者](../role-based-access-control/built-in-roles.md#avere-contributor)。
* 叢集節點使用內置角色[Avere 運算子](../role-based-access-control/built-in-roles.md#avere-operator)。

如果需要為 Avere vFXT 元件自訂訪問角色，則必須定義自己的角色，然後在創建這些角色時將其分配給 VM。 不能在 Azure 應用商店中使用部署範本。 通過在 Azure 門戶中打開票證，請參閱 Microsoft 客戶服務和支援，如[獲取系統説明](avere-vfxt-open-ticket.md)中所述。

## <a name="next-steps"></a>後續步驟

[部署概述](avere-vfxt-deploy-overview.md)提供了為 Azure 系統創建 Avere vFXT 並使其準備好提供資料所需的步驟的全域視圖。
