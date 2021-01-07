---
title: 控制監視到的流量
description: 感應器會自動對其執行深層封包偵測和流量，以及解析網路裝置的相關資訊，例如裝置屬性和網路行為。 有數個工具可用來控制每個感應器偵測到的流量類型。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: b2f95ddb13896ed461a20f1948fef34569586d1f
ms.sourcegitcommit: 8f0803d3336d8c47654e119f1edd747180fe67aa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2021
ms.locfileid: "97977062"
---
# <a name="control-what-traffic-is-monitored"></a>控制監視到的流量

感應器會自動對其執行深層封包偵測和流量，以及解析網路裝置的相關資訊，例如裝置屬性和行為。 有數個工具可用來控制每個感應器偵測到的流量類型。

## <a name="learning-and-smart-it-learning-modes"></a>學習和智慧型 IT 學習模式

學習模式會指示您的感應器瞭解您網路的一般活動。 範例是在網路中探索到的裝置、在網路中偵測到的通訊協定、特定裝置之間的檔案傳輸等等。 此活動會成為您的網路基準。

系統會在安裝後自動啟用學習模式，並在關閉之前保持啟用狀態。 大約的學習模式期間是在兩到六周之間，視網路大小和複雜度而定。 在這段期間之後，當停用學習模式時，偵測到的任何新活動都會觸發警示。 當原則引擎發現與您學到的基準偏差時，就會觸發警示。

在學習期間完成並停用學習模式之後，感應器可能會偵測到一般 IT 活動（例如 DNS 和 HTTP 要求）的結果，這是一種異常的高度基準變更。 活動稱為非決定性的 IT 行為。 此行為可能也會觸發不必要的原則違規警示和系統通知。 若要減少這些警示和通知的數量，您可以啟用 **智慧型 IT 學習** 功能。

當智慧 IT 學習啟用時，感應器會根據特定的警示案例來追蹤會產生不具決定性 IT 行為的網路流量。

感應器會監視此流量七天。 如果在七天內偵測到相同的非決定性 IT 流量，它會繼續監視另七天的流量。 如果未偵測到流量已滿七天，則會停用該案例的智慧型 IT 學習。 針對該案例偵測到的新流量，只會產生警示和通知。

使用智慧型 IT 學習可協助您減少因 IT 案例而產生的不必要警示和通知數目。

如果您的感應器是由內部部署管理主控台所控制，您就無法停用學習模式。 在這樣的情況下，只能從管理主控台停用學習模式。

依預設會啟用 (學習和智慧型 IT 學習) 的學習功能。

若要啟用或停用學習：

- 選取 [ **系統設定** ]，並切換 **學習** 和 **智慧型 IT 學習** 選項。

:::image type="content" source="media/concept-learning-modes/toggle-options-for-learning-and-smart-it-learning.png" alt-text="系統設定切換畫面。":::

## <a name="configure-subnets"></a>設定子網路

子網設定會影響您在裝置對應中看到裝置的方式。

根據預設，感應器會探索您的子網設定，並在 [ **子網** 設定] 對話方塊中填入此資訊。

為了將焦點放在 OT 裝置上，裝置對應中的子網會自動匯總 IT 裝置。 每個子網都會以單一實體的形式呈現在地圖上，包括互動式折迭/展開的功能，可將「向下切入」至 IT 子網，然後返回。

當您使用子網時，請選取 [ICS 子網] 來識別 [OT 子網]。 然後，您可以將地圖視圖放在 OT 和 ICS 網路上，然後折迭至最小的 IT 網路元素表示。 這項工作可減少地圖上顯示的裝置總數，並清楚說明 OT 和 ICS 網路元素。

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/expand-network-option.png" alt-text="顯示對網路進行篩選的螢幕擷取畫面。":::

您可以藉由匯出探索到的資料、手動變更，然後匯入您手動定義的子網清單，來變更設定或手動變更子網資訊。 如需匯出和匯入的詳細資訊，請參閱匯 [入裝置資訊](how-to-import-device-information.md)。

在某些情況下（例如使用公用範圍作為內部範圍的環境），您可以選取 [ **不要偵測網際網路活動** ] 選項，以指示感應器將所有子網解析為內部子網。 當您選取該選項時：

- 公用 IP 位址會被視為本機位址。

- 系統不會傳送未授權網際網路活動的警示，這會減少在外部地址收到的通知和警示。

若要設定子網：

1. 在側邊功能表上，選取 [ **系統設定**]。

