---
title: Azure Stack Edge 迷你 R 安全性指南 |Microsoft Docs
description: 描述安全慣例、指導方針和考慮，並說明如何安全地安裝和操作您的 Azure Stack Edge 迷你 R 裝置。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: 507ceef0f13951eafdcb02d586f35c1d61764c4e
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466279"
---
# <a name="azure-stack-edge-mini-r-safety-instructions"></a>Azure Stack Edge 迷你 R 安全性指示

![警告圖示 1 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ 讀取安全性注意事項圖示 ](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png)
 **讀取安全性和健康情況資訊**

請先閱讀本文中的所有安全性資訊，再使用您的 Azure Stack Edge 迷你 R 裝置、單一電池套件的組合、一個 AC/DC 電源供應器、一個模組電源卡和一個伺服器模組。 若未遵循指示，可能會導致您的屬性發生火災、電擊、傷害或損毀。 使用 Azure Stack Edge 迷你 R 之前，請先閱讀以下的所有安全性資訊。

## <a name="safety-icon-conventions"></a>安全性圖示慣例

危險警示符號的下列信號字是：

| 圖示 | 描述 |
|:--- |:--- |
| ![危險符號](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)| **危險：** 指出危險的情況，如果無法避免，則會導致死亡或嚴重傷害。 <br> **警告：** 指出危險的情況，如果無法避免，可能會導致死亡或嚴重傷害。 <br> **注意：** 指出危險的情況，如果無法避免，可能會導致輕微或適中的傷害。|
|

在設定並執行您的 Azure Stack Edge 迷你 R 裝置時，會觀察到下列危險圖示：

| 圖示 | 描述 |
|:--- |:--- |
| ![請先閱讀所有指示](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png) | 請先閱讀所有指示 |
| ![危險符號](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) | 危險符號 |
| ![電擊圖示](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) | 電擊危險 |
| ![僅限室內使用](./media/azure-stack-edge-mini-r-safety/icon-safety-indoor-use-only.png) | 僅限室內使用 |
| ![無使用者可自行維修零件圖示](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) | 沒有使用者維修的部分。 除非受過適當訓練，否則請勿觸碰。 |
|

## <a name="handling-precautions-and-site-selection"></a>處理預防措施與網站選擇

Azure Stack Edge 迷你 R 裝置具有下列處理預防措施和網站選擇準則：

![警告圖示2電擊 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ 圖示 ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)
 ![ 無使用者維修零件圖示 ](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) **警告：**

* 檢查「已接收」的裝置是否受損。 如果裝置主機殼已損毀， [請聯絡 Microsoft 支援服務](azure-stack-edge-placeholder.md) 以取得更換。 請勿嘗試操作裝置。
* 如果您懷疑裝置無法正常運作， [請聯絡 Microsoft 支援服務](azure-stack-edge-placeholder.md) 以取得更換。 請勿嘗試維修裝置。
* 裝置不含使用者可自行維修的零件。 內含具危險性的電壓、電流和能階。 請勿開啟。 請將裝置寄回給 Microsoft 進行維修。

