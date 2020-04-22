---
title: Azure 密鑰保管庫監視和警報 |微軟文件
description: 創建儀錶板以監視金鑰保管庫的運行狀況並配置警報。
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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726939"
---
# <a name="monitoring-and-alerting-for-azure-key-vault"></a>監視 Azure 金鑰保管庫並發出警報

## <a name="overview"></a>概觀

開始使用密鑰保管庫存儲生產機密後,請務必監視密鑰保管庫的運行狀況,以確保服務按預期運行。 當您開始擴展服務時,發送到密鑰保管庫的請求數量將上升。 這可能會增加請求的延遲,在極端情況下,可能會導致請求受到限制,從而影響服務的性能。 如果金鑰保管庫發送了異常數量的錯誤代碼,則還需要收到警報,以便快速通知您任何訪問策略或防火牆配置問題。 此文件將涵蓋以下主題:

+ 要監控的基本金鑰保存庫指標
+ 如何設定指標與建立儀表板 
+ 如何在指定閾值下建立警報 

## <a name="basic-key-vault-metrics-to-monitor"></a>要監控的基本金鑰保存庫指標

+ 保險庫可用性  
+ 保險庫飽和度 
+ 服務 API 延遲 
+ 服務 API 關閉的總資料(按作用類型) 
+ 錯誤代碼(按狀態代碼篩選) 

**保管庫可用性**- 此指標應始終為 100%,這是要監視的重要指標,因為它可以快速顯示密鑰保管庫是否遇到中斷。 

**保存庫飽和**度 – 金鑰保管庫每秒可提供的請求數基於正在執行的操作類型。 某些保管庫操作具有較低的每秒請求閾值。 此指標聚合所有工序類型中密鑰保管庫的總使用方式,得出指示當前密鑰保管庫使用方式的百分比值。 有關金鑰保管庫服務限制的完整清單,請參閱以下文檔。 [Azure 金鑰保存庫服務限制](service-limits.md)

**服務 API 延遲**- 此指標顯示對密鑰保管庫的調用的平均延遲。 儘管密鑰保管庫可能處於服務限制範圍內,但密鑰保管庫的高利用率可能會引入延遲,從而導致下游應用程序失敗。 

**API 總命中**- 此指標顯示對密鑰保管庫進行的所有調用。 這將有助於您確定哪些應用程式正在調用密鑰保管庫。 

**錯誤代碼**– 此指標將顯示密鑰保管庫是否遇到異常數量的錯誤。 有關錯誤代碼和故障排除指南的完整清單,請參閱以下文檔。 [Azure 金鑰保存庫 REST API 錯誤代碼](rest-error-codes.md)

## <a name="how-to-configure-metrics-and-create-a-dashboard"></a>如何設定指標與建立儀表板

1. 登入 Azure 入口網站
2. 導覽到金鑰保存庫
3. 在**監視**下選擇**指標** 

> [!div class="mx-imgBorder"]
> ![Azure 門戶的螢幕截圖](../media/alert-1.png)

4. 將圖表的標題更新到您希望在儀錶板上看到的內容。 
5. 選擇範圍。 在此示例中,我們將選擇單個密鑰保管庫。 
6. 選擇指標**整體保管庫可用性**和聚合**平均值** 
7. 將時間範圍更新為「最近 24 小時」,並將時間粒度更新為 1 分鐘。 

> [!div class="mx-imgBorder"]
> ![Azure 門戶的螢幕截圖](../media/alert-2.png)

8. 對保管庫飽和度和服務 API 延遲指標重複上述步驟。 選擇 **「固定到儀錶板**」可將指標保存到儀錶板中。 

> [!IMPORTANT]
> 選擇「固定到儀錶板」 並儲存您設定的每個指標。 如果離開頁面並在不保存的情況下返回到該頁面,則配置更改將丟失。 

9. 要監視金鑰保管庫上的所有類型的操作,請使用 **「總服務 API」,** 然後選擇**按活動類型應用拆分**

> [!div class="mx-imgBorder"]
> ![Azure 門戶的螢幕截圖](../media/alert-3.png)

10. 要監視金鑰保管庫上的錯誤代碼,請使用 **「總服務 API」,** 然後選擇**按活動類型應用拆分**

> [!div class="mx-imgBorder"]
> ![Azure 門戶的螢幕截圖](../media/alert-4.png)

現在,您將有一個如下所示的儀錶板。 您可以按下每個磁貼右上角的 3 個點,並根據需要重新排列和調整磁貼的大小。 

保存併發佈儀錶板後,它將在 Azure 訂閱中創建新資源。 以搜尋「共用儀錶板」,您可以隨時看到它。 

> [!div class="mx-imgBorder"]
> ![Azure 門戶的螢幕截圖](../media/alert-5.png)

## <a name="how-to-configure-alerts-on-your-key-vault"></a>如何設定金鑰保存庫上的警示 

本節將介紹如何在密鑰保管庫上配置警報,以便提醒您的團隊,如果密鑰保管庫處於不正常狀態,請立即採取行動。 您可以設定發送電子郵件的警報,最好是向團隊 DL 發送、觸發事件網格通知或呼叫或發送電話號碼。 您還可以根據固定值選擇靜態警報,或者選擇動態警報,如果受監視指標在定義的時間範圍內超過密鑰保管庫的平均限制一定次數,則動態警報將提醒您。 

> [!IMPORTANT]
> 請注意,新配置的警報最多可能需要 10 分鐘才能開始發送通知。 

