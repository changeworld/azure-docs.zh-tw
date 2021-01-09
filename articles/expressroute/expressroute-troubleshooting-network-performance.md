---
title: 針對網路連結效能進行疑難排解： Azure
description: 本頁提供測試 Azure 網路連結效能的標準化方法。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: troubleshooting
ms.date: 01/07/2021
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 35e080e0fe45c18ad6a6d5392e0c78b116853c3e
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98027463"
---
# <a name="troubleshooting-network-performance"></a>網路效能疑難排解
## <a name="overview"></a>概觀
Azure 提供穩定且快速的方式，從您的內部部署網路連線到 Azure。 大型和小型的客戶可以成功使用站對站 VPN 和 ExpressRoute 等方法，在 Azure 中經營其業務。 但是當效能不符合您預期的情況或先前的經驗時，會發生什麼事？ 本文可協助您將測試方式標準化，並為您的特定環境進行基準測試。

您將瞭解如何輕鬆且一致地測試兩部主機之間的網路延遲和頻寬。 您也會收到一些有關如何查看 Azure 網路以協助找出問題點的建議。 討論的 PowerShell 指令碼和工具在網路上 (要測試之連結的任一端) 需要有兩部主機。 一部主機必須是 Windows 伺服器或桌面，另一部主機則可以是 Windows 或 Linux。 

>[!NOTE]
>疑難排解的方法、工具和使用的方法都是個人喜好設定。 本文件說明通常採用的方法和工具。 您的方法可能會有所不同，使用不同的方法解決問題並沒有對錯之分。 不過，如果您沒有一個既定的方法，則可以將本文件當作開始建構自己的方法、工具和喜好設定的途徑，以便為網路問題進行疑難排解。
>
>

## <a name="network-components"></a>網路元件
深入了解疑難排解之前，讓我們先討論一些通用的術語和構成要素。 這個討論可確保我們將考慮端對端鏈結中，可在 Azure 中連線的每個構成要素。
![1][1]

我將說明最高層級的三種主要網路路由網域；

- Azure 網路 (右側的藍色雲)
- 網際網路或 WAN (中央的綠色雲)
- 公司網路 (左側的桃色雲)

由右至左查看圖表之後，讓我們簡短地討論一下每個構成要素：
 - **虛擬機器** -伺服器可能會有多個 nic。 確定任何靜態路由、預設路由和作業系統設定都是以您認為的方式傳送和接收流量。 此外，每個 VM SKU 都有頻寬限制。 如果您要使用較小的 VM SKU，則您的流量會受到 NIC 可用頻寬的限制。 我通常使用 DS5v2 進行測試 (然後在完成測試之後刪除以節省成本) 以確保 VM 有足夠的頻寬。
 - **NIC**：請確定您知道指派給上述 NIC 的私人 IP。
 - **NIC NSG**：可能會有在 NIC 層級套用的特定 NSG，請確定 NSG 規則集適用於您想要通過的流量。 例如，確定已開放連接埠 5201 (用於 iPerf)、3389 (用於 RDP) 或 22 (用於 SSH)，以允許通過測試流量。
 - **VNet 子網路**：NIC 會指派給特定子網路，請確定您知道哪些規則與該子網路相關聯。
 - **子網路 NSG**：就像 NIC 一樣，也可以在子網路套用 NSG。 請確定 NSG 規則集適用於您想要通過的流量  針對輸入至 NIC 的流量 (，子網 NSG 會先套用，接著再套用 NIC NSG。 當流量從 VM 輸出時，會先套用 NIC NSG，然後再將子網 NSG 套用) 。
 - **子網 UDR** User-Defined 路由可將流量導向中繼躍點 (例如防火牆或負載平衡器) 。 確定您知道您的流量是否有 UDR。 如果是的話，請瞭解它的位置，以及下一個躍點將對您的流量採取什麼動作。 例如，防火牆可以通過一些流量，並拒絕相同兩部主機之間的其他流量。
 - **閘道子網路/NSG/UDR**：就像 VM 子網路一樣，閘道子網路可以擁有 NSG 和 UDR。 請確定您知道它們是否存在，以及它們對您的流量有何影響。
 - **VNet 閘道 (ExpressRoute)**：一旦啟用對等互連 (ExpressRoute) 或 VPN 之後，則不會有很多設定可以影響流量如何路由或是否路由。 如果您有連接到多個 ExpressRoute 線路或 VPN 通道的 VNet 閘道，您應該留意連線權數設定。 連接權數會影響連線喜好設定，並決定您的流量所採用的路徑。
 - **路由篩選** (未顯示) -透過 ExpressRoute 使用 Microsoft 對等互連時，需要路由篩選。 如果您未收到任何路由，請檢查路由篩選是否已正確設定並套用至線路。

