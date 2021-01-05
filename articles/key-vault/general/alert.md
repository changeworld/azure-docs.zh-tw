---
title: Azure Key Vault 監視和警示 |Microsoft Docs
description: 建立儀表板來監視金鑰保存庫的健康情況，並設定警示。
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 04/06/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to learn the options available to monitor the health of my vaults
ms.openlocfilehash: 7ff3daded48612fd308396420dca65cd81d9fafc
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724692"
---
# <a name="monitoring-and-alerting-for-azure-key-vault"></a>Azure Key Vault 的監視和警示

## <a name="overview"></a>總覽

當您開始使用 key vault 來儲存您的生產環境密碼時，請務必監視金鑰保存庫的健康情況，以確定您的服務會如預期般運作。 當您開始調整服務時，傳送至金鑰保存庫的要求數將會增加。 這可能會增加要求的延遲，而且在極端情況下，會導致您的要求受到節流處理，進而影響服務效能。 如果您的金鑰保存庫傳送異常數目的錯誤碼，您也需要收到警示，讓您可以快速收到任何存取原則或防火牆設定問題的通知。 本檔將討論下列主題：

+ 要監視的基本 Key Vault 計量
+ 如何設定計量和建立儀表板 
+ 如何在指定的閾值建立警示 

## <a name="basic-key-vault-metrics-to-monitor"></a>要監視的基本 Key Vault 計量

+ 保存庫可用性  
+ 保存庫飽和度 
+ 服務 API 延遲 
+ 服務 API 點擊次數總計 (依活動類型篩選)  
+ 錯誤代碼 (依狀態碼篩選)  

保存 **庫可用性**-此計量應一律為100%，這是要監視的重要計量，因為它可以在您的金鑰保存庫發生中斷時快速顯示。 

保存 **庫飽和度**–金鑰保存庫每秒可以提供的要求數目，取決於所執行的作業類型。 某些保存庫作業具有較低的每秒要求數閾值。 此計量會將金鑰保存庫的總使用量匯總到所有作業類型，以顯示指出您目前金鑰保存庫使用量的百分比值。 如需金鑰保存庫服務限制的完整清單，請參閱下列檔。 [Azure 金鑰保存庫服務限制](service-limits.md)

**服務 API 延遲** -此計量會顯示呼叫金鑰保存庫的平均延遲（以服務測量）。 它不包含用戶端或用戶端與服務之間的網路所耗用的時間。

**API 點擊總數** -此計量會顯示對您的金鑰保存庫發出的所有呼叫。 這可協助您識別哪些應用程式正在呼叫您的金鑰保存庫。 

**錯誤碼** –此計量會顯示您的金鑰保存庫是否遇到不尋常的錯誤數量。 如需完整的錯誤碼清單和疑難排解指引，請參閱下列檔。 [Azure Key Vault REST API 錯誤碼](rest-error-codes.md)

## <a name="how-to-configure-metrics-and-create-a-dashboard"></a>如何設定計量和建立儀表板

1. 登入 Azure 入口網站
2. 流覽至您的 Key Vault
3. 選取 **監視** 下的 **計量** 

> [!div class="mx-imgBorder"]
> ![醒目顯示 [監視] 區段下 [計量] 選項的螢幕擷取畫面。](../media/alert-1.png)

4. 將圖表的標題更新為您想要在儀表板上看到的內容。 
5. 選取範圍。 在此範例中，我們會選取單一金鑰保存庫。 
6. 選取計量 **整體保存庫可用性** 和匯總 **平均** 
7. 將時間範圍更新為過去24小時，並將時間細微性更新為1分鐘。 

> [!div class="mx-imgBorder"]
> ![顯示整體保存庫可用性計量的螢幕擷取畫面。](../media/alert-2.png)

8. 針對保存庫飽和度和服務 API 延遲計量重複上述步驟。 選取 [ **釘選到儀表板** ]，將您的計量儲存至儀表板。 

