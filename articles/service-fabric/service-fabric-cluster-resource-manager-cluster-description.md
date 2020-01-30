---
title: 使用叢集 Resource Manager 描述叢集
description: 藉由指定叢集 Resource Manager 的容錯網域、升級網域、節點屬性和節點容量來描述 Service Fabric 叢集。
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 7142e3f9aaa25e7ba327194c04ad6a9b5f4e3ad1
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2020
ms.locfileid: "76774483"
---
# <a name="describe-a-service-fabric-cluster-by-using-cluster-resource-manager"></a>使用叢集描述 Service Fabric 叢集 Resource Manager
Azure Service Fabric 的叢集 Resource Manager 功能提供數個用來描述叢集的機制：

* 容錯網域
* 升級網域
* 節點屬性
* 節點容量

在執行時間期間，叢集 Resource Manager 會使用這項資訊，以確保叢集中執行之服務的高可用性。 在強制執行這些重要規則時，它也會嘗試優化叢集中的資源耗用量。

## <a name="fault-domains"></a>容錯網域
容錯網域是協調失敗的任何區域。 單一電腦是一個容錯網域。 它本身可能會因為各種原因而失敗，從電源供應器故障到故障的 NIC 固件都會失敗。 

連線至相同乙太網路交換器的電腦位於相同的容錯網域中。 這是指共用單一電源來源或單一位置的機器。 

由於硬體錯誤的重迭程度自然，容錯網域原本就是階層式的。 它們在 Service Fabric 中是以 Uri 表示。

正確設定容錯網域是很重要的，因為 Service Fabric 會使用這項資訊來安全地放置服務。 Service Fabric 不想要放置服務，使容錯網域遺失（由某個元件失敗所造成），會導致服務關閉。 

在 Azure 環境中，Service Fabric 會使用環境所提供的容錯網域資訊，以代表您正確地設定叢集中的節點。 針對 Service Fabric 的獨立實例，容錯網域會在叢集設定時定義。 