這表示您目前位於連結的 WAN 部分。 此路由網域可以是您的服務提供者、您的公司 WAN 或網際網路。 有許多與這些連結相關的躍點、裝置和公司可能會導致難以進行疑難排解。 您必須先將 Azure 和您的公司網路排除在一起，才能調查其間的躍點。

在上圖中，最左邊是您的公司網路。 根據您公司的規模，此路由網域可以是您與 WAN 之間的一些網路裝置，或是校園/企業網路中多層裝置之間的一些網路裝置。

由於這三個不同的高階網路環境有其複雜性。 最好是從邊緣開始，並嘗試顯示效能良好的位置，以及降低效能。 這種方法有助於找出這三個的問題路由網域。 然後您就可以專注于該特定環境的疑難排解。

## <a name="tools"></a>工具
大部分的網路問題都可以使用 Ping 和 traceroute 等基本工具加以分析和隔離。 很罕見地，您必須使用 Wireshark 之類的工具，做為封包分析的深度。 

為協助進行疑難排解，將開發 Azure 連線能力工具組 (AzureCT)，以便將其中部分工具置於簡易的套件內。 針對效能測試，iPerf 和 PSPing 等工具可以提供您網路的相關資訊。 iPerf 是常用的基本效能測試控管，相當容易使用。 PSPing 是 SysInternals 所開發的一種 Ping 工具。 PSPing 可以進行 ICMP 和 TCP ping，以連接到遠端主機。 這兩種都是輕量型工具，只要將檔案複製到主機上的目錄就可以「安裝」妥當。

這些工具和方法會包裝在 PowerShell 模組中， (可供您安裝和使用的 AzureCT) 。

### <a name="azurect---the-azure-connectivity-toolkit"></a>AzureCT：Azure 連線能力工具組
AzureCT PowerShell 模組有兩個構成要素，分別是[可用性測試][Availability Doc]和[效能測試][Performance Doc]。 本文件僅涉及效能測試，因此，請將注意力放在此 PowerShell 模組中的兩個連結效能命令上。

使用此工具組進行效能測試有三個基本步驟。 1) 安裝 PowerShell 模組、2) 安裝支援的應用程式 iPerf 和 PSPing、3) 執行效能測試。

1. 安裝 PowerShell 模組

    ```powershell
    (new-object Net.WebClient).DownloadString("https://aka.ms/AzureCT") | Invoke-Expression
    
    ```

    此命令會下載 PowerShell 模組，並將其安裝在本機上。

2. 安裝支援的應用程式
    ```powershell
    Install-LinkPerformance
    ```
    此 AzureCT 命令會將 iPerf 和 PSPing 安裝在新目錄 "C:\ACTTools" 中，也會開啟 Windows 防火牆連接埠以允許 ICMP 和連接埠 5201 (iPerf) 流量。

