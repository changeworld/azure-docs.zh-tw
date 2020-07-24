---
title: 適用于 SAP 應用程式的 Azure 鄰近放置群組 |Microsoft Docs
description: 說明使用 Azure 鄰近放置群組的 SAP 部署案例
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
ms.openlocfilehash: 7aa71062c86d57cabe8579e13011956137804f74
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87079786"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>適用于 SAP 應用程式之最佳網路延遲的 Azure 鄰近性放置群組
以 SAP NetWeaver 或 SAP S/4HANA 架構為基礎的 SAP 應用程式，對於 SAP 應用層與 SAP 資料庫層之間的網路延遲是敏感的。 此敏感度是在應用層中執行的大部分商務邏輯所產生的結果。 因為 SAP 應用層會執行商務邏輯，所以會以較高的頻率向資料庫層發出查詢，以每秒數千或數十個的速率。 在大部分情況下，這些查詢的本質很簡單。 它們通常可以在500毫秒或更少的資料庫層上執行。

在網路上將這類查詢從應用層傳送到資料庫層，並接收結果集的時間，對於執行商務程式所花費的時間有重大影響。 這是網路延遲的敏感性，這就是為什麼您需要在 SAP 部署專案中達到最佳的網路延遲。 如需如何分類網路延遲的指導方針，請參閱[SAP 附注 #1100926-常見問題：網路效能](https://launchpad.support.sap.com/#/notes/1100926/E)。

在許多 Azure 區域中，資料中心的數量已增加。 可用性區域的引進也會觸發這項成長。 同時，客戶（特別是高階 SAP 系統）在 M 系列系列或 HANA 大型實例中使用更特殊的 VM Sku。 在特定 Azure 區域中的所有資料中心都無法使用這些 Azure 虛擬機器類型。 由於這兩個喜好傾向，客戶的網路延遲不在最佳範圍內。 在某些情況下，此延遲會導致其 SAP 系統的效能不佳。

為了避免這些問題，Azure 提供[鄰近的放置群組](../../linux/co-location.md)。 此新功能已用來部署各種 SAP 系統。 如需鄰近放置群組的限制，請參閱本段落開頭的參考文章。 本文涵蓋 Azure 鄰近性放置群組可以或應該使用的 SAP 案例。

## <a name="what-are-proximity-placement-groups"></a>什麼是鄰近放置群組？ 
Azure 鄰近性放置群組是邏輯結構。 當定義時，它會系結至 Azure 區域和 Azure 資源群組。 部署 Vm 時，會參考鄰近放置群組：

- 在資料中心內部署的第一個 Azure VM。 您可以將第一部虛擬機器視為「範圍 VM」，這是根據最終與特定可用性區域的使用者定義結合的 Azure 配置演算法，部署在資料中心內。
- 所有後續部署的 Vm 都會參考鄰近性放置群組，以便將所有後續部署的 Azure Vm 放在與第一部虛擬機器相同的資料中心。

> [!NOTE]
> 如果未部署可在第一個 VM 所在的資料中心內執行特定 VM 類型的主機硬體，則要求的 VM 類型部署將會失敗。 您會收到失敗訊息。

單一[Azure 資源群組](../../../azure-resource-manager/management/manage-resources-portal.md)可以有多個指派的鄰近放置群組。 但是鄰近放置群組只能指派給一個 Azure 資源群組。

當您使用鄰近放置群組時，請記住下列考慮：

- 當您針對 SAP 系統以最佳效能為目標，並使用鄰近放置群組將自己限制為系統的單一 Azure 資料中心時，您可能無法在放置群組內合併所有類型的 VM 系列。 之所以會發生這些限制，是因為執行特定 VM 類型所需的主機硬體可能不會出現在部署放置群組「已設定範圍的 VM」的資料中心內。
- 在這類 SAP 系統的生命週期期間，您可能會被迫將系統移至另一個資料中心。 如果您決定向外延展 HANA DBMS 層（例如，從四個節點移至16個節點），而且沒有足夠的容量來取得您在資料中心內使用之類型的額外12個 Vm，則此移動可能是必要的。
- 由於硬體解除委任，Microsoft 可能會針對您在不同資料中心內使用的 VM 類型（而不是您最初使用的）建立容量。 在這種情況下，您可能需要將所有鄰近位置群組的 Vm 移到另一個資料中心。

## <a name="proximity-placement-groups-with-sap-systems-that-use-only-azure-vms"></a>具有僅使用 Azure Vm 之 SAP 系統的鄰近放置群組
Azure 上大多數的 SAP NetWeaver 和 S/4HANA 系統部署都不會使用[HANA 大型實例](./hana-overview-architecture.md)。 針對不使用 HANA 大型實例的部署，請務必在 SAP 應用層與 DBMS 層之間提供最佳效能。 若要這麼做，請只針對系統定義 Azure 鄰近放置群組。

在大部分的客戶部署中，客戶會為 SAP 系統建立單一的[Azure 資源群組](../../../azure-resource-manager/management/manage-resources-portal.md)。 在這種情況下，在生產 ERP 系統資源群組及其鄰近位置群組之間，會有一對一的關係。 在其他情況下，客戶會以水準方式組織其資源群組，並收集單一資源群組中的所有生產系統。 在此情況下，您在生產環境 SAP 系統的資源群組與生產環境 SAP ERP、SAP BW 等的數個鄰近放置群組之間，會有一對多關聯性。

避免在單一的鄰近放置群組中，將數個 SAP 生產或非生產系統進行捆綁。 當少數的 SAP 系統或 SAP 系統，以及某些周圍的應用程式需要低延遲的網路通訊時，您可以考慮將這些系統移至一個鄰近放置群組。 您應該避免系統的配套，因為您在鄰近放置群組中群組的系統越多，機率就愈高：

- 您需要的 VM 類型無法在鄰近放置群組的範圍內，于特定資料中心內執行。
- 因為您在一段時間內將軟體新增至鄰近放置群組，所以當您需要更多時，這類非主流 Vm 的資源最後可能會無法使用。

以下是理想的設定，如下所示：

![只有 Azure Vm 的鄰近放置群組](./media/sap-proximity-placement-scenarios/ppg-for-all-azure-vms.png)

在此情況下，單一 SAP 系統會群組在每一個資源群組中，每個都有一個鄰近放置群組。 無論您使用的是 HANA 向外延展或 DBMS 相應增加設定，都不會有任何相關性。

## <a name="proximity-placement-groups-and-hana-large-instances"></a>鄰近放置群組和 HANA 大型實例
如果您的某些 SAP 系統依賴應用層的[Hana 大型實例](./hana-overview-architecture.md)，當您使用部署在[修訂版4資料列或戳記](./hana-network-architecture.md#networking-architecture-for-hana-large-instance)中的 hana 大型實例單位時，您可以在 hana 大型實例單位與 Azure vm 之間體驗大量的網路延遲改善。 其中一項改進是 HANA 大型實例單位在部署時，會使用鄰近放置群組進行部署。 您可以使用該鄰近放置群組來部署您的應用層 Vm。 因此，這些 Vm 將會部署在裝載您的 HANA 大型實例單位的相同資料中心內。

若要判斷您的 HANA 大型實例單位是否部署在修訂4戳記或資料列中，請參閱[AZURE HANA 大型實例控制的 Azure 入口網站](./hana-li-portal.md#look-at-attributes-of-single-hli-unit)一文。 在您的 HANA 大型實例單位的屬性總覽中，您也可以判斷近接位置群組的名稱，因為它是在部署 HANA 大型實例單位時所建立的。 屬性總覽中出現的名稱是您應該將應用層 Vm 部署到的鄰近放置群組的名稱。

與僅使用 Azure 虛擬機器的 SAP 系統相較之下，當您使用 HANA 大型實例時，您在決定要使用多少個[Azure 資源群組](../../../azure-resource-manager/management/manage-resources-portal.md)時，會有較小的彈性。 [Hana 大型實例租](./hana-know-terms.md)使用者的所有「Hana 大型實例」單位都會分組在單一資源群組中，如[這篇文章](./hana-li-portal.md#display-of-hana-large-instance-units-in-the-azure-portal)所述。 除非您將部署到不同的租使用者，例如生產和非生產系統或其他系統，否則您所有的 HANA 大型實例單位都會部署在一個 HANA 大型實例租使用者中。 此租使用者與資源群組具有一對一的關聯性。 但是，每一個單位都會定義個別的鄰近放置群組。

因此，單一租使用者的 Azure 資源群組和鄰近放置群組之間的關聯性將如下所示：

![鄰近放置群組和 HANA 大型實例](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)

## <a name="example-of-deployment-with-proximity-placement-groups"></a>使用鄰近放置群組進行部署的範例
以下是一些 PowerShell 命令，可讓您使用 Azure 鄰近放置群組來部署 Vm。

在您登入[Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)之後，第一個步驟是檢查您是否位於想要用於部署的 Azure 訂用帳戶中：

<pre><code>
Get-AzureRmContext
</code></pre>

如果您需要變更為不同的訂用帳戶，您可以執行下列命令來完成此動作：

<pre><code>
Set-AzureRmContext -Subscription "my PPG test subscription"
</code></pre>

執行下列命令來建立新的 Azure 資源群組：

<pre><code>
New-AzResourceGroup -Name "myfirstppgexercise" -Location "westus2"
</code></pre>

執行下列命令來建立新的鄰近放置群組：

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose" -Location "westus2"
</code></pre>

使用如下所示的命令，將您的第一個 VM 部署到鄰近放置群組：

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

上述命令會部署以 Windows 為基礎的 VM。 在此 VM 部署成功之後，鄰近位置群組的資料中心範圍會定義在 Azure 區域內。 所有參考鄰近位置群組的後續 VM 部署（如先前的命令所示）都會部署在相同的 Azure 資料中心，只要 VM 類型可以裝載于放在該資料中心的硬體上，而且該 VM 類型的容量可供使用。

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>結合可用性設定組和可用性區域與鄰近性放置群組
使用 SAP 系統部署可用性區域的缺點之一，就是您無法使用特定區域內的可用性設定組來部署 SAP 應用層。 您想要在與 DBMS 層相同的區域中部署 SAP 應用層。 不支援在部署單一 VM 時參考可用性區域和可用性設定組。 因此，在過去，您必須藉由參考區域來強制部署應用層。 您無法確定應用層 Vm 散佈在不同的更新和失敗網域。

藉由使用鄰近放置群組，您可以略過這項限制。 以下是部署順序：

- 建立鄰近放置群組。
- 藉由參考可用性區域，部署您的錨點 VM，通常是 DBMS 伺服器。
- 建立參考 Azure 鄰近性群組的可用性設定組。 （請參閱本文稍後的命令）。
- 藉由參考可用性設定組和鄰近放置群組來部署應用層 Vm。

您不需要像上一節所示部署第一個 VM，而是在部署 VM 時參照可用性區域和鄰近放置群組：

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "letsgetclose" -Size "Standard_E16_v3"
</code></pre>

此虛擬機器部署成功時，會將 SAP 系統的資料庫實例裝載在一個可用性區域中。 鄰近放置群組的範圍固定為其中一個資料中心，其中代表您所定義的可用性區域。

假設您以與 DBMS Vm 相同的方式部署中央服務 Vm，並參考相同的區域或區域和相同的鄰近放置群組。 在下一個步驟中，您必須建立要用於 SAP 系統之應用層的可用性設定組。

您必須定義並建立鄰近放置群組。 建立可用性設定組的命令需要對鄰近放置群組識別碼（而非名稱）進行額外的參考。 您可以使用下列命令來取得鄰近放置群組的識別碼：

<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose"
</code></pre>

當您建立可用性設定組時，如果您使用受控磁片，則需要考慮其他參數（除非另有指定，否則為預設值）和鄰近性放置群組：

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "myfirstppgexercise" -Name "myppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

在理想的情況下，您應該使用三個容錯網域。 但支援的容錯網域數目可能會因區域而異。 在此情況下，特定區域可能會有兩個容錯網域的最大數目。 若要部署您的應用層 Vm，您需要新增可用性設定組名稱和鄰近放置組名的參考，如下所示：

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppgavsetappvm" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

此部署的結果為：
- 適用于您的 SAP 系統的 DBMS 層和中央服務，位於特定可用性區域或可用性區域。
- SAP 應用層，其位於與 DBMS VM 或 Vm 相同的 Azure 資料中心內的可用性設定組。

> [!NOTE]
> 因為您會將一個 DBMS VM 部署到一個區域，而第二個 DBMS VM 放到另一個區域以建立高可用性設定，所以每個區域都需要不同的鄰近放置群組。 您所使用的任何可用性設定組也是如此。

## <a name="move-an-existing-system-into-proximity-placement-groups"></a>將現有的系統移至鄰近位置群組
如果您已經部署 SAP 系統，您可能會想要將某些重要系統的網路延遲優化，並在相同的資料中心內找出應用層和 DBMS 層。 若要將完整 Azure 可用性設定組的 Vm 移至已設定範圍的現有近接位置群組，您必須關閉可用性設定組的所有 Vm，並透過 Azure 入口網站、PowerShell 或 CLI，將可用性設定組指派給現有的鄰近放置群組。 如果您想要將不屬於可用性設定組的 VM 移到現有的鄰近放置群組，您只需要關閉 VM，並將它指派給現有的鄰近放置群組。 


## <a name="next-steps"></a>後續步驟
請參閱檔：

- [Azure 上的 SAP 工作負載：規劃和部署檢查清單](./sap-deployment-checklist.md)
- [預覽：使用 Azure CLI 將 Vm 部署至鄰近放置群組](../../linux/proximity-placement-groups.md)
- [預覽：使用 PowerShell 將 Vm 部署至鄰近放置群組](../../windows/proximity-placement-groups.md)
- [適用于 SAP 工作負載的 Azure 虛擬機器 DBMS 部署考慮](./dbms_guide_general.md)
