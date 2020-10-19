---
title: 使用叢集資源管理員描述叢集
description: 藉由指定叢集資源管理員的容錯網域、升級網域、節點屬性和節點容量來描述 Service Fabric 叢集。
author: masnider
ms.topic: conceptual
ms.date: 07/28/2020
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 71629ebf1397c00face500f0bfd9c8e92deacc5e
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173033"
---
# <a name="describe-a-service-fabric-cluster-by-using-cluster-resource-manager"></a>使用叢集資源管理員描述 Service Fabric 叢集

Azure Service Fabric 的叢集資源管理員功能提供了數種用來描述叢集的機制：

* 容錯網域
* 升級網域
* 節點屬性
* 節點容量

在執行時間期間，叢集資源管理員會使用這項資訊來確保在叢集中執行之服務的高可用性。 在強制執行這些重要規則時，它也會嘗試優化叢集中的資源耗用量。

## <a name="fault-domains"></a>容錯網域

容錯網域是協調失敗的任何區域。 單一機器是容錯網域。 它可能會因各種原因而失敗，從電源供應器故障到故障的 NIC 固件失敗。

連線到相同乙太網路交換器的電腦位於相同的容錯網域中。 這是共用單一電源來源或單一位置的機器。

由於容錯網域的本質是在重迭的硬體錯誤中，因此容錯網域本質上是階層式的。 它們在 Service Fabric 中是以 Uri 表示。

正確設定容錯網域很重要，因為 Service Fabric 會使用這項資訊安全地放置服務。 Service Fabric 不會想要放置服務，使容錯網域遺失 (因為某些) 元件的失敗而導致服務中斷。

在 Azure 環境中，Service Fabric 會使用環境所提供的容錯網域資訊，代表您正確地設定叢集中的節點。 若為 Service Fabric 的獨立實例，則會在叢集設定時定義容錯網域。

> [!WARNING]
> 提供給 Service Fabric 的容錯網域資訊必須正確。 例如，假設您的 Service Fabric 叢集節點是在10部虛擬機器中執行，並在5個實體主機上執行。 在此情況下，即使有 10 部虛擬機器，也只有 5 個不同的 (最上層) 容錯網域。 共用相同的實體主機會導致 Vm 共用相同的根容錯網域，因為 Vm 在實體主機失敗時遇到協調失敗。  
>
> Service Fabric 預期節點的容錯網域不會變更。 確保 Vm 高可用性的其他機制（例如 [HA vm](/previous-versions/system-center/virtual-machine-manager-2008-r2/cc967323(v=technet.10))）可能會造成與 Service Fabric 衝突。 這些機制會從一部主機將 Vm 透明地遷移至另一部主機。 他們不會重新設定或通知 VM 內的執行中程式碼。 因此，它們 *不支援* 做為執行 Service Fabric 叢集的環境。 
>
> Service Fabric 應該僅採用高可用性技術。 不需要像是即時 VM 遷移和 San 這樣的機制。 如果這些機制搭配 Service Fabric 使用，則會 _降低_ 應用程式的可用性和可靠性。 原因是它們會導致額外的複雜性、加入失敗的集中來源，以及使用與 Service Fabric 中的相關聯的可靠性和可用性策略。
>
>

在下圖中，我們會將構成容錯網域的所有實體上色，並列出產生的所有不同容錯網域。 在此範例中，我們有資料中心 ("DC")、機架 ("R") 和刀鋒伺服器 ("B")。 如果每個分頁都有一部以上的虛擬機器，容錯網域階層中可能會有另一個層級。

<center>
![透過容錯網域組織的節點][Image1]
</center>

在執行時間期間，Service Fabric 叢集資源管理員會考慮叢集中的容錯網域，並規劃配置。 服務的具狀態複本或無狀態實例會散發，因此它們位於不同的容錯網域中。 跨容錯網域散發服務，可確保當容錯網域在階層的任何層級失敗時，服務的可用性不會受到危害。

叢集資源管理員不在意容錯網域階層中有多少層級。 它會嘗試確保不會影響階層中的任何一個部分，而不會影響其中所執行的服務。