> [!IMPORTANT]
> 選取 [釘選到儀表板]，並儲存您設定的每個度量。 如果您離開頁面並返回它而不儲存，則您的設定變更將會遺失。 

9. 若要監視金鑰保存庫上的所有作業類型，請使用 **服務 API 點擊計量總計**，然後選取 [**依活動類型套用分割**]

> [!div class="mx-imgBorder"]
> ![顯示 [套用分割] 按鈕的螢幕擷取畫面。](../media/alert-3.png)

10. 若要監視金鑰保存庫的錯誤碼，請使用 **服務 API 結果計量總計**，然後選取 [套用 **依活動類型套用分割**]

> [!div class="mx-imgBorder"]
> ![顯示所選服務 API 結果計量總計的螢幕擷取畫面。](../media/alert-4.png)

現在您會有一個看起來像這樣的儀表板。 您可以按一下每個圖格右上角的三個點，您可以視需要重新排列和調整磚大小。 

一旦您儲存併發布儀表板，它會在您的 Azure 訂用帳戶中建立新的資源。 您可以藉由搜尋「共用儀表板」隨時查看。 

> [!div class="mx-imgBorder"]
> ![顯示已發佈儀表板的螢幕擷取畫面。](../media/alert-5.png)

## <a name="how-to-configure-alerts-on-your-key-vault"></a>如何在您的 Key Vault 上設定警示 

本節將說明如何在您的金鑰保存庫上設定警示，讓您可以在金鑰保存庫處於狀況不良狀態時，提醒您的小組立即採取行動。 您可以設定警示，以傳送電子郵件、最好是團隊 DL、引發事件方格通知，或是電話號碼或文字。 您也可以選擇以固定值為基礎的靜態警示，或在受監視的計量超過所定義時間範圍內的特定次數時警示您的動態警示。 

> [!IMPORTANT]
> 請注意，新設定的警示最多可能需要10分鐘的時間，才會開始傳送通知。 

### <a name="configure-an-action-group"></a>設定動作群組 

動作群組是可設定的通知和屬性清單。

1. 登入 Azure 入口網站
2. 在搜尋方塊中搜尋 **警示**
3. 選取 [**管理動作**]

> [!div class="mx-imgBorder"]
> ![醒目顯示 [管理動作] 按鈕的螢幕擷取畫面。](../media/alert-6.png)

4. 選取 [ **+ 新增動作群組**]

> [!div class="mx-imgBorder"]
> ![醒目顯示 [+ 新增動作群組] 按鈕的螢幕擷取畫面。](../media/alert-7.png)

5. 選擇動作群組的 **動作類型** 。 在此範例中，我們將建立電子郵件警示。

> [!div class="mx-imgBorder"]
> ![醒目顯示新增動作群組所需欄位的螢幕擷取畫面。](../media/alert-8.png)

> [!div class="mx-imgBorder"]
> ![顯示新增電子郵件或 SMS 訊息警示所需內容的螢幕擷取畫面。](../media/alert-9.png)

6. 按一下頁面底部的 [確定]。 您已成功建立動作群組。 

現在您已設定動作群組，接下來我們將設定金鑰保存庫警示閾值。 

### <a name="configure-alert-thresholds"></a>設定警示閾值 

1. 在 Azure 入口網站中選取您的金鑰保存庫資源，然後選取 [**監視**] 底下的 [**警示**]

> [!div class="mx-imgBorder"]
> ![在 [監視] 區段下顯示 [警示] 功能表選項的螢幕擷取畫面。](../media/alert-10.png)

2. 選取 **新的警示規則**

> [!div class="mx-imgBorder"]
> ![顯示 [+ 新增警示規則] 按鈕的螢幕擷取畫面。](../media/alert-11.png)

3. 選取警示規則的範圍。 您可以選取單一保存庫或多個保存庫。 

> [!IMPORTANT]
> 請注意，當您針對警示範圍選取多個保存庫時，所有選取的保存庫都必須位於相同的區域中。 您必須為不同區域中的保存庫設定個別的警示規則。 