> [!WARNING]
> 提供給 Service Fabric 的容錯網域資訊必須正確。 例如，假設您的 Service Fabric 叢集的節點是在10部虛擬機器中執行，並在5個實體主機上執行。 在此情況下，即使有 10 部虛擬機器，也只有 5 個不同的 (最上層) 容錯網域。 共用相同的實體主機會使 Vm 共用相同的根容錯網域，因為當 Vm 的實體主機失敗時，會遇到協調失敗。  
>
> Service Fabric 預期節點的容錯網域不會變更。 確保 Vm 高可用性的其他機制（例如[HA vm](https://technet.microsoft.com/library/cc967323.aspx)）可能會與 Service Fabric 發生衝突。 這些機制會使用從一部主機到另一部主機的透明 Vm 遷移。 它們不會重新設定或通知 VM 內的執行中程式碼。 因此，它們*不支援*做為執行 Service Fabric 叢集的環境。 
>
> Service Fabric 應該僅採用高可用性技術。 不需要像是即時 VM 遷移和 San 等機制。 如果這些機制與 Service Fabric 搭配使用，則會_降低_應用程式的可用性和可靠性。 原因是它們會帶來額外的複雜性、新增失敗的集中式來源，以及使用與 Service Fabric 中的可靠性和可用性策略相衝突。 
>
>

在下圖中，我們會將構成容錯網域的所有實體色彩，並列出產生的所有不同容錯網域。 在此範例中，我們有資料中心 ("DC")、機架 ("R") 和刀鋒伺服器 ("B")。 如果每個分頁都有多個虛擬機器，容錯網域階層中可能會有另一個層級。

<center>

透過容錯網域組織的 ![節點][Image1]
</center>

在執行時間期間，Service Fabric 叢集 Resource Manager 會考慮叢集中的容錯網域，並規劃配置。 服務的具狀態複本或無狀態實例已散發，因此它們位於不同的容錯網域中。 跨容錯網域散發服務，可確保當容錯網域在階層的任何層級失敗時，服務的可用性不會受到危害。

叢集 Resource Manager 並不在意容錯網域階層中有多少層級。 它會嘗試確保階層中的任何一個部分遺失，並不會影響在其中執行的服務。 

如果相同數目的節點在容錯網域階層中的每個深度層級，則最好這麼做。 如果故障網域的「樹狀結構」在您的叢集中是不平衡的，叢集 Resource Manager 比較難找出服務的最佳配置。 不平衡容錯網域配置表示遺失某些網域會影響服務的可用性，而不是其他網域。 因此，叢集 Resource Manager 會在兩個目標之間產生損毀： 

* 它想要將服務放在該「繁重」網域中的電腦上。 
* 它想要將服務放在其他網域中，以免網域遺失也不會造成問題。 

不平衡網域外觀為何？ 下圖顯示兩個不同的叢集版面配置。 在第一個範例中，節點會平均分散到容錯網域。 在第二個範例中，一個容錯網域具有比其他容錯網域更多的節點。 

<center>

![兩個不同的叢集版面配置][Image2]
</center>

在 Azure 中，您可以選擇哪一個容錯網域包含節點。 但視您布建的節點數目而定，您仍然可以得到具有更多節點的容錯網域，而不是其他資源。 

例如，假設您在叢集中有五個容錯網域，但針對節點類型（**NodeType**）布建七個節點。 在此情況下，前兩個容錯網域最後會有更多節點。 如果您繼續部署只有幾個實例的多個**NodeType**實例，問題就會變差。 基於這個理由，我們建議每個節點類型中的節點數目是容錯網域數目的倍數。

## <a name="upgrade-domains"></a>升級網域
升級網域是另一項功能，可協助 Service Fabric 叢集 Resource Manager 瞭解叢集的版面配置。 升級網域會定義同時升級的節點集。 升級網域有助於叢集 Resource Manager 瞭解和協調升級之類的管理作業。

升級網域和容錯網域很類似，但有幾個主要差異。 首先，協調硬體故障的區域會定義容錯網域。 另一方面，升級網域則是由原則所定義。 您可以決定想要多少個，而不是讓環境規定數位。 當您執行節點時，您可以擁有多個升級網域。 容錯網域和升級網域之間的另一個差異是升級網域不是階層式。 相反地，它們更像是簡單的標記。 

下圖顯示三個跨三個容錯網域分割的升級網域。 它也會顯示可設定狀態服務的三個不同複本的一個可能位置，每個複本最後會在不同的容錯和升級網域中。 此位置允許在服務升級期間遺失容錯網域，但仍有一份程式碼和資料。  

<center>

容錯和升級網域的 ![位置][Image3]
</center>

擁有大量升級網域有其優缺點。 較多的升級網域表示升級的每個步驟都更細微，而且會影響較少的節點或服務數目。 較少的服務必須一次移動，而不會對系統產生較少的流失。 這通常是為了改善可靠性，因為在升級期間引進的任何問題會影響較少的服務。 升級網域越多，也表示在其他節點上需要較少的可用緩衝區來處理升級的影響。 

例如，如果您有五個升級網域，每個中的節點會處理大約20% 的流量。 如果您需要關閉升級網域以進行升級，該負載通常需要移到某個位置。 因為您有四個剩餘的升級網域，每個都必須有足夠的空間來容納總流量的5%。 較多的升級網域表示您在叢集中的節點上需要較少的緩衝區。 

如果您改用10個升級網域，請考慮。 在此情況下，每個升級網域只會處理總流量的10%。 當升級步驟通過叢集時，每個網域的空間只需要大約1.1% 的總流量。 較多的升級網域通常可讓您以更高的使用率執行節點，因為您需要的保留容量較少。 容錯網域也是如此。  

擁有許多升級網域的缺點是升級通常需要較長的時間。 Service Fabric 會在升級網域完成後的短時間內等候，並在開始升級下一項之前先執行檢查。 這些延遲可讓系統在升級繼續之前偵測由升級帶來的問題。 缺點是可接受的，因為它會防止不良的變更一次影響到太多服務。

升級網域太少的存在有許多負面的副作用。 當每個升級網域都已關閉並進行升級時，整體容量中的一大部分就無法使用。 例如，如果您只有三個升級網域，您一次只會考慮整體服務或叢集容量的三分之一。 因為您在叢集的其餘部分需要足夠的容量來處理工作負載，所以不需要一次關閉許多服務。 維護該緩衝區表示在正常作業期間，這些節點的載入較不會如此。 這會增加執行服務的成本。

環境中容錯或升級網域的總數沒有實際限制，對於它們如何重疊也沒有條件約束。 但有一些常見的模式：

- 容錯網域和升級網域對應1:1
- 每個節點（實體或虛擬作業系統實例）一個升級網域
- 「等量」或「矩陣」模型，其中容錯網域和升級網域會形成具有機器的矩陣，通常會向下對角線

<center>

容錯和升級網域的 ![版面配置][Image4]
</center>

要選擇哪一種版面配置並沒有最佳的答案。 各有其優缺點。 例如，1FD:1UD 模型相當容易設定。 每個節點模型一個升級網域的模型，最像是人們習慣的。 在升級期間，每個節點都會獨立更新。 這類似於以往手動升級小型集合機器的方式。

最常見的模型是 FD/UD 矩陣，其中容錯網域和升級網域會形成一個資料表，而節點則會沿著對角線開始放置。 這是在 Azure 中的 Service Fabric 叢集預設使用的模型。 對於具有多個節點的叢集，所有專案最後看起來就像密集矩陣模式。

> [!NOTE]
> 裝載于 Azure 中的 Service Fabric 叢集不支援變更預設策略。 只有獨立叢集才提供該自訂。
>

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>容錯和升級網域條件約束及產生的行為
### <a name="default-approach"></a>預設方法
根據預設，叢集 Resource Manager 會讓服務在容錯和升級網域之間保持平衡。 這會經由模型化成為[條件約束](service-fabric-cluster-resource-manager-management-integration.md)。 容錯和升級網域的條件約束狀態：「針對特定的服務分割區，在相同的兩個網域之間的服務物件（無狀態服務實例或具狀態服務複本）數目之間應該不會有大於1的差異階層的層級。」

假設此條件約束提供「最大差異」保證。 容錯和升級網域的條件約束可防止違反規則的特定移動或相片順序。

例如，假設我們有一個具有六個節點的叢集，並設定了五個容錯網域和五個升級網域。

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

假設我們以**TargetReplicaSetSize** （或適用于無狀態服務， **InstanceCount**）值5來建立服務。 複本將會落在 N1-N5 上。 事實上，不論建立多少像這樣的服務，都不會用到 N6。 原因為何？ 讓我們看看目前的配置和選擇 N6 時情況如何之間的差異。

以下是我們所擁有的配置，以及每個容錯和升級網域的複本總數：

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

此配置在每個容錯網域和升級網域的節點方面都會達到平衡。 它也會在每個容錯和升級網域的複本數目方面達到平衡。 每個網域都擁有相同數量的節點，以及相同數量的複本。

現在，讓我們看看改用 N6 而不使用 N2 的情況。 複本將會如何散佈？

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

此配置違反容錯網域條件約束的「最大差異」保證定義。 FD0 有兩個複本，而 FD1 則為零。 FD0 和 FD1 之間的差異總計為二，大於一的最大差異。 由於違反了條件約束，因此叢集 Resource Manager 不允許這種相片順序。 同樣地，如果我們挑選了 N2 和 N6 （而不是 N1 和 N2），我們會得到：

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

此配置會在容錯網域方面達到平衡。 但現在會違反升級網域條件約束，因為 UD0 有零個複本，而 UD1 有兩個。 此配置也無效，叢集 Resource Manager 將不會選取此配置。

這個分配具狀態複本或無狀態執行個體的方法提供了可能的最佳容錯效果。 如果一個網域停止運作，複本/實例的最小數目就會遺失。 

另一方面，此方法則可能太過嚴苛，而不允許叢集利用所有資源。 就某些叢集設定而言，會無法使用某些節點。 這可能會導致 Service Fabric 不會放置您的服務，因此會產生警告訊息。 在上述範例中，無法使用某些叢集節點（在此範例中為 N6）。 即使您已將節點新增至該叢集（N7-N10），複本/實例還是只會放在 N1 – N5 上，因為容錯和升級網域的條件約束。 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |



### <a name="alternative-approach"></a>替代方法

叢集 Resource Manager 支援容錯和升級網域的另一個條件約束版本。 它允許放置，同時仍然保證最低層級的安全性。 覆寫準則約束的指示如下：「針對特定的服務分割區，複本在網域間散發，應確保分割區不會遭受仲裁遺失。」 假設此條件約束提供「仲裁安全」保證。 

> [!NOTE]
> 就具狀態服務而言，我們將「仲裁遺失」定義為一種大多數分割區複本都同時停止運作的情況。 例如，如果**TargetReplicaSetSize**為5，則有三個複本的集合代表仲裁。 同樣地，如果**TargetReplicaSetSize**為6，則仲裁需要四個複本。 在這兩種情況下，如果分割區想要繼續正常運作，就不能同時關閉兩個以上的複本。 
>
> 對於無狀態服務，*仲裁遺失*不會造成這種情況。 即使大部分的實例同時關閉，無狀態服務仍會繼續正常運作。 因此，我們會將焦點放在本文其餘部分的具狀態服務。
>

讓我們回到先前的範例。 使用條件約束的「仲裁安全」版本，這三個配置都是有效的。 即使第二個配置中的 FD0 失敗或第三個配置中的 UD1 失敗，資料分割仍會有仲裁。 （大部分複本仍然會啟動）。使用此版本的條件約束，幾乎可以隨時使用 N6。

「仲裁安全」方法提供比「最大差異」方法更多的彈性。 這是因為在幾乎任何叢集拓撲中，都可以更輕鬆地找到有效的複本散發套件。 不過，此方法無法保證最佳容錯特性，因為有些錯誤比其他錯誤來得嚴重。 

在最糟的情況下，大部分複本可能會遺失一個網域和一個額外複本的失敗。 例如，不需要有三個失敗，就會遺失具有五個複本或實例的仲裁，因此您現在可以失去多數，只有兩個失敗。 

### <a name="adaptive-approach"></a>調適型方法
由於這兩種方法都有優缺點，因此我們引進了可結合這兩種策略的調適型方法。

> [!NOTE]
> 從 Service Fabric 版本6.2 開始，這是預設行為。 
> 
> 調適型方法預設會使用「差異上限」邏輯，而只有在必要時，才會切換至「仲裁防護」邏輯。 叢集 Resource Manager 藉由查看叢集和服務的設定方式，自動找出所需的策略。
> 
> 叢集 Resource Manager 應該針對服務使用「仲裁型」邏輯，這兩種情況都成立：
>
> * 容錯網域的數目和升級網域的數目可平均地整除服務的**TargetReplicaSetSize** 。
> * 節點數目小於或等於容錯網域的數目乘以升級網域的數目。
>
> 請記住，叢集 Resource Manager 會將這種方法用於無狀態和具狀態服務，即使仲裁遺失與無狀態服務無關也是一樣。

讓我們回到先前的範例，並假設叢集現在有八個節點。 叢集仍然設定為使用五個容錯網域和五個升級網域，而該叢集上裝載之服務的**TargetReplicaSetSize**值仍然是五個。 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

因為滿足所有必要的條件，叢集 Resource Manager 將會使用「以仲裁為基礎」的邏輯來散發服務。 這可讓您使用 N6-N8。 在此情況下，其中一個可能的服務散發可能如下所示：

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

如果您服務的**TargetReplicaSetSize**值已縮減為四（例如），叢集 Resource Manager 將會注意到該變更。 它會繼續使用「最大差異」邏輯，因為**TargetReplicaSetSize**不會再由容錯網域和升級網域的數目 dividable。 如此一來，將會發生特定的複本移動，以便在節點 N1-N5 上散發剩餘的四個複本。 如此一來，就不會違反容錯網域和升級網域邏輯的「最大差異」版本。 

在先前的配置中，如果**TargetReplicaSetSize**值為5，而 N1 已從叢集中移除，升級網域的數目就會變成等於四。 同樣地，叢集 Resource Manager 會開始使用「最大差異」邏輯，因為升級網域的數目並不會再將服務的**TargetReplicaSetSize**值分開。 因此，當複本 R1 再次建立時，必須在 N4 上，以避免違反容錯和升級網域的條件約束。

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |N/A |N/A |N/A |N/A |N/A |N/A |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

## <a name="configuring-fault-and-upgrade-domains"></a>設定容錯和升級網域
在 Azure 託管的 Service Fabric 部署中，容錯網域和升級網域會自動定義。 Service Fabric 會從 Azure 中取用環境資訊。

如果您要建立自己的叢集（或想要在開發環境中執行特定拓撲），您可以自行提供容錯網域和升級網域資訊。 在此範例中，我們定義了九個節點的本機開發叢集，其橫跨三個資料中心（每個都有三個機架）。 此叢集也有三個跨三個資料中心的升級網域。 以下是 ClusterManifest 中的設定範例：

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one box/one single server -->
    <WindowsServer IsScaleMin="true">
      <NodeList>
        <Node NodeName="Node01" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType01" FaultDomain="fd:/DC01/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node02" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType02" FaultDomain="fd:/DC01/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node03" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType03" FaultDomain="fd:/DC01/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node04" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType04" FaultDomain="fd:/DC02/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node05" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType05" FaultDomain="fd:/DC02/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node06" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType06" FaultDomain="fd:/DC02/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node07" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType07" FaultDomain="fd:/DC03/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node08" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType08" FaultDomain="fd:/DC03/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node09" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType09" FaultDomain="fd:/DC03/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
      </NodeList>
    </WindowsServer>
  </Infrastructure>
```

這個範例會使用 Clusterconfig.x509.multimachine.json 來進行獨立部署：

```json
"nodes": [
  {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm3",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm4",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm5",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm6",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm7",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm8",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm9",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD3"
  }
],
```

> [!NOTE]
> 當您透過 Azure Resource Manager 定義叢集時，Azure 會指派容錯網域和升級網域。 因此，您 Azure Resource Manager 範本中的節點類型和虛擬機器擴展集的定義不會包含容錯網域或升級網域的相關資訊。
>

## <a name="node-properties-and-placement-constraints"></a>節點屬性和放置條件約束
有時候（事實上，大部分的情況下）您會想要確保某些工作負載只在叢集中的特定節點類型上執行。 例如，某些工作負載可能需要 Gpu 或 Ssd，有些則可能不會。 

以特定工作負載為目標的絕佳範例，幾乎是每個多層式架構。 某些機器會做為應用程式的前端或 API 服務端，並公開至用戶端或網際網路。 其他電腦 (通常有不同的硬體資源) 處理計算層或儲存層的工作。 它們通常_不會_直接公開至用戶端或網際網路。 

Service Fabric 預期在某些情況下，特定的工作負載可能需要在特定硬體設定上執行。 例如：

* 現有的多層式應用程式已「提升並轉移」到 Service Fabric 的環境中。
* 工作負載必須在特定硬體上執行，以達到效能、調整或安全性隔離的原因。
* 工作負載應該與其他工作負載隔離，以因應原則或資源耗用量的原因。

為了支援這類設定，Service Fabric 包含可套用至節點的標記。 這些標記稱為*節點屬性*。 *放置條件約束*是附加至您為一或多個節點屬性選取的個別服務的語句。 放置條件約束會定義應該執行服務的位置。 條件約束集是可擴充的。 任何索引鍵/值組都可以使用。 

<center>

![叢集版面配置的不同工作負載][Image5]
</center>

### <a name="built-in-node-properties"></a>內建節點屬性
Service Fabric 會定義一些可以自動使用的預設節點屬性，因此您不需要定義它們。 在每個節點上定義的預設屬性是**NodeType**和**NodeName**。 

例如，您可以將放置條件約束撰寫為 `"(NodeType == NodeType03)"`。 **NodeType**是常用的屬性。 這項功能很有用，因為它會對應1:1 與電腦類型。 每個機器類型都對應至傳統多層式架構應用程式中的工作負載類型。

<center>

![放置條件約束和節點屬性][Image6]
</center>

## <a name="placement-constraints-and-node-property-syntax"></a>放置條件約束和節點屬性語法 
節點屬性中指定的值可以是字串、布林或帶正負號的 long。 服務的語句稱為「放置*條件約束*」，因為它會限制服務可在叢集中執行的位置。 條件約束可以是任何可在叢集中的節點屬性上運作的布林值語句。 這些布林值語句中的有效選取器包括：

* 建立特定語句的條件式檢查：

  | 引數 | 語法 |
  | --- |:---:|
  | 「等於」 | "==" |
  | 「不等於」 | "!=" |
  | 「大於」 | ">" |
  | 「大於或等於」 | ">=" |
  | 「小於」 | "<" |
  | 「小於或等於」 | "<=" |

* 群組和邏輯作業的布林語句：

  | 引數 | 語法 |
  | --- |:---:|
  | 「和」 | "&&" |
  | 「或」 | "&#124;&#124;" |
  | 「否」 | "!" |
  | 「組成單一陳述式」 | "()" |

以下是一些基本條件約束語句的範例：

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

服務只能放置在整體放置條件約束陳述式評估為 "True" 的節點上。 未定義屬性的節點不符合包含屬性的任何放置條件約束。

假設下列節點屬性是針對 ClusterManifest 中的節點類型所定義：

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

下列範例會顯示透過 Clusterconfig.x509.multimachine.json 定義的節點屬性，以供獨立部署或適用于 Azure 託管叢集的範本. json。 

> [!NOTE]
> 在您的 Azure Resource Manager 範本中，節點類型通常是參數化的。 它看起來會像 `"[parameters('vmNodeType1Name')]"`，而不是 NodeType01。
>

```json
"nodeTypes": [
    {
        "name": "NodeType01",
        "placementProperties": {
            "HasSSD": "true",
            "NodeColor": "green",
            "SomeProperty": "5"
        },
    }
],
```

您可以為服務建立服務放置*條件約束*，如下所示：

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasSSD == true && SomeProperty >= 4)";
// Add other required ServiceDescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

```PowerShell
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

如果 NodeType01 的所有節點都是有效的，您也可以選取具有條件約束 `"(NodeType == NodeType01)"`的節點類型。

服務的放置條件約束可在執行時間以動態方式更新。 如有需要，您可以在叢集中移動服務、新增和移除需求等等。 Service Fabric 可確保即使在進行這些類型的變更時，服務仍會保持運作並可供使用。

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

```PowerShell
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

會針對每個已命名的服務實例指定放置條件約束。 更新一律會取代 (覆寫) 先前指定的項目。

叢集定義會定義節點上的屬性。 變更節點的屬性需要升級至叢集設定。 升級節點的屬性需要每個受影響的節點重新啟動，以報告其新的屬性。 Service Fabric 管理這些輪流升級。

## <a name="describing-and-managing-cluster-resources"></a>描述和管理叢集資源
任何 Orchestrator 的其中一個最重要的作業是協助管理叢集中的資源耗用量。 管理叢集資源可以表示幾個不同的項目。 

首先，確保電腦不會多載。 這表示確定電腦不會執行比它們能夠處理還多的服務。 

第二種是平衡和優化，這對於有效率地執行服務很重要。 符合成本效益或效能敏感性的服務供應專案，不允許某些節點處於經常性存取狀態，而有些則是冷的。 熱節點會導致資源爭用和效能不佳。 冷節點代表浪費的資源和成本增加。 

Service Fabric 將資源表示為*計量*。 度量是您想要向 Service Fabric 描述的任何邏輯或實體資源。 計量的範例包括 "WorkQueueDepth" 或 "MemoryInMb"。 如需 Service Fabric 可在節點上管理之實體資源的相關資訊，請參閱[資源管理](service-fabric-resource-governance.md)。 如需叢集 Resource Manager 所使用之預設計量和如何設定自訂計量的詳細資訊，請參閱[這篇文章](service-fabric-cluster-resource-manager-metrics.md)。

計量不同于放置條件約束和節點屬性。 節點屬性是節點本身的靜態描述項。 計量會描述節點所擁有的資源，以及服務在節點上執行時所使用的資源。 節點屬性可能是**HasSSD** ，而且可能設定為 true 或 false。 該 SSD 上可用的空間量，以及服務所取用的數量，會是「DriveSpaceInMb」之類的計量。 

就像放置條件約束和節點屬性一樣，Service Fabric 叢集 Resource Manager 無法瞭解計量名稱的意義。 計量名稱只是字串。 最好是將單位宣告為計量名稱的一部分，這是您在可能不明確時所建立的度量。

## <a name="capacity"></a>容量
如果您關閉所有資源*平衡*，Service Fabric 叢集 Resource Manager 仍會確保沒有任何節點超出其容量。 除非叢集已滿或工作負載大於任何節點，否則管理容量溢位是可能的。 容量是叢集 Resource Manager 用來瞭解節點具有多少資源的另一個*條件約束*。 另外也會追蹤整個叢集的剩餘容量。 

服務層級的容量和耗用量均以度量來表示。 例如，計量可能是 "ClientConnections"，而節點的 "ClientConnections" 容量可能是32768。 其他節點可能會有其他限制。 在該節點上執行的服務可能表示其目前正在使用32256的計量 "ClientConnections"。

在執行時間期間，叢集 Resource Manager 會追蹤叢集和節點上的剩餘容量。 為了追蹤容量，叢集 Resource Manager 會從服務執行所在的節點容量中減去每個服務的使用量。 有了這種資訊，叢集 Resource Manager 可以找出要放置或移動複本的位置，讓節點不會超出容量。

<center>

![叢集節點和容量][Image7]
</center>

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription metric = new ServiceLoadMetricDescription();
metric.Name = "ClientConnections";
metric.PrimaryDefaultLoad = 1024;
metric.SecondaryDefaultLoad = 0;
metric.Weight = ServiceLoadMetricWeight.High;
serviceDescription.Metrics.Add(metric);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

```PowerShell
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ClientConnections,High,1024,0)
```

您可以看到叢集資訊清單中定義的容量。 以下是 ClusterManifest 的範例：

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

以下是透過 Clusterconfig.x509.multimachine.json 定義的容量範例，適用于獨立部署或適用于 Azure 託管叢集的範本. json： 

```json
"nodeTypes": [
    {
        "name": "NodeType03",
        "capacities": {
            "ClientConnections": "65536",
        }
    }
],
```

服務的負載通常會動態變更。 假設複本的 "ClientConnections" 負載已從1024變更為2048。 在其上執行的節點，該計量的容量只有512。 現在複本或實例的位置無效，因為該節點上沒有足夠的空間。 叢集 Resource Manager 必須讓節點回到容量以下。 它會將一或多個複本或實例從該節點移至其他節點，藉此減少超過容量的節點上的負載。 

叢集 Resource Manager 嘗試將移動複本的成本降到最低。 您可以深入瞭解[移動成本](service-fabric-cluster-resource-manager-movement-cost.md)，以及重新[平衡策略和規則](service-fabric-cluster-resource-manager-metrics.md)的相關資訊。

## <a name="cluster-capacity"></a>叢集容量
Service Fabric 叢集 Resource Manager 如何讓整體叢集太滿？ 有了動態載入，就不會有太多工作。 服務可以讓其負載尖峰獨立于叢集 Resource Manager 所採取的動作。 如此一來，如果明天突然增加，您目前有足夠空間的叢集可能會動能不足。 

叢集中的控制項 Resource Manager 協助避免問題。 您可以做的第一件事，就是防止建立新的工作負載，使叢集變得完整。

假設您建立無狀態服務，而且它有一些相關聯的負載。 服務會關心「DiskSpaceInMb」度量。 服務會針對服務的每個實例取用五個單位的 "DiskSpaceInMb"。 您想要建立服務的三個執行個體。 這表示您需要在叢集中有15個單位的 "DiskSpaceInMb"，才能讓您甚至建立這些服務實例。

叢集 Resource Manager 會持續計算每個計量的容量和耗用量，讓它能夠判斷叢集中的剩餘容量。 如果空間不足，叢集 Resource Manager 會拒絕建立服務的呼叫。

由於需求只會有15個單位可用，因此您可以透過許多不同的方式來配置此空間。 例如，在15個不同節點上可能會有一個剩餘的容量單位，或五個不同節點上有三個剩餘的容量單位。 如果叢集 Resource Manager 可以重新排列，讓三個節點上有五個可用的單位，則會放置服務。 重新安排叢集通常都可行，除非叢集幾乎已滿，或因為某些原因致使現有服務無法合併。

## <a name="buffered-capacity"></a>緩衝處理的容量
緩衝處理的容量是叢集 Resource Manager 的另一項功能。 它可以保留整體節點容量的某些部分。 此容量緩衝區僅用於在升級和節點失敗期間放置服務。 

針對所有節點，會針對每個計量以全域方式指定緩衝的容量。 您為保留容量挑選的值是您在叢集中擁有的容錯和升級網域數目的功能。 更多容錯和升級網域表示您可以為緩衝處理的容量挑選較低的數位。 如果您有較多網域，則在升級和失敗期間，無法使用的叢集部分當然會比較少。 只有當您也指定了計量的節點容量時，指定緩衝處理的容量才有意義。

以下範例說明如何在 ClusterManifest 中指定已緩衝處理的容量：

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

以下範例說明如何透過 Clusterconfig.x509.multimachine.json 為獨立部署或 json （適用于 Azure 託管的叢集）指定緩衝的容量：

```json
"fabricSettings": [
  {
    "name": "NodeBufferPercentage",
    "parameters": [
      {
          "name": "SomeMetric",
          "value": "0.15"
      },
      {
          "name": "SomeOtherMetric",
          "value": "0.20"
      }
    ]
  }
]
```

當叢集耗盡計量的緩衝容量時，建立新的服務會失敗。 防止建立新服務以保留緩衝區，可確保升級和失敗不會造成節點超出容量。 緩衝處理的容量是選擇性的，但我們建議在定義計量容量的任何叢集中使用。

叢集 Resource Manager 會公開此負載資訊。 對於每個計量，這項資訊包括： 
- 緩衝處理的容量設定。
- 總容量。
- 目前的耗用量。
- 是否要將每個度量視為平衡。
- 標準差的統計資料。
- 具有最大和最小負載的節點。  
  
下列程式碼顯示該輸出的範例：

```PowerShell
PS C:\Users\user> Get-ServiceFabricClusterLoadInformation
LastBalancingStartTimeUtc : 9/1/2016 12:54:59 AM
LastBalancingEndTimeUtc   : 9/1/2016 12:54:59 AM
LoadMetricInformation     :
                            LoadMetricName        : Metric1
                            IsBalancedBefore      : False
                            IsBalancedAfter       : False
                            DeviationBefore       : 0.192450089729875
                            DeviationAfter        : 0.192450089729875
                            BalancingThreshold    : 1
                            Action                : NoActionNeeded
                            ActivityThreshold     : 0
                            ClusterCapacity       : 189
                            ClusterLoad           : 45
                            ClusterRemainingCapacity : 144
                            NodeBufferPercentage  : 10
                            ClusterBufferedCapacity : 170
                            ClusterRemainingBufferedCapacity : 125
                            ClusterCapacityViolation : False
                            MinNodeLoadValue      : 0
                            MinNodeLoadNodeId     : 3ea71e8e01f4b0999b121abcbf27d74d
                            MaxNodeLoadValue      : 15
                            MaxNodeLoadNodeId     : 2cc648b6770be1bc9824fa995d5b68b1
```

## <a name="next-steps"></a>後續步驟
* 如需叢集 Resource Manager 內架構和資訊流程的相關資訊，請參閱叢集[Resource Manager 架構總覽](service-fabric-cluster-resource-manager-architecture.md)。
* 定義磁碟重組計量是在節點上合併負載而不是將其分散出去的一種方式。若要瞭解如何設定磁碟重組，請參閱[Service Fabric 中的計量和負載磁碟重組](service-fabric-cluster-resource-manager-defragmentation-metrics.md)。
* 從頭開始，並[取得 Service Fabric 叢集 Resource Manager 的簡介](service-fabric-cluster-resource-manager-introduction.md)。
* 若要瞭解叢集 Resource Manager 如何管理並平衡叢集中的負載，請參閱[平衡您的 Service Fabric](service-fabric-cluster-resource-manager-balancing.md)叢集。

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
