---
title: 收據-表單辨識器
titleSuffix: Azure Cognitive Services
description: 瞭解使用表單辨識器 API 的接收分析相關概念-使用方式與限制。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: pafarley
ms.openlocfilehash: 7a14b3c93a6c545648faf28c991764e990e487b1
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2020
ms.locfileid: "88724959"
---
# <a name="receipt-concepts"></a>接收概念

AzureForm 辨識器可以使用其中一個預先建立的模型來分析收據。 回條 API 會以英文從銷售收據中解壓縮重要資訊，例如商家名稱、交易日期、交易總計、明細專案等等。 

## <a name="understanding-receipts"></a>瞭解收據 

許多企業和個人仍依賴手動將資料從其銷售收據中解壓縮，不論是企業支出報表、償還、稅務用途、預算或其他用途。 通常在這些情況下，需要實體回條的影像以供驗證之用。  

從這些收據自動解壓縮資料可能很複雜。 收據可能會 crumpled 且難以閱讀，而且收據的 smartphone 影像可能會低品質。 此外，每個市場、區域和商家的收據範本和欄位可能會有極大的差異。 這兩種資料提取和現場偵測的挑戰，是因為有一個獨特的問題。  

使用光學字元辨識 (OCR) 和預建的收據模型，收據 API 會啟用這些收據處理案例。 因為模型已預先定型資料，所以您可以在一個步驟中輕鬆地分析您的收據，而 &mdash; 不需要模型定型或標記。

![範例收據](./media/contoso-receipt-small.png)

## <a name="what-does-the-receipt-api-do"></a>收據 API 有哪些功能？ 

預建的收據 API 會將銷售收據的內容 &mdash; ，與您通常會在餐廳、零售商或雜貨商店取得的收據類型進行解壓縮。

### <a name="fields-extracted"></a>已解壓縮的欄位

* 商家名稱 
* 商家位址 
* 商家電話號碼 
* 交易日期 
* 異動時間 
* 小計 
* 稅金 
* 總計 
* 提示 
* 明細專案的解壓縮 (例如專案數量、專案價格、專案名稱) 

### <a name="additional-features"></a>其他功能

收據 API 也會傳回下列資訊：

* 收據類型 (例如明細、信用卡等) 
* 欄位信賴等級 (每個欄位都會傳回相關聯的信賴值) 
* OCR 原始文字 (OCR 將整個收據的文字輸出解壓縮) 

## <a name="input-requirements"></a>輸入需求

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="supported-locales"></a>支援的地區設定 

* **預先建立** 的回條2.0 版 (GA) 支援 en-us 地區設定的銷售收據
* **預先建立的收據 2.1-preview。 1** (公開預覽) 新增下列 EN 回條地區設定的額外支援： 
  * EN-US 
  * EN-CA 
  * EN-GB 
  * EN-US 

  > [!NOTE]
  > 語言輸入 
  >
  > 預建的收據 2.1-preview。1有選擇性的要求參數，可指定其他英文市場的收據地區設定。 若為美式英文的銷售收據 (EN-US) 、加拿大 (EN-US) 、英國 (半 GB) 和印度 () ，您可以指定地區設定以取得改進的結果。 如果在 2.1-preview. 1 中未指定地區設定，此模型將會預設為 EN-US 模型。

## <a name="customer-scenarios"></a>客戶案例  

使用接收 API 所解壓縮的資料可以用來執行各種工作。 以下是我們的客戶使用收據 API 完成的一些範例。 

### <a name="business-expense-reporting"></a>商務支出報告  

提出業務費用通常需要花費時間從收據的影像手動輸入資料。 使用接收 API，您可以使用已解壓縮的欄位來部分自動化此程式，並快速分析您的收據。  

因為接收 API 具有簡單的 JSON 輸出，所以您可以用多種方式使用已解壓縮的域值。 與內部費用應用程式整合，以預先填入 expense reports。 如需此案例的詳細資訊，請參閱 Acumatica 如何運用回條 API， [讓支出回報較不困難的流程](https://customers.microsoft.com/en-us/story/762684-acumatica-partner-professional-services-azure)。  

### <a name="auditing-and-accounting"></a>審核和帳戶處理 

收據 API 輸出也可以用來在支出報告和補償程式中的各個點上，執行大量費用的分析。 您可以處理收據以進行手動審核或快速核准。  

收據輸出也適用于一般書籍，以供商務或個人使用。 使用回條 API，將任何原始收據影像/PDF 資料轉換成可採取動作的數位輸出。

### <a name="consumer-behavior"></a>消費者行為 

回條包含有用的資料，可供您用來分析消費者行為和購物趨勢。