如果相同數目的節點位於容錯網域階層中的每個深度層級，則最好如此。 如果故障網域的「樹狀結構」在您的叢集中是不對稱的，叢集資源管理員很難找出最佳的服務配置。 不平衡容錯網域配置表示遺失某些網域會影響服務的可用性，而不是其他網域。 因此，叢集資源管理員會在兩個目標之間損毀：

* 它想要使用該「繁重」網域中的電腦，方法是將服務放在這些電腦上。 
* 它想要將服務放在其他網域中，以免網域遺失也不會造成問題。

不平衡網域外觀為何？ 下圖顯示兩個不同的叢集配置。 在第一個範例中，節點會平均分散在容錯網域中。 在第二個範例中，一個容錯網域有比其他容錯網域更多的節點。

<center>
![兩個不同的叢集配置][Image2]
</center>

在 Azure 中，您可以選擇要為您管理哪個容錯網域包含節點。 但是，視您布建的節點數目而定，您仍然可以得到具有更多節點的容錯網域，而非其他節點。

例如，假設您在叢集中有五個容錯網域，但為節點類型 (**NodeType**) 布建七個節點。 在此情況下，前兩個容錯網域最後會有更多節點。 如果您繼續使用幾個實例來部署多個 **NodeType** 實例，問題會變得更糟。 基於這個理由，我們建議每個節點類型中的節點數目是容錯網域數目的倍數。

## <a name="upgrade-domains"></a>升級網域

升級網域是另一項功能，可協助 Service Fabric 叢集資源管理員瞭解叢集的版面配置。 升級網域會定義同時升級的節點集。 升級網域有助於叢集資源管理員瞭解及協調像是升級的管理作業。

升級網域與容錯網域很類似，但有幾個主要差異。 首先，協調的硬體失敗區域會定義容錯網域。 相反地，升級網域是由原則所定義。 您可以決定想要多少數目，而不是讓環境決定數位。 您可以在進行節點時擁有任意數量的升級網域。 容錯網域和升級網域之間的另一項差異在於升級網域不是階層式。 相反地，它們比較像簡單的標記。

下圖顯示三個在三個容錯網域中劃分的升級網域。 它也會針對具狀態服務的三個不同複本顯示一個可能的位置，每個複本最後都在不同的容錯網域和升級網域中。 這項放置允許在服務升級期間遺失容錯網域，但仍有一份程式碼和資料。  

<center>
![放置容錯網域和升級網域][Image3]
</center>

擁有大量升級網域有一些優缺點。 更多升級網域表示升級的每個步驟都較細微，並且會影響較少的節點或服務數目。 較少的服務必須一次移動，而不會對系統造成流失。 這通常是為了改善可靠性，因為升級期間引進的任何問題會影響較少的服務。 更多升級網域也表示，在其他節點上需要較少的可用緩衝區來處理升級的影響。

例如，如果您有五個升級網域，則每個網域中的節點會處理大約20% 的流量。 如果您需要關閉升級網域以進行升級，則該負載通常需要移至某處。 由於您有四個剩餘的升級網域，因此每個升級網域都必須有大約25% 的總流量空間。 更多升級網域表示您在叢集中的節點上需要較少的緩衝區。

請考慮是否有10個升級網域。 在此情況下，每個升級網域只會處理大約總流量的10%。 當升級步驟通過叢集時，每個網域都只需要有大約11% 的總流量空間。 更多升級網域通常可讓您以更高的使用率執行節點，因為您需要較少的保留容量。 容錯網域也是如此。  

擁有許多升級網域的缺點是升級通常需要較長的時間。 Service Fabric 在升級網域完成後等候一小段時間，並在開始升級下一個檢查之前執行檢查。 這些延遲可讓系統在升級繼續之前偵測由升級帶來的問題。 缺點是可接受的，因為它會防止不良的變更一次影響到太多服務。