2. 在 [ **系統設定** ] 視窗中，選取 [ **子網**]。

   :::image type="content" source="media/how-to-control-what-traffic-is-monitored/edit-subnets-configuration-screen.png" alt-text="顯示 [子網設定] 畫面的螢幕擷取畫面。"::: 

3. 若要在探索到新裝置時自動新增子網，請保持選取 **自動子網學習** 。

4. 若要將所有子網解析為內部子網，請選取 [ **不要偵測到網際網路活動**]。

5. 選取 [ **新增網路** ]，並為每個子網定義下列參數：

    - 子網 IP 位址。
    - 子網路遮罩位址。
    - 子網名稱。 建議您以有意義的名稱來命名每個子網，以方便您識別，讓您可以區分它與網路。 名稱最多可以有60個字元。

6. 若要將此子網標示為 OT 子網，請選取 [ **ICS 子網**]。

7. 若要在根據 Purdue 層級排列地圖時個別呈現子網，請選取 [已 **隔離**]。

8. 若要刪除子網，請選取 [] :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/delete-icon.png" border="false"::: 。

9. 若要刪除所有子網，請選取 [ **全部清除**]。

10. 若要匯出已設定的子網，請選取 [ **匯出**]。 子網資料表會下載至您的工作站。

11. 選取 [儲存]。

### <a name="importing-information"></a>匯入資訊 

若要匯入子網資訊，請選取 [匯 **入** ] 並選取要匯入的 CSV 檔案。 子網資訊會以您匯入的資訊進行更新。 如果您重要的是空白欄位，將會遺失您的資料。

## <a name="detection-engines"></a>偵測引擎

自我學習分析引擎可免除更新簽章或定義規則的需求。 引擎會使用 ICS 專屬的行為分析和資料科學，針對異常、惡意程式碼、操作問題、通訊協定違規和基準網路活動偏差持續分析網路流量。

> [!NOTE]
> 建議您啟用所有安全性引擎。

當引擎偵測到偏差時，就會觸發警示。 您可以從警示畫面或從夥伴系統來查看和管理警示。

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/deviation-alert-screen.png" alt-text="顯示偏差偵測的螢幕擷取畫面。":::

觸發警示的引擎名稱會顯示在警示標題之下。

### <a name="protocol-violation-engine"></a>通訊協定違規引擎 

當封包結構或域值不符合通訊協定規格時，就會發生通訊協定違規。

範例案例：「不合法的 MODBUS 作業 (函式程式 *代碼零)* 」警示。 此警示表示主要裝置已傳送函式程式碼為0的要求至次要裝置。 根據通訊協定規格，不允許此動作，而次要裝置可能無法正確處理輸入。

### <a name="policy-violation-engine"></a>原則違規引擎

發生原則違規時，與已學習或設定的設定中定義的基準行為偏離。

範例案例：「 *未經授權的 HTTP 使用者代理程式* 」警示。 此警示表示未依原則學習或核准的應用程式，會用來做為裝置上的 HTTP 用戶端。 這可能是新的網頁瀏覽器或該裝置上的應用程式。

### <a name="malware-engine"></a>惡意程式碼引擎

惡意程式碼引擎會偵測惡意的網路活動。

範例案例：「 *可疑的惡意活動 (Stuxnet)* 」警示。 此警示表示感應器偵測到已知與 Stuxnet 惡意程式碼相關的可疑網路活動。 這項惡意程式碼是一種以產業控制和 SCADA 網路為目標的先進持續性威脅。

### <a name="anomaly-engine"></a>異常引擎

異常引擎偵測到網路行為異常。

範例案例：「通道 *中的定期行為* 」警示。 元件會檢查網路連接，並尋找資料傳輸的定期和迴圈行為。 這種行為在產業網路中很常見。

### <a name="operational-engine"></a>操作引擎

作業引擎會偵測到操作事件或有故障的實體。

範例案例：「 *懷疑裝置已中斷連線 (沒有回應)* 」警示。 當裝置未回應預先定義期間的任何一種要求時，就會引發此警示。 此警示可能表示裝置關機、中斷連接或故障。

### <a name="enable-and-disable-engines"></a>啟用和停用引擎

當您停用原則引擎時，引擎產生的資訊將無法供感應器使用。 例如，如果您停用異常引擎，就不會收到網路異常的警示。 如果您建立了轉送規則，則不會傳送引擎學習的異常。 若要啟用或停用原則引擎，請針對特定引擎選取 [ **啟用** ] 或 [ **停用** ]。

