---
title: 使用群集資源管理器描述群集
description: 通過為群集資源管理器指定容錯域、升級域、節點屬性和節點容量來描述服務結構群集。
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 7142e3f9aaa25e7ba327194c04ad6a9b5f4e3ad1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258768"
---
# <a name="describe-a-service-fabric-cluster-by-using-cluster-resource-manager"></a>使用群集資源管理器描述服務結構群集
Azure 服務結構的群集資源管理器功能提供了幾種用於描述群集的機制：

* 容錯網域
* 升級網域
* 節點屬性
* 節點容量

在運行時，群集資源管理器使用此資訊來確保群集中運行的服務的高可用性。 在執行這些重要規則的同時，它還嘗試優化群集中的資源消耗。

## <a name="fault-domains"></a>容錯網域
容錯網域是協調失敗的任何區域。 單台電腦是故障域。 由於各種原因，它可以自行失敗，從電源故障到磁碟機故障，到 NIC 固件損壞。 

連接到同一乙太網交換器的電腦位於同一故障域中。 共用單一電源或位於單個位置的電腦也是如此。 

由於硬體故障重疊是自然的，因此故障域本質上是階層式。 它們在服務結構中表示為 URI。

正確設置容錯域非常重要，因為 Service Fabric 使用此資訊安全地放置服務。 Service Fabric 不希望放置服務，以便故障域丟失（由某些元件故障引起）導致服務停機。 

在 Azure 環境中，Service Fabric 使用環境提供的容錯域資訊代表您正確配置群集中的節點。 對於 Service Fabric 的獨立實例，在設置群集時定義容錯域。 

