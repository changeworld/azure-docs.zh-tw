---
title: 適用于 SAP 應用程式的 Azure 鄰近放置群組 |Microsoft Docs
description: 描述 Azure 鄰近放置群組的 SAP 部署案例
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/29/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1e6aaf1b37073bf93e0aca8237161bf11af3a872
ms.sourcegitcommit: 42922af070f7edf3639a79b1a60565d90bb801c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2020
ms.locfileid: "97827218"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>適用于 SAP 應用程式之最佳網路延遲的 Azure 鄰近放置群組
以 SAP NetWeaver 或 SAP S/4HANA 架構為基礎的 SAP 應用程式，對 SAP 應用層與 SAP 資料庫層之間的網路延遲很敏感。 此敏感度是大部分在應用層中執行之商務邏輯的結果。 由於 SAP 應用層會執行商務邏輯，因此會以極高的頻率（每秒數以千計或數萬千）向資料庫層發出查詢。 在大部分的情況下，這些查詢的本質很簡單。 它們通常可以在資料庫層上以500微秒或更短的時間執行。

在網路上將這類查詢從應用層傳送到資料庫層，並接收結果集的時間，對於執行商務程式所花費的時間有重大的影響。 這種對網路延遲的敏感度，是您可能想要在 SAP 部署專案中達到特定的網路延遲上限。 如需如何分類網路延遲的指導方針，請參閱 [SAP 附注 #1100926-常見問題：網路效能](https://launchpad.support.sap.com/#/notes/1100926/E) 。

在許多 Azure 區域中，資料中心的數目已增加。 同時，客戶（尤其是高階 SAP 系統）會使用更多的特殊 VM Sku （M 或 Mv2 系列）或 HANA 大型實例。 這些 Azure 虛擬機器類型在所有補充 Azure 區域的資料中心內都不一定都有提供。 這些事實可能會產生可將 SAP 應用層和 SAP DBMS 層之間的網路延遲優化的機會。

為了讓您有機會將網路延遲優化，Azure 提供 [鄰近的放置群組](../../linux/co-location.md)。 鄰近放置群組可用來將不同 VM 類型的群組強制分組到單一 Azure 資料中心，以便將這些不同 VM 類型之間的網路延遲優化到最可能。 在將第一個 VM 部署到這類鄰近放置群組的過程中，VM 會系結至特定的資料中心。 就像這個潛在客戶的音效一樣，結構的使用也會帶來一些限制：

- 您無法假設所有 Azure VM 類型都可在每個 Azure 資料中心內使用。 如此一來，就可以限制一個鄰近放置群組中不同 VM 類型的組合。 發生這些限制的原因是執行特定 VM 類型所需的主機硬體，可能不會出現在部署放置群組的資料中心內。
- 當您調整某個鄰近放置群組內 Vm 的部分大小時，您無法自動假設在所有情況下，新的 VM 類型都可在與鄰近放置群組一部分的其他 Vm 相同的資料中心內使用
- 由於 Azure 解除硬體，它可能會將鄰近位置群組的特定 Vm 強制至另一個 Azure 資料中心。 如需涵蓋此案例的詳細資訊，請閱讀檔 [共置資源以改善延遲](../../linux/co-location.md#planned-maintenance-and-proximity-placement-groups)  

> [!IMPORTANT]
> 由於可能會有限制，因此應該使用鄰近放置群組：
>
> - 只有在必要時
> - 僅限單一 SAP 系統的資料細微性，而不是整個系統內容或完整 SAP 環境的資料細微性
> - 以將鄰近放置群組中的不同 VM 類型和 Vm 數目保持為最小值的方式

假設您藉由指定可用性區域來部署 Vm，並選取相同的可用性區域，這些 Vm 之間的網路延遲應該足以讓 SAP NetWeaver 和 S/4HANA 系統符合效能和輸送量。 這項假設與特定區域是否已從一個資料中心或多個資料中心建立的事實無關。 在區域部署中使用鄰近位置群組的唯一原因是，您想要將 Azure 可用性設定組部署的 Vm 與已部署區域的 Vm 一起配置。


## <a name="what-are-proximity-placement-groups"></a>什麼是鄰近放置群組？ 
Azure 鄰近放置群組是邏輯結構。 定義鄰近位置群組時，會將其系結至 Azure 區域和 Azure 資源群組。 部署 Vm 時，會參考鄰近放置群組：

- 第一個部署在資料中心的 Azure VM。 您可以將第一部虛擬機器視為「範圍 VM」，其部署在以 Azure 配置演算法為基礎的資料中心，而這些演算法最終會與特定可用性區域的使用者定義結合。
- 所有後續部署的 Vm 都會參考鄰近放置群組，以將所有後續部署的 Azure Vm 放在與第一個虛擬機器相同的資料中心內。

> [!NOTE]
> 如果沒有部署的主機硬體可在放置第一個 VM 的資料中心內執行特定的 VM 類型，則所要求的 VM 類型部署將不會成功。 您將會收到失敗訊息。

單一 [Azure 資源群組](../../../azure-resource-manager/management/manage-resources-portal.md) 可以有多個指派的鄰近放置群組。 但鄰近放置群組只能指派給一個 Azure 資源群組。


## <a name="proximity-placement-groups-with-sap-systems-that-use-only-azure-vms"></a>使用只有 Azure Vm 的 SAP 系統的鄰近放置群組
大部分在 Azure 上的 SAP NetWeaver 和 S/4HANA 系統部署都不會使用「 [HANA 大型實例](./hana-overview-architecture.md)」。 針對不使用 HANA 大型實例的部署，請務必提供 SAP 應用層與 DBMS 層之間的最佳效能。 若要這樣做，請定義僅適用于系統的 Azure 鄰近放置群組。

在大部分的客戶部署中，客戶會為 SAP 系統建立單一 [Azure 資源群組](../../../azure-resource-manager/management/manage-resources-portal.md) 。 在此情況下，例如生產 ERP 系統資源群組及其鄰近位置群組之間的一對一關聯性。 在其他情況下，客戶會以水準方式組織其資源群組，並收集單一資源群組中的所有生產系統。 在此情況下，您的資源群組與生產 SAP 系統的資源群組之間會有一對多關聯性，而生產環境 sap ERP、SAP BW 等的數個鄰近放置群組之間會有一對多關聯性。

避免將多個 SAP 生產或非生產系統組合在單一鄰近放置群組中。 當少數的 SAP 系統或 SAP 系統以及某些周圍應用程式需要低延遲的網路通訊時，您可以考慮將這些系統移到一個鄰近放置群組。 避免系統組合，因為您在鄰近放置群組中分組的系統愈多，可能會有更高的機會：

- 您需要的 VM 類型無法在鄰近放置群組所屬的特定資料中心內執行。
- 由於您要在一段時間內將軟體新增至鄰近放置群組，因此當您需要更多資源時，可能會導致非主流 Vm （例如 M 系列 Vm）的資源無法完成。

以下是理想設定的說明，如下所示：

![只有 Azure Vm 的鄰近位置群組](./media/sap-proximity-placement-scenarios/ppg-for-all-azure-vms.png)

在此情況下，單一 SAP 系統會分組在一個資源群組中，每個資源群組都有一個鄰近放置群組。 無論您是使用 HANA 相應放大或 DBMS 相應增加設定，都不會有任何相依性。

## <a name="proximity-placement-groups-and-hana-large-instances"></a>鄰近放置群組和 HANA 大型實例
如果您的某些 SAP 系統依賴適用于應用層的 [Hana 大型實例](./hana-overview-architecture.md) ，當您使用以 [修訂4資料列或戳記](./hana-network-architecture.md#networking-architecture-for-hana-large-instance)部署的 hana 大型實例單位時，您可能會在 hana 大型實例單位與 Azure vm 之間遇到網路延遲的重大改進。 其中一個改進是在部署 HANA 大型實例單位時，使用鄰近放置群組進行部署。 您可以使用該鄰近位置群組來部署應用層 Vm。 如此一來，這些 Vm 將會部署在裝載 HANA 大型實例單位的相同資料中心內。

若要判斷您的 HANA 大型實例單位是否部署在修訂4戳記或資料列中，請 [透過 Azure 入口網站查看 AZURE Hana 大型實例控制](./hana-li-portal.md#look-at-attributes-of-single-hli-unit)的文章。 在您的 HANA 大型實例單位的屬性總覽中，您也可以決定鄰近放置群組的名稱，因為它是在部署 HANA 大型實例單位時建立的。 出現在屬性總覽中的名稱是您應該將應用層 Vm 部署到鄰近放置群組的名稱。

相較于僅使用 Azure 虛擬機器的 SAP 系統，當您使用 HANA 大型實例時，您在決定要使用多少個 [Azure 資源群組](../../../azure-resource-manager/management/manage-resources-portal.md) 時，會有較少的彈性。 [Hana 大型實例租](./hana-know-terms.md)使用者的所有 Hana 大型實例單位會分組在單一資源群組中，如[本文所述](./hana-li-portal.md#display-of-hana-large-instance-units-in-the-azure-portal)。 除非您部署至不同的租使用者以分開，例如生產和非生產系統或其他系統，否則您所有的 HANA 大型實例單位都會部署在一個 HANA 大型實例租使用者中。 此租使用者與資源群組有一對一的關聯性。 但系統會為每個單一單位定義個別的鄰近放置群組。

因此，單一租使用者的 Azure 資源群組和鄰近位置群組之間的關聯性如下所示：

![鄰近放置群組和 HANA 大型實例](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)

## <a name="example-of-deployment-with-proximity-placement-groups"></a>使用鄰近位置群組部署的範例
以下是一些您可以用來透過 Azure 鄰近放置群組來部署 Vm 的 PowerShell 命令。

第一個步驟是在您登入 [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)之後，請檢查您是否在要用於部署的 Azure 訂用帳戶中：

<pre><code>
Get-AzureRmContext
</code></pre>

如果您需要變更為不同的訂用帳戶，您可以執行此命令來執行此動作：

<pre><code>
Set-AzureRmContext -Subscription "my PPG test subscription"
</code></pre>

執行此命令以建立新的 Azure 資源群組：

<pre><code>
New-AzResourceGroup -Name "myfirstppgexercise" -Location "westus2"
</code></pre>

藉由執行此命令來建立新的鄰近位置群組：

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose" -Location "westus2"
</code></pre>

使用類似下列的命令，將您的第一個 VM 部署到鄰近位置群組：

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

上述命令會部署以 Windows 為基礎的 VM。 在此 VM 部署成功之後，鄰近放置群組的資料中心範圍會定義在 Azure 區域內。 所有參考鄰近位置群組的後續 VM 部署（如先前的命令所示）都會部署在相同的 Azure 資料中心，只要 VM 類型可以裝載于放置於該資料中心的硬體上，且該 VM 類型的容量可供使用。

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>結合可用性設定組和可用性區域與鄰近放置群組
使用 SAP 系統部署可用性區域的其中一個缺點是，您無法使用特定區域內的可用性設定組來部署 SAP 應用層。 您想要將 SAP 應用層部署在與 DBMS 層相同的區域中。 不支援在部署單一 VM 時參考可用性區域和可用性設定組。 因此，先前強制您藉由參考區域來部署應用層。 您已失去可確保應用層 Vm 分散在不同更新和失敗網域的能力。

藉由使用鄰近位置群組，您可以略過這項限制。 以下是部署順序：

- 建立鄰近位置群組。
- 參考可用性區域，以部署您的錨點 VM （通常是 DBMS 伺服器）。
- 建立參考 Azure 近接群組的可用性設定組。  (請參閱本文稍後的命令。 ) 
- 參考可用性設定組和鄰近位置群組，以部署應用層 Vm。

您可以在部署 VM 時參考可用性區域和鄰近位置群組，而不是依照上一節所示範的方式部署第一個 VM：

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "letsgetclose" -Size "Standard_E16_v3"
</code></pre>

成功部署此虛擬機器將會在一個可用性區域中裝載 SAP 系統的資料庫實例。 鄰近放置群組的範圍會固定到代表您所定義之可用性區域的其中一個資料中心。

假設您以與 DBMS Vm 相同的方式部署中央服務 Vm，參考相同的區域或區域，以及相同的鄰近放置群組。 在下一個步驟中，您必須建立要用於 SAP 系統應用層的可用性設定組。

定義和建立鄰近位置群組。 用來建立可用性設定組的命令需要鄰近放置群組識別碼的額外參考 (不) 名稱。 您可以使用下列命令來取得鄰近位置群組的識別碼：

<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose"
</code></pre>

當您建立可用性設定組時，除非另有指定) 和鄰近位置群組，否則您在使用受控磁片時，必須考慮使用其他參數 (預設值：

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "myfirstppgexercise" -Name "myppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

在理想情況下，您應該使用三個容錯網域。 但支援的容錯網域數目可能會因區域而異。 在此情況下，特定區域最多可以有兩個容錯網域的數目。 若要部署應用層 Vm，您需要新增您的可用性設定組名稱和鄰近位置組名的參考，如下所示：

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppgavsetappvm" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

此部署的結果為：
- 適用于 SAP 系統的 DBMS 層和中央服務，位於特定的可用性區域或可用性區域。
- 在與 DBMS VM 或 Vm 位於相同 Azure 資料中心的可用性設定組中的 SAP 應用層。

> [!NOTE]
> 因為您將一個 DBMS VM 部署至一個區域，並將第二個 DBMS VM 部署到另一個區域，以建立高可用性設定，所以每個區域都需要不同的鄰近放置群組。 您所使用的任何可用性設定組也是如此。

## <a name="move-an-existing-system-into-proximity-placement-groups"></a>將現有的系統移至鄰近位置群組
如果您已經部署 SAP 系統，您可能會想要將某些重要系統的網路延遲優化，並在相同的資料中心找出應用層和 DBMS 層。 若要將完整 Azure 可用性設定組的 Vm 移至已設定範圍的現有鄰近位置群組，您需要關閉可用性設定組的所有 Vm，並透過 Azure 入口網站、PowerShell 或 CLI 將可用性設定組指派給現有的鄰近放置群組。 如果您想要將不屬於可用性設定組的 VM 移至現有的鄰近放置群組，則只需要關閉 VM，並將它指派給現有的鄰近放置群組。 


## <a name="next-steps"></a>後續步驟
請參閱下列檔：

- [Azure 上的 SAP 工作負載：規劃和部署檢查清單](./sap-deployment-checklist.md)
- [預覽：使用 Azure CLI 將 Vm 部署到鄰近位置群組](../../linux/proximity-placement-groups.md)
- [預覽：使用 PowerShell 將 Vm 部署至鄰近位置群組](../../windows/proximity-placement-groups.md)
- [適用于 SAP 工作負載的 Azure 虛擬機器 DBMS 部署考慮](./dbms_guide_general.md)