有太少的升級網域存在有許多負面副作用。 當每個升級網域都已關閉且正在升級時，整體容量中有很大的部分無法使用。 比方說，如果您只有三個升級網域，您一次會關閉整體服務或叢集容量的三分之一。 因為您在叢集的其餘部分需要足夠的容量來處理工作負載，所以不需要立即關閉許多服務。 維護該緩衝區即表示在正常作業期間，這些節點的載入比其他節點更少。 這會增加執行服務的成本。

環境中容錯或升級網域的總數沒有實際限制，對於它們如何重疊也沒有條件約束。 但有一些常見的模式：

* 對應1:1 的容錯網域和升級網域
*  (實體或虛擬作業系統實例的每個節點一個升級網域) 
* 「等量」或「矩陣」模型，其中的容錯網域和升級網域會形成一個矩陣，其中的機器通常會在對角線下執行

<center>
![容錯網域和升級網域的版面配置][Image4]
</center>

選擇何種版面配置並沒有最佳的答案。 各有其優缺點。 例如，1FD:1UD 模型相當容易設定。 每個節點模型的一個升級網域模型，最類似于人們的用途。 在升級期間，每個節點都會個別更新。 這類似於以往手動升級小型集合機器的方式。

最常見的模型是 FD/UD 矩陣，其中的容錯網域和升級網域形成資料表，而節點則是沿著對角線開始放置。 這是在 Azure 中的 Service Fabric 叢集預設使用的模型。 對於具有許多節點的叢集，所有專案最終都會像密集的矩陣模式。

> [!NOTE]
> 裝載在 Azure 中的 Service Fabric 叢集不支援變更預設策略。 只有獨立叢集提供該自訂專案。
>

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>容錯和升級網域條件約束及產生的行為
### <a name="default-approach"></a>預設方法

依預設，叢集資源管理員可讓服務在容錯網域和升級網域之間保持平衡。 這會經由模型化成為[條件約束](service-fabric-cluster-resource-manager-management-integration.md)。 容錯網域和升級網域的條件約束：「針對特定的服務分割區，在相同階層層級的任何兩個網域之間的服務物件 (無狀態服務實例或具狀態服務) 複本之間，應該永遠不會有一個以上的差異。」

假設此條件約束提供「最大差異」保證。 容錯網域和升級網域的條件約束會防止違反規則的特定移動或相片順序。

例如，假設我們有一個具有六個節點的叢集，其設定有五個容錯網域和五個升級網域。

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

現在假設我們建立了具有 **TargetReplicaSetSize** (的服務，或針對無狀態服務， **InstanceCount**) 值為5。 複本將會落在 N1-N5 上。 事實上，不論建立多少像這樣的服務，都不會用到 N6。 原因為何？ 讓我們看看目前的配置和選擇 N6 時情況如何之間的差異。

以下是我們的配置，以及每個容錯網域和升級網域的複本總數：

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

此配置會在每個容錯網域和升級網域的節點方面進行平衡。 它也會根據每個容錯網域和升級網域的複本數目進行平衡。 每個網域都擁有相同數量的節點，以及相同數量的複本。

現在，讓我們看看改用 N6 而不使用 N2 的情況。 複本將會如何散佈？

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

此配置違反容錯網域條件約束的「最大差異」保證定義。 FD0 有兩個複本，而 FD1 則為零。 FD0 和 FD1 之間的差異總計為二，大於1的最大差異。 因為違反了條件約束，叢集資源管理員不允許這種相片順序。

同樣地，如果我們挑選 N2 和 N6 (而不是 N1 和 N2) ，我們會得到：

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

此配置會根據容錯網域進行平衡。 但現在它違反了升級網域的條件約束，因為 UD0 有零個複本，而 UD1 有兩個複本。 此配置也無效，叢集資源管理員不會挑選。

這個分配具狀態複本或無狀態執行個體的方法提供了可能的最佳容錯效果。 如果某個網域停止運作，就會遺失最少的複本/實例數目。