3. 執行效能測試

    首先，您必須在遠端主機上使用伺服器模式安裝並執行 iPerf。 此外，請確定遠端主機是接聽 3389 (RDP 用於 Windows) 還是 22 (SSH 用於 Linux)，並在連接埠 5201 上允許 iPerf 的流量。 如果遠端主機是 Windows，請安裝 AzureCT 並執行 Install-LinkPerformance 命令。 此命令會設定 iPerf，以及在伺服器模式中成功啟動 iPerf 所需的防火牆規則。 
    
    一旦遠端電腦準備就緒之後，請在本機電腦上開啟 PowerShell 並開始測試：
    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 10
    ```

    此命令會執行一連串的並行載入和延遲測試，以協助您預估網路連結的頻寬容量和延遲。

4. 檢閱測試的輸出

    PowerShell 輸出格式如下所示：

    ![4][4]

    所有 iPerf 和 PSPing 測試的詳細結果都位於 AzureCT 工具目錄 "C:\ACTTools" 下的個別文字檔中。

## <a name="troubleshooting"></a>疑難排解
如果效能測試未提供您預期的結果，請找出原因應該是漸進式的逐步流程。 由於路徑中的元件數目的不同，系統化方法可提供比跳躍更快的解析度路徑。 藉由有系統地進行疑難排解，您可以避免多次進行不必要的測試。

>[!NOTE]
>以下的案例是效能問題，而不是連線問題。 如果流量完全沒有通過，則步驟將會不同。
>
>

首先，挑戰您的假設。 您的預期合理嗎？ 比方說，如果您有 1 Gbps 的 ExpressRoute 電路和100毫秒的延遲。 透過高延遲連結的 TCP 效能特性，並不合理預期會有超過 1 Gbps 的流量。 如需有關效能假設的詳細資訊，請參閱[參考](#references)一節。

接下來，我建議從路由網域之間的邊緣開始，並嘗試將問題隔離到單一主要路由網域。 您可以從公司網路、WAN 或 Azure 網路開始。 人們通常會在路徑中有「黑箱」。 雖然犯罪黑箱很容易，但它可能會大幅延遲解析度。 尤其是在問題所在的區域內，您可以進行變更以修正問題。 請確定您先盡職調查之後，才交由服務提供者或 ISP 處理。

一旦您已經找出可能含有問題的主要路由網域之後，您應該建立上述區域的圖表。 藉由在 [白板]、[記事本] 或 [Visio] 上繪製圖表，您可以有系統地處理並隔離問題。 您可以規劃測試點，並在清除區域時更新地圖，或隨著測試的進行，挖掘更深入的資料。

既然您有圖表，就可以開始將網路分成區段，並縮小問題的範圍。 找出適用的範圍以及不適用的範圍。 繼續移動您的測試點，以向下隔離到引起問題的構成要素。

此外，別忘了查看其他層的 OSI 模型。 將重點放在網路及第 1-3 層 (實體、資料和網路層) 很容易，但問題也可能會在應用程式層的第 7 層。 請保持開放的心態並驗證假設。

## <a name="advanced-expressroute-troubleshooting"></a>進階 ExpressRoute 疑難排解
如果您不確定雲端邊緣實際上的位置，隔離 Azure 構成要素可能是一個挑戰。 使用 ExpressRoute 時，邊緣是一個名為 Microsoft Enterprise Edge (MSEE) 的網路構成要素。 **使用 ExpressRoute 時**，MSEE 是 microsoft 網路的第一個聯繫點，以及離開 microsoft 網路時的最後一個躍點。 當您在 VNet 閘道與 ExpressRoute 線路之間建立連接物件時，您實際上是在建立與 MSEE 的連線。 將 MSEE 辨識為第一個或最後一個躍點，取決於流量對於隔離 Azure 網路問題的關鍵。 瞭解哪一種方向可證明問題是在 Azure 中，還是在 WAN 或公司網路中進一步下游。 

![2][2]

>[!NOTE]
> 請注意，MSEE 不在 Azure 雲端。 ExpressRoute 實際上是在 Microsoft 網路的邊緣，而不是在 Azure 中。 當您將 ExpressRoute 連線到 MSEE 之後，您會連線到 Microsoft 的網路，接著您就可以移至任何雲端服務，像是使用 Microsoft 對等互連) 的 Microsoft 365 (，或透過私人和/或 Microsoft 對等互連) 的 Azure (。
>
>

如果有兩個 Vnet 連接至 **相同** 的 ExpressRoute 線路，您可以進行一系列的測試來找出 Azure 中的問題。
 
### <a name="test-plan"></a>測試計劃
1. 在 VM1 和 VM2 之間執行 Get-LinkPerformance 測試。 此測試會針對問題是否出在本機，提供深入見解。 如果此測試產生可接受的延遲和頻寬結果，您可以將本機的 VNet 網路標示為良好。
2. 假設本機的 VNet 流量良好，請在 VM1 和 VM3 之間執行 Get-LinkPerformance 測試。 此測試會透過 Microsoft 網路，向下連線至 MSEE，然後再連回 Azure。 如果此測試產生可接受的延遲和頻寬結果，您可以將 Azure 網路標示為良好。
3. 如果 Azure 已排除，您可以在公司網路上執行類似的測試順序。 如果測試結果也良好，則應該與您的服務提供者或 ISP 共同診斷您的 WAN 連線。 範例：在兩個分公司之間，或您的支援人員及資料中心伺服器之間，執行這項測試。 根據您要測試的內容，尋找可以執行該路徑的端點，例如伺服器和用戶端電腦。

>[!IMPORTANT]
> 請務必針對您標示一天時間的每個測試，執行測試並將結果記錄在一個通用位置 (我喜歡用 OneNote 或 Excel)。 每個測試回合都應該有相同的輸出，因此您可以跨測試回合比較所產生的資料，而不會有資料上的「漏洞」。 多項測試間的一致性是我使用 AzureCT 進行疑難排解的主要原因。 *神奇之處* 不在於我執行的確切負載案例中，而是在於我可以從每個測試取得 *一致的測試和資料輸出*。 如果您之後發現問題是偶爾發生的，則記錄時間以及每次獲得一致的資料特別有幫助。 一開始努力收集資料，便可以避免花費數小時重新測試相同的案例 (我在多年前學到了這一點)。
>
>

## <a name="the-problem-is-isolated-now-what"></a>問題遭到隔離，接下來怎麼辦？
找出問題越多越好，解決方案就越快越好。 當您在進行疑難排解時，無法進一步瞭解。 例如，您會看到整個服務提供者的連結會透過歐洲取得躍點，但是您預期路徑會保持在亞洲。 至此，您應該與某人聯繫以取得協助。 您詢問的物件取決於您隔離問題的路由網域。 如果您可以將它縮小至更好的特定元件。

針對公司網路問題，您的內部 IT 部門或服務提供者可以協助進行裝置設定或硬體修復。

針對 WAN，與您的服務提供者或 ISP 共用您的測試結果將有助於開始使用。 這樣做也會避免複製您已完成的相同工作。 如果他們想要自行驗證您的結果，請不要感冒。 根據其他人報告的結果進行疑難排解時，「要信任也要查證」是很好的座右銘。

透過 Azure，一旦您盡可能地詳細隔離問題之後，就可以檢閱 [Azure 網路文件][Network Docs]，之後如果仍然需要，[請開啟支援票證][Ticket Link]。

## <a name="references"></a>參考
### <a name="latencybandwidth-expectations"></a>延遲/頻寬期望
>[!TIP]
> 您要測試的端點之間的地理延遲 (英哩或公里) 是目前最大的延遲構成要素。 雖然沒有相關的設備延遲 (實體和虛擬構成要素、躍點數目等)，但處理 WAN 連線時，地理位置已經公認是整體延遲的最大構成要素。 同樣重要的是要注意，距離是光纖行程的距離，而不是直線距離或路段圖地圖距離。 這個距離很難準確無誤。 因此，我通常使用網際網路上的城市距離計算機，而且知道這種方法是非常不準確的測量方法，但足以設定一般預期。
>
>

我已經在美國華盛頓州的西雅圖設定好 ExpressRoute。 下表顯示我看到測試到各個 Azure 位置的延遲和頻寬。 我已經估計了測試每一端之間的地理距離。

測試設定：
 - 使用連線到 ExpressRoute 線路的 10 Gbps NIC 執行 Windows Server 2016 的實體伺服器。
 - 在啟用私人對等互連時識別之位置中的 10Gbps Premium ExpressRoute 線路。
 - 在指定的區域中，具有 UltraPerformance 閘道的 Azure VNet。
 - 在 VNet 上執行 Windows Server 2016 的 DS5v2 VM。 在已安裝 AzureCT 的情況下，從預設 Azure 映像 (未最佳化或自訂) 建置的 VM 未加入網域。
 - 所有測試都會針對六個測試回合使用 AzureCT Get-LinkPerformance 命令加上5分鐘的負載測試。 例如：

    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 300
    ```
 - 對於每個測試，其資料流程的負載都是從內部部署實體伺服器 (位於西雅圖的 iPerf 用戶端) 向上流動至 Azure VM (所列 Azure 地區中的 iPerf 伺服器)。
 - 「延遲」資料行的資料來自無負載測試 (不執行 iPerf 的 TCP 延遲測試)。
 - 「最大頻寬」資料行的資料來自視窗大小為 1-Mb 的 16 個 TCP 流量負載測試。