整體分數會顯示在 [ **系統設定** ] 畫面的右下角。 分數表示透過威脅防護引擎啟用的可用保護百分比。 每個引擎都有20% 的可用保護。

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/protection-score-screen.png" alt-text="顯示分數的螢幕擷取畫面。":::

## <a name="configure-dhcp-address-ranges"></a>設定 DHCP 位址範圍

您的網路可能包含靜態和動態 IP 位址。 通常，靜態位址可透過歷史學家、控制器和網路基礎結構裝置（例如交換器和路由器）在網路上找到。 動態 IP 配置通常是在具有膝上型電腦、電腦、智慧型手機和其他可攜式裝置的來賓網路上執行， (使用不同位置的 Wi-Fi 或 LAN 實體連接) 。

如果您使用的是動態網路，則會處理在指派新的 IP 位址時所發生的 IP 位址變更。 您可以藉由定義 DHCP 位址範圍來完成這項作業。

例如，當 DHCP 伺服器指派 IP 位址時，就可能會發生變更。

在每個感應器上定義動態 IP 位址，可在 IP 位址變更的實例中提供完整且透明的支援。 這可確保每個唯一裝置的完整報告。

感應器主控台會顯示與裝置相關聯的最新 IP 位址，並指出哪些裝置是動態的。 例如：

- 無論 IP 位址變更為何，資料採礦報告和裝置清查報表都會將所有從裝置學習的活動合併為一個實體。 這些報告會指出哪些位址已定義為 DHCP 位址。

  :::image type="content" source="media/how-to-control-what-traffic-is-monitored/populated-device-inventory-screen-v2.png" alt-text="顯示裝置清查的螢幕擷取畫面。":::

- [ **裝置屬性** ] 視窗會指出裝置是否已定義為 DHCP 裝置。

若要設定 DHCP 位址範圍：

1.  從側邊功能表上，選取 [**系統設定**] 視窗中的 [ **DHCP 範圍**]。

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/dhcp-address-range-screen.png" alt-text="顯示 DHCP 範圍選取範圍的螢幕擷取畫面。":::

2.  藉由將 **From** 和 To 值設定 **為來** 定義新的範圍。

3.  （選擇性）：定義範圍名稱，最多256個字元。

4.  若要將範圍匯出至 CSV 檔案，請選取 [ **匯出**]。

5. 若要從 CSV 檔案手動新增多個範圍，請選取 [匯 **入** ]，然後選取檔案。

    > [!NOTE]
    > 從 CSV 檔案匯入的範圍會覆寫現有的範圍設定。

6. 選取 [儲存]。

## <a name="configure-dns-servers-for-reverse-lookup-resolution"></a>設定 DNS 伺服器以進行反向查閱解析

若要增強資產擴充，您可以設定多部 DNS 伺服器來 carryout 反向查閱。 您可以解析與網路子網中偵測到的 IP 位址相關聯的主機名稱或 Fqdn。 例如，如果感應器探索到 IP 位址，它可能會查詢多部 DNS 伺服器來解析主機名稱。

支援所有 CIDR 格式。

主機名稱會出現在資產清查和資產對應中，也會出現在報表中。

您可以針對特定的每小時間隔排程反向查閱解析排程，例如每12小時。 或者，您可以排程特定的時間。

若要定義 DNS 伺服器：

1. 選取 [ **系統設定** ]，然後選取 [ **DNS 設定**]。

2. 選取 [ **新增 DNS 伺服器**]。

    :::image type="content" source="media/how-to-enrich-asset-information/dns-reverse-lookup-configuration-screen.png" alt-text="顯示 [新增 DNS 伺服器] 選項的螢幕擷取畫面。":::

3. 在 [ **排程反向 DNS 查閱** ] 欄位中，選擇下列其中一項：

    - 每小時 (的間隔) 。
  
    - 特定時間。 使用歐洲格式。 例如，使用 **14:30** 而非 **2:30 PM**。

4. 在 [ **Dns 伺服器位址** ] 欄位中，輸入 dns IP 位址。

5. 在 [ **Dns 伺服器埠** ] 欄位中，輸入 dns 埠。

6. 將網路 IP 位址解析為資產 Fqdn。 在 [ **標籤數目** ] 欄位中，新增要顯示的網域標籤數目。 從左至右顯示最多30個字元。

7. 在 [ **子網** ] 欄位中，輸入您要 DNS 伺服器查詢的子網。

8. 如果您想要起始反向對應，請選取 [ **啟用** ] 切換。

