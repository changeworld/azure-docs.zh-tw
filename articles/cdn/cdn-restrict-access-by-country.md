---
title: 依國家/地區限制 Azure CDN 內容 |Microsoft Docs
description: 瞭解如何使用地區篩選功能，依國家/地區限制 Azure CDN 內容的存取。
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
ms.topic: how-to
ms.date: 06/19/2018
ms.author: allensu
ms.openlocfilehash: fba1f0b1f8160dece41c312b61cbc8ae9571436d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84887025"
---
# <a name="restrict-azure-cdn-content-by-countryregion"></a>依國家/地區限制 Azure CDN 內容

## <a name="overview"></a>總覽
當使用者要求內容時，不論使用者是在什麼位置提出這項要求，依預設系統都會提供內容。 不過，在某些情況下，您可能會想要依國家/地區限制對內容的存取。 使用*地區篩選*功能時，您可以在 CDN 端點上建立特定路徑的規則，以允許或封鎖所選國家/地區的內容。

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
*張*     
*/Photos/Strasbourg/*     
/Photos/Strasbourg/city.png**

### <a name="define-the-type-of-action"></a>定義動作的類型

從 [動作]**** 清單中，選取 [允許]**** 或 [封鎖]****： 

- **允許**：只允許來自指定國家/地區的使用者存取從遞迴路徑要求的資產。

- **封鎖**：來自指定國家/地區的使用者會被拒絕存取從遞迴路徑要求的資產。 如果尚未針對該位置設定其他國家/地區篩選選項，則會允許所有其他使用者存取。

例如，用於封鎖 /Photos/Strasbourg/** 路徑的地區篩選規則會篩選出下列檔案：     
*HTTP： \/ / \<endpoint> . azureedge.net/Photos/Strasbourg/1000.jpg* 
 *HTTP： \/ / \<endpoint> . azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg*

### <a name="define-the-countriesregions"></a>定義國家/地區
從 [**國家/地區代碼**] 清單中，選取您想要封鎖或允許路徑的國家/地區。 

在您完成選取國家/地區之後，請選取 [**儲存**] 以啟動新的地區篩選規則。 

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

5. 從清單中選取一或多個國家/地區，然後選取 **[完成]** 以啟用規則。 
    
    新的規則便會出現在 [國家 (地區)篩選]**** 頁面的資料表中。

    ![地區篩選規則](./media/cdn-filtering/cdn-geo-filtering-premium-rules.png)

### <a name="clean-up-resources"></a>清除資源
在 [國家/地區篩選規則] 資料表中，選取規則旁的 [刪除] 圖示加以刪除，或按一下 [編輯] 圖示加以修改。

## <a name="considerations"></a>考量
* 地區篩選組態的變更不會立即生效：
   * 若為**來自 Microsoft 的標準 Azure CDN** 設定檔，通常會在 10 分鐘內完成傳播。 
   * 若為**來自 Akamai 的標準 Azure CDN** 設定檔，通常會在一分鐘內完成傳播。 
   * 若為**來自 Verizon 的標準 Azure CDN** 和**來自 Verizon 的進階 Azure CDN** 設定檔，通常會在 10 分鐘內完成傳播。 
 
* 這項功能不支援萬用字元 (例如 *)。

* 會將相對路徑相關聯的地區篩選組態，遞迴地套用到該路徑。

* 只能對相同的相對路徑套用一個規則。 也就是說，您無法建立多個指向相同相對路徑的國家/地區篩選。 不過，因為國家/地區篩選是遞迴的，所以資料夾可以有多個國家/地區篩選。 換句話說，先前設定之資料夾的子資料夾可以被指派不同的國家/地區篩選。

* 地區篩選功能會使用國家/地區代碼來定義允許或封鎖受保護目錄之要求的國家/地區。 雖然 Akamai 和 Verizon 設定檔可支援大部分的相同國碼 (地區碼)，但兩者有一些差異。 如需詳細資訊，請參閱[AZURE CDN 國家/地區代碼](/previous-versions/azure/mt761717(v=azure.100))。 

