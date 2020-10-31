---
title: Azure 串流分析的資料表儲存體輸出
description: 本文說明 azure 資料表儲存體作為 Azure 串流分析的輸出。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: ea775ef472687485dbd2f30c4f60adc33c0eaa73
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93127343"
---
# <a name="table-storage-output-from-azure-stream-analytics"></a>Azure 串流分析的資料表儲存體輸出

[Azure 資料表儲存體](../storage/common/storage-introduction.md) 提供高可用性且可大幅擴充的儲存體，可讓應用程式自動調整來滿足使用者需求。 資料表儲存體是 Microsoft 的 NoSQL 索引鍵/屬性存放區，您可以針對結構化資料使用，但結構描述的限制較少。 使用 Azure 資料表儲存資料時，資料可長期儲存而且調閱方便。

下表列出屬性名稱及其描述以建立表格輸出。

| 屬性名稱 | 描述 |
| --- | --- |
| 輸出別名 |此為易記名稱，用於在查詢中將查詢輸出指向這個資料表儲存體。 |
| 儲存體帳戶 |您傳送輸出的儲存體帳戶名稱。 |
| 儲存體帳戶金鑰 |與儲存體帳戶相關聯的存取金鑰。 |
| 資料表名稱 |資料表的名稱。 如果資料表不存在，則會建立資料表。 |
| 資料分割索引鍵 |包含分割區索引鍵的輸出資料行名稱。 在構成實體主索引鍵第一個部分的資料表內，分割區索引鍵是資料分割的唯一識別碼。 大小最高為 1 KB 的字串值。 |
| 列索引鍵 |其中包含資料列索引鍵的輸出資料行名稱。 資料列索引鍵是分割區內實體的唯一識別碼。 其可構成實體主索引鍵的第二個部分。 資料列索引鍵是大小可以高達 1 KB 的字串值。 |
| 批次大小 |批次作業的記錄數目。 預設值 (100)通常足以應付大部分的作業。 如需修改此設定的詳細資料，請參閱[資料表批次作業規格](/java/api/com.microsoft.azure.storage.table.tablebatchoperation)。 |

## <a name="partitioning"></a>資料分割

分割區索引鍵是任何輸出資料行。 輸出寫入器的數目會遵循完全平行化 [查詢](stream-analytics-scale-jobs.md)的輸入資料分割。

## <a name="output-batch-size"></a>輸出批次大小

如需訊息大小上限，請參閱 [Azure 儲存體限制](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)。 預設為每個單一交易100個實體，但您可以視需要將其設定為較小的值。

## <a name="next-steps"></a>後續步驟

* [快速入門：使用 Azure 入口網站建立串流分析作業](stream-analytics-quick-create-portal.md)
* [快速入門：使用 Azure CLI 建立 Azure 串流分析作業](quick-create-azure-cli.md)
* [快速入門：使用 ARM 範本建立 Azure 串流分析作業](quick-create-azure-resource-manager.md)
* [快速入門：使用 Azure PowerShell 建立串流分析作業](stream-analytics-quick-create-powershell.md)
* [快速入門：使用 Visual Studio 建立 Azure 串流分析作業](stream-analytics-quick-create-vs.md)
* [快速入門：在 Visual Studio Code 中建立 Azure 串流分析作業](quick-create-visual-studio-code.md)