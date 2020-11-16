---
title: '在 Windows 虛擬桌面 (preview 的 (QoS) 中執行服務品質) '
titleSuffix: Azure
description: 如何設定 Windows 虛擬桌面的 QoS (preview) 。
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: b61faf74d96e2571e91f7bf9d10eac88cdbf8345
ms.sourcegitcommit: 18046170f21fa1e569a3be75267e791ca9eb67d0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/16/2020
ms.locfileid: "94639251"
---
# <a name="implement-quality-of-service-qos-for-windows-virtual-desktop-preview"></a>在 Windows 虛擬桌面 (preview 的 (QoS) 中執行服務品質) 

> [!IMPORTANT]
> 適用于 Windows 虛擬桌面的服務品質 (QoS) 原則支援目前處於公開預覽狀態。
> 此預覽版是在沒有服務等級協定的情況下提供，不建議將它用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[RDP Shortpath](./shortpath.md) 會在遠端桌面用戶端與會話主機之間提供直接以 UDP 為基礎的傳輸。 RDP Shortpath 可設定 RDP 資料的服務品質 (QoS) 原則。
Windows 虛擬桌面中的 QoS 可讓您對網路延遲敏感的即時 RDP 流量，在較不敏感的流量前面進行「線上剪下」。 這類較不敏感流量的範例是下載新的應用程式，在這種情況下下載額外的第二個應用程式並不是那麼多。 QoS 會使用 Windows 群組原則物件來識別和標記即時串流中的所有封包，並協助您的網路為 RDP 流量提供專用的頻寬部分。

如果您支援的大量使用者群組遇到本文所述的任何問題，您可能需要執行 QoS。 只有少數使用者的小型企業可能不需要 QoS，但它應該也有説明。

如果沒有某種形式的 QoS，您可能會看到下列問題：

* 抖動-以不同速率抵達的 RDP 封包，會導致視覺和音訊問題
* 封包遺失–捨棄的封包，這會導致重新傳輸需要額外的時間
* 延遲的往返時間 (RTT) – RDP 封包需要很長的時間才能到達其目的地，這會導致從遠端應用程式的輸入和回應之間有明顯的延遲。

解決這些問題的最簡單方式，就是將資料連線的大小增加到網際網路的內部和外部。 因為這通常是成本高昂的，QoS 提供了一種方式來管理您所擁有的資源，而不是更有效率地增加頻寬。 為了解決品質問題，我們建議您先使用 QoS，然後只在必要時才新增頻寬。

為使 QoS 生效，您必須在整個組織中套用一致的 QoS 設定。 路徑中無法支援您 QoS 優先順序的任何部分，都可能會降低 RDP 會話的品質。

## <a name="introduction-to-qos-queues"></a>QoS 佇列簡介

為了提供 QoS，網路裝置必須有方法可將流量分類，而且必須能夠區別 RDP 與其他網路流量。

當網路流量進入路由器時，流量就會放入佇列中。 如果未設定 QoS 原則，則只會有一個佇列，而且所有資料都會被視為具有相同優先順序的先進先出。 這表示 RDP 流量可能會卡在流量的後方，而不會有幾個額外的毫秒延遲也不會造成問題。