> [!WARNING]
> 向 Service Fabric 提供的故障域資訊準確非常重要。 例如，假設您的服務 Fabric 群集的節點在 10 台虛擬機器內運行，在 5 個物理主機上運行。 在此情況下，即使有 10 部虛擬機器，也只有 5 個不同的 (最上層) 容錯網域。 共用同一物理主機會導致 VM 共用同一根容錯域，因為如果 VM 的物理主機發生故障，則 VM 會遇到協調故障。  
>
> 服務交換矩陣希望節點的容錯域不會更改。 確保 VM 高可用性的其他機制（如[HA-VM）](https://technet.microsoft.com/library/cc967323.aspx)可能會導致與服務結構衝突。 這些機制使用 VM 從一個主機到另一個主機的透明遷移。 它們不會重新配置或通知 VM 內正在運行的代碼。 因此，它們*不支援*作為運行 Service Fabric 群集的環境。 
>
> Service Fabric 應該僅採用高可用性技術。 不需要即時 VM 遷移和 SAN 等機制。 如果這些機制與 Service Fabric 結合使用，則會_降低_應用程式的可用性和可靠性。 原因是它們引入了額外的複雜性，增加了集中式故障源，並使用與 Service Fabric 中的策略相衝突的可靠性和可用性策略。 
>
>

在下圖中，我們對所有對容錯域作出貢獻的實體進行著色，並列出所有不同的容錯域。 在此範例中，我們有資料中心 ("DC")、機架 ("R") 和刀鋒伺服器 ("B")。 如果每個邊欄選項卡包含多個虛擬機器，則故障域層次結構中可能還有另一層。

<center>

![通過容錯域組織的節點][Image1]
</center>

在運行時，服務結構群集資源管理器會考慮群集中的容錯域並規劃佈局。 服務的有狀態副本或無狀態實例被分發，因此它們位於單獨的容錯域中。 跨容域分發服務可確保當層次結構的任何級別發生故障時，服務的可用性不會受到損害。

群集資源管理器不關心故障域層次結構中有多少層。 它努力確保層次結構的任何一部分的丟失不會影響在其中運行的服務。 

最好在容域層次結構中每個深度級別具有相同的節點數。 如果群集中的容錯域的"樹"不平衡，群集資源管理器將更難確定服務的最佳分配。 不平衡的容錯域佈局意味著某些域的丟失比其他域對服務的可用性的影響更大。 因此，群集資源管理器在兩個目標之間被撕裂： 

* 它希望通過在"重"域中放置服務來使用電腦。 
* 它希望將服務放在其他域中，以便域的丟失不會導致問題。 

不平衡網域外觀為何？ 下圖顯示了兩個不同的群集佈局。 在第一個示例中，節點均勻分佈在容錯域中。 在第二個示例中，一個容錯域的節點比其他容錯域多得多。 

<center>

![兩種不同的群集佈局][Image2]
</center>

在 Azure 中，管理哪個容錯域包含節點的選擇。 但是，根據預配的節點數，您最終仍可能擁有比其他節點更多的容錯域。 

例如，假設您在群集中有五個容錯域，但為節點類型 **（NodeType）** 預配了七個節點。 在這種情況下，前兩個容錯域最終具有更多的節點。 如果繼續部署更多的**NodeType**實例，並且只有幾個實例，問題會變得更糟。 因此，我們建議每個節點類型的節點數是容錯域數的倍數。

## <a name="upgrade-domains"></a>升級網域
升級域是説明服務結構群集資源管理器瞭解群集佈局的另一項功能。 升級域定義同時升級的節點集。 升級域可説明群集資源管理器瞭解和協調管理操作（如升級）。

升級域與容錯域很像，但有幾個關鍵差異。 首先，協調的硬體故障區域定義容錯域。 另一方面，升級域由策略定義。 您可以決定要多少，而不是讓環境決定數位。 可以具有與節點一樣多的升級域。 容錯域和升級域之間的另一個區別是升級域不是階層式。 相反，它們更像是一個簡單的標記。 

下圖顯示了三個跨三個容錯域條帶化的升級域。 它還顯示一個狀態服務的三個不同副本的可能位置，其中每個副本都位於不同的容錯域和升級域中。 此放置允許在服務升級期間丟失容錯域，並且仍然具有一個代碼和資料的副本。  

<center>

![放置與故障和升級域][Image3]
</center>

擁有大量升級域是有利弊的。 升級域越多，升級的每個步驟都更加精細，並影響較少的節點或服務。 一次需要移動的服務更少，給系統帶來更少的流失。 這往往會提高可靠性，因為升級期間引入的任何問題對服務的影響較少。 更多的升級域還意味著您需要更少的可用緩衝區在其他節點上來處理升級的影響。 

例如，如果您有五個升級域，則每個域中的節點處理大約 20% 的流量。 如果需要關閉該升級域進行升級，則該負載通常需要轉到某處。 由於您還有四個升級域，因此每個域必須具有總流量的 5% 的空間。 更多的升級域意味著群集中的節點上需要的緩衝區更少。 

請考慮您是否有 10 個升級域。 在這種情況下，每個升級域將僅處理大約 10% 的總流量。 當升級通過群集時，每個域只需要留出大約 1.1% 的總流量的空間。 升級域越多，通常允許您以更高的利用率運行節點，因為您需要較少的預留容量。 故障域也是如此。  

擁有許多升級域的缺點是升級往往需要更長的時間。 Service Fabric 在升級域完成後等待一小段時間，並在開始升級下一個域之前執行檢查。 這些延遲可讓系統在升級繼續之前偵測由升級帶來的問題。 缺點是可接受的，因為它會防止不良的變更一次影響到太多服務。

升級域太少的存在有許多負面的副作用。 當每個升級域都關閉並正在升級時，您的總體容量有很大一部分不可用。 例如，如果您只有三個升級域，則一次將佔用大約三分之一的總體服務或群集容量。 同時關閉這麼多服務是不可取的，因為群集的其餘部分需要足夠的容量來處理工作負載。 維護該緩衝區意味著在正常操作期間，這些節點的載入速度比其他節點少。 這會增加執行服務的成本。

環境中容錯或升級網域的總數沒有實際限制，對於它們如何重疊也沒有條件約束。 但也有常見的模式：

- 映射為 1：1 的故障域和升級域
- 每個節點一個升級域（物理或虛擬作業系統實例）
- 一個"條紋"或"矩陣"模型，其中容錯域和升級域形成一個矩陣，機器通常運行在對角線上

<center>

![故障和升級域的佈局][Image4]
</center>

沒有最佳答案選擇哪個佈局。 各有其優缺點。 例如，1FD:1UD 模型相當容易設定。 每個節點模型的一個升級域的模型與人們習慣的最類似。 在升級期間，每個節點將獨立更新。 這類似於以往手動升級小型集合機器的方式。

最常見的模型是 FD/UD 矩陣，其中容錯域和升級域組成一個表，節點沿對角線開始放置。 這是在 Azure 中的 Service Fabric 叢集預設使用的模型。 對於具有許多節點的群集，所有內容最終都看起來像一個密集的矩陣模式。

> [!NOTE]
> Azure 中託管的服務結構群集不支援更改預設策略。 只有獨立群集提供該自訂。
>

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>容錯和升級網域條件約束及產生的行為
### <a name="default-approach"></a>預設方法
預設情況下，群集資源管理器使跨容錯域和升級域的服務保持平衡。 這會經由模型化成為[條件約束](service-fabric-cluster-resource-manager-management-integration.md)。 容錯和升級域的約束規定："對於給定的服務分區，同一上的任意兩個域之間的服務物件數（無狀態服務實例或有狀態服務副本）不應有大於 1 的差別等級水準。

假設此約束提供了"最大差異"保證。 故障域和升級域的約束可防止某些違反規則的移動或安排。

例如，假設我們有一個包含六個節點的群集，配置了五個容錯域和五個升級域。

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

現在，假設我們創建一個服務，其**目標複製集大小**（或者，對於無狀態服務，**實例計數**）值為 5。 複本將會落在 N1-N5 上。 事實上，不論建立多少像這樣的服務，都不會用到 N6。 原因為何？ 讓我們看看目前的配置和選擇 N6 時情況如何之間的差異。

以下是我們得到的佈局以及每個故障和升級域的副本總數：

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

此佈局在每個容錯域和升級域的節點方面是平衡的。 它還在每個故障和升級域的副本數量方面處於平衡。 每個網域都擁有相同數量的節點，以及相同數量的複本。

現在，讓我們看看改用 N6 而不使用 N2 的情況。 複本將會如何散佈？

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

此佈局違反了我們對容錯域約束的"最大差異"保證的定義。 FD0 有兩個副本，而 FD1 為零。 FD0 和 FD1 之間的差值為 2，大於 1 的最大差值。 由於違反了約束，群集資源管理器不允許這種安排。 同樣，如果我們選擇 N2 和 N6（而不是 N1 和 N2），我們將得到：

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

此佈局在容錯域方面是平衡的。 但現在它違反了升級域約束，因為 UD0 沒有副本，UD1 有兩個副本。 此佈局也無效，群集資源管理器不會選擇。

這個分配具狀態複本或無狀態執行個體的方法提供了可能的最佳容錯效果。 如果一個域出現故障，則丟失最少的副本/實例數。 

另一方面，此方法則可能太過嚴苛，而不允許叢集利用所有資源。 就某些叢集設定而言，會無法使用某些節點。 這可能會導致 Service Fabric 不放置服務，從而導致警告訊息。 在前面的示例中，某些叢集節點不能使用（示例中為 N6）。 即使您向該群集 （N7-N10） 添加了節點，由於故障和升級域的限制，副本/實例將僅放置在 N1_N5 上。 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |



### <a name="alternative-approach"></a>替代方法

群集資源管理器支援故障和升級域的約束的另一個版本。 它允許放置，同時仍然保證最低安全級別。 替代約束可以如下："對於給定的服務分區，跨域的複製副本分發應確保分區不會遭受仲裁損失。 假設此約束提供了"仲裁安全"保證。 

> [!NOTE]
> 就具狀態服務而言，我們將「仲裁遺失」** 定義為一種大多數分割區複本都同時停止運作的情況。 例如，如果**TargetReplicaSetSize 為**5，則任意三個副本的一組表示仲裁。 同樣，如果**TargetReplicaSetSize 為**6，則需要四個副本來達到仲裁狀態。 在這兩種情況下，如果分區想要繼續正常運行，則只能同時關閉兩個副本。 
>
> 對於一個無國籍的服務，不存在*法定人數損失*。 即使大多數實例同時關閉，無狀態服務仍繼續正常運行。 因此，我們將在本文的其餘部分中重點介紹有狀態服務。
>

讓我們回到先前的範例。 使用約束的"仲裁安全"版本，所有三個佈局都將有效。 即使 FD0 在第二個佈局中失敗，或者第三個佈局中的 UD1 失敗，分區仍將具有仲裁。 （大多數副本仍然會打開。使用此版本的約束，N6 幾乎總是可以使用。

與"最大差異"方法相比，"仲裁安全"方法提供了更大的靈活性。 原因是，在幾乎任何群集拓撲中查找有效的副本分發更容易。 不過，此方法無法保證最佳容錯特性，因為有些錯誤比其他錯誤來得嚴重。 

在最壞的情況下，大多數副本可能會因一個域和一個附加副本的故障而丟失。 例如，現在只需兩個失敗，即可失去五個副本或實例的仲裁，而不是需要三個失敗。 

### <a name="adaptive-approach"></a>調適型方法
由於兩種方法都有優點和劣勢，因此我們引入了一種將這兩種策略相結合的自我調整方法。

> [!NOTE]
> 這是從服務結構版本 6.2 開始的預設行為。 
> 
> 調適型方法預設會使用「差異上限」邏輯，而只有在必要時，才會切換至「仲裁防護」邏輯。 群集資源管理器通過查看群集和服務的配置方式，自動找出需要的策略。
> 
> 群集資源管理器應對服務使用"基於仲裁"的邏輯，這兩個條件都是正確的：
>
> * 服務**的目標複本集大小**被容錯域數和升級域數均勻地分割。
> * 節點數小於或等於容錯域數乘以升級域數。
>
> 請記住，群集資源管理器將此方法同時用於無狀態和有狀態服務，即使仲裁丟失與無狀態服務無關。

讓我們回到前面的示例，假設群集現在具有八個節點。 群集仍配置了五個容錯域和五個升級域，而該群集上託管的服務的目標**複製集大小**值仍為 5 個。 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

由於滿足所有必要條件，群集資源管理器將在分發服務時使用"基於仲裁"的邏輯。 這允許使用 N6-N8。 在這種情況下，一個可能的服務分發可能如下所示：

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

如果服務**的目標複本集大小**值減少到四個（例如），群集資源管理器將注意到該更改。 它將使用"最大差異"邏輯繼續使用，因為**TargetReplicaSetSize**不再可以通過容錯域和升級域的數量來分。 因此，將發生某些副本移動，以在節點 N1-N5 上分發剩餘的四個副本。 這樣，容錯域和升級域邏輯的"最大差異"版本就不會被違反。 

在上一個佈局中，如果**TargetReplicaSetSize**值為 5，並且從群集中刪除 N1，則升級域數將等於 4。 同樣，群集資源管理器開始使用"最大差異"邏輯，因為升級域的數量不再均勻地劃分服務的**TargetReplicaSetSize**值。 因此，複製副本 R1 在再次生成時必須降落在 N4 上，以便不違反故障和升級域的約束。

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |N/A |N/A |N/A |N/A |N/A |N/A |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

## <a name="configuring-fault-and-upgrade-domains"></a>設定容錯和升級網域
在 Azure 託管的服務結構部署中，將自動定義容錯域和升級域。 Service Fabric 會從 Azure 中取用環境資訊。

如果要創建自己的群集（或希望在開發中運行特定拓撲），則可以提供容錯域並自行升級域資訊。 在此示例中，我們定義了一個跨越三個資料中心（每個資料中心有三個機架）的九節點本地開發群集。 此群集還有三個跨這三個資料中心的升級域。 下面是 ClusterManifest.xml 中的配置示例：

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

此示例將 ClusterConfig.json 用於獨立部署：

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
> 通過 Azure 資源管理器定義群集時，Azure 會分配容錯域和升級域。 因此，Azure 資源管理器範本中節點類型和虛擬機器縮放集的定義不包括有關故障域或升級域的資訊。
>

## <a name="node-properties-and-placement-constraints"></a>節點屬性和放置條件約束
有時（事實上，大多數時候），您需要確保某些工作負載僅在群集中的某些類型的節點上運行。 例如，某些工作負載可能需要 GPU 或 SSD，而其他工作負載可能不需要。 

將硬體定位到特定工作負載的一個很好的例子是幾乎每個 n 層體系結構。 某些電腦充當應用程式的前端或 API 服務端，並暴露在用戶端或互聯網上。 其他電腦 (通常有不同的硬體資源) 處理計算層或儲存層的工作。 它們通常_不會_直接公開至用戶端或網際網路。 

Service Fabric 期望在某些情況下，可能需要在特定硬體設定上運行特定工作負載。 例如：

* 現有的 n 層應用程式已"提升並移入"到服務結構環境中。
* 出於性能、規模或安全隔離原因，必須在特定硬體上運行工作負載。
* 出於策略或資源消耗原因，應將工作負載與其他工作負載隔離開來。

為了支援這些類型的配置，Service Fabric 包含可應用於節點的標記。 這些標記稱為*節點屬性*。 *放置約束*是附加到單個服務的語句，您為一個或多個節點屬性選擇這些語句。 放置條件約束會定義應該執行服務的位置。 約束集是可擴展的。 任何鍵/值對都可以工作。 

<center>

![群集佈局的不同工作負載][Image5]
</center>

### <a name="built-in-node-properties"></a>內置節點屬性
Service Fabric 定義了一些可自動使用的預設節點屬性，因此您不必定義它們。 在每個節點上定義的預設屬性是**NodeType**和**NodeName**。 

例如，可以將放置約束寫入`"(NodeType == NodeType03)"`。 **節點類型**是常用屬性。 它很有用，因為它與機器的類型對應 1：1。 每個機器類型都對應至傳統多層式架構應用程式中的工作負載類型。

<center>

![放置約束和節點屬性][Image6]
</center>

## <a name="placement-constraints-and-node-property-syntax"></a>放置約束和節點屬性語法 
節點屬性中指定的值可以是字串、布林或簽名長。 服務中的語句稱為放置*約束*，因為它約束服務在群集中可以運行的位置。 約束可以是對群集中的節點屬性操作的任何布林語句。 這些布林語句中的有效選擇器是：

* 用於創建特定語句的條件檢查：

  | 引數 | 語法 |
  | --- |:---:|
  | 「等於」 | "==" |
  | 「不等於」 | "!=" |
  | 「大於」 | ">" |
  | 「大於或等於」 | ">=" |
  | 「小於」 | "<" |
  | 「小於或等於」 | "<=" |

* 用於分組和邏輯操作的布林語句：

  | 引數 | 語法 |
  | --- |:---:|
  | 「和」 | "&&" |
  | 「或」 | "&#124;&#124;" |
  | 「否」 | "!" |
  | 「組成單一陳述式」 | "()" |

下面是基本約束語句的一些示例：

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

服務只能放置在整體放置條件約束陳述式評估為 "True" 的節點上。 未定義屬性的節點與包含該屬性的任何放置約束不匹配。

假設以下節點屬性為 ClusterManifest.xml 中的節點類型定義：

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

下面的示例顯示通過 ClusterConfig.json 為獨立部署定義的節點屬性，或為 Azure 託管群集定義的 Template.json。 

> [!NOTE]
> 在 Azure 資源管理器範本中，節點類型通常參數化。 它看起來像`"[parameters('vmNodeType1Name')]"`而不是NodeType01。
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

您可以為服務創建服務放置*約束*，如下所示：

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

如果 NodeType01 的所有節點都有效，您還可以選擇具有約束`"(NodeType == NodeType01)"`的節點類型。

服務的位置約束可以在運行時動態更新。 如果需要，可以在群集中移動服務、添加和刪除要求等。 即使進行了這些類型的更改，Service Fabric 也能確保服務保持狀態並可用。

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

```PowerShell
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

為每個命名的服務實例指定放置約束。 更新一律會取代 (覆寫) 先前指定的項目。

叢集定義會定義節點上的屬性。 更改節點的屬性需要升級到群集配置。 升級節點的屬性需要每個受影響的節點重新啟動，以報告其新的屬性。 Service Fabric 管理這些輪流升級。

## <a name="describing-and-managing-cluster-resources"></a>描述和管理群集資源
任何 Orchestrator 的其中一個最重要的作業是協助管理叢集中的資源耗用量。 管理叢集資源可以表示幾個不同的項目。 

首先，確保電腦不會多載。 這表示確定電腦不會執行比它們能夠處理還多的服務。 

其次，有平衡和優化，這對於高效運行服務至關重要。 經濟高效的服務產品不能允許某些節點發熱，而其他節點則冷。 熱節點會導致資源爭用和性能不佳。 冷節點表示資源浪費和成本增加。 

服務結構表示資源為*指標*。 度量是您想要向 Service Fabric 描述的任何邏輯或實體資源。 指標的示例包括"工作佇列深度"或"記憶體 InMb"。 有關 Service Fabric 可以在節點上管理的物理資源的資訊，請參閱[資源治理](service-fabric-resource-governance.md)。 有關群集資源管理器使用的預設指標以及如何配置自訂指標的資訊，請參閱[本文](service-fabric-cluster-resource-manager-metrics.md)。

指標與放置約束和節點屬性不同。 節點屬性是節點本身的靜態描述項。 指標描述節點擁有的資源以及服務在節點上運行時消耗的資源。 節點屬性可能是**HasSSD，** 並且可能設置為 true 或 false。 該 SSD 上的可用空間量以及服務消耗的量將是一個指標，如"DriveSpaceInMb"。 

與放置約束和節點屬性一樣，Service Fabric 群集資源管理器不了解指標的名稱的含義。 計量名稱只是字串。 最好將單位聲明為創建指標名稱的一部分，而當它們可能不明確時。

## <a name="capacity"></a>Capacity
如果關閉所有資源*平衡*，服務結構群集資源管理器仍將確保沒有節點超出其容量。 除非叢集已滿或工作負載大於任何節點，否則管理容量溢位是可能的。 容量是群集資源管理器用來瞭解節點有多少資源的另一個*約束*。 另外也會追蹤整個叢集的剩餘容量。 

服務層級的容量和耗用量均以度量來表示。 例如，指標可能是"用戶端連接"，並且節點的容量可能為 32，768。 其他節點可以有其他限制。 運行在該節點上的服務可以說它當前正在消耗 32，256 個指標"用戶端連接"。

在運行時，群集資源管理器跟蹤群集和節點上的剩餘容量。 為了跟蹤容量，群集資源管理器從運行服務的節點的容量中減去每個服務的用量。 有了此資訊，群集資源管理器可以找出放置或移動副本的位置，以便節點不會超出容量。

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

您可以看到在群集清單中定義的容量。 下面是 ClusterManifest.xml 的示例：

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

下面是通過 ClusterConfig.json 為 Azure 託管群集定義的容量示例： 

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

服務的負載經常動態變化。 假設副本的"用戶端連接"負載從 1，024 更改為 2，048。 然後，它運行的節點僅剩 512 個該指標的容量。 現在副本或實例的位置無效，因為該節點上沒有足夠的空間。 群集資源管理器必須使節點返回容量以下。 通過將一個或多個副本或實例從該節點移動到其他節點，可降低超過容量的節點上的負載。 

群集資源管理器嘗試將移動副本的成本降至最低。 你可以瞭解更多關於[移動成本和](service-fabric-cluster-resource-manager-movement-cost.md)[再平衡策略和規則](service-fabric-cluster-resource-manager-metrics.md)。

## <a name="cluster-capacity"></a>叢集容量
服務結構群集資源管理器如何防止整個群集過滿？ 使用動態負載，它能做的並不多。 服務可以獨立于群集資源管理器執行的操作來顯示其負載峰值。 因此，如果明天出現峰值，則今天具有大量空間的群集可能動力不足。 

群集資源管理器中的控制項有助於防止問題。 您可以做的第一件事是防止創建新的工作負載，這將導致群集已滿。

假設您創建了一個無狀態服務，並且它有一些與之關聯的負載。 該服務關注"磁碟空間 InMb"指標。 該服務將為每個服務的實例消耗五個單位的"DiskSpaceInMb"。 您想要建立服務的三個執行個體。 這意味著，您需要 15 個單位的"DiskSpaceInMb"才能在群集中存在，以便您甚至創建這些服務實例。

群集資源管理器持續計算每個指標的容量和消耗量，以便確定群集中的剩餘容量。 如果沒有足夠的空間，群集資源管理器將拒絕創建服務的調用。

由於要求只有 15 個單位可用，因此可以通過許多不同的方式分配此空間。 例如，15 個不同節點上可能還有一個剩餘容量單元，或者在五個不同節點上剩下三個容量單元。 如果群集資源管理器可以重新排列內容，以便在三個節點上有五個可用單元，則它會放置服務。 重新安排叢集通常都可行，除非叢集幾乎已滿，或因為某些原因致使現有服務無法合併。

## <a name="buffered-capacity"></a>緩衝容量
緩衝容量是群集資源管理器的另一個功能。 它可以保留整體節點容量的某些部分。 此容量緩衝區僅用於在升級和節點故障期間放置服務。 

緩衝容量按每個節點的指標全域指定。 為預留容量選擇的值是群集中具有的容錯和升級域數的函數。 更多的容錯和升級域意味著您可以為緩衝容量選擇較低的數位。 如果您有較多網域，則在升級和失敗期間，無法使用的叢集部分當然會比較少。 僅當還指定指標的節點容量時，指定緩衝容量才有意義。

下面是如何在 ClusterManifest.xml 中指定緩衝容量的示例：

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

下面是如何通過 ClusterConfig.json 為獨立部署或 Azure 託管群集的 Template.json 指定緩衝容量的示例：

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

當叢集耗盡計量的緩衝容量時，建立新的服務會失敗。 防止建立新服務以保留緩衝區，可確保升級和失敗不會造成節點超出容量。 緩衝容量是可選的，但我們建議在定義指標容量的任何群集中使用它。

群集資源管理器公開此負載資訊。 對於每個計量，這項資訊包括： 
- 緩衝容量設置。
- 總容量。
- 當前消耗量。
- 每個指標是否被視為平衡。
- 有關標準差的統計資訊。
- 負載最大且最少的節點。  
  
以下代碼顯示了該輸出的示例：

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
* 有關群集資源管理器中的體系結構和資訊流的資訊，請參閱[群集資源管理器體系結構概述](service-fabric-cluster-resource-manager-architecture.md)。
* 定義磁碟重組指標是整合節點負載而不是將其分散出去的一種方式。要瞭解如何配置磁碟重組，請參閱[服務結構 中的指標和負載磁碟重組](service-fabric-cluster-resource-manager-defragmentation-metrics.md)。
* 從一開始，[獲得服務結構集群資源管理器的介紹](service-fabric-cluster-resource-manager-introduction.md)。
* 要瞭解群集資源管理器如何管理和平衡群集中的負載，請參閱[平衡服務結構群集](service-fabric-cluster-resource-manager-balancing.md)。

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
