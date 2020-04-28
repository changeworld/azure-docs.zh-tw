---
title: 了解 Azure 串流分析的輸入
description: 本文說明 Azure 串流分析作業中輸入的概念，比較串流輸入與參考資料輸入的差別。
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 6b841d6b47e009c3b01d9925e11d352c00ed5c19
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75426425"
---
# <a name="understand-inputs-for-azure-stream-analytics"></a>了解 Azure 串流分析的輸入

Azure 串流分析作業能連接一或多個資料輸入。 每個輸入都定義一個與現有資料來源的連線。 串流分析接受來自數種事件來源的資料，包括事件中樞、IoT 中樞及 Blob 儲存體。 輸入的參考依據，是您為每項作業撰寫之串流 SQL 查詢中的名稱。 在查詢中，您可以聯結多個輸入來混合資料，或是比較串流資料與參考資料的查閱，然後將結果傳遞給輸出。 

串流分析具有與三種資源的第一類整合做為輸入：
- [Azure 事件中樞](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT 中心](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob 儲存體](https://azure.microsoft.com/services/storage/blobs/) 

這些輸入資源可以與您的串流分析作業或不同的訂用帳戶存留在相同的 Azure 訂用帳戶中。

您可以使用 [ [Azure 入口網站](stream-analytics-quick-create-portal.md#configure-job-input)]、[ [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.streamanalytics/New-azStreamAnalyticsInput)]、[ [.net API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions)]、[ [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input)] 和 [ [Visual Studio](stream-analytics-tools-for-visual-studio-install.md) ] 來建立、編輯和測試串流分析作業輸入。

## <a name="stream-and-reference-inputs"></a>資料流和參考輸入
當資料發送到資料來源時，串流分析作業會即時取用並處理該資料。 輸入分為兩種類型：資料流輸入和參考資料輸入。

### <a name="data-stream-input"></a>資料流輸入
資料流是一段時間內發生的無限制事件序列。 串流分析作業必須包含至少一個資料流輸入。 支援將事件中樞、IoT 中樞和 Blob 儲存體當成資料流輸入來源。 事件中樞用來從多個裝置和服務收集事件資料流。 這些資料流可能包括社交媒體活動摘要、股票交易資訊或來自感應器的資料。 IoT 中心經過最佳化，最適合從物聯網 (IoT) 案例中相互連接的裝置收集資料。  Blob 儲存體可作為輸入來源，將大量資料內嵌為資料流，例如記錄檔。  

如需串流資料輸入的詳細資訊，請參閱[將資料作為輸入串流處理至串流分析中](stream-analytics-define-inputs.md)

### <a name="reference-data-input"></a>參考資料輸入
串流分析也支援稱為「參考資料」** 的輸入。 參考資料是完全靜態或緩慢變更的資料。 這些資料通常用來執行相互關聯和查閱。 比方說，您可能會將資料流輸入中的資料聯結至參考資料中的資料，很像是執行 SQL 聯結來查詢靜態值。 目前支援 Azure Blob 儲存體和 Azure SQL Database 做為參考資料的輸入來源。 參考資料源 blob 的大小上限為 300 MB，視查詢複雜度和配置的串流單位而定（如需詳細資訊，請參閱參考資料檔的[大小限制](stream-analytics-use-reference-data.md#size-limitation)一節）。

如需參考資料輸入的詳細資訊，請參閱[使用參考資料在串流分析中進行查閱](stream-analytics-use-reference-data.md)

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [快速入門：使用 Azure 入口網站建立串流分析作業](stream-analytics-quick-create-portal.md)