另一方面，此方法則可能太過嚴苛，而不允許叢集利用所有資源。 就某些叢集設定而言，會無法使用某些節點。 這可能會導致 Service Fabric 不會放置您的服務，因此會產生警告訊息。 在上述範例中，某些叢集節點無法在範例)  (N6 中使用。 即使您已將節點新增至該叢集 (N7 N10) ，複本/實例只會放置在 N1 – N5，因為容錯網域和升級網域的限制。

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |

### <a name="alternative-approach"></a>替代方法

叢集資源管理員針對容錯網域和升級網域支援另一個版本的條件約束。 它可允許放置，同時仍然保證最基本的安全性層級。 覆寫準則約束的陳述如下：「針對特定的服務分割區，跨網域的複本散發應該確保分割區不會遭受仲裁遺失」。 假設此條件約束提供「仲裁安全」保證。

> [!NOTE]
> 就具狀態服務而言，我們將「仲裁遺失」** 定義為一種大多數分割區複本都同時停止運作的情況。 例如，如果 **TargetReplicaSetSize** 是五，則任何三個複本的集合都會表示仲裁。 同樣地，如果 **TargetReplicaSetSize** 為6，仲裁就需要四個複本。 在這兩種情況下，如果分割區想要繼續正常運作，則不能同時關閉兩個以上的複本。
>
> 若為無狀態服務，就不會有 *仲裁遺失*。 即使大部分的實例同時關閉，無狀態服務仍會繼續正常運作。 因此，我們會將焦點放在本文的其餘部分中的具狀態服務。
>

讓我們回到先前的範例。 使用「仲裁安全」版本的條件約束，所有三種配置都有效。 即使第二個配置中的 FD0 失敗，或第三個配置中的 UD1 失敗，資料分割仍會具有仲裁。  (大部分的複本仍會啟動。 ) 這一版的條件約束，N6 幾乎都可以使用。

「仲裁安全」方法提供比「最大差異」方法更多的彈性。 原因是，您可以更輕鬆地找到幾乎任何叢集拓撲中有效的複本散發套件。 不過，此方法無法保證最佳容錯特性，因為有些錯誤比其他錯誤來得嚴重。

在最糟的情況下，可能會因為一個網域和一個額外複本的失敗而遺失大部分的複本。 例如，不需要三個失敗就能遺失具有五個複本或實例的仲裁，您現在可以在發生兩次失敗時遺失多數。

### <a name="adaptive-approach"></a>調適型方法

由於這兩種方法都有優缺點，因此我們引進了結合這兩種策略的調適型方法。

> [!NOTE]
> 這是從 Service Fabric 6.2 版開始的預設行為。
>
> 調適型方法預設會使用「差異上限」邏輯，而只有在必要時，才會切換至「仲裁防護」邏輯。 叢集資源管理員藉由查看叢集和服務的設定方式，自動找出所需的策略。
>
> 叢集資源管理員應該針對下列兩個條件都成立的服務使用「仲裁型」邏輯：
>
> * 服務的**TargetReplicaSetSize**可平均地除以容錯網域數目和升級網域數目。
> * 節點數目小於或等於容錯網域數目乘以升級網域的數目。
>
> 請記住，叢集資源管理員會針對無狀態和具狀態服務使用這種方法，即使仲裁遺失與無狀態服務無關也是一樣。

讓我們回到先前的範例，並假設叢集現在有八個節點。 叢集仍設定有五個容錯網域和五個升級網域，而該叢集上裝載之服務的 **TargetReplicaSetSize** 值仍維持五個。

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

由於滿足所有必要的條件，叢集資源管理員將會使用「以仲裁為基礎」的邏輯來發佈服務。 這可讓您使用 N6-N8。 在此情況下，其中一個可能的服務散發如下所示：

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

如果您服務的 **TargetReplicaSetSize** 值縮減為四 (例如) ，叢集資源管理員會注意到該變更。 它會繼續使用「最大差異」邏輯，因為 **TargetReplicaSetSize** 不會再由容錯網域和升級網域的數目 dividable。 如此一來，將會發生某些複本的移動，以在節點 N1-N5 上散發其餘四個複本。 如此一來，就不會違反容錯網域和升級網域邏輯的「最大差異」版本。

在先前的版面配置中，如果 **TargetReplicaSetSize** 值為5，且已從叢集中移除 N1，升級網域的數目就會等於4。 同樣地，叢集資源管理員會開始使用「最大差異」邏輯，因為升級網域的數目不再平均地分割服務的 **TargetReplicaSetSize** 值。 如此一來，當複本 R1 重新建立時，就必須在 N4 上，才不會違反容錯網域和升級網域的條件約束。

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |N/A |N/A |N/A |N/A |N/A |N/A |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

## <a name="configuring-fault-and-upgrade-domains"></a>設定容錯和升級網域

在 Azure 託管的 Service Fabric 部署中，會自動定義容錯網域和升級網域。 Service Fabric 會從 Azure 中取用環境資訊。

如果您要建立自己的叢集 (或想要在開發) 中執行特定拓撲，您可以自行提供容錯網域和升級網域資訊。 在此範例中，我們定義了九個節點的本機開發叢集，橫跨三個資料中心， (各自有三個機架) 。 此叢集也有三個在這三個資料中心之間劃分的升級網域。 以下是 ClusterManifest.xml 中的設定範例：

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

此範例會針對獨立部署使用 ClusterConfig.json：

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
> 當您透過 Azure Resource Manager 定義叢集時，Azure 會指派容錯網域和升級網域。 因此，您的 Azure Resource Manager 範本中的節點類型和虛擬機器擴展集的定義不包含容錯網域或升級網域的相關資訊。
>

## <a name="node-properties-and-placement-constraints"></a>節點屬性和放置條件約束

有時候 (事實上，大部分的情況下) 您會想要確保特定工作負載只在叢集中的特定節點類型上執行。 例如，某些工作負載可能需要 Gpu 或 Ssd，而其他工作負載可能不需要。

針對特定工作負載，將硬體設為目標的絕佳範例幾乎是每個多層式架構。 某些機器可作為應用程式的前端或 API 服務端，並公開給用戶端或網際網路。 其他電腦 (通常有不同的硬體資源) 處理計算層或儲存層的工作。 它們通常_不會_直接公開至用戶端或網際網路。

Service Fabric 預期在某些情況下，特定的工作負載可能需要在特定硬體設定上執行。 例如：

* 現有的多層式應用程式已「提升並轉移」到 Service Fabric 環境中。
* 工作負載必須在特定硬體上執行，以達到效能、規模或安全性隔離的原因。
* 工作負載應該與其他工作負載隔離，以達到原則或資源耗用量原因。

為了支援這類設定，Service Fabric 包含可套用至節點的標記。 這些標記稱為*節點屬性*。 *放置條件約束* 是附加至您為一或多個節點屬性選取的個別服務的語句。 放置條件約束會定義應該執行服務的位置。 這組條件約束是可擴充的。 任何索引鍵/值組都可以運作。

<center>
![叢集配置的不同工作負載][Image5]
</center>

### <a name="built-in-node-properties"></a>內建節點屬性

Service Fabric 定義可自動使用的一些預設節點屬性，因此您不需要定義它們。 在每個節點上定義的預設屬性為 **NodeType** 和 **NodeName**。

例如，您可以將放置條件約束撰寫為 `"(NodeType == NodeType03)"` 。 **NodeType** 是經常使用的屬性。 這項功能很有用，因為它會與一部機器的型別對應1:1。 每個機器類型都對應至傳統多層式架構應用程式中的工作負載類型。

<center>
![放置條件約束和節點屬性][Image6]
</center>

## <a name="placement-constraints-and-node-property-syntax"></a>放置條件約束和節點屬性語法

節點屬性中指定的值可以是字串、布林值或帶正負號的 long。 服務的語句稱為放置 *條件約束* ，因為它會限制服務可在叢集中執行的位置。 條件約束可以是在叢集中的節點屬性上操作的任何布林值語句。 這些布林值語句中的有效選取器如下：

* 建立特定語句的條件式檢查：

  | 陳述式 | 語法 |
  | --- |:---:|
  | 「等於」 | "==" |
  | 「不等於」 | "!=" |
  | 「大於」 | ">" |
  | 「大於或等於」 | ">=" |
  | 「小於」 | "<" |
  | 「小於或等於」 | "<=" |

* 分組和邏輯作業的布林值語句：

  | 陳述式 | 語法 |
  | --- |:---:|
  | 「和」 | "&&" |
  | 「或」 | "&#124;&#124;" |
  | 「否」 | "!" |
  | 「組成單一陳述式」 | "()" |

以下是基本條件約束語句的一些範例：

* `"Value >= 5"`
* `"NodeColor != green"`
* `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

服務只能放置在整體放置條件約束陳述式評估為 "True" 的節點上。 未定義屬性的節點不符合包含該屬性的任何放置條件約束。

假設已針對 ClusterManifest.xml 中的節點類型定義下列節點屬性：

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

下列範例顯示透過 ClusterConfig.json 針對獨立部署定義的節點屬性，或針對裝載于 Azure 的叢集的 Template.js。

> [!NOTE]
> 在您的 Azure Resource Manager 範本中，節點類型通常是參數化的。 它看起來會像這樣， `"[parameters('vmNodeType1Name')]"` 而不是 NodeType01。
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

您可以針對服務建立服務放置 *條件約束* ，如下所示：

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

如果 NodeType01 的所有節點都有效，您也可以選取具有條件約束的節點類型 `"(NodeType == NodeType01)"` 。

服務的放置條件約束可在執行時間動態更新。 如有需要，您可以在叢集中移動服務、新增和移除需求等等。 Service Fabric 可確保即使在進行這些類型的變更時，服務仍保持啟動並可供使用。

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

```PowerShell
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

放置條件約束是針對每個命名服務實例指定的。 更新一律會取代 (覆寫) 先前指定的項目。

叢集定義會定義節點上的屬性。 變更節點的屬性需要升級到叢集設定。 升級節點的屬性需要每個受影響的節點重新啟動，以報告其新的屬性。 Service Fabric 管理這些輪流升級。

## <a name="describing-and-managing-cluster-resources"></a>描述和管理叢集資源

任何 Orchestrator 的其中一個最重要的作業是協助管理叢集中的資源耗用量。 管理叢集資源可以表示幾個不同的項目。

首先，確保電腦不會多載。 這表示確定電腦不會執行比它們能夠處理還多的服務。

其次，有了平衡和優化，這對於有效率地執行服務相當重要。 符合成本效益或效能敏感性的服務供應專案不允許某些節點經常性存取，有些節點則是冷。 經常性節點會導致資源爭用和效能不佳。 冷節點代表浪費的資源，並增加成本。

Service Fabric 將資源表示為 *度量*。 度量是您想要向 Service Fabric 描述的任何邏輯或實體資源。 計量範例為 "WorkQueueDepth" 或 "MemoryInMb"。 如需 Service Fabric 可以在節點上管理之實體資源的詳細資訊，請參閱 [資源管理](service-fabric-resource-governance.md)。 如需叢集資源管理員所使用的預設計量以及如何設定自訂度量的詳細資訊，請參閱 [這篇文章](service-fabric-cluster-resource-manager-metrics.md)。

計量不同于放置條件約束和節點屬性。 節點屬性是節點本身的靜態描述項。 計量會描述節點所擁有的資源，以及服務在節點上執行時所耗用的資源。 節點屬性可能會 **HasSSD** ，而且可能設定為 true 或 false。 該 SSD 上的可用空間量，以及服務所耗用的空間，將會是 "DriveSpaceInMb" 之類的度量。

就像放置條件約束和節點屬性一樣，Service Fabric 叢集資源管理員不會瞭解度量的名稱。 計量名稱只是字串。 將單位宣告為計量名稱的一部分（當這些單位可能不明確時），是很好的做法。

## <a name="capacity"></a>Capacity

如果您關閉所有的資源 *平衡*，Service Fabric 叢集資源管理員仍會確定沒有任何節點超出其容量。 除非叢集已滿或工作負載大於任何節點，否則管理容量溢位是可能的。 容量是另一項 *限制* ，叢集資源管理員用來瞭解節點有多少資源。 另外也會追蹤整個叢集的剩餘容量。

服務層級的容量和耗用量均以度量來表示。 例如，計量可能是 "ClientConnections"，而節點的 "ClientConnections" 容量可能是32768。 其他節點可以有其他限制。 在該節點上執行的服務，可以說它目前正在耗用32256的計量 "ClientConnections"。

在執行時間期間，叢集資源管理員會追蹤叢集中和節點上的剩餘容量。 若要追蹤容量，叢集資源管理員從服務執行所在的節點容量減去每個服務的使用量。 利用這種資訊，叢集資源管理員可以找出要放置或移動複本的位置，讓節點不會超出容量。

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

您可以看到叢集資訊清單中定義的容量。 以下是 ClusterManifest.xml 的範例：

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

以下是透過 ClusterConfig.js開啟的容量範例，適用于獨立部署或適用于 Azure 託管叢集的 Template.js：

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

服務的負載通常會動態變更。 假設複本的「ClientConnections」負載已從1024變更為2048。 它執行所在的節點之後，該計量的容量只剩下512。 現在複本或實例的放置無效，因為該節點上沒有足夠的空間。 叢集資源管理員必須讓節點回到容量以下。 它會藉由將一或多個複本或實例從該節點移至其他節點，來減少超過容量之節點的負載。

叢集資源管理員會嘗試將移動複本的成本降至最低。 您可以深入瞭解 [移動成本](service-fabric-cluster-resource-manager-movement-cost.md) ，以及重新 [平衡策略和規則](service-fabric-cluster-resource-manager-metrics.md)。

## <a name="cluster-capacity"></a>叢集容量

Service Fabric 如何叢集資源管理員保持整體叢集太滿？ 有了動態載入，就不會有很多。 服務可能會使負載尖峰，而不受叢集資源管理員所採取的動作影響。 如此一來，如果未來有大量的空間，您的叢集可能會動能不足。

叢集資源管理員中的控制項可協助防止問題。 您可以做的第一件事，是防止建立新的工作負載，而導致叢集變成已滿。

假設您建立無狀態服務，而且它有一些相關聯的負載。 服務會在意「DiskSpaceInMb」度量。 服務的每個實例都會耗用五個單位的 "DiskSpaceInMb"。 您想要建立服務的三個執行個體。 這表示您需要在叢集中有15個單位的 "DiskSpaceInMb"，讓您甚至可以建立這些服務實例。

叢集資源管理員會持續計算每個度量的容量和耗用量，讓它可以判斷叢集中的剩餘容量。 如果沒有足夠的空間，叢集資源管理員會拒絕建立服務的呼叫。

由於需求只有15個單位可供使用，因此您可以透過許多不同的方式來配置此空間。 例如，在15個不同節點上可能會有一個剩餘的容量單位，或五個不同節點上的三個剩餘容量單位。 如果叢集資源管理員可以重新排列專案，讓三個節點上有五個可用的單位，則會放置服務。 重新安排叢集通常都可行，除非叢集幾乎已滿，或因為某些原因致使現有服務無法合併。

## <a name="node-buffer-and-overbooking-capacity"></a>節點緩衝區和 overbooking 容量

如果指定計量的節點容量，則如果總負載會高於指定的節點容量，叢集資源管理員將永遠不會將複本放置或移至節點。 如果叢集接近完整容量，而且必須放置、取代或移動具有大量負載的複本，這有時候可能會導致無法放置新複本或取代失敗的複本。

為了提供更大的彈性，您可以指定節點緩衝區或 overbooking 容量。 針對度量指定節點緩衝區或 overbooking 容量時，叢集資源管理員會嘗試以緩衝區或 overbooking 容量保持未使用的方式來放置或移動複本，但如果動作會增加服務可用性（例如，），則允許使用緩衝區或 overbooking 容量：

* 新的複本放置或取代失敗的複本
* 升級期間的位置
* 修正軟和硬性條件約束違規
* 碎片 整理

節點緩衝區容量代表低於指定節點容量的容量保留部分，而 overbooking 容量則代表超過指定節點容量的額外容量部分。 在這兩種情況下，叢集資源管理員都會嘗試保持此容量可用。

例如，如果節點具有指定的 *CpuUtilization* 為100的容量，且該計量的節點緩衝區百分比設定為20%，則總計和未緩衝的容量將會分別是100和80，而且在正常情況下，叢集資源管理員不會將超過80單位的負載放在節點上。

![總容量等於節點容量 (節點緩衝區 + 未緩衝) ](./media/service-fabric-cluster-resource-manager-cluster-description/node-capacity.png)

當您想要保留節點容量的一部分，而該部分只會用於增加上述服務可用性的動作時，應該使用節點緩衝區。

另一方面，如果使用 node overbooking 百分比並設定為20%，則總計和無緩衝的容量將分別為120和100。

![總容量等於 overbooking 容量加上節點容量 (Overbooking + 未緩衝) ](./media/service-fabric-cluster-resource-manager-cluster-description/node-capacity-with-overbooking.png)

當您想要允許叢集資源管理員將複本放在節點上，即使其總資源使用量超過容量，也應使用 Overbooking 容量。 這可以用來提供服務的額外可用性（代價是效能）。 如果使用 overbooking，則使用者應用程式邏輯必須能夠處理的實體資源可能比可能需要的少。

如果指定了節點緩衝區或 overbooking 容量，叢集資源管理員將不會移動或放置複本，如果在節點緩衝區和節點容量的總容量 (節點容量，以及 overbooking) 的情況下，則不會移動複本。

Overbooking 容量也可以指定為無限。 在此情況下，叢集資源管理員會嘗試將節點的總負載保留在指定的節點容量之下，但允許在節點上放置遠較大的負載，而這可能會導致嚴重的效能降低。

計量不能同時指定節點緩衝區和 overbooking 的容量。

以下是如何在 *ClusterManifest.xml*中指定節點緩衝區或 overbooking 容量的範例：

```xml
<Section Name="NodeBufferPercentage">
    <Parameter Name="SomeMetric" Value="0.15" />
</Section>
<Section Name="NodeOverbookingPercentage">
    <Parameter Name="SomeOtherMetric" Value="0.2" />
    <Parameter Name=”MetricWithInfiniteOverbooking” Value=”-1.0” />
</Section>
```

以下範例將說明如何透過 *ClusterConfig.json* 來指定節點緩衝區或 overbooking 容量，以進行獨立部署或針對 Azure 裝載的叢集 *Template.json* ：

```json
"fabricSettings": [
  {
    "name": "NodeBufferPercentage",
    "parameters": [
      {
          "name": "SomeMetric",
          "value": "0.15"
      }
    ]
  },
  {
    "name": "NodeOverbookingPercentage",
    "parameters": [
      {
          "name": "SomeOtherMetric",
          "value": "0.20"
      },
      {
          "name": "MetricWithInfiniteOverbooking",
          "value": "-1.0"
      }
    ]
  }
]
```

## <a name="next-steps"></a>後續步驟

* 如需叢集資源管理員內的架構和資訊流程的相關資訊，請參閱 [叢集資源管理員架構總覽](service-fabric-cluster-resource-manager-architecture.md)。
* 定義重組計量是合併節點上的負載，而不是分散的方式之一。若要瞭解如何設定磁碟重組，請參閱 [Service Fabric 中的計量和負載磁碟重組](service-fabric-cluster-resource-manager-defragmentation-metrics.md)。
* 從頭開始著手，並 [取得 Service Fabric 叢集資源管理員的簡介](service-fabric-cluster-resource-manager-introduction.md)。
* 若要瞭解叢集資源管理員如何管理和平衡叢集中的負載，請參閱 [平衡您的 Service Fabric](service-fabric-cluster-resource-manager-balancing.md)叢集。

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