![3][3]

### <a name="latencybandwidth-results"></a>延遲/頻寬結果
>[!IMPORTANT]
> 這些數字僅供一般參考。 許多因素都會影響延遲，而且在經過一段時間之後，這些值通常就會變成一致，但是在 Azure 或服務提供者網路中的條件可以隨時透過不同的途徑傳送流量，因此延遲和頻寬可能會受到影響。 一般而言，這些變更的影響不會造成顯著的差異。
>
>

| ExpressRoute<br/>位置|Azure<br/>區域 | 預估<br/>距離 (公里) | Latency|1 個工作階段<br/>頻寬 | 最大值<br/>頻寬 |
| ------------------------------------------ | --------------------------- |  - | - | - | - |
| Seattle | 美國西部 2        |    191 公里 |   5 毫秒 | 262.0 Mbits/秒 |  3.74 Gbits/秒 |
| Seattle | 美國西部          |  1,094 公里 |  18 毫秒 |  82.3 Mbits/秒 |  3.70 Gbits/秒 |
| Seattle | 美國中部       |  2,357 公里 |  40 毫秒 |  38.8 Mbits/秒 |  2.55 Gbits/秒 |
| Seattle | 美國中南部 |  2,877 公里 |  51 毫秒 |  30.6 Mbits/秒 |  2.49 Gbits/秒 |
| Seattle | 美國中北部 |  2,792 公里 |  55 毫秒 |  27.7 Mbits/秒 |  2.19 Gbits/秒 |
| Seattle | 美國東部 2        |  3,769 公里 |  73 毫秒 |  21.3 Mbits/秒 |  1.79 Gbits/秒 |
| Seattle | 美國東部          |  3,699 公里 |  74 毫秒 |  21.1 Mbits/秒 |  1.78 Gbits/秒 |
| Seattle | 日本東部       |  7,705 公里 | 106 毫秒 |  14.6 Mbits/秒 |  1.22 Gbits/秒 |
| Seattle | 英國南部         |  7,708 公里 | 146 毫秒 |  10.6 Mbits/秒 |   896 Mbits/秒 |
| Seattle | 西歐      |  7,834 公里 | 153 毫秒 |  10.2 Mbits/秒 |   761 Mbits/秒 |
| Seattle | 澳大利亞東部   | 12,484 公里 | 165 毫秒 |   9.4 Mbits/秒 |   794 Mbits/秒 |
| Seattle | 東南亞   | 12,989 公里 | 170 毫秒 |   9.2 Mbits/秒 |   756 Mbits/秒 |
| Seattle | 巴西南部 *   | 10,930 公里 | 189 毫秒 |   8.2 Mbits/秒 |   699 Mbits/秒 |
| Seattle | 印度南部      | 12,918 公里 | 202 毫秒 |   7.7 Mbits/秒 |   634 Mbits/秒 |

\* 巴西延遲是不錯的範例，其中的直線距離明顯不同於光纖運行距離。 預期的延遲會在160毫秒的鄰近地區，但實際上是189毫秒。 延遲的差異似乎指出某處有網路問題。 但事實上，光纖線不會以直線移至巴西。 因此，您應該預期會有額外的1000公里或從西雅圖前往巴西。

## <a name="next-steps"></a>後續步驟
1. 從 GitHub 下載 Azure 連線能力工具組，網址為： [https://aka.ms/AzCT][ACT]
2. 依照指示進行[連結效能測試][Performance Doc]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-network-performance/network-components.png "Azure 網路元件"
[2]: ./media/expressroute-troubleshooting-network-performance/expressroute-troubleshooting.png "ExpressRoute 疑難排解"
[3]: ./media/expressroute-troubleshooting-network-performance/test-diagram.png "效能測試環境"
[4]: ./media/expressroute-troubleshooting-network-performance/powershell-output.png "PowerShell 輸出"

<!--Link References-->
[Performance Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/PerformanceTesting.md
[Availability Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/AvailabilityTesting.md
[Network Docs]: ../index.yml
[Ticket Link]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview
[ACT]: https://aka.ms/AzCT