當您執行 QoS 時，您可以使用數個擁塞管理功能（例如 Cisco 的優先順序佇列和以類別為 [基礎的加權公平佇列 ](https://www.cisco.com/en/US/docs/ios/12_0t/12_0t5/feature/guide/cbwfq.html#wp17641) ）來定義多個佇列， (CBWFQ) 和擁塞規避功能，例如 [加權隨機早期偵測 (WRED) ](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/qos_conavd/configuration/15-mt/qos-conavd-15-mt-book/qos-conavd-cfg-wred.html)。

簡單的說，QoS 是在您的資料網路中建立虛擬「共乘通道」。 因此，某些類型的資料絕不會或很少發生延遲。 一旦建立這些通道，您就可以調整其相對大小，更有效地管理您所擁有的連線頻寬，同時仍能為貴組織的使用者供應商務級體驗。

## <a name="qos-implementation-checklist"></a>QoS 執行檢查清單

概括而言，請執行下列動作來執行 QoS：

1. [請確定您的網路已就緒](#make-sure-your-network-is-ready)
2. [請確定已啟用 RDP Shortpath](./shortpath.md) -反向連接傳輸不支援 QoS 原則
3. 在工作階段主機上[執行插入 DSCP 標記](#insert-dscp-markers)

當您準備要執行 QoS 時，請記住下列指導方針：

* 最短的會話主機路徑
* 不建議使用任何阻礙，例如 proxy 或封包檢查裝置

## <a name="make-sure-your-network-is-ready"></a>請確定您的網路已就緒

如果您正在考慮 QoS 的執行，您應該已經決定頻寬需求和其他 [網路需求](/windows-server/remote/remote-desktop-services/network-guidance?context=/azure/virtual-desktop/context/context)。
  
網路上的流量擁塞會大幅影響媒體質量。 缺乏頻寬會導致效能降低和使用者體驗不佳。 當 Windows 虛擬桌面採用和使用方式增加時，請使用 [Log Analytics](./diagnostics-log-analytics.md) 找出問題，然後使用 QoS 和選擇性的頻寬新增來進行調整。

### <a name="vpn-considerations"></a>VPN 考慮

QoS 在用戶端與會話主機之間的所有連結上執行時，才會如預期般運作。 如果您在內部網路上使用 QoS，且使用者從遠端位置登入，您只能在內部管理的網路內設定優先順序。 雖然遠端位置可能會藉由執行虛擬私人網路 (VPN) 來接收受控連線，但 VPN 本身會增加封包額外負荷，並在即時流量中建立延遲。

在具有橫跨洲之受控連結的全球組織中，我們強烈建議使用 QoS，因為這些連結的頻寬與 LAN 的頻寬有限。

## <a name="insert-dscp-markers"></a>插入 DSCP 標記

您可以使用群組原則物件 () GPO 來執行 QoS，以將工作階段主機導向將 DSCP 標記插入識別為特定流量類型的 IP 封包標頭中。 您可以設定路由器和其他網路裝置來辨識這些標記，並將流量放在另一個較高優先順序的佇列中。

您可以將 DSCP 標記與郵資戳記進行比較，以向郵寄工作者指出傳遞的緊急程度，以及如何以最理想的方式進行排序，以提供快速的傳遞。 設定好網路以提供 RDP 資料流程的優先順序之後，遺失的封包和延遲的封包應該會大幅降低。

當所有網路裝置都使用相同的分類、標記和優先順序之後，就可以減少或消除延遲、捨棄的封包和抖動。 從 RDP 的觀點來看，基本設定步驟是封包的分類和標記。 不過，若要讓端對端 QoS 成功，您也必須小心地將 RDP 設定與基礎網路設定一致。
DSCP 值會告知設定較高的網路，提供封包或串流的優先順序為何。

我們建議使用 DSCP 值46，此值會對應至 **加速轉送 (EF)** DSCP 類別。

### <a name="implement-qos-on-session-host-using-group-policy"></a>使用群組原則在工作階段主機上執行 QoS

您可以在群組原則中使用以原則為基礎的服務品質 (QoS) 來設定預先定義的 DSCP 值。

若要為已加入網域的工作階段主機建立 QoS 原則，請先登入已安裝群組原則管理的電腦。 開啟群組原則管理] (選取 [開始]，指向 [系統管理工具]，然後選取 [群組原則管理]) ，然後完成下列步驟：

1. 在群組原則管理] 中，找出應該在其中建立新原則的容器。 例如，如果所有的會話主機電腦都位於名為「 **工作階段主機** 」的 OU 中，則應該在會話主控 ou 中建立新的原則。

2. 以滑鼠右鍵按一下適當的容器，然後選取 [ **在這個網域中建立 GPO 並連結** 到]。

3. 在 [ **新增 GPO** ] 對話方塊的 [ **名稱** ] 方塊中，輸入新群組原則物件的名稱，然後選取 **[確定]** 。

4. 在新建立的原則上按一下滑鼠右鍵，然後選取 [ **編輯** ]。

5. 在 [群組原則管理編輯器] 中，依序展開 [ **電腦** 設定]、[ **Windows 設定** ]、[以 **原則為基礎的 QoS** ]，然後選取 [ **建立新原則** ]。

6. 在 [以 **原則為基礎的 QoS** ] 對話方塊中，于 [開啟] 頁面的 [ **名稱** ] 方塊中輸入新原則的名稱。 選取 [ **指定 DSCP 值** ]，並將值設定為 **46** 。 保持未選取 [ **指定輸出節流比率** ]，然後選取 **[下一步]** 。

7. 在下一個頁面上，選取 [ **只有具有這個可執行檔名稱的應用程式** ] 並輸入 **svchost.exe** 的名稱，然後選取 **[下一步]** 。 此設定會指示原則只優先處理與遠端桌面服務相符的流量。

8. 在第三個頁面上，確認已選取 [ **任何來源 ip 位址** ] 和 [ **任何目的地 ip 位址** ]，然後選取 **[下一步]** 。 這兩個設定可確保封包會受到管理，不論哪一部電腦 (IP 位址) 傳送封包，以及哪個電腦 (IP 位址) 將接收封包。

9. 在第四頁上，從 [ **選取此 QoS 原則套用的通訊協定** ] 下拉式清單選取 [ **UDP** ]。

10. 在 [ **指定來源埠號碼** ] 標題底下，選取 [ **從這個來源埠] 或 [範圍** ]。 在隨附的文字方塊中，輸入 **3390** 。 選取 [完成]。

您所建立的新原則將不會生效，直到您的會話主機電腦上的群組原則重新整理為止。 雖然群組原則會定期重新整理，但您可以依照下列步驟強制立即重新整理：

1. 在您想要重新整理群組原則的每個工作階段主機上，以系統管理員身分 ([ *以系統管理員身分執行* ]) 開啟命令提示字元。

1. 在命令提示字元中，輸入

   ```console
   gpupdate /force
   ```

### <a name="implement-qos-on-session-host-using-powershell"></a>使用 PowerShell 在工作階段主機上執行 QoS

您可以使用下列 PowerShell Cmdlet 來設定 RDP Shortpath 的 QoS：

```powershell
New-NetQosPolicy -Name "RDP Shortpath" -AppPathNameMatchCondition "svchost.exe" -IPProtocolMatchCondition UDP -IPSrcPortStartMatchCondition 3390 -IPSrcPortEndMatchCondition 3390 -DSCPAction 46 -NetworkProfile All
```

## <a name="related-articles"></a>相關文章

* [服務品質 (QoS) 原則](/windows-server/networking/technologies/qos/qos-policy-top)

## <a name="next-steps"></a>後續步驟

* 若要深入瞭解 Windows 虛擬桌面的頻寬需求，請參閱 [瞭解遠端桌面通訊協定 (RDP) Windows 虛擬桌面的頻寬需求](rdp-bandwidth.md)。
* 若要深入瞭解 Windows 虛擬桌面網路連線能力，請參閱 [瞭解 Windows 虛擬桌面網路連線能力](network-connectivity.md)。
