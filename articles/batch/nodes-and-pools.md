---
title: Azure Batch 中的節點和集區
description: 從開發觀點了解計算節點和集區，以及如何在 Azure Batch 工作流程中使用。
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 16a5309711b9c8633da9ba473c1b55bc2e54c334
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87385750"
---
# <a name="nodes-and-pools-in-azure-batch"></a>Azure Batch 中的節點和集區

在 Azure Batch 工作流程中，「計算節點」(或「節點」) 是一部虛擬機器，可用來處理您應用程式的部分工作負載。 「集區」是應用程式執行所在的節點集合。 本文會詳細說明節點和集區，以及在 Azure Batch 工作流程中加以建立及使用時的考量。

## <a name="nodes"></a>節點

節點是 Azure 虛擬機器 (VM) 或雲端服務 VM，專門用來處理您應用程式的部分工作負載。 節點大小決定配置給節點的 CPU 核心數目、記憶體容量，以及本機檔案系統大小。

您可以使用 Azure 雲端服務、[Azure 虛擬機器 Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?filters=virtual-machine-images&page=1) 中的映像，或您準備的自訂映像來建立 Windows 或 Linux 節點的集區。

節點可以執行節點作業系統環境所支援的任何可執行檔或指令碼。 可執行檔或指令碼包括 \*.exe、\*.cmd、\*.bat 和 PowerShell 指令碼 (適用於 Windows)，以及二進位檔、Shell 和 Python 指令碼 (適用於 Linux)。

Batch 中的所有計算節點也包括︰