### <a name="configure-an-action-group"></a>設定操作群組 

操作組是通知和屬性的可配置清單。

1. 登入 Azure 入口網站
2. 搜尋搜尋框搜尋**警示**
3. 選擇 **"管理操作"**

> [!div class="mx-imgBorder"]
> ![Azure 門戶的螢幕截圖](../media/alert-6.png)

4. 選擇 **= 新增操作群組**

> [!div class="mx-imgBorder"]
> ![Azure 門戶的螢幕截圖](../media/alert-7.png)

5. 選擇操作群組**的操作型態**。 在此示例中,我們將創建電子郵件警報。

> [!div class="mx-imgBorder"]
> ![Azure 門戶的螢幕截圖](../media/alert-8.png)

> [!div class="mx-imgBorder"]
> ![Azure 門戶的螢幕截圖](../media/alert-9.png)

6. 按一下頁面底部的 [確定]****。 您已成功建立一個操作群組。 

現在,您已配置了操作組,我們將配置密鑰保管庫警報閾值。 

### <a name="configure-alert-thresholds"></a>設定警示閾值 

1. 在 Azure 門戶中選擇金鑰保管庫資源,然後選擇 **"監視**下的**警報**"

> [!div class="mx-imgBorder"]
> ![Azure 門戶的螢幕截圖](../media/alert-10.png)

2. 選擇**新的警示規則**

> [!div class="mx-imgBorder"]
> ![Azure 門戶的螢幕截圖](../media/alert-11.png)

3. 選擇警報規則的範圍。 您可以選擇單個保管庫或多個保管庫。 

> [!IMPORTANT]
> 請注意,當您為警報範圍選擇多個保管庫時,所有選定的保管庫必須位於同一區域。 您必須為不同區域中的保管庫配置單獨的警報規則。 

> [!div class="mx-imgBorder"]
> ![Azure 門戶的螢幕截圖](../media/alert-12.png)

4. 選擇警報的條件。 您可以選擇以下任何信號,並定義警報的邏輯。 密鑰保管庫團隊建議配置以下警報閾值。 

    + 金鑰保管庫可用性降至 100% 以下(靜態閾值)
    + 金鑰保管庫延遲大於 500 毫秒(靜態閾值) 
    + 總體保管庫飽和度大於 75%(靜態閾值) 
    + 總體保管庫飽和度超過平均值(動態閾值)
    + 總誤差碼高於平均值(動態閾值) 

> [!div class="mx-imgBorder"]
> ![Azure 門戶的螢幕截圖](../media/alert-13.png)

### <a name="example-1-configuring-a-static-alert-threshold-for-latency"></a>範例 1:設定延遲的靜態警報閾值

選擇**整體服務 API 延遲**作為訊號名稱
> [!div class="mx-imgBorder"]
> ![Azure 門戶的螢幕截圖](../media/alert-14.png)

請參閱以下配置參數。

+ 將閾值設定為**靜態** 
+ 將運算子設定為**大於**
+ 將集合模式設定為**平均值**
+ 將閾值設定為**500**
+ 將聚合週期設定為**5 分鐘**
+ 將評估頻率設定為**1 分鐘**
+ 選取 [完成]   

> [!div class="mx-imgBorder"]
> ![Azure 門戶的螢幕截圖](../media/alert-15.png)

### <a name="example-2-configuring-a-dynamic-alert-threshold-for-vault-saturation"></a>範例2:為保管庫飽和配置動態警報閾值 

使用動態警報時,您將能夠查看所選密鑰保管庫的歷史數據。 藍色區域表示密鑰保管庫的平均使用方式。 紅色區域顯示將觸發警報的尖峰,前提是滿足警報配置中的其他條件。 紅點顯示在聚合時間視窗期間滿足警報條件的違規實例。 您可以在設定的時間內設置一定數量的違規后觸發警報。 如果不想包含過去的資料,則可以在高級設置中排除下面的舊數據。 

> [!div class="mx-imgBorder"]
> ![Azure 門戶的螢幕截圖](../media/alert-16.png)

請參閱以下配置參數。

+ 將閾值設定為**動態** 
+ 將運算子設定為**大於**
+ 將集合模式設定為**平均值**
+ 將閾值敏感度設定為**中等**
+ 將聚合週期設定為**5 分鐘**
+ 將評估頻率設定為**1 分鐘**
+ **可選**設定進階設定 
+ 選取 [完成] 

> [!div class="mx-imgBorder"]
> ![Azure 門戶的螢幕截圖](../media/alert-17.png)

5. 新增已設定的操作群組

> [!div class="mx-imgBorder"]
> ![Azure 門戶的螢幕截圖](../media/alert-18.png)

6. 開啟警示並分配嚴重性

> [!div class="mx-imgBorder"]
> ![Azure 門戶的螢幕截圖](../media/alert-19.png)

7. 建立警示 


## <a name="next-steps"></a>後續步驟

恭喜您,您現在已成功創建了監視儀錶板,併為密鑰保管庫配置了警報! 遵循上述所有步驟后,當密鑰保管庫滿足您配置的警報條件時,應收到電子郵件警報。 範例如下所示。 使用本文中設置的工具主動監視密鑰保管庫的運行狀況。 

### <a name="example-email-alert"></a>電子郵件警示範例 

> [!div class="mx-imgBorder"]
> ![Azure 門戶的螢幕截圖](../media/alert-20.png)
