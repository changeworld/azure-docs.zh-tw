---
title: Azure Stack Edge Pro R 安全性指南 |Microsoft Docs
description: 描述安全慣例、指導方針和考慮，並說明如何安全地安裝和操作您的 Azure Stack Edge Pro R 裝置。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: alkohli
ms.openlocfilehash: ac3f2cc1c68ea552b2858d932217a28055fee0fd
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466306"
---
# <a name="azure-stack-edge-pro-r-safety-instructions"></a>Azure Stack Edge Pro R 安全性指示

![警告圖示](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
![閱讀安全性注意事項圖示](./media/azure-stack-edge-pro-r-safety/icon-safety-read-all-instructions.png)
**讀取的安全性和健全狀況資訊**

使用 Azure Stack Edge Pro R 裝置之前，請先閱讀本文中的所有安全性資訊。 若未遵循指示，可能會導致您的屬性發生火災、電擊、傷害或損毀。 使用 Azure Stack Edge Pro R 之前，請先閱讀以下的所有安全性資訊。

## <a name="safety-icon-conventions"></a>安全性圖示慣例

危險警示符號的下列信號字是：

| 圖示 | 描述 |
|:--- |:--- |
| ![危險符號](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)| **危險：** 指出危險的情況，如果無法避免，則會導致死亡或嚴重傷害。 <br> **警告：** 指出危險的情況，如果無法避免，可能會導致死亡或嚴重傷害。 <br> **注意：** 指出危險的情況，如果無法避免，可能會導致輕微或適中的傷害。|
|

在設定並執行您的 Azure Stack Edge Pro R Edge 裝置時，會觀察到下列危險圖示：

| 圖示 | 描述 |
|:--- |:--- |
| ![請先閱讀所有指示](./media/azure-stack-edge-pro-r-safety/icon-safety-read-all-instructions.png) | 請先閱讀所有指示 |
| ![危險符號](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) | 危險符號 |
| ![傾倒危險圖示](./media/azure-stack-edge-pro-r-safety/icon-safety-tip-hazard.png)  | 傾倒危險|
| ![超重圖示](./media/azure-stack-edge-pro-r-safety/icon-safety-heavy-weight.png)  | 重度加權風險|
| ![電擊圖示](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) | 電擊危險 |
| ![無使用者可自行維修零件圖示](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) | 沒有使用者維修的部分。 除非受過適當訓練，否則請勿觸碰。 |
| ![多個電源來源圖示](./media/azure-stack-edge-pro-r-safety/icon-safety-disconnect-all-power.png)  | 多個電源來源。 中斷所有電源線，以移除設備的所有電源。 |
| ![捏合點圖示](./media/azure-stack-edge-pro-r-safety/icon-pinching-points.png)  | 捏合點存在。 |
| ![熱元件或表面圖示](./media/azure-stack-edge-pro-r-safety/icon-hot-component-surface.png)  | 表示熱元件或表面。 |
|

## <a name="handling-precautions-and-site-selection"></a>處理預防措施與網站選擇

![警告圖示](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) **警告：**

* 例如，您必須在移動和處理隨用隨付裝置時使用手套，例如，PPE) 的適當設備 (、) 和個人保護 (設備。

![警告圖示 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)![ 提示危險圖示 ](./media/azure-stack-edge-pro-r-safety/icon-safety-tip-hazard.png) **警告：**

* 將設備放在平坦、固定且穩定的表面，以避免潛在的秘訣或值得期待危險。
* 請勿堆疊超過2個以上的裝置。
* 請先確定系統是安全的，然後再進行堆疊。
* 請勿重新放置或移動堆疊裝置。
* 請勿堆疊以外部纜線生氣勃勃的裝置。
* 在堆疊作業期間，確定電源線未 crushed 或損毀。
* 裝置不會當做資料表或工作區使用。

![警告圖示 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ 電擊圖示 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png)
 ![ 無使用者維修零件圖示 ](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) **警告：**

* 檢查「已接收」的裝置是否受損。 如果裝置主機殼已損毀， [請聯絡 Microsoft 支援服務](azure-stack-edge-contact-microsoft-support.md) 以取得更換。 請勿嘗試操作裝置。
* 如果您懷疑裝置無法正常運作， [請聯絡 Microsoft 支援服務](azure-stack-edge-contact-microsoft-support.md) 以取得更換。 請勿嘗試維修裝置。
* 裝置不含使用者可自行維修的零件。 內含具危險性的電壓、電流和能階。 請勿開啟。 請將裝置寄回給 Microsoft 進行維修。

![警告圖示 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ 超權數圖示 ](./media/azure-stack-edge-pro-r-safety/icon-safety-heavy-weight.png) **警告：**

* 移除 UPS 的電源供應器模組會公開 UPS 內的生氣勃勃元件。 請勿在電源供應器模組區間內插入外部物件。
* 請勿嘗試自行將 Azure Stack Edge Pro R Edge 裝置提起。 主機殼可在52公斤和93公斤 (115 磅和205磅) ;移動和抬起設備時，請使用機械協助或其他適合的協助。 在移動及提起設備時，符合當地 occupational 健康情況和安全需求。
* 請勿在沒有適當機械輔助的情況下嘗試抬起設備。 請注意，嘗試提起此權數可能會造成嚴重的傷害。

![警告圖示](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)  **警告：**

* 系統的設計目的是在受控制的環境中操作。 選擇網站：
  * Ventilated 和遠離熱來源，包括直接日光和 radiators。
  * 未暴露于濕度或雨。
  * 位於可將振動和實體震動降至最低的空間中。  系統是根據 >.MIL-STD-810G 而設計的電擊和震動。
  * 與電氣裝置所產生的強大電磁欄位隔離。
  * 提供正確的接地輸出。
  * 提供足夠的空間來存取電源線 (的) ，因為它們會作為產品的主要電源中斷連線。
* 在工作區中設定設備，以在設備周圍提供適當的空氣迴圈;確定在裝置執行時已完全移除前端和後端。
* 在無導電污染物的溫度控管區域中安裝設備，並允許在設備周圍有足夠的空氣流通。
* 讓設備遠離液體來源與過度潮濕的環境。
* 請勿讓任何液體或任何外部物件進入系統。 請勿將飲料或任何其他液體容器放在系統上或附近。

## <a name="heater-precautions"></a>加熱器預防措施

![警告圖示 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ 熱元件或表面圖示 1 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **警告：** 

* 當系統開啟電源時，自動加熱器作業可能會因為加熱器元件蓋上的高介面溫度而造成觸控危險。 當系統開機時，請勿接觸此介面。 關閉系統電源之後，允許10分鐘的冷卻時間。

![警告圖示 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ 捏合點圖示 1 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **警告：** 

* 當系統開機時，自動 actuation 的後端 plenum 門可能會產生一個低點危險。 當系統開機時，請隨時留意此區域。

## <a name="electrical-precautions"></a>電源處理預防措施

![警告圖示 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ 電擊圖示 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **警告：**

* 提供電源供應線安全的接地線。 替代目前的 (AC) 電源線具有三個線路接地插頭 (具有接地針腳) 的插頭。 此插頭僅適用於接地的 AC 插座。 請勿破壞接地插腳的功能。
* 假設電源供應線上的插頭是主要的中斷裝置，請確保插座位於裝置附近且可輕鬆觸及。
* 如果有下列任何一種情況，請拉插線，而) 不是 (纜線，以拔下電源線 (s) ：

  * 電源線或插頭已磨損或因其他原因損毀。
  * 您不慎將某物質灑落於裝置外殼內。
  * 裝置暴露於雨水或過多溼氣下。
  * 裝置掉落，且裝置外殼受損。
  * 您懷疑裝置需要檢查或維修。
* 在您移動單元，或者認為單元受到任何損毀時，請一律拔除電源。
* 提供適當的電源及電力多載保護，以符合下列電源規格：

  * 電壓：100至240伏特的 AC
  * 目前： 20 A、每個電源線的最大值。 提供電源線 (s) 。
  * 頻率：50至 60 Hz

![警告圖示電源 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ 電擊圖示 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png)
 ![ 多重電源來源 ](./media/azure-stack-edge-pro-r-safety/icon-safety-disconnect-all-power.png) **警告：**

* 針對沒有中斷電源供應 (UPS) 的系統，請將所有 AC 電源線 () ，以完全移除設備的 AC 電源。
* 若為具有 UPS 的系統，請拔掉所有 AC 電源線 (的) ，並使用 UPS 電源開關來消除系統的動力。 UPS 包含有害的 AC 和 DC 電壓。

![警告圖示 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ 電擊圖示 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **警告：**

* 對於配備 UPS、AC 和/或 DC 電壓的系統，在電池或公用程式所產生的 UPS 輸出中，一定會有 AC 電壓的潛在風險。 為了避免設備損壞或個人傷害，請一律假設 UPS 輸出上可能會有電壓，即使是從主要電源中斷連線。
* 對於配備 UPS 的系統而言，所有 UPS 生氣勃勃的外部連線都是女性。 請勿移除案例或將任何資訊插入到 UPS 上的這些或任何連接器。

![警告圖示 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ 電擊圖示 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **警告：**

* 請勿嘗試修改或使用 AC 電源線 (s) ，而不是設備所提供的電源線。

![警告圖示 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ 沒有使用者的維修零件圖示 ](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) **警告：**

* 此設備包含鋰硬幣的儲存格及/或鋰鐵 phosphate 電池。 請勿嘗試服務設備。 此設備的電池不是由使用者維修。 如果電池被不正確的類型所取代，則會有爆炸的風險。
* 移除 UPS 的電池模組會公開 UPS 內的生氣勃勃元件。 請勿在電池模組區間內插入外部物件。

## <a name="regulatory-information"></a>法規資訊

本節包含 Azure Stack Edge Pro R 裝置、法規模型編號： Azure Stack Edge Pro R 的法規資訊。

Azure Stack Edge Pro R Edge 裝置的設計目的，是為了與列出的 NRTL 列名搭配使用 (UL、CSA、ETL 等 ) ，以及 IEC/EN 60950-1 或 IEC/EN 62368-1 相容 (CE 標示) 資訊技術設備。

裝置設計為在下列環境中運作：

| 環境 | 規格 |
|:--- |:--- |
|溫度規格 | <ul><li>儲存體溫度：-33 &deg; C – 63 &deg; C ( – 28 &deg; f-145 &deg; F)  </li><li>持續操作： 5 &deg; C – 43 &deg; c (41 &deg; F – 110 &deg; F) </li><li>最大溫度梯度 (操作和儲存) ： 20 &deg; C/h (68 &deg; F/h) </li></ul> |
|相對濕度規格 | <ul><li>儲存體：5% 到 95% RH with 33 &deg; C (91 &deg; F) 最大露點點。 大氣必須隨時處於非壓縮。</li><li>操作：5% 到85% 的相對濕度，29 &deg; C (84.2 &deg; F) 最大露點點</li></ul> |
| 最高高度規格 | <ul><li>不含 UPS 的 (操作) ： 15000 ft (4572 計量) </li><li>使用 UPS) 操作 (： 6.561 ft (2000 計量) </li><li>儲存體： 40000 ft (12192 計量) </li></ul> |

<!--|Standard operating temperature specifications | <ul>Continuous operation (for altitude less than 950 m or 3117 ft): +5&deg;C–45&deg;C (41&deg;F–113&deg;F) with no direct sunlight on the equipment</ui>
|Expanded operating temperature specifications | <ul><li>Continuous operation: 5&deg;C to 45&deg;C at 5% to 85% RH with 29&deg;C dew point.</li><li></= 1% of annual operating hours: –5&deg;C to 55&deg;C at 5% to 90% RH with 29&deg;C dew point. |
| Expanded operating temperature restrictions | <ul><li>Do not perform cold start below -15&deg;C according to IEC 60945.</li><li>The operating temperature specified is for a maximum altitude of 950 meters.</li></ul> |
| Gaseous contamination specifications | <ul><li>Copper coupon corrosion rate: < 300 &Aring;/month per Class G1 as defined by ANSI/ISA71.04-1985.</li><li>Silver coupon corrosion rate: < 200 &Aring;/month as defined by AHSRAE TC9.9.</li></ul>|

> [!NOTE]
> Outside the standard operating temperature (10&deg;C to 35&deg;C):
> - The system can operate continuously in temperatures as low as 5&deg;C and as high as 45&deg;C. For temperatures between 35&deg;C and 45&deg;C, de-rate maximum allowable temperature by 1&deg;C per 175 m above 950 m (1&deg;F per 319 ft).
> - The system can operate down to –5&deg;C or up to 55&deg;C for a maximum of 1% of its annual operating hours. For temperatures between 45&deg;C and 55&deg;C, de-rate maximum allowable temperature by 1&deg;C per 125 m above 950 m (1&deg;F per 228 ft).
>
> When operating in the expanded temperature range:
> - System performance may be impacted.
> - Ambient temperature warnings may be reported in the System Event Log.     
>
> Maximum corrosive contaminant levels measured at &lt;/= 50% relative humidity. 


A device that has a UPS installed is designed to operate in the following environments:

| Environment | Specifications |
|:---  |:--- |
|Temperature specifications | <ul><li>Storage temperature: –40&deg;C–70&deg;C (–40&deg;F-158&deg;F) per Mil-Std 810G Method 501.5, Proc 1</li><li>Continuous operation (for altitude less than 950 m or 3117 ft): 5&deg;C–45&deg;C (41&deg;F–113&deg;F), with no direct sunlight on the equipment</li><li>Excursion temperature: 50&deg;C per Mil-Std 810G</li><li>Maximum temperature gradient (operating and storage): 20&deg;C/h (68&deg;F/h)</li></ul> |
|Relative humidity specifications | <ul><li>Storage: 5% to 95% RH with 33&deg;C (91&deg;F) maximum dew point. Atmosphere must be non-condensing at all times.</li><li>Operating: 5% to 85% relative humidity with 29&deg;C (84.2&deg;F) maximum dew point</li></ul>|
|Maximum altitude specifications | <ul><li>Storage: Mil-Std 810G method 500.5, Proc. I, 40,000 ft for 1 hour after stabilization </li><li>Operating: Mil-Std 810G method 500.5, Proc. II, air carriage, 15,000 ft for 1 hour after stabilization</li></ul>|
|Standard operating temperature specifications |<ul><li>Continuous operation (for altitude less than 950 m or 3117 ft)</li><li>+5&deg;C–45&deg;C (41&deg;F–113&deg;F) with no direct sunlight on the equipment</li></ul>|
|Expanded operating temperature specifications | <ul><li>Continuous operation: 5&deg;C to 45&deg;C at 5% to 85% RH with 29&deg;C dew point.</li><li>&lt;/= 1% of annual operating hours: 0&deg;C to 50&deg;C at 5% to 90% RH with 29&deg;C dew point.</li></ul>|
|Expanded operating temperature restrictions | <ul><li>Do not perform cold start below -15C Per IEC 60945</li><li>The operating temperature specified is for a maximum altitude of 950 meters</li></ul> |
|Gaseous contamination specifications | <ul><li>Copper coupon corrosion rate: < 300 &Aring;/month per Class G1 as defined by ANSI/ISA71.04-1985.</li><li>Silver coupon corrosion rate: < 200 &Aring;/month as defined by AHSRAE TC9.9.|-->

<!--
> [!NOTE]
> Outside the standard operating temperature (10&deg;C to 35&deg;C):
> - The system can operate continuously in temperatures as low as 5&deg;C and as high as 45&deg;C. For temperatures between 35&deg;C and 45&deg;C, de-rate maximum allowable temperature by 1&deg;C per 175 m above 950 m (1&deg;F per 319 ft).
> - The system can operate down to 0&deg;C or up to 50&deg;C for a maximum of 1% of its annual operating hours. For temperatures between 45&deg;C and 55&deg;C, de-rate maximum allowable temperature by 1&deg;C per 125 m above 950 m (1&deg;F per 228 ft).
> 
> When operating in the expanded temperature range:
> - System performance may be impacted.
> - Ambient temperature warnings may be reported in the System Event Log.
>
> Maximum corrosive contaminant levels measured at &lt;/= 50% relative humidity. --> 


> ![注意圖示 ](./media/azure-stack-edge-pro-r-safety/icon-safety-notice.png) **通知：** 對 &nbsp; 不是由 Microsoft 明確核准的設備所進行的變更或修改，可能會讓使用者的授權無法操作設備。

加拿大和美國：

注意：此設備已經過測試，併發現符合一種數位裝置的限制（遵循 FCC 規則的第15部分）。 這些限制旨在提供合理的保護，以防止在商業環境中操作設備時的有害干擾。 本設備會產生、使用及可能散發射頻能量；如果未遵照說明手冊安裝和使用，可能對無線電通訊造成有害干擾。 此設備在居住區域中的作業可能會造成有害干擾，在這種情況下，使用者將需要自行修正干擾。

本裝置符合 FCC 規章第 15 條的規定和加拿大工業部豁免授權 RSS 標準。 操作應遵循以下兩項條件：(1) 本裝置不得造成有害干擾，以及 (2) 本裝置必須接受任何接收到的干擾，包括可能導致不當操作裝置的干擾。

![法規資訊警告1](./media/azure-stack-edge-mini-r-safety/regulatory-information-1.png)


可 () /NMB-3 () Microsoft Corporation （一種 Microsoft 方式，Redmond，WA 98052，美國）。
美國： (800) 426-9400 加拿大： (800) 933-4750

歐盟：要求歐盟符合性聲明的複本。 

![警告圖示](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)

此為 A 級產品。 在本國環境中，本產品可能導致迫使使用者須採取適當措施的無線電干擾。

廢棄電池、電氣和電子設備的處置：

![警告圖示14](./media/azure-stack-edge-mini-r-safety/icon-ewaste-disposal.png)

產品本身或其電池或包裝上若有此符號，表示該產品及其所包含的任何電池均不得連同住居垃圾一起處置。 您需自行負責將其交由適當的收集點進行電池、電氣和電子設備的回收。 這種個別的收集和回收將有助於保護天然資源，以及防止因不當的處置而導致電池、電氣和電子設備可能包含的危險物質外洩，進而對人體健康和環境造成潛在的負面後果。 如需應將電池、電氣和電子廢棄物送交何處處理的詳細資訊，請洽詢您當地的縣 (市)/自治區辦公室、住居垃圾處置服務人員，或是您購買本產品的商店。 如需 WEEE 的相關資訊，請連絡 erecycle@microsoft.com。

本產品包含一或多個幣式電池。

Microsoft 愛爾蘭 Sandyford Ind Est 都柏林 D18 KX32 IRL 電話號碼： + 353 1 295 3826 傳真號碼： + 353 1 706 4110



## <a name="next-steps"></a>後續步驟

- [準備部署 Azure Stack Edge Pro R Edge](azure-stack-edge-pro-r-deploy-prep.md)
