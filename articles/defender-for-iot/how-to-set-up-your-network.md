---
title: 設定您的網路
description: 瞭解解決方案架構、網路準備、必要條件和其他所需的資訊，以確保您已成功設定網路以使用適用于 IoT 的 Azure Defender 裝置。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/06/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 3e9380f067b091c4473b8c29bda3d31bb93cbc6d
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2020
ms.locfileid: "97839227"
---
# <a name="about-azure-defender-for-iot-network-setup"></a>關於適用于 IoT 的 Azure Defender 網路設定

適用于 IoT 的 Azure Defender 提供持續的 ICS 威脅監視和裝置探索。 此平臺包含下列元件：

**適用于 IoT 的 Defender 感應器：** 感應器會使用被動 (無代理程式) 監視來收集 ICS 網路流量。 被動和不造成干擾，感應器對 OT 和 IoT 網路和裝置的效能都不會有任何影響。 感應器會連接到 SPAN 埠或網路點，並立即開始監視您的網路。 偵測會顯示在感應器主控台中。 在那裡，您可以在網路地圖、裝置清查和廣泛的報表範圍內進行查看、調查和分析。 範例包括風險評量報告、資料採礦查詢和攻擊媒介。 

**Defender For IoT 內部部署管理主控台**：內部部署管理主控台提供所有網路裝置的匯總視圖。 它提供重要的關鍵，以及 IoT 風險指標，以及所有設備的警示。 與 SOC 工作流程和操作手冊緊密整合，可讓您輕鬆地排定風險降低的風險，以及跨網站的相互關聯。 

**適用于 iot 的 Defender iot 入口網站：** 適用于 IoT 的 Defender 應用程式可協助您購買解決方案設備、安裝和更新軟體，以及更新 TI 套件。 

本文提供解決方案架構、網路準備、必要條件等相關資訊，協助您成功設定網路以使用 Defender for IoT 設備。 使用本文資訊的讀者應該會在操作及管理 OT 和 IoT 網路方面獲得經驗。 範例包括自動化工程師、工廠經理、網路基礎結構服務提供者、網路安全性團隊、Ciso 或 Cio。

如需協助或支援，請聯絡 [Microsoft 支援服務](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)。

## <a name="on-site-deployment-tasks"></a>現場部署工作

網站部署工作包括：

