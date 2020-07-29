---
title: Azure Key Vault 監視和警示 |Microsoft Docs
description: 建立儀表板來監視金鑰保存庫的健康情況並設定警示。
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to learn the options available to monitor the health of my vaults
ms.openlocfilehash: cc0d969ff6eb76732768dfed2826762920ae9e62
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81726939"
---
# <a name="monitoring-and-alerting-for-azure-key-vault"></a>Azure Key Vault 的監視和警示

## <a name="overview"></a>總覽

一旦您開始使用 key vault 來儲存您的生產秘密，請務必監視金鑰保存庫的健全狀況，以確保您的服務如預期運作。 當您開始調整服務時，傳送至金鑰保存庫的要求數將會增加。 這可能會增加要求的延遲，而且在極端情況下，會導致您的要求受到節流處理，而這會影響您的服務效能。 如果您的金鑰保存庫傳送了不尋常的錯誤代碼，您也需要收到警示，讓您可以快速地收到任何存取原則或防火牆設定問題的通知。 本檔將涵蓋下列主題：

+ 要監視的基本 Key Vault 計量
+ 如何設定計量並建立儀表板 
+ 如何在指定的閾值建立警示 

## <a name="basic-key-vault-metrics-to-monitor"></a>要監視的基本 Key Vault 計量

+ 保存庫可用性  
+ 保存庫飽和度 
+ 服務 API 延遲 
+ 服務 API 點擊次數總計（依活動類型篩選） 
+ 錯誤碼（依狀態碼篩選） 

保存**庫可用性**-此計量應一律為100%，這是要監視的重要計量，因為它可以在您的金鑰保存庫發生中斷時快速顯示。 

保存**庫飽和度**–金鑰保存庫可服務的每秒要求數目，取決於所執行的作業類型。 某些保存庫作業會有較低的每秒要求數閾值。 此計量會匯總所有作業類型上金鑰保存庫的總使用量，並顯示表示目前金鑰保存庫使用量的百分比值。 如需金鑰保存庫服務限制的完整清單，請參閱下列檔。 [Azure 金鑰保存庫服務限制](service-limits.md)

**服務 API 延遲**-此計量會顯示呼叫金鑰保存庫的平均延遲。 雖然您的金鑰保存庫可能在服務限制內，但金鑰保存庫的高使用率可能會導致造成應用程式下游失敗的延遲。 

**總 API**叫用-此計量會顯示對您的金鑰保存庫所做的所有呼叫。 這可協助您識別哪些應用程式正在呼叫您的金鑰保存庫。 

**錯誤碼**–此計量會顯示您的金鑰保存庫是否遇到異常的錯誤數量。 如需錯誤碼和疑難排解指引的完整清單，請參閱下列檔。 [Azure Key Vault REST API 錯誤代碼](rest-error-codes.md)

## <a name="how-to-configure-metrics-and-create-a-dashboard"></a>如何設定計量並建立儀表板

1. 登入 Azure 入口網站
2. 流覽至您的 Key Vault
3. 選取**監視**下的**計量** 

> [!div class="mx-imgBorder"]
> ![Azure 入口網站的螢幕擷取畫面](../media/alert-1.png)

4. 將圖表的標題更新為您想要在儀表板上看到的專案。 
5. 選取範圍。 在此範例中，我們將選取單一金鑰保存庫。 
6. 選取度量**整體保存庫可用性**和匯總**Avg** 
7. 將 [時間範圍] 更新為 [過去24小時]，並將 [時間細微性] 更新為1分鐘。 

> [!div class="mx-imgBorder"]
> ![Azure 入口網站的螢幕擷取畫面](../media/alert-2.png)

8. 針對 [保存庫飽和度] 和 [服務 API 延遲] 計量重複上述步驟。 選取 [**釘選到儀表板**]，將您的計量儲存到儀表板。 

> [!IMPORTANT]
> 選取 [釘選到儀表板]，並儲存您設定的每個度量。 如果您離開頁面並返回而不儲存，則您的設定變更將會遺失。 

9. 若要監視金鑰保存庫上的所有作業類型，請使用 [**服務 API 點擊總數**] 計量，然後選取 [套用**分割依據活動類型**]

> [!div class="mx-imgBorder"]
> ![Azure 入口網站的螢幕擷取畫面](../media/alert-3.png)

10. 若要監視金鑰保存庫上的錯誤碼，請使用 [**服務 API 的總結果**計量]，然後選取 [套用**分割依據活動類型**]

> [!div class="mx-imgBorder"]
> ![Azure 入口網站的螢幕擷取畫面](../media/alert-4.png)

現在您會有一個看起來像這樣的儀表板。 您可以按一下每個磚右上方的3個點，您可以視需要重新排列和調整磚大小。 

一旦您儲存併發布儀表板，它會在您的 Azure 訂用帳戶中建立新的資源。 您隨時都能藉由搜尋「共用的儀表板」來看到它。 

> [!div class="mx-imgBorder"]
> ![Azure 入口網站的螢幕擷取畫面](../media/alert-5.png)

## <a name="how-to-configure-alerts-on-your-key-vault"></a>如何在 Key Vault 上設定警示 

本節將說明如何在金鑰保存庫上設定警示，以便在您的金鑰保存庫處於狀況不良狀態時，提醒您的小組立即採取動作。 您可以設定警示來傳送電子郵件，最好是使用小組 DL、引發事件方格通知，或撥打電話號碼或將其設為文字。 您也可以選擇以固定值為基礎的靜態警示，或如果受監視的計量在定義的時間範圍內，超過金鑰保存庫的平均限制，則會向您發出警示的動態警示。 