![警告圖示 3 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **注意：**

建議您作業系統：

* 遠離熱來源，包括直接日光和 radiators。
* 未暴露于濕度或 rain 的位置。
* 位於可將振動和實體震動降至最低的空間中。  系統是根據 >.MIL-STD-810G 而設計的電擊和震動。
* 與電氣裝置所產生的強大電磁欄位隔離。
* 請勿讓任何液體或任何外部物件進入系統。 請勿將飲料或任何其他液體容器放在系統上或附近。

![警告圖示 4 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ 沒有使用者的維修零件圖示 ](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) **警告：**

* 此設備包含鋰電池。 請勿嘗試提供電池套件的服務。 此設備的電池不是由使用者維修。 如果電池被不正確的類型所取代，則會有爆炸的風險。

![警告圖示 5 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **注意：**

只有當電池組屬於 Azure Stack Edge 迷你 R 裝置的一部分時，才會對其收費，而不會以個別裝置收費。

![警告圖示 6 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **注意：**

* 電池套件上的開啟/關閉開關僅用來將電池放電到伺服器模組。 如果電源卡已插入電池組，即使交換器處於關閉位置，仍會將電源傳遞給伺服器模組。

![警告圖示7警告 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **：**

* 請勿燒錄或短路電池套件。 必須妥善回收或處置。

![警告圖示8警告 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **：**

* 除了使用提供的 AC/DC 電源供應器，此系統也可以選擇使用提供的欄位類型2590電池。 在此情況下，終端使用者應確認它符合所有適用的安全、運輸、環境及任何其他國家/地區的規範。
* 當您使用類型為2590電池的系統操作時，會在電池製造商指定的使用狀況內操作電池。

![警告圖示9警告 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **：**

此裝置有兩個 SFP + 埠可搭配光學收發器使用。 為了避免危險的鐳射放射，請只搭配類別1收發器使用。

## <a name="electrical-precautions"></a>電源處理預防措施

Azure Stack Edge 迷你 R 裝置具有下列的電氣預防措施：

![警告圖示 10 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) ![ 電擊圖示 ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) **警告：**

搭配電源供應器使用時：

* 提供電源供應線安全的接地線。 替代目前的 (AC) 電源線具有三個線路接地插頭 (具有接地針腳) 的插頭。 此插頭僅適用於接地的 AC 插座。 請勿破壞接地插腳的功能。
* 假設電源供應線上的插頭是主要的中斷裝置，請確保插座位於裝置附近且可輕鬆觸及。
* 如果有下列任何一種情況，請拉插線，而) 不是 (纜線，以拔下電源線 (s) ：

  * 電源線或插頭已磨損或因其他原因損毀。
  * 裝置暴露于雨、過度濕度或其他液體。
  * 裝置已卸載，且裝置大小寫已損毀。
  * 您懷疑裝置需要檢查或維修。
* 在您移動單元，或者認為單元受到任何損毀時，請一律拔除電源。

* 提供適當的電源及電力多載保護，以符合下列電源規格：

* 電壓： 100-240 伏特 AC
* 目前：1.7 安培
* 頻率：50至 60 Hz

![警告圖示11電擊 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ 圖示 ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) **警告：**

* 請勿嘗試修改或使用 AC 電源線 (s) ，而不是設備所提供的電源線。

![警告圖示 12 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ 電擊圖示 ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)
 ![ 室內僅使用 ](./media/azure-stack-edge-mini-r-safety/icon-safety-indoor-use-only.png) **警告：**

* 以這個符號標示的電源供應器只會針對室內使用進行評分。

## <a name="regulatory-information"></a>法規資訊

以下包含 Azure Stack Edge 迷你 R 裝置、法規模型編號： TMA01 的法規資訊。

Azure Stack Edge 迷你 R 裝置的設計目的，是為了與列示 (UL、CSA、ETL 等 ) 以及 IEC/EN 60950-1 或 IEC/EN 62368-1 相容的 NRTL 列名搭配使用， (CE 標示) 資訊技術設備。

在美國和加拿大以外的國家/地區，如果) 裝置的長度大於3個，則不會與設備一起 (的網路纜線。

設備是設計為在下列環境中運作：

| 環境 | 規格 |
|:---  |:--- |
| 系統溫度規格 | <ul><li>儲存體溫度：– 20 &deg; c – 50 &deg; c ( – 4 &deg; f-122 &deg; f) </li><li>持續操作： 0 &deg; c – 40 &deg; C (32 &deg; F – 104 &deg; F) </li></ul> |
| 相對濕度 (RH) 規格 | <ul><li>儲存體：5% 到95% 的相對濕度</li><li>操作：10% 到90% 的相對濕度</li></ul>|
| 最高高度規格 | <ul><li>操作：15000英尺 (4572 計量) </li><li>非操作：40000英尺 (12192 計量) </li></ul>|

> ![注意圖示 ](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **通知：** 對 &nbsp; 不是由 Microsoft 明確核准的設備所進行的變更或修改，可能會讓使用者的授權無法操作設備。

加拿大和美國：

注意：此設備已經過測試，併發現符合一種數位裝置的限制（遵循 FCC 規則的第15部分）。 這些限制旨在提供合理的保護，以防止在商業環境中操作設備時的有害干擾。 本設備會產生、使用及可能散發射頻能量；如果未遵照說明手冊安裝和使用，可能對無線電通訊造成有害干擾。 此設備在居住區域中的作業可能會造成有害干擾，在這種情況下，使用者將需要自行修正干擾。

本裝置符合 FCC 規章第 15 條的規定和加拿大工業部豁免授權 RSS 標準。 操作應遵循以下兩項條件：(1) 本裝置不得造成有害干擾，以及 (2) 本裝置必須接受任何接收到的干擾，包括可能導致不當操作裝置的干擾。

![法規資訊警告1](./media/azure-stack-edge-mini-r-safety/regulatory-information-1.png)

可 () /NMB-3 () Microsoft Corporation （一種 Microsoft 方式，Redmond，WA 98052，美國）。
美國： (800) 426-9400 加拿大： (800) 933-4750

歐盟：要求歐盟符合性聲明的複本。

> ![警告圖示 13 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) 這是產品的類別。 在本國環境中，本產品可能導致迫使使用者須採取適當措施的無線電干擾。

廢棄電池、電氣和電子設備的處置：

![警告圖示14](./media/azure-stack-edge-mini-r-safety/icon-ewaste-disposal.png)

產品本身或其電池或包裝上若有此符號，表示該產品及其所包含的任何電池均不得連同住居垃圾一起處置。 您需自行負責將其交由適當的收集點進行電池、電氣和電子設備的回收。 這種個別的收集和回收將有助於保護天然資源，以及防止因不當的處置而導致電池、電氣和電子設備可能包含的危險物質外洩，進而對人體健康和環境造成潛在的負面後果。 如需應將電池、電氣和電子廢棄物送交何處處理的詳細資訊，請洽詢您當地的縣 (市)/自治區辦公室、住居垃圾處置服務人員，或是您購買本產品的商店。 如需 WEEE 的相關資訊，請連絡 erecycle@microsoft.com。

本產品包含一或多個幣式電池。
Microsoft 愛爾蘭 Sandyford Ind Est 都柏林 D18 KX32 IRL 電話號碼： + 353 1 295 3826 傳真號碼： + 353 1 706 4110

## <a name="next-steps"></a>後續步驟

- [準備部署 Azure Stack Edge 迷你 R](azure-stack-edge-mini-r-deploy-prep.md)
