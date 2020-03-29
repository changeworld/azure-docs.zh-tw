---
title: SAP 應用程式的 Azure 接近放置組 |微軟文檔
description: 使用 Azure 鄰近放置組描述 SAP 部署方案
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/17/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 01ce1599f86082aef3ff53d298cc53896074af66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277603"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>Azure 接近放置組，實現 SAP 應用程式的最佳網路延遲
基於 SAP NetWeaver 或 SAP S/4HANA 體系結構的 SAP 應用程式對 SAP 應用程式層和 SAP 資料庫層之間的網路延遲非常敏感。 這種敏感性是應用程式層中運行的大多數業務邏輯的結果。 由於 SAP 應用程式層運行業務邏輯，因此以每秒數千或數萬的速率以高頻率向資料庫層發出查詢。 在大多數情況下，這些查詢的性質很簡單。 它們通常可以在 500 微秒或更短秒內在資料庫層上運行。

在網路上花費的時間將此類查詢從應用程式層發送到資料庫層並接收結果集返回的時間對運行業務流程所需的時間有重大影響。 這種對網路延遲的敏感度是您需要在 SAP 部署專案中實現最佳網路延遲的原因。 有關如何對網路延遲進行分類的指南，請參閱[SAP 注釋#1100926 - 常見問題解答：網路性能](https://launchpad.support.sap.com/#/notes/1100926/E)。

在許多 Azure 區域中，資料中心的數量有所增加。 這種增長也是由可用性區域的引入引起的。 同時，客戶（尤其是高端 SAP 系統）的客戶在 M 系列系列（HANA 大型實例）中使用更多特殊的 VM SKU。 這些 Azure 虛擬機器類型在特定 Azure 區域中的所有資料中心中不可用。 由於這兩種趨勢，客戶經歷了網路延遲，這些延遲不在最佳範圍內。 在某些情況下，這種延遲會導致其 SAP 系統的性能不理想。

為了防止這些問題，Azure 提供[鄰近放置組](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)。 此新功能已用於部署各種 SAP 系統。 有關鄰近放置組的限制，請參閱本段開頭引用的文章。 本文介紹可以或應該使用 Azure 鄰近放置組的 SAP 方案。

## <a name="what-are-proximity-placement-groups"></a>什麼是鄰近放置組？ 
Azure 鄰近放置組是邏輯構造。 定義一個區域時，它將綁定到 Azure 區域和 Azure 資源組。 部署 VM 時，鄰近放置組由以下方式引用：

- 部署在資料中心中的第一個 Azure VM。 您可以將第一個虛擬機器視為"範圍 VM"，該虛擬機器基於 Azure 分配演算法部署在資料中心中，最終與特定可用性區域的使用者定義相結合。
- 部署的所有後續 VM 都引用鄰近放置組，以便將所有隨後部署的 Azure VM 放在第一個虛擬機器的同一資料中心中。

> [!NOTE]
> 如果沒有部署能夠在第一個 VM 所在的資料中心運行特定 VM 類型的主機硬體，則請求的 VM 類型的部署不會成功。 你會得到一條失敗的消息。

單個[Azure 資源組](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal)可以為其分配多個鄰近位置組。 但是，只能將鄰近放置組分配給一個 Azure 資源組。

使用鄰近放置組時，請記住以下注意事項：

- 當您旨在為 SAP 系統實現最佳性能，並使用接近放置組將自己限制為系統的單個 Azure 資料中心時，可能無法在放置組中組合所有類型的 VM 系列。 出現這些限制的原因是，在部署放置組的"作用域 VM"的資料中心中可能不存在運行特定 VM 類型的主機硬體。
- 在這種 SAP 系統的生命週期中，您可能被迫將系統移動到另一個資料中心。 例如，如果您決定橫向擴展 HANA DBMS 層應從 4 個節點移動到 16 個節點，並且沒有足夠的容量來獲取資料中心中使用的 12 個 VM，則可能需要此移動。
- 由於硬體的停用，Microsoft 可能會為您在其他資料中心使用的 VM 類型（而不是最初使用的資料中心）建立容量。 在這種情況下，您可能需要將所有鄰近放置組的 VM 移動到另一個資料中心。

## <a name="proximity-placement-groups-with-sap-systems-that-use-only-azure-vms"></a>具有僅使用 Azure VM 的 SAP 系統的鄰近放置組
Azure 上的大多數 SAP NetWeaver 和 S/4HANA 系統部署不使用[HANA 大型實例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)。 對於不使用 HANA 大型實例的部署，在 SAP 應用程式層和 DBMS 層之間提供最佳性能非常重要。 為此，請只為系統定義 Azure 接近放置組。

在大多數客戶部署中，客戶為 SAP 系統構建單個[Azure 資源組](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal)。 在這種情況下，生產 ERP 系統資源組與其鄰近放置組之間存在一對一關聯性。 在其他情況下，客戶水準組織其資源組，並在單個資源組中收集所有生產系統。 在這種情況下，生產 SAP 系統的資源組與生產 SAP ERP、SAP BW 等的幾個鄰近放置組之間存在一對多關聯性。

避免將多個 SAP 生產或非生產系統捆綁在單個接近放置組中。 當少量 SAP 系統或 SAP 系統以及一些周圍應用程式需要進行低延遲網路通信時，可以考慮將這些系統移動到一個接近放置組中。 您應該避免捆綁系統，因為在接近放置組中分組的系統越多，機會就越高：

- 您需要無法在鄰近放置組限定到的特定資料中心中運行的 VM 類型。
- 非主流 VM（如 M 系列 VM）的資源最終可能得不到滿足，因為隨著時間的推移，您會將軟體添加到接近放置組。

如下所述，理想的配置如下所示：

![僅具有 Azure VM 的鄰近放置組](./media/sap-proximity-placement-scenarios/ppg-for-all-azure-vms.png)

在這種情況下，單個 SAP 系統分別分組到一個資源組中，每個資源組各有一個鄰近放置組。 您使用 HANA 橫向擴展配置還是 DBMS 擴展配置，沒有相關性。

## <a name="proximity-placement-groups-and-hana-large-instances"></a>鄰近放置組和 HANA 大型實例
如果某些 SAP 系統依賴于適用于應用程式層[的 HANA 大型實例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)，則當您使用在[修訂版 4 行或圖章](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)中部署的 HANA 大型實例單元時，HANA 大型實例單元和 Azure VM 之間的網路延遲可能會顯著改善。 一個改進是，HANA 大型實例單元在部署時，使用接近放置組進行部署。 您可以使用該鄰近放置組來部署應用程式層 VM。 因此，這些 VM 將部署在承載 HANA 大型實例單元的同一資料中心中。

要確定 HANA 大型實例單元是否部署在修訂版 4 戳或行中，請[選中"Azure HANA 大型實例"通過 Azure 門戶進行控制](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#look-at-attributes-of-single-hli-unit)的文章。 在 HANA 大型實例單元的屬性概述中，還可以確定鄰近放置組的名稱，因為它是在部署 HANA 大型實例單元時創建的。 屬性概述中顯示的名稱是應將應用程式層 VM 部署到的鄰近放置組的名稱。

與僅使用 Azure 虛擬機器的 SAP 系統相比，在使用 HANA 大型實例時，在決定要使用的[Azure 資源組](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal)數量時的靈活性較低。 [HANA 大型實例租戶](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-know-terms)的所有 HANA 大型實例單元都分組到單個資源組中，如[本文](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#display-of-hana-large-instance-units-in-the-azure-portal)所述。 除非您部署到不同的租戶以分離生產和非生產系統或其他系統，否則所有 HANA 大型實例單元都將部署在一個 HANA 大型實例租戶中。 此租戶與資源組具有一對一關聯性。 但是，將為每個單個單元定義單獨的接近放置組。

因此，Azure 資源組和單個租戶的鄰近放置組之間的關係如下所示：

![鄰近放置組和 HANA 大型實例](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)

## <a name="example-of-deployment-with-proximity-placement-groups"></a>具有鄰近放置組的部署示例
以下是一些 PowerShell 命令，可用於使用 Azure 鄰近放置組部署 VM。

登錄到[Azure 雲行殼](https://azure.microsoft.com/features/cloud-shell/)後，第一步是檢查您是否在要用於部署的 Azure 訂閱中：

<pre><code>
Get-AzureRmContext
</code></pre>

如果需要更改為其他訂閱，可以通過運行此命令來執行此操作：

<pre><code>
Set-AzureRmContext -Subscription "my PPG test subscription"
</code></pre>

通過運行此命令創建新的 Azure 資源組：

<pre><code>
New-AzResourceGroup -Name "myfirstppgexercise" -Location "westus2"
</code></pre>

通過運行此命令創建新的鄰近放置組：

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose" -Location "westus2"
</code></pre>

使用如下所示的命令將第一個 VM 部署到接近放置組中：

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

前面的命令部署基於 Windows 的 VM。 此 VM 部署成功後，在 Azure 區域中定義鄰近放置組的資料中心範圍。 引用鄰近放置組的所有後續 VM 部署（如前一命令所示）都將部署在同一 Azure 資料中心中，只要 VM 類型可以託管在放置在該資料中心的硬體上，並且該 VM 類型的容量為可用。

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>將可用性集和可用性區域與鄰近放置組相結合
對 SAP 系統部署使用可用性區域的缺點之一是，無法使用特定區域內的可用性集來部署 SAP 應用程式層。 您希望 SAP 應用程式層部署在同一區域中的 DBMS 層。 不支援在部署單個 VM 時引用可用性區域和可用性集。 因此，以前，您被迫通過引用區域來部署應用程式層。 您失去了確保應用程式層 VM 分佈在不同更新和失敗域的能力。

通過使用接近放置組，可以繞過此限制。 下面是部署順序：

- 創建接近放置組。
- 通過引用可用性區域來部署錨定 VM（通常是 DBMS 伺服器）。
- 創建引用 Azure 鄰近組的可用性集。 （請參閱本文後面的命令。
- 通過引用可用性集和鄰近放置組來部署應用程式層 VM。

部署 VM 時，引用可用性區域和鄰近放置組時，引用的不是上一節所示部署第一個 VM：

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "letsgetclose" -Size "Standard_E16_v3"
</code></pre>

成功部署此虛擬機器將在一個可用性區域中託管 SAP 系統的資料庫實例。 鄰近放置組的範圍固定到表示您定義的可用區域的資料中心之一。

假設您以與 DBMS VM 相同的方式部署中央服務 VM，引用相同的區域或區域以及相同的鄰近放置組。 在下一步中，您需要創建要用於 SAP 系統應用程式層的可用性集。

您需要定義和創建接近放置組。 創建可用性集的命令需要對鄰近放置組 ID（而不是名稱）的附加引用。 您可以使用以下命令獲取接近放置組的 ID：

<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose"
</code></pre>

創建可用性集時，在使用託管磁片（除非另有指定否則預設）和鄰近放置組時需要考慮其他參數：

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "myfirstppgexercise" -Name "myppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

理想情況下，應使用三個容錯域。 但是，受支援的容錯域的數量可能因地區而異。 在這種情況下，特定區域可能的最大容錯域數為兩個。 要部署應用程式層 VM，需要添加對可用性集名稱和鄰近放置組名稱的引用，如下所示：

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppgavsetappvm" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

此部署的結果是：
- 位於特定可用性區域或可用性區域的 SAP 系統的 DBMS 層和中央服務。
- 通過與 DBMS VM 或 VM 位於同一 Azure 資料中心的可用性集的 SAP 應用程式層。

> [!NOTE]
> 由於將一個 DBMS VM 部署到一個區域，將第二個 DBMS VM 部署到另一個區域以創建高可用性配置，因此每個區域都需要不同的鄰近放置組。 使用的任何可用性集也是如此。

## <a name="move-an-existing-system-into-proximity-placement-groups"></a>將現有系統移動到接近放置組
如果已經部署了 SAP 系統，則可能需要優化某些關鍵系統的網路延遲，並將應用程式層和 DBMS 層位於同一資料中心中。 要將完整 Azure 可用性集的 VM 移動到已限定的現有鄰近放置組，需要關閉可用性集的所有 VM，並通過 Azure 門戶將可用性集分配給現有鄰近放置組，電源外殼或 CLI。 如果要將不屬於可用性集的 VM 移動到現有接近放置組中，只需關閉 VM 並將其分配給現有鄰近放置組。 


## <a name="next-steps"></a>後續步驟
查看文檔：

- [Azure 上的 SAP 工作負載：規劃和部署檢查表](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- [預覽：使用 Azure CLI 將 VM 部署到鄰近放置組](https://docs.microsoft.com/azure/virtual-machines/linux/proximity-placement-groups)
- [預覽：使用 PowerShell 將 VM 部署到鄰近放置組](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups)
- [用於 SAP 工作負載的 Azure 虛擬機器 DBMS 部署注意事項](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