> [!div class="mx-imgBorder"]
> ![顯示如何選取保存庫的螢幕擷取畫面。](../media/alert-12.png)

4. 選取警示的條件。 您可以選擇下列任何信號，並定義警示的邏輯。 Key Vault 團隊建議設定下列警示閾值。 

    + Key Vault 可用性降至低於 100% (靜態閾值) 
    + Key Vault 延遲大於500毫秒 (靜態閾值)  
    + 整體保存庫飽和度大於 75% (靜態閾值)  
    + 整體保存庫飽和度超過平均 (動態閾值) 
    + 高於平均 (動態閾值) 的錯誤碼總數 

> [!div class="mx-imgBorder"]
> ![顯示您選取警示條件之位置的螢幕擷取畫面。](../media/alert-13.png)

### <a name="example-1-configuring-a-static-alert-threshold-for-latency"></a>範例1：設定延遲的靜態警示閾值

選取 **整體服務 API 延遲** 作為信號名稱
> [!div class="mx-imgBorder"]
> ![顯示整體服務 API 延遲信號名稱的螢幕擷取畫面。](../media/alert-14.png)

請參閱下列設定參數。

+ 將閾值設定為 **靜態** 
+ 將運算子設定為 **大於**
+ 將匯總類型設定為 **平均**
+ 將臨界值設定為 **500**
+ 將匯總期間設定為 **5 分鐘**
+ 將評估頻率設定為 **1 分鐘**
+ 選取 [完成]   

> [!div class="mx-imgBorder"]
> ![反白顯示已設定警示邏輯的螢幕擷取畫面。](../media/alert-15.png)

### <a name="example-2-configuring-a-dynamic-alert-threshold-for-vault-saturation"></a>範例2：設定保存庫飽和度的動態警示閾值 

當您使用動態警示時，您將能夠看到您所選取的金鑰保存庫的歷程記錄資料。 藍色區域表示金鑰保存庫的平均使用量。 紅色區域會顯示可能已觸發警示設定中其他準則的尖峰。 紅點會顯示在匯總時間範圍內符合警示準則的違規實例。 您可以設定警示，以在設定的時間內經過特定數目的違規之後引發。 如果您不想要包含過去的資料，可以選擇在 [advanced settings] 中排除舊的資料。 

> [!div class="mx-imgBorder"]
> ![顯示整體保存庫飽和度圖表的螢幕擷取畫面。](../media/alert-16.png)

請參閱下列設定參數。

+ 將閾值設定為 **動態** 
+ 將運算子設定為 **大於**
+ 將匯總類型設定為 **平均**
+ 將閾值敏感度設定為 [**中**]
+ 將匯總期間設定為 **5 分鐘**
+ 將評估頻率設定為 **1 分鐘**
+ **選擇性** 設定 Advanced Settings 
+ 選取 [完成] 

> [!div class="mx-imgBorder"]
> ![Azure 入口網站的螢幕擷取畫面](../media/alert-17.png)

5. 新增您已設定的動作群組

> [!div class="mx-imgBorder"]
> ![顯示如何新增動作群組的螢幕擷取畫面。](../media/alert-18.png)

6. 啟用警示並指派嚴重性

> [!div class="mx-imgBorder"]
> ![顯示啟用警示並指派嚴重性的螢幕擷取畫面。](../media/alert-19.png)

7. 建立警示 


## <a name="next-steps"></a>後續步驟

恭喜，您現在已成功建立監視儀表板，並已為您的金鑰保存庫設定警示！ 當您遵循上述所有步驟之後，您應該會在金鑰保存庫符合您設定的警示準則時收到電子郵件警示。 範例如下所示。 使用您在本文中設定的工具，主動監視金鑰保存庫的健康情況。 

### <a name="example-email-alert"></a>範例電子郵件警示 

> [!div class="mx-imgBorder"]
> ![強調設定電子郵件警示所需資訊的螢幕擷取畫面。](../media/alert-20.png)