> [!IMPORTANT]
> 請注意，最多可能需要10分鐘的時間，新設定的警示才會開始傳送通知。 

### <a name="configure-an-action-group"></a>設定動作群組 

動作群組是可設定的通知和屬性清單。

1. 登入 Azure 入口網站
2. 在搜尋方塊中搜尋**警示**
3. 選取 [**管理動作**]

> [!div class="mx-imgBorder"]
> ![Azure 入口網站的螢幕擷取畫面](../media/alert-6.png)

4. 選取 [ **+ 新增動作群組**]

> [!div class="mx-imgBorder"]
> ![Azure 入口網站的螢幕擷取畫面](../media/alert-7.png)

5. 選擇動作群組的 [**動作類型**]。 在此範例中，我們將建立電子郵件警示。

> [!div class="mx-imgBorder"]
> ![Azure 入口網站的螢幕擷取畫面](../media/alert-8.png)

> [!div class="mx-imgBorder"]
> ![Azure 入口網站的螢幕擷取畫面](../media/alert-9.png)

6. 按一下頁面底部的 [確定]****。 您已成功建立動作群組。 

現在您已設定動作群組，我們將設定金鑰保存庫警示閾值。 

### <a name="configure-alert-thresholds"></a>設定警示閾值 

1. 在 Azure 入口網站中選取您的金鑰保存庫資源，然後選取 [**監視**] 下的 [**警示**]

> [!div class="mx-imgBorder"]
> ![Azure 入口網站的螢幕擷取畫面](../media/alert-10.png)

2. 選取**新的警示規則**

> [!div class="mx-imgBorder"]
> ![Azure 入口網站的螢幕擷取畫面](../media/alert-11.png)

3. 選取警示規則的範圍。 您可以選取單一保存庫或多個。 

> [!IMPORTANT]
> 請注意，當您為警示範圍選取多個保存庫時，所有選取的保存庫都必須位於相同的區域。 您必須為不同區域中的保存庫設定不同的警示規則。 

> [!div class="mx-imgBorder"]
> ![Azure 入口網站的螢幕擷取畫面](../media/alert-12.png)

4. 選取警示的條件。 您可以選擇下列任何信號，並定義警示的邏輯。 Key Vault 小組建議您設定下列警示臨界值。 

    + Key Vault 可用性低於100% （靜態閾值）
    + Key Vault 延遲大於500毫秒（靜態閾值） 
    + 整體保存庫飽和度大於75% （靜態閾值） 
    + 整體保存庫飽和度超過平均（動態閾值）
    + 高於平均的錯誤碼總計（動態閾值） 

> [!div class="mx-imgBorder"]
> ![Azure 入口網站的螢幕擷取畫面](../media/alert-13.png)

### <a name="example-1-configuring-a-static-alert-threshold-for-latency"></a>範例1：設定延遲的靜態警示閾值

選取 [**整體服務 API 延遲**] 作為 [信號名稱]
> [!div class="mx-imgBorder"]
> ![Azure 入口網站的螢幕擷取畫面](../media/alert-14.png)

請參閱下列設定參數。

+ 將閾值設定為**靜態** 
+ 將運算子設定為**大於**
+ 將匯總類型設定為 [**平均**]
+ 將臨界值設定為**500**
+ 將匯總期間設定為**5 分鐘**
+ 將評估頻率設定為**1 分鐘**
+ 選取 [完成]   

> [!div class="mx-imgBorder"]
> ![Azure 入口網站的螢幕擷取畫面](../media/alert-15.png)

### <a name="example-2-configuring-a-dynamic-alert-threshold-for-vault-saturation"></a>範例2：設定保存庫飽和度的動態警示閾值 

當您使用動態警示時，您將能夠看到已選取之金鑰保存庫的歷程記錄資料。 藍色區域代表金鑰保存庫的平均使用量。 紅色區域顯示會在符合警示設定中的其他準則時觸發警示的尖峰。 紅色的點會顯示在匯總時間範圍內符合警示準則的違規實例。 您可以將警示設定為在設定的時間內發生特定數目的違規之後引發。 如果您不想要包含過去的資料，可以選擇在 [高級設定] 中排除下列舊資料。 

> [!div class="mx-imgBorder"]
> ![Azure 入口網站的螢幕擷取畫面](../media/alert-16.png)

請參閱下列設定參數。

+ 將閾值設定為 [**動態**] 
+ 將運算子設定為**大於**
+ 將匯總類型設定為 [**平均**]
+ 將閾值敏感度設定為 [**中**]
+ 將匯總期間設定為**5 分鐘**
+ 將評估頻率設定為**1 分鐘**
+ **選擇性**設定 Advanced 設定 
+ 選取 [完成] 

> [!div class="mx-imgBorder"]
> ![Azure 入口網站的螢幕擷取畫面](../media/alert-17.png)

5. 新增您已設定的動作群組

> [!div class="mx-imgBorder"]
> ![Azure 入口網站的螢幕擷取畫面](../media/alert-18.png)

6. 啟用警示並指派嚴重性

> [!div class="mx-imgBorder"]
> ![Azure 入口網站的螢幕擷取畫面](../media/alert-19.png)

7. 建立警示 


## <a name="next-steps"></a>後續步驟

恭喜，您現在已成功建立監視儀表板，並已為您的金鑰保存庫設定警示！ 當您遵循上述所有步驟之後，您應該會在金鑰保存庫符合您設定的警示準則時收到電子郵件警示。 範例如下所示。 使用您在本文中設定的工具，主動監視金鑰保存庫的健康情況。 

### <a name="example-email-alert"></a>範例電子郵件警示 

> [!div class="mx-imgBorder"]
> ![Azure 入口網站的螢幕擷取畫面](../media/alert-20.png)