### <a name="test-the-dns-configuration"></a>測試 DNS 設定 

使用測試資產，確認您所定義的設定是否正常運作：

1. 啟用 **DNS 查閱** 切換。

2. 選取 [測試]。

3. 在 [**伺服器的 DNS 反向對應測試**] 對話方塊中，輸入 [**查閱位址**] 中的位址。

    :::image type="content" source="media/how-to-enrich-asset-information/dns-reverse-lookup-test-screen.png" alt-text="顯示 [查閱位址] 區域的螢幕擷取畫面。":::

4. 選取 [測試]。

## <a name="configure-windows-endpoint-monitoring"></a>設定 Windows 端點監視

您可以使用 Windows 端點監視功能設定適用于 IoT 的 Azure Defender，以選擇性地探查 Windows 系統。 這可為您提供更具體且更精確的裝置相關資訊，例如 service pack 層級。

您可以設定特定範圍和主機的探查，並將它設定為只在需要時才執行。 您可以使用 Windows Management Instrumentation (WMI) （這是 Microsoft 用來管理 Windows 系統的標準指令碼語言）來完成選擇性探查。

> [!NOTE]
> - 您一次只能執行一次掃描。
> - 您可以取得具有網域或本機系統管理員許可權之使用者的最佳結果。
> - 開始進行 WMI 設定之前，請設定防火牆規則，以使用 UDP 埠135和1024以上的所有 TCP 埠，開啟從感應器到掃描子網的輸出流量。

當探查完成時，可從匯出記錄的選項中取得具有所有探查嘗試的記錄檔。 記錄檔包含所有探查的 IP 位址。 針對每個 IP 位址，記錄檔會顯示成功和失敗的資訊。 另外還有一個錯誤碼，也就是衍生自例外狀況的免費字串。 最後一個記錄檔的掃描只會保留在系統中。

您可以執行已排程的掃描或手動掃描。 當掃描完成時，您可以在 CSV 檔案中查看結果。

**先決條件**

使用 UDP 埠135和1024以上的所有 TCP 埠，設定防火牆規則，以開啟從感應器到掃描子網的輸出流量。

若要設定自動掃描：

1. 在側邊功能表上，選取 [ **系統設定**]。

2. 選取 [ **Windows 端點監視**] :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false"::: 。

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="顯示 Windows 端點監視選取範圍的螢幕擷取畫面。":::

3. 在 [ **掃描排程** ] 窗格中，設定選項，如下所示：

      - **依固定間隔 (小時)**：根據時間間隔設定掃描排程。

      - **依特定時間**：根據特定時間設定掃描排程，然後選取 [ **儲存掃描**]。

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/schedule-a-scan-screen-v2.png" alt-text="顯示 [儲存掃描] 按鈕的螢幕擷取畫面。":::

4. 若要定義掃描範圍，請選取 [ **設定掃描範圍**]。

5. 設定 IP 位址範圍，並新增您的使用者和密碼。

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/edit-scan-range-screen.png" alt-text="顯示新增使用者和密碼的螢幕擷取畫面。":::

6. 若要從掃描中排除 IP 範圍，請選取範圍旁的 [ **停** 用]。

7. 若要移除範圍，請選取 :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/remove-scan-icon.png" border="false"::: 範圍旁的。

8. 選取 [儲存]。 [ **編輯掃描範圍** 設定] 對話方塊會關閉，而範圍數目會出現在 [ **掃描範圍** ] 窗格中。

若要執行手動掃描：

1. 在側邊功能表上，選取 [ **系統設定**]。

2. 選取 [ **Windows 端點監視**] :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false"::: 。

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="顯示 Windows 端點監視設定畫面的螢幕擷取畫面。":::

3. 在 [ **動作** ] 窗格中，選取 [ **開始掃描**]。 狀態列會出現在 [ **動作** ] 窗格上，並顯示掃描程式的進度。

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/started-scan-screen-v2.png" alt-text="顯示 [開始掃描] 按鈕的螢幕擷取畫面。":::

若要查看掃描結果：

1. 當掃描完成時，請在 [ **動作** ] 窗格中選取 [ **View scan Results**]。 具有掃描結果的 CSV 檔案會下載到您的電腦。

## <a name="see-also"></a>請參閱

[調查裝置清查](how-to-investigate-sensor-detections-in-a-device-inventory.md) 
 中的感應器偵測[調查裝置對應中的感應器](how-to-work-with-the-sensor-device-map.md)偵測