- 工作可參考的標準[資料夾結構](files-and-directories.md)與相關聯的[環境變數](jobs-and-tasks.md)。
- **防火牆** 設定。
- [遠端存取](error-handling.md#connect-to-compute-nodes) Windows (遠端桌面通訊協定 (RDP)) 和 Linux (安全殼層 (SSH)) 節點。

根據預設，節點可以彼此通訊，但無法與不屬於相同集區的虛擬機器進行通訊。 若要允許節點與其他虛擬機器或內部部署網路安全地進行通訊，您可以[在 Azure 虛擬網路（VNet）的子網中](batch-virtual-network.md)布建集區。 當您這麼做時，可以透過公用 IP 位址存取您的節點。 這些公用 IP 位址是由 Batch 所建立，而且可能會在集區的存留期間變更。 您也可以使用您控制的[靜態公用 IP 位址來建立集](create-pool-public-ip.md)區，以確保它們不會意外變更。

## <a name="pools"></a>集區

集區是應用程式執行所在的節點集合。

Azure Batch 集區的建置基礎為核心 Azure 計算平台。 這些集區可提供大規模的配置、應用程式安裝、資料散發、健全狀況監視，以及在集區內彈性調整 ([調整規模](#automatic-scaling-policy)) 計算節點數目。

系統會指派唯一的名稱及 IP 位址給新增至集區的每個節點。 當節點從集區中移除時，就會失去對作業系統或檔案所做的任何變更，且其名稱及 IP 位址都會釋出供未來使用。 當節點離開集區時，其存留期就結束。

集區只能由建立它的 Batch 帳戶使用。 Batch 帳戶可以建立多個集區，以符合其將執行之應用程式的資源需求。

集區可以手動建立，或當您指定要完成的工作時，由 Batch 服務自動建立。 當您建立集區時，您可以指定下列屬性：

- [節點作業系統和版本](#operating-system-and-version)
- [節點類型和目標節點數目](#node-type-and-target)
- [節點大小](#node-size)
- [自動調整原則](#automatic-scaling-policy)
- [工作排程原則](#task-scheduling-policy)
- [通訊狀態](#communication-status)
- [啟動工作](#start-tasks)
- [應用程式套件](#application-packages)
- [虛擬網路 (VNet) 和防火牆設定](#virtual-network-vnet-and-firewall-configuration)
- [存留期](#pool-and-compute-node-lifetime)

> [!IMPORTANT]
> Batch 帳戶具有預設配額，以限制 Batch 帳戶中的核心數目。 對應到計算節點數目的核心數目。 在 [Azure Batch 服務的配額和限制](batch-quota-limit.md)中，您可以找到預設配額及如何[增加配額](batch-quota-limit.md#increase-a-quota)的說明。 如果您的集區未達其目標節點數目，可能是因為核心配額的原因。

## <a name="operating-system-and-version"></a>作業系統和版本

當您建立 Batch 集區時，要指定 Azure 虛擬機器設定，以及您想要在集區中每個計算節點上執行的作業系統類型。

## <a name="configurations"></a>組態

Batch 中有兩種類型的集區設定可供使用。

### <a name="virtual-machine-configuration"></a>虛擬機器設定

**虛擬機器設定**會指定集區是由 Azure 虛擬機器所組成。 這些 VM 可能會從 Linux 或 Windows 映像加以建立。

以虛擬機器設定作為基礎建立集區時，您不僅需要指定節點的大小和用來建立這些節點的映像來源，也必須在節點上安裝**虛擬機器映像參考**和 Batch **節點代理程式 SKU**。 如需指定這些集區屬性的詳細資訊，請參閱 [在 Azure Batch 集區中佈建 Linux 計算節點](batch-linux-nodes.md)。 您可以選擇性地將一或多個空的資料磁碟連結至從 Marketplace 映像建立的集區 VM，或是在自訂映像中包含資料磁碟，以建立 VM。 包含資料磁碟時，您必須在 VM 內掛接並格式化磁碟，才能使用這些磁碟。

### <a name="cloud-services-configuration"></a>雲端服務設定

**雲端服務設定**會指定集區是由 Azure 雲端服務節點所組成。 雲端服務「只」提供 Windows 計算節點。

雲端服務組態集區可用的作業系統列於 [Azure 客體 OS 版次與 SDK 相容性矩陣](../cloud-services/cloud-services-guestos-update-matrix.md)。 建立包含雲端服務節點的集區時，您需要指定節點大小及其「作業系統系列」(這會決定作業系統所安裝的 .NET 版本)。 雲端服務部署至 Azure 的速度比執行 Windows 的虛擬機器還快。 如果您需要 Windows 計算節點的集區，可能會發現雲端服務提供了部署時間方面的效能優勢。

如同雲端服務內的背景工作角色，您可以指定 [OS 版本] (如需背景工作角色的詳細資訊，請參閱[雲端服務概觀](../cloud-services/cloud-services-choose-me.md))。 建議為 [OS 版本]指定 `Latest (*)`，以便自動升級節點，而且不需為了因應新發行的版本而執行工作。 選取特定 OS 版本的主要使用案例是為了確保應用程式相容性，以允許在更新版本之前執行回溯相容性測試。 通過驗證之後，即可更新集區的 [OS 版本] 並安裝新的 OS 映像。 任何執行中的工作都將會中斷並重新排入佇列。

### <a name="node-agent-skus"></a>節點代理程式 SKU

當您建立一個集區時，必須選取適當的 **nodeAgentSkuId**，視您 VHD 的基礎映像 OS 而定。 您可以呼叫[列出受支援節點代理程式 SKU 的清單](/rest/api/batchservice/list-supported-node-agent-skus)作業，藉此取得對應至其 OS 映像參考的可用節點代理程式 SKU 識別碼。

### <a name="custom-images-for-virtual-machine-pools"></a>適用於虛擬機器集區的自訂映像

若要了解如何建立具有自訂映像的集區，請參閱[使用共用映像庫來建立自訂集區](batch-sig-images.md)。

或者，您可以使用[受控映像](batch-custom-images.md)資源來建立虛擬機器的自訂集區。 如需有關從 Azure VM 準備自訂 Linux 映像的資訊，請參閱[如何建立虛擬機器或 VHD 的映像](../virtual-machines/linux/capture-image.md)。 如需有關從 Azure VM 準備自訂 Windows 映像的資訊，請參閱[在 Azure 中建立一般化 VM 的受控映像](../virtual-machines/windows/capture-image-resource.md)。

### <a name="container-support-in-virtual-machine-pools"></a>虛擬機器集區中的容器支援

使用 Batch API 建立虛擬機器設定集區時，您可以設定集區執行設定集區執行 Docker 容器中的工作。 目前，您必須使用支援 Docker 容器的映像來建立集區。 使用 Windows Server 2016 Datacenter 與 Azure Marketplace 中的容器映像，或者提供自訂 VM 映像，其中包含 Docker Community Edition 或 Enterprise Edition 和任何所需的驅動程式。 集區設定必須包含[容器設定](/rest/api/batchservice/pool/add)，該設定會在集區建立時將容器映像複製到 VM。 然後，在集區上執行的工作就可以參考容器映像和容器執行選項。

如需詳細資訊，請參閱[在 Azure Batch 上執行 Docker 容器應用程式](batch-docker-container-workloads.md)。

## <a name="node-type-and-target"></a>節點類型和目標

當您建立集區時，可以指定您想要的節點類型及每個類型的目標數目。 節點有兩個類型︰

- **專用節點。** 專用計算節點會保留給您的工作負載使用。 它們比低優先順序的節點昂貴，但保證永遠不會遭到佔用。
- **低優先順序節點。** 低優先順序的節點會利用 Azure 中剩餘的容量來執行 Batch 工作負載。 低優先順序的節點每小時比專用節點更便宜，並可啟用需要大量計算能力的工作負載。 如需詳細資訊，請參閱[使用低優先順序的 VM 搭配 Batch](batch-low-pri-vms.md)。

Azure 多餘的容量不足時，可能會佔用低優先順序的節點。 如果節點在執行工作時遭到佔用，工作會重新排入佇列並於計算節點再次可用時再次執行。 低優先順序的節點很適合用於作業完成時間有彈性且工作分散於許多節點的工作負載。 在決定使用適合您情節的低優先順序節點之前，請確定會盡可能降低因優先佔用而遺失的工作數，且可輕鬆重新建立。

您可以在相同的集區中同時擁有低優先順序和專用的計算節點。 每種節點類型都有其自己的目標設定，您可以對其指定所需的節點數目。

計算節點數目稱為「目標」，因為在某些情況下，您的集區可能無法達到所需的節點數目。 例如，如果集區先達到 Batch 帳戶的[核心配額](batch-quota-limit.md)，它就可能無法達成目標。 或者，如果您已將可限制節點數目上限的自動調整公式套用至集區，集區可能無法達成目標。

如需低優先順序和專用節點的價格資訊，請參閱 [Batch 價格](https://azure.microsoft.com/pricing/details/batch/)。

## <a name="node-size"></a>節點大小

當您建立 Azure Batch 集區時，您可以從幾乎所有的 VM 系列及 Azure 中可用的大小進行選擇。 Azure 針對不同的工作負載提供各種 VM 大小，包括特製化的 [HPC](../virtual-machines/sizes-hpc.md)或[已啟用的 GPU](../virtual-machines/sizes-gpu.md) VM 大小。 

如需詳細資訊，請參閱[為 Azure Batch 集區中的運算節點選擇 VM 大小](batch-pool-vm-sizes.md)。

## <a name="automatic-scaling-policy"></a>自動調整原則

針對動態工作負載，您可以將自動調整原則套用至集區。 Batch 服務將會根據計算案例的目前工作負載和資源使用狀況，定期評估您的公式，並動態調整集區中的計算節點數目。 這樣一來，您只會使用所需資源並可釋放不需要的資源，因而能夠降低應用程式的整體執行成本。

撰寫 [自動調整公式](batch-automatic-scaling.md#autoscale-formulas) 並將該公式與集區建立關聯，以啟用自動調整。 Batch 服務使用此公式來決定集區中下一個調整間隔 (您可以設定的間隔) 的目標節點數目。 您可以在建立集區時指定集區的自動調整設定，或稍後在集區上啟用調整。 您也可以更新已啟用調整的集區上的調整設定。

例如，或許作業需要您提交大量要執行的工作。 您可以指派調整公式給集區，以根據目前已排入佇列的工作數目和作業中工作的完成率來調整集區中的節點數目。 Batch 服務會定期評估公式，並根據工作負載和其他公式設定來調整集區大小。 服務會在有大量排入佇列的工作時視需要新增節點，並在沒有排入佇列或正在執行時移除節點。

調整公式可以根據下列度量：

- **時間度量** 是以指定時數內每隔五分鐘收集的統計資料為基礎。
- **資源度量** 是以 CPU 使用量、頻寬使用量、記憶體使用量和節點的數目為基礎。
- **工作計量**是以工作狀態為基礎，例如 [作用中] \(已排入佇列)、[執行中] 或 [已完成]。

當自動調整減少集區中的計算節點數目時，您必須考量如何處理在減少作業時正在執行的工作。 為了配合這一點，Batch 會提供您可以納入公式中的[「節點解除配置選項」](/rest/api/batchservice/pool/removenodes#computenodedeallocationoption)。 例如，您可以指定執行中的工作立即停止，然後重新排入佇列以便在另一個節點上執行，或允許先完成再從集區中移除節點。 請注意，將節點解除配置選項設定為 `taskcompletion` 或 `retaineddata` 會避免集區調整大小作業，直到所有工作都完成，或所有工作保留期間都已過期為止。

如需關於自動調整應用程式的詳細資訊，請參閱 [自動調整 Azure Batch 集區中的計算節點](batch-automatic-scaling.md)。

> [!TIP]
> 若要獲得最大的計算資源使用率，請將節點的目標數目設定成在作業結束時降為零，但允許執行中的工作完成。

## <a name="task-scheduling-policy"></a>工作排程原則

[每個節點的工作數上限](batch-parallel-node-tasks.md) 組態選項會決定可在集區內的每個計算節點上平行執行的工作數目上限。

預設組態會指定在節點上一次執行一項工作，但在某些情況下，在一個節點上同時執行兩項以上工作較為有利。 請參閱[並行節點工作](batch-parallel-node-tasks.md)一文中的[範例案例](batch-parallel-node-tasks.md#example-scenario)，以了解如何從每個節點的多項工作受惠。

您也可以指定「填滿類型」，以決定 Batch 是要將工作平均分散到集區中的所有節點，還是將最大數目的工作分配給一個節點後，再將工作指派給另一個節點。

## <a name="communication-status"></a>通訊狀態

大部分情況下，工作會獨立運作，並不需要彼此通訊。 不過，有一些工作必須進行通訊的應用程式，例如 [MPI 案例](batch-mpi.md)。

您可以設定集區來允許**節點間通訊**，以便集區內的節點可在執行階段進行通訊。 啟用節點間通訊時，[雲端服務組態] 集區中的節點可以在超過 1100 個連接埠上彼此通訊，而且 [虛擬機器組態] 集區並不會限制任何連接埠的流量。

啟用節點間通訊也會影響叢集內的節點位置，且由於部署限制，可能會限制集區中的節點數目上限。 如果您的應用程式不需要節點之間的通訊，Batch 服務可將許多不同叢集和資料中心的大量節點配置給集區，以發揮更強大的平行處理能力。

## <a name="start-tasks"></a>啟動工作

如有需要，您可以新增[啟動工作](jobs-and-tasks.md#start-task)，將在每個節點加入集區以及每次重新啟動節點或重新安裝其映像時，於該節點上執行。 啟動工作特別適合用於準備計算節點，以便執行工作，例如在計算節點上安裝工作要執行的應用程式。

## <a name="application-packages"></a>應用程式封裝

您可以指定要部署至集區中計算節點的應用程式封裝。 應用程式封裝會提供您的工作執行之應用程式的簡化部署和版本控制。 您針對集區指定的應用程式封裝會安裝於加入該集區的每個節點，以及在節點重新啟動或重新安裝映像時安裝。

如需使用應用程式套件將應用程式部署至 Batch 節點的詳細資訊，請參閱[使用 Batch 應用程式套件將應用程式部署至計算節點](batch-application-packages.md)。

## <a name="virtual-network-vnet-and-firewall-configuration"></a>虛擬網路 (VNet) 和防火牆設定

當您在 Batch 中佈建計算節點集區時，可以將此集區與 Azure [虛擬網路 (VNet)](../virtual-network/virtual-networks-overview.md) 的子網路產生關聯。 若要使用 Azure VNet，Batch 用戶端 API 必須使用 Azure Active Directory (AD) 驗證。 Azure Batch 對於 Azure AD 的支援記載於[使用 Active Directory 驗證 Batch 服務解決方案](batch-aad-auth.md)中。

### <a name="vnet-requirements"></a>VNet 需求

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

如需在 VNet 中設定 Batch 集區的詳細資訊，請參閱[使用虛擬網路建立虛擬機器的集區](batch-virtual-network.md)。

> [!TIP]
> 若要確保用來存取節點的公用 IP 位址不會變更，您可以[使用您所控制的指定公用 ip 位址來建立集](create-pool-public-ip.md)區。

## <a name="pool-and-compute-node-lifetime"></a>集區和計算節點存留期

您在設計 Azure Batch 解決方案時，必須指定如何及何時建立集區，以及這些集區中的計算節點可用性要保持多久。

在極端情況下，您可以針對您所提交的每項作業建立集區，並在其工作執行完成時立即移除此集區。 這樣只有在必要時才會配置節點，而且節點會在變成閒置後立即關閉，因此可達到最高使用率。 雖然這表示作業必須等候節點進行配置，但請務必注意，工作會在節點個別配置且啟動工作完成時立即排程執行。 Batch「不會」等到集區中的所有節點都可用，才將工作指派到節點。 這可確保所有可用節點的最大使用率。

就另一個極端而言，如果讓作業立即啟動是最高的優先順序，則可以預先建立集區，並使其節點在作業提交之前成為可用。 在此情況下，工作可以立即啟動，但節點可能會閒置以等候指派。

有一個組合方法通常用來處理可變但進行中的負載。 您可以有一個集區，其中已提交多個作業，且可以根據作業負載來相應增加或減少節點數目。 您可以根據目前的負載被動完成，或在負載可預測時主動完成。 如需詳細資訊，請參閱[自動調整原則](#automatic-scaling-policy)。

## <a name="security-with-certificates"></a>憑證的安全性

在加密或解密工作的敏感資訊 (例如 [Azure 儲存體帳戶](accounts.md#azure-storage-accounts)的金鑰) 時，您通常需要使用憑證。 若要支援此功能，您可以在節點上安裝憑證。 加密的機密資料會透過命令列參數或內嵌在其中一個工作資源中而傳遞至工作，已安裝的憑證可用來解密這些資料。

您可以使用[新增憑證](/rest/api/batchservice/certificate/add)作業 (Batch REST) 或 [CertificateOperations.CreateCertificate](/dotnet/api/microsoft.azure.batch.certificateoperations) 方法 (Batch .NET)，將憑證新增至 Batch 帳戶。 然後，您可以將憑證與新的或現有的集區產生關聯。

當憑證與集區相關聯時，Batch 服務會在集區中的每個節點上安裝憑證。 當節點啟動時，在啟動任何工作之前 (包括[開始工作](jobs-and-tasks.md#start-task)和[作業管理員工作](jobs-and-tasks.md#job-manager-task))，Batch 服務會安裝適當的憑證。

如果您將憑證新增至現有集區，必須重新啟動其計算節點，憑證才會套用至節點。

## <a name="next-steps"></a>後續步驟

- 了解[作業和工作](jobs-and-tasks.md)。
