---
title: 按國家/地區限制 Azure CDN 內容 |微軟文件
description: 瞭解如何透過使用地理篩選功能限制按國家/地區訪問 Azure CDN 內容。
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2018
ms.author: allensu
ms.openlocfilehash: 7ae7224efdaa281106dfbe2118ab0092c8284c6e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260151"
---
# <a name="restrict-azure-cdn-content-by-countryregion"></a>依國家/地區限制 Azure CDN 內容

## <a name="overview"></a>概觀
當使用者要求內容時，不論使用者是在什麼位置提出這項要求，依預設系統都會提供內容。 但是,在某些情況下,您可能希望按國家/地區限制對內容的訪問。 使用*地理篩選*功能,您可以在 CDN 終結點上的特定路徑上創建規則,以允許或阻止選定國家 / 地區中的內容。

> [!IMPORTANT]
> **來自 Microsoft 的標準 Azure CDN** 設定檔不支援以路徑為基礎的地區篩選。
> 

## <a name="standard-profiles"></a>標準設定檔
本節中的程序僅適用於**來自 Akamai 的 Azure CDN 標準**和**來自 Verizon 的 Azure CDN 標準**設定檔。 

若為**來自 Verizon 的 Azure CDN 進階**設定檔，則必須使用**管理**入口網站來啟動地區篩選。 如需詳細資訊，請參閱[來自 Verizon 的 Azure CDN 進階設定檔](#azure-cdn-premium-from-verizon-profiles)。

### <a name="define-the-directory-path"></a>定義目錄路徑
若要存取地區篩選功能，請在入口網站內選取 CDN 端點，然後在左側功能表的 [設定] 下選取 [地區篩選]****。 

![地區篩選標準](./media/cdn-filtering/cdn-geo-filtering-standard.png)

從 [路徑]**** 方塊中，指定要允許或拒絕使用者存取的位置相對路徑。 

您可藉由指定目錄路徑 (例如，/pictures/**)，並使用正斜線 (/) 或選取特定資料夾，來對所有檔案套用地區篩選。 您也可以對單一檔案套用地區篩選 (例如，/pictures/city.png**)。 系統允許多個規則；在輸入規則後，會出現空白資料列供您輸入下一個規則。

例如，下列目錄路徑篩選條件全都有效：   
*/*                                 
*/照片/*     
*/照片/斯特拉斯堡/*     
/Photos/Strasbourg/city.png**

### <a name="define-the-type-of-action"></a>定義動作的類型

從 [動作]**** 清單中，選取 [允許]**** 或 [封鎖]****： 

- **允許**:只允許來自指定國家/地區的用戶訪問從遞歸路徑請求的資產。

- **塊**:來自指定國家/地區的用戶被拒絕訪問從遞歸路徑請求的資產。 如果未為該位置配置其他國家/地區篩選選項,則將允許所有其他用戶訪問。

例如，用於封鎖 /Photos/Strasbourg/** 路徑的地區篩選規則會篩選出下列檔案：     
*http:\<端點>.azureedge.net/照片/斯特拉斯堡/1000.jpg http: 端點>.azureedge.net/照片/斯特拉斯堡/大教堂/1000.jpg \/ /*
*\//\<*

### <a name="define-the-countriesregions"></a>定義國家/地區
在**國家/地區代碼清單**中,選擇要阻止或允許路徑的國家/地區。 

選擇完國家/地區後,選擇 **「保存」** 以啟動新的地理篩選規則。 

![地區篩選規則](./media/cdn-filtering/cdn-geo-filtering-rules.png)

### <a name="clean-up-resources"></a>清除資源
若要刪除規則，請從 [地區篩選]**** 頁面上的清單中選取規則，然後選擇 [刪除]****。

## <a name="azure-cdn-premium-from-verizon-profiles"></a>來自 Verizon 的 Azure CDN 進階設定檔
在**來自 Verizon 的 Azure CDN 進階**設定檔上，用於建立地區篩選規則的使用者介面並不相同：

1. 在 Azure CDN 設定檔的頂端功能表中，選取 [管理]****。

2. 在 Verizon 入口網站中，選取 [HTTP 大型]****，然後選取 [國家 (地區) 篩選]****。

    ![地區篩選標準](./media/cdn-filtering/cdn-geo-filtering-premium.png)

3. 選取 [新增國家 (地區) 篩選]****。

    [步驟 1：]**** 頁面隨即出現。

4. 輸入目錄路徑、選取 [封鎖]**** 或 [新增]****，然後選取 [下一步]****。

    [步驟 2：]**** 頁面隨即出現。 

5. 從清單中選擇一個或多個國家/地區,然後選擇 **「完成」** 以啟動規則。 
    
    新的規則便會出現在 [國家 (地區)篩選]**** 頁面的資料表中。

    ![地區篩選規則](./media/cdn-filtering/cdn-geo-filtering-premium-rules.png)

### <a name="clean-up-resources"></a>清除資源
在國家/地區篩選規則表中,選擇規則旁邊的刪除圖示以將其刪除,或者選擇編輯圖示以對其進行修改。

## <a name="considerations"></a>考量
* 地區篩選組態的變更不會立即生效：
   * 若為**來自 Microsoft 的標準 Azure CDN** 設定檔，通常會在 10 分鐘內完成傳播。 
   * 若為**來自 Akamai 的標準 Azure CDN** 設定檔，通常會在一分鐘內完成傳播。 
   * 若為**來自 Verizon 的標準 Azure CDN** 和**來自 Verizon 的進階 Azure CDN** 設定檔，通常會在 10 分鐘內完成傳播。 
 
* 這項功能不支援萬用字元 (例如 *)。

* 會將相對路徑相關聯的地區篩選組態，遞迴地套用到該路徑。

* 只能對相同的相對路徑套用一個規則。 也就是說,您不能創建指向同一相對路徑的多個國家/區域篩選器。 但是,由於國家/地區篩選器是遞歸的,因此資料夾可以有多個國家/區域篩選器。 換句話說,可以為以前配置的資料夾的子資料夾分配不同的國家/地區篩選器。

* 地理篩選功能使用國家/地區代碼來定義允許或阻止安全目錄請求的國家/地區。 雖然 Akamai 和 Verizon 設定檔可支援大部分的相同國碼 (地區碼)，但兩者有一些差異。 有關詳細資訊,請參閱[Azure CDN 國家/ 地區代碼](/previous-versions/azure/mt761717(v=azure.100))。 