- [收集網站資訊](#collect-site-information)

- [準備設定工作站](#prepare-a-configuration-workstation)

- [規劃機架安裝](#planning-rack-installation)

### <a name="collect-site-information"></a>收集網站資訊

記錄網站資訊，例如：

- 感應器管理網路資訊。

- 網站網路架構。

- 實體環境。

- 系統整合。

- 規劃的使用者認證。

- 設定工作站。

- SSL 憑證 (選擇性) 。

- SMTP 驗證 (選擇性) 。 若要使用具有驗證的 SMTP 伺服器，請準備您的伺服器所需的認證。

- DNS 伺服器 (選擇性) 。 準備您的 DNS 伺服器 IP 和主機名稱。

如需重要網站資訊的詳細清單和說明，請參閱 [範例網站書籍](#example-site-book)。

#### <a name="successful-monitoring-guidelines"></a>成功的監視指導方針

若要找出在每個生產網路中連接設備的最佳位置，建議您遵循此程式：

:::image type="content" source="media/how-to-set-up-your-network/production-network-diagram.png" alt-text="生產網路設定的圖表範例。":::

### <a name="prepare-a-configuration-workstation"></a>準備設定工作站

準備 Windows 工作站，包括下列各項：

- 連接至感應器管理介面。

- 支援的瀏覽器

- 終端機軟體，例如 PuTTY。

請確定工作站上已開啟必要的防火牆規則。 如需詳細資訊，請參閱 [網路存取需求](#network-access-requirements) 。

#### <a name="supported-browsers"></a>支援的瀏覽器

感應器和內部部署管理主控台 web 應用程式支援下列瀏覽器：

- Chrome 32 +

- Microsoft Edge 86 +

- Internet Explorer 10+

### <a name="network-access-requirements"></a>網路存取需求

確認您的組織安全性原則允許存取下列各項：

| **目的** | **通訊協定** | **傳輸** | **內或外** | **通訊埠** | **類別** |
| ----------- | ----------- | ------------ | ---------- | -------- | ------------ |
| **存取 web 主控台** | HTTPS | TCP | 內或外 | 443 | 適用于 Defender for IoT 平臺的內部部署管理主控台 |
| **存取 CLI** | SSH | TCP | 內或外 | 22 | CLI |
| **Defender for IoT 平臺和內部部署管理主控台之間的連線** | SSL | TCP | 內或外 | 443 | 感應器和內部部署管理主控台|
| **內部部署管理主控台，用來做為感應器的 NTP** | NTP | UDP| 到 CM | 123 | 時間同步 | 
| **連線到外部 NTP 伺服器的感應器 (是否相關)** | NTP | UDP | 內或外| 123 | 時間同步 |
| **適用于 IoT 平臺的 Defender 與管理平臺和郵件伺服器之間的連線 (（如果相關）)** | SMTP | TCP | 超出感應器管理 | 25 | 電子郵件 |
| **從內部部署管理主控台傳送到 Syslog 伺服器的記錄 (（如果相關）)** | syslog | UDP | 超出感應器管理| 514 | LEEF |
| **DNS 伺服器埠 (是否相關)** | DNS | N/A | 內或外| 53 | DNS |
| **適用于 IoT 平臺的 Defender 和內部部署管理主控台之間的連線，可 Active Directory (（如果相關) ）** | LDAPS | TCP | 內或外 | 636 <br />389 | Active Directory |
| **遠端 SNMP 收集器 (是否相關)** | SNMP | UDP | 超出感應器管理| 161 | 監視 |
| **Windows 端點監視 (是否相關)** | WMI | UDP | 超出感應器管理| 135 | 監視 |
| **Windows 端點監視 (是否相關)** | WMI | TCP | 超出感應器管理| 1024和更新版本 | 監視 |
| **如果相關)  (通道** | 隧道 | TCP | 到 CM | 9000<br />除了埠443之外<br />從終端使用者到內部部署管理主控台 <br />從感應器到內部部署管理主控台的埠22 | 監視 |
| **輸出至適用于 IoT 中樞的 Defender** | HTTPS | TCP | 超出感應器管理| **URL**<br />*. azure-devices.net:443<br />或如果不支援萬用字元<br />{您的 IoT 中樞名稱}。 azure-devices.net:443 |

### <a name="planning-rack-installation"></a>規劃機架安裝

規劃機架安裝：

1. 為您的設備網路設定準備監視器和鍵盤。
2. 配置設備的機架空間。
3. 具有適用于設備的 AC 電源。
4. 準備 LAN 纜線以將管理連接至網路交換器。
5. 準備區域網路纜線，以連接交換器 SPAN (鏡像) 埠，或網路點擊至適用于 IoT 的 Defender 裝置。 
6. 設定、連接和驗證鏡像交換器中的 SPAN 埠，如架構審核會話中所述。
7. 將設定的範圍埠連接到執行 Wireshark 的電腦，並確認埠已正確設定。
8. 開啟所有相關的防火牆埠。

## <a name="about-passive-network-monitoring"></a>關於被動網路監視

設備會透過切換鏡像埠 (SPAN 埠) 或網路點擊，來接收來自多個來源的流量。 管理埠會連線至企業、公司或感應器管理網路，並可連線至內部部署管理主控台或適用于 IoT 的 Defender 入口網站。

:::image type="content" source="media/how-to-set-up-your-network/switch-with-port-mirroring.png" alt-text="具有埠鏡像之受控交換器的圖表。":::

### <a name="purdue-model"></a>Purdue 模型

下列各節將說明 Purdue 層級。

:::image type="content" source="media/how-to-set-up-your-network/purdue-model.png" alt-text="Purdue 模型的圖表。":::

####  <a name="level-0-cell-and-area"></a>層級0：資料格和區域  

層級0是由基本製造過程中牽涉到的各種感應器、傳動器和裝置所組成。 這些裝置會執行產業自動化和控制系統的基本功能，例如：

- 推動馬達。
- 測量變數。
- 設定輸出。
- 執行按鍵功能，例如繪製、焊接和折彎。

#### <a name="level-1-process-control"></a>層級1：流程式控制制

層級1是由控制和操作製造程式的內嵌控制器所組成，其主要功能是與層級0裝置通訊。 在離散製造中，這些裝置是可程式化的邏輯控制器 (Plc) 或遠端遙測單位 (RTUs) 。 在流程製造中，基本控制器稱為分散式控制系統 (DC) 。

#### <a name="level-2-supervisory"></a>層級2：監督

層級2代表與生產設備區域的執行時間監督和操作相關聯的系統和函數。 這些通常包括下列各項： 

- 運算子介面或 HMIs

- 鬧鐘或警示系統

- 進程 historian 和 batch 管理系統

- 控制室工作站

這些系統會與層級1中的 Plc 和 RTUs 通訊。 在某些情況下，他們會與網站或企業 (層級4和等級 5) 系統和應用程式通訊或共用資料。 這些系統主要是以 (Unix 或 Microsoft Windows) 的標準計算裝置和作業系統為基礎。

#### <a name="levels-3-and-35-site-level-and-industrial-perimeter-network"></a>層級3和3.5：網站層級和產業周邊網路

網站層級代表產業自動化和控制系統的最高層級。 存在於此層級的系統和應用程式會管理整個網站的產業自動化和控制功能。 層級0到3會視為對網站作業而言很重要。 存在於此層級的系統和函數可能包含下列各項：

- 生產報表 (例如，週期時間、品質索引、預測性維護) 

- 工廠 historian

- 詳細的生產排程

- 網站層級的作業管理

- 裝置和材質管理

- 修補啟動伺服器

- 檔案伺服器

- 產業網域、Active Directory、終端機伺服器

這些系統會與生產區域通訊，並與企業 (層級4和等級 5) 系統和應用程式共用資料。  

#### <a name="levels-4-and-5-business-and-enterprise-networks"></a>層級4和5：商務和商業網路

層級4和層級5代表集中式 IT 系統和功能所在的網站或商業網路。 IT 組織會直接管理這些層級的服務、系統和應用程式。

## <a name="planning-for-network-monitoring"></a>規劃網路監視

下列範例顯示產業控制網路的不同拓撲類型，以及感應器的最佳監視和位置考慮。

### <a name="what-should-be-monitored"></a>應監視哪些專案？

應監視通過層級1和2的流量。

### <a name="what-should-the-defender-for-iot-appliance-connect-to"></a>適用于 IoT 設備的 Defender 會如何連線？

適用于 IoT 的 Defender 設備應連接到受控交換器，以查看第1層和第2層之間的產業通訊 (在某些情況下也會有第3層) 。

下圖是多層式多租使用者網路的一般抽象概念，具有廣泛的網路安全性生態系統，通常是由 SOC 和 MSSP 運作。

一般而言，NTA 感應器會部署在 OSI 模型的第0至3層。

:::image type="content" source="media/how-to-set-up-your-network/osi-model.png" alt-text="OSI 模型的圖表。":::

#### <a name="example-ring-topology"></a>範例：環狀拓撲

環狀網路是一種拓撲，其中每個交換器或節點只會連線到兩個其他交換器，形成流量的單一連續路徑。

:::image type="content" source="media/how-to-set-up-your-network/ring-topology.PNG" alt-text="環狀拓撲的圖表。":::

#### <a name="example-linear-bus-and-star-topology"></a>範例：線性匯流排和星狀拓撲

在星狀網路中，每個主機都會連接到中央中樞。 在最簡單的形式中，一部中央中樞會作為傳輸訊息的管道。 在下列範例中，不會監視較低的參數，而且將不會看到維持在這些交換器本機的流量。 裝置可能會根據 ARP 訊息加以識別，但是將遺失連線資訊。

:::image type="content" source="media/how-to-set-up-your-network/linear-bus-star-topology.PNG" alt-text="線性匯流排和星狀拓撲的圖表。":::

#### <a name="multisensor-deployment"></a>Multisensor 部署

以下是部署多個感應器的一些建議：

| * * 數位 * *| **米** | **相依性** | **感應器數目** |
|--|--|--|--|
| 切換開關之間的最大距離 | 80計量 | 準備的乙太網路纜線 | 超過1個 |
| OT 網路數目 | 超過1個 | 沒有實體連線能力 | 超過1個 |
| 參數數目 | 可以使用 RSPAN 設定 | 最多8個參數，以接近感應器的區域（依纜線距離） | 超過1個 |

#### <a name="traffic-mirroring"></a>流量鏡像  

若只要查看流量分析的相關資訊，您必須將適用于 IoT 平臺的 Defender 連線到交換器上的鏡像埠，或只包含工業 ICS 和 SCADA 流量的點一下。 

:::image type="content" source="media/how-to-set-up-your-network/switch.jpg" alt-text="針對您的設定使用此參數。":::

您可以使用下列方法來監視交換器流量：

- [切換 SPAN 埠](#switch-span-port)

- [遠端範圍 (RSPAN) ](#remote-span-rspan)

- [主動與被動匯總點](#active-and-passive-aggregation-tap)

SPAN 和 RSPAN 是 Cisco 術語。 其他品牌的交換器具有類似的功能，但可能使用不同的術語。

#### <a name="switch-span-port"></a>切換 SPAN 埠

交換器埠分析器會將交換器上介面上的本機流量鏡像至相同交換器上的介面。 以下是一些考慮：

- 確認相關的參數支援埠鏡像功能。  

- 預設會停用鏡像選項。

- 建議您設定所有交換器的埠，即使沒有任何資料與其連線。 否則，rogue 裝置可能會連線到未受監視的埠，而不會在感應器上收到警示。

- 在使用廣播或多播訊息的網路上，將交換器設定為僅鏡像 RX (接收) 傳輸。 否則，將會針對多個使用中的埠重複多播訊息，並將頻寬相乘。

下列設定範例僅供參考，且以 Cisco 2960 交換器為基礎， (24 埠) 執行 IOS。 它們只是典型範例，因此請勿使用它們作為指示。 其他 Cisco 作業系統和其他品牌交換器的鏡像埠則以不同方式設定。

:::image type="content" source="media/how-to-set-up-your-network/span-port-configuration-terminal-v2.png" alt-text="SPAN 埠設定終端機的圖表。":::
:::image type="content" source="media/how-to-set-up-your-network/span-port-configuration-mode-v2.png" alt-text="SPAN 埠配置模式的圖表。":::

##### <a name="monitoring-multiple-vlans"></a>監視多個 Vlan

適用于 IoT 的 Defender 可讓您監視網路中設定的 Vlan。 不需要為 IoT 系統設定 Defender。 使用者必須確保您網路中的交換器設定為將 VLAN 標記傳送至適用于 IoT 的 Defender。

下列範例顯示必須在 Cisco 參數上設定的必要命令，才能在 Defender for IoT 中啟用監視 Vlan：

**監視會話**：此命令負責將 vlan 傳送至 SPAN 埠的程式。

- 監視會話1來源介面 Gi1/2

- 監視會話1篩選封包類型良好的 Rx

- 監視會話1目的地介面 fastEthernet1/1 封裝 dot1q

**監視主幹埠 F.E. Gi1/1**：在主幹埠上設定 vlan。

- 介面 GigabitEthernet1/1

- 交換器間主幹封裝 dot1q

- 交換器模式主幹

#### <a name="remote-span-rspan"></a>遠端範圍 (RSPAN) 

遠端 SPAN 會話會將來自多個分散式來源埠的流量鏡像到專用的遠端 VLAN 中。  

:::image type="content" source="media/how-to-set-up-your-network/remote-span.png" alt-text="遠端範圍的圖表。":::

接著，會透過主幹埠將多個交換器中的資料傳遞至包含實體目的地埠的特定參數。 此埠會連接到適用于 IoT 的 Defender 平臺。  

##### <a name="more-about-rspan"></a>深入瞭解 RSPAN

- RSPAN 是一種先進的功能，需要特殊的 VLAN 才能在交換器之間進行跨越監視的流量。 所有參數都不支援 RSPAN。 確認參數支援 RSPAN 函數。

- 預設會停用鏡像選項。

- 必須在來源與目的地切換之間的主幹埠上允許遠端 VLAN。

- 所有連接相同 RSPAN 會話的交換器都必須來自相同的廠商。

> [!NOTE]
> 請確定在交換器之間共用遠端 VLAN 的主幹埠未定義為鏡像會話來源埠。
>
> 遠端 VLAN 會將主幹埠的頻寬增加到鏡像會話的頻寬大小。 確認交換器的主幹埠支援該埠。  

:::image type="content" source="media/how-to-set-up-your-network/remote-vlan.jpg" alt-text="遠端 VLAN 的圖表。":::

#### <a name="rspan-configuration-examples"></a>RSPAN 設定範例

RSPAN：根據 Cisco catalyst 2960 (24 埠) 。

**來源交換器設定範例：**

:::image type="content" source="media/how-to-set-up-your-network/rspan-configuration.png" alt-text="RSPAN 設定的螢幕擷取畫面。":::

1. 進入全域配置模式。

1. 建立專用的 VLAN。

1. 將 VLAN 識別為 RSPAN VLAN。

1. 返回 [設定終端機] 模式。

1. 將所有24個埠設定為會話來源。

1. 將 RSPAN VLAN 設定為會話目的地。

1. 返回具有特殊許可權的 EXEC 模式。

1. 確認埠鏡像設定。

**目的地交換器設定範例：**

1. 進入全域配置模式。

1. 將 RSPAN VLAN 設定為會話來源。

1. 將實體埠24設定為會話目的地。

1. 返回具有特殊許可權的 EXEC 模式。

1. 確認埠鏡像設定。

1. 儲存組態。

#### <a name="active-and-passive-aggregation-tap"></a>主動與被動匯總點

主動或被動的匯總點會以內嵌方式安裝到網路纜線。 它會將 RX 和 TX 複製到監視感應器。

終端機存取點 (點) 是一種硬體裝置，可讓網路流量從埠 A 流向埠 B，並從埠 B 流向埠 A，而不會中斷。 它會持續建立流量流程兩端的完全相同複本，而不會危及網路的完整性。 若有需要，您可以使用切換設定來匯總傳輸和接收流量。 如果不支援匯總，則每個點會使用兩個感應器埠來監視傳送和接收流量。

點擊很有利的原因有很多。 它們都是以硬體為基礎，而且無法遭入侵。 它們會傳遞所有流量，甚至是損毀的訊息，切換通常會下降。 它們並不是處理器敏感性，因此，封包時間很精確，因為參數會將鏡像函式處理為低優先順序的工作，這可能會影響鏡像封包的時間。 基於法庭的考慮，您可以使用最適合的裝置。

您也可以利用匯總工具來監視埠。 這些裝置是以處理器為基礎，且不像硬體點擊一樣安全。 它們可能不會反映確切的封包時間。

:::image type="content" source="media/how-to-set-up-your-network/active-passive-tap-v2.PNG" alt-text="主動與被動點擊的圖表。":::

##### <a name="common-tap-models"></a>常見的點一下模型

這些模型已針對相容性進行測試。 其他廠商和型號也可能相容。

| 影像 | 型號 |
| -- | -- |
| :::image type="content" source="media/how-to-set-up-your-network/garland-p1gccas-v2.png" alt-text="Garland P1GCCAS 的螢幕擷取畫面。":::  | Garland P1GCCAS  |
| :::image type="content" source="media/how-to-set-up-your-network/ixia-tpa2-cu3-v2.png" alt-text="IXIA TPA2-CU3 的螢幕擷取畫面。":::  | IXIA TPA2-CU3  |
| :::image type="content" source="media/how-to-set-up-your-network/us-robotics-usr-4503-v2.png" alt-text="美國機器人 USR 4503 的螢幕擷取畫面。":::  | 美國機器人 USR 4503  |

##### <a name="special-tap-configuration"></a>特殊分點設定

| Garland 分流 | 美國機器人 |
| ----------- | --------------- |
| 請確定跳線的設定如下：<br />:::image type="content" source="media/how-to-set-up-your-network/jumper-setup-v2.jpg" alt-text="美國機器人開關的螢幕擷取畫面。":::| 請確定匯總模式為使用中狀態。 |

## <a name="deployment-validation"></a>部署驗證

### <a name="engineering-self-review"></a>工程自我審視  

檢查您的 OT 和 ICS 網狀圖是定義最佳連線位置最有效率的方式，您可以在其中取得最相關的流量以進行監視。

網站工程師知道他們的網路看起來是什麼樣子。 具有區域網路和營運團隊的評論會話通常會說明預期，並定義最適合用來連接設備的位置。

相關資訊：

- 已知裝置 (試算表) 清單  

- 預估的裝置數目  

- 廠商和產業通訊協定

- 參數的模型，用以確認埠鏡像選項可供使用

- 管理交換器 (的相關資訊，例如，它) 以及它們是否為外部資源

- 網站上的 OT 網路清單

#### <a name="common-questions"></a>常見問題

- 實施的整體目標為何？ 完整的清查和正確的網路對應是否很重要？

- ICS 中有多個或多餘的網路嗎？ 所有要監視的網路嗎？

- ICS 和企業 (商務) 網路之間是否有通訊？ 這些通訊是受監視的嗎？

- 是否已在網路設計中設定 Vlan？

- 如何使用固定或暫時性裝置來維護執行中的 ICS？

- 哪些防火牆會安裝在受監視網路中？

- 受監視網路中是否有任何路由？

- 受監視網路上的作用中通訊協定是什麼？

- 如果我們連接到這個參數，我們會看到 HMI 與 Plc 之間的通訊嗎？

- ICS 交換器和企業防火牆之間的實體距離為何？ 

- 未受管理的交換器是否可以取代為受控交換器，或使用網路點擊選項？

- 網路中是否有任何序列通訊？ 如果是，請將它顯示在網狀圖表上。

- 如果 IoT 設備的 Defender 應該連接到該交換器，該封包中是否有實體可用的機架空間？

#### <a name="additional-considerations"></a>其他考量

Defender for IoT 設備的目的是要監視來自第1層和第2層的流量。

針對某些架構，如果此層有流量，則適用于 IoT 的 Defender 設備也會監視第3層。 當您正在檢查網站架構並決定是否要監視交換器時，請考慮下列變數：

- 成本/效益和監視此交換器的重要性為何？

- 如果交換器未受管理，是否可以監視來自較高層級交換器的流量？

  如果 ICS 架構是環狀拓撲，則只需要監視此環中的一個參數。

- 此網路中的安全性或操作風險為何？

- 是否可以監視交換器的 VLAN？ 該 VLAN 是否會顯示在另一個可監視的交換器中？

#### <a name="technical-validation"></a>技術驗證

從交換器範圍 (PCAP 檔) 接收記錄流量的範例 (或鏡像) 埠可協助：

- 驗證是否已正確設定參數。

- 確認通過交換器的流量是否與監視) 流量 (有關。

- 識別此交換器中的頻寬和預估的裝置數目。

您可以將範例 PCAP 檔錄製 (幾分鐘的) 時間，方法是透過 Wireshark 應用程式，將膝上型電腦連接到已設定的範圍埠。

:::image type="content" source="media/how-to-set-up-your-network/laptop-connected-to-span.jpg" alt-text="連接至 SPAN 埠的膝上型電腦螢幕擷取畫面。":::
:::image type="content" source="media/how-to-set-up-your-network/sample-pcap-file.PNG" alt-text="記錄範例 PCAP 檔的螢幕擷取畫面。":::

#### <a name="wireshark-validation"></a>Wireshark 驗證

- 檢查是否有 *單播封包* 存在於記錄流量中。 單播是從一個位址到另一個位址。 如果大部分的流量都是 ARP 訊息，則交換器設定不正確。

- 移至 **統計資料**  >  **通訊協定** 階層。 確認產業有通訊協定存在。

:::image type="content" source="media/how-to-set-up-your-network/wireshark-validation.png" alt-text="Wireshark 驗證的螢幕擷取畫面。":::

## <a name="troubleshooting"></a>疑難排解

使用這些區段來針對問題進行疑難排解：

- [無法使用 web 介面進行連接](#cant-connect-by-using-a-web-interface)

- [設備沒有回應](#appliance-is-not-responding)

### <a name="cant-connect-by-using-a-web-interface"></a>無法使用 web 介面進行連接

1. 確認您嘗試連線的電腦與設備位於相同的網路上。

2. 確認 GUI 網路已連接至感應器上的管理埠。

3. 偵測設備 IP 位址。 如果沒有對 ping 的回應：

    1. 將監視器和鍵盤連接到設備。

    1. 使用 **支援** 使用者和密碼登入。

    1. 使用 [命令 **網路] 清單** 來查看目前的 IP 位址。

    :::image type="content" source="media/how-to-set-up-your-network/list-of-network-commands.png" alt-text="Network list 命令的螢幕擷取畫面。":::

4. 如果網路參數設定不正確，請使用下列程式來變更它：

    1. 使用 [ **網路編輯-設定**] 命令。

    1. 若要變更管理網路的 IP 位址，請選取 [ **Y**]。

    1. 若要變更子網路遮罩，請選取 [ **Y**]。

    1. 若要變更 DNS，請選取 [ **Y**]。

    1. 若要變更預設閘道 IP 位址，請選取 [ **Y**]。

    1. 針對 [僅限感應器的輸入介面變更 (]) 選取 [ **Y**]。

    1. 針對橋接器介面，選取 [ **N**]。

    1. 若要套用設定，請選取 [ **Y**]。

5. 重新開機之後，請與使用者支援人員聯繫，並使用 **network list** 命令來確認參數是否已變更。

6. 再次嘗試從 GUI 進行 ping 並連接。

### <a name="appliance-is-not-responding"></a>設備沒有回應

1. 使用監視器和鍵盤連接到設備，或使用 PuTTY 從遠端連線至 CLI。

2. 使用支援認證來登入。

3. 使用 **系統健全** 狀態命令，並檢查所有進程是否正在執行。

    :::image type="content" source="media/how-to-set-up-your-network/system-sanity-command.png" alt-text="系統健全命令的螢幕擷取畫面。":::

如有任何其他問題，請聯絡 [Microsoft 支援服務](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)。

## <a name="example-site-book"></a>範例網站書籍

您可以使用範例網站書籍來取得和檢查網路設定所需的重要資訊。

### <a name="site-checklist"></a>網站檢查清單

在網站部署之前，請先檢查這份清單：

| **#** | **工作或活動** | **狀態** | **註解** |
|--|--|--|--|
| 1 | 提供 global。 | ☐ |  |
| 3 | 訂購設備。 | ☐ |  |
| 4 | 準備網路中的子網清單。 | ☐ |  |
| 5 | 提供生產網路的 VLAN 清單。 | ☐ |  |
| 6 | 提供網路中的交換器模型清單。 | ☐ |  |
| 7 | 提供工業設備的廠商和通訊協定清單。 | ☐ |  |
| 8 | 提供感應器 (IP 位址、子網、D-GW、DNS) 的網路詳細資料。 | ☐ |  |
| 9 | 建立必要的防火牆規則和存取清單。 | ☐ |  |
| 10 | 在交換器上建立跨越埠監視的埠，或視需要設定網路點擊。 | ☐ |  |
| 11 | 準備感應器設備的機架空間。 | ☐ |  |
| 12 | 為人員準備工作站。 | ☐ |  |
| 13 | 提供適用于 IoT 機架裝置 Defender 的鍵盤、監視器和滑鼠。 | ☐ |  |
| 14 | 機架並連接設備。 | ☐ |  |
| 15 | 配置網站資源以支援部署。 | ☐ |  |
| 16 | 建立 Active Directory 群組或本機使用者。 | ☐ |  |
| 17 | 設定訓練 (的自我學習) 。 | ☐ |  |
| 18 | 移至或不進入。 | ☐ |  |
| 19 | 排程部署日期。 | ☐ |  |


| **日期** | **注意** | **部署日期** | **注意** |
|--|--|--|--|
| 適用於 IoT 的 Defender |  | 網站名稱 * |  |
| 名稱 |  | 名稱 |  |
| 位置 |  | 位置 |  |

#### <a name="architecture-review"></a>架構審查

業界網狀圖的總覽可讓您為適用于 IoT 設備的 Defender 定義適當的位置。

1.  觀看產業的全球網狀圖環境。 例如：

    :::image type="content" source="media/how-to-set-up-your-network/ot-global-network-diagram.png" alt-text="適用于全球網路的工業 OT 環境圖。":::

    > [!NOTE]
    > 適用于 IoT 的 Defender 設備應連接到較低層級的交換器，以查看交換器上端口之間的流量。  

2. 在網路中提供大約數量的裝置 (選擇性) 。

3. 提供生產網路的子網清單和描述 (選擇性) 。 

    |  **#**  | **子網路名稱** | **說明** |
    |--| --------------- | --------------- |
    | 1  | |
    | 2  | |
    | 3  | |
    | 4  | |

4. 提供生產網路的 VLAN 清單。

    | **#** | **VLAN 名稱** | **說明** |
    |--|--|--|
    | 1 |  |  |
    | 2 |  |  |
    | 3 |  |  |
    | 4 |  |  |

5. 若要確認交換器具有埠鏡像功能，請提供 Defender for IoT 平臺應連接的交換器模型編號：

    | **#** | **開關** | **型號** | **流量鏡像支援 (範圍、RSPAN 或無)** |
    |--|--|--|--|
    | 1 |  |  |
    | 2 |  |  |
    | 3 |  |  |
    | 4 |  |  |

    協力廠商是否管理交換器？ Y 或 N 

    如果是，誰？ __________________________________ 

    什麼是原則？ __________________________________ 

    例如：

    - 西門子

    - 幼圓的自動化–乙太網路或 IP

    - Emerson – DeltaV, Ovation
    
6. 是否有裝置可透過網路中的序列連接進行通訊？ [是] 或 [否] 

    如果是，請指定哪個序列通訊協定： ________________ 

    如果是，請在網狀圖表上標示哪些裝置會與序列通訊協定通訊，以及它們的位置： 
 
    <Add your network diagram with marked serial connection> 

7. 針對 QoS，感應器的預設設定為 1.5 Mbps。 如果您想要變更它，請指定： ________________ 

   業務單位 (BU) ： ________________ 

### <a name="specifications-for-site-equipment"></a>網站設備的規格

#### <a name="network"></a>網路  

感應器設備透過網路介面卡連線到交換器 SPAN 埠。 它會連線到客戶的公司網路，以透過其他專用的網路介面卡進行管理。

提供將在公司網路中連線之感應器 NIC 的位址詳細資料： 

|  Item               | 設備1 | 設備2 | 設備3 |
| --------------- | ------------- | ------------- | ------------- |
| 設備 IP 位址    |               |               |               |
| 子網路          |               |               |               |
| 預設閘道 |               |               |               |
| DNS             |               |               |               |
| 主機名稱       |               |               |               |

#### <a name="idraciloserver-management"></a>iDRAC/iLO/伺服器管理

|       Item          | 設備1 | 設備2 | 設備3 |
| --------------- | ------------- | ------------- | ------------- |
| 設備 IP 位址     |               |               |               |
| 子網路          |               |               |               |
| 預設閘道 |               |               |               |
| DNS             |               |               |               |

#### <a name="on-premises-management-console"></a>內部部署管理主控台  

|       Item          | 使用中 | 使用 HA) 的被動 ( |
| --------------- | ------ | ----------------------- |
| IP 位址             |        |                         |
| 子網路          |        |                         |
| 預設閘道 |        |                         |
| DNS             |        |                         |

#### <a name="snmp"></a>SNMP  

|   Item              | 詳細資料 |
| --------------- | ------ |
| IP              |        |
| IP 位址 | |
| 使用者名稱 | |
| 密碼 | |
| 驗證類型 | MD5 或 SHA |
| 加密 | DES 或 AES |
| 祕密金鑰 | |
| SNMP v2 社區字串 |

### <a name="cm-ssl-certificate"></a>CM SSL 憑證

您是否打算使用 SSL 憑證？ [是] 或 [否]

如果是，您會使用哪一種服務來產生？ 您將在憑證中包含哪些屬性 (例如，網域或 IP 位址) ？

### <a name="smtp-authentication"></a>SMTP 驗證

您是否打算使用 SMTP 將警示轉寄至電子郵件伺服器？ [是] 或 [否]

如果是，您將使用何種驗證方法？  

### <a name="active-directory-or-local-users"></a>Active Directory 或本機使用者

請聯絡 Active Directory 系統管理員，以建立 Active Directory 網站使用者群組或建立本機使用者。 請務必讓您的使用者準備好部署日期。 

### <a name="iot-device-types-in-the-network"></a>網路中的 IoT 裝置類型

| 裝置類型 | 網路中的裝置數目 | 平均頻寬 |
| --------------- | ------ | ----------------------- |
| 相機 | |
| X 光機 | |

## <a name="see-also"></a>請參閱

[關於適用于 IoT 的 Defender 安裝](how-to-install-software.md)
