---
title: Data Lake Analytics 近期變更項目
description: 本文提供 Data Lake Analytics 近期變更項目清單。
author: xujiang1
ms.service: data-lake-analytics
ms.topic: overview
ms.author: xujiang1
ms.date: 07/31/2020
ms.openlocfilehash: 6f4885594e4f67d91c595d8afdddc15e8ed4ae75
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263175"
---
# <a name="whats-new-in-data-lake-analytics"></a>Data Lake Analytics 的新功能

Azure Data Lake Analytics 會因特定元件而定期更新。 為了讓您隨時掌握最新的更新訊息，本文提供下列相關資訊：

- 重要元件搶鮮版 (Beta) 通知
- 重要元件版本資訊，例如：元件的可用版本清單、目前的預設版本等等。


## <a name="notification-of-key-component-beta-preview"></a>重要元件搶鮮版 (Beta) 預覽通知

沒有可供預覽的重要元件搶鮮版 (Beta)。 

## <a name="u-sql-runtime"></a>U-SQL 執行階段

Azure Data Lake U-SQL 執行階段 (包括編譯器、最佳化工具和作業管理員) 會處理您的 U-SQL 程式碼。

從任何工具提交 Azure Data Lake Analytics 作業時，您的作業將會使用生產環境中目前可用的預設執行階段。 

執行階段版本將會定期更新。 而先前的執行階段將會保留一段時間。 當新的搶鮮版 (Beta) 準備好推出預覽版本時，也會在該處提供。

> [!CAUTION]
> - 選擇與預設值不同的執行階段可能會中斷您的 U-SQL 作業。 強烈建議不要將這些非預設版本用於生產環境，只建議供測試之用。
> - 非預設的執行階段版本具有固定的生命週期， 且會自動過期。

下列版本是目前的預設執行階段版本。

- release-20200124live_adl_16283022_2

若要了解如何針對 U-SQL 執行階段失敗進行疑難排解，請參閱 [U-SQL 執行階段失敗疑難排解](runtime-troubleshoot.md)。

## <a name="net-framework"></a>.NET Framework

Azure Data Lake Analytics 現在使用的是 **.NET Framework v4.7.2**。 

如果您的 Azure Data Lake Analytics U-SQL 指令碼使用自訂群組件，而這些自訂群組件使用 .NET 程式庫，請驗證您的程式碼，以檢查是否有任何中斷。

若要了解如何針對 .NET 升級進行疑難排解，請使用 [.NET 升級疑難排解](runtime-troubleshoot.md)。

## <a name="release-note"></a>版本資訊

如需近期更新的詳細資料，請參閱 [Azure Data Lake Analytics 版本資訊](https://github.com/Azure/AzureDataLake/tree/master/docs/Release_Notes)。


## <a name="next-steps"></a>後續步驟

* 透過 [Azure 入口網站](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [CLI](data-lake-analytics-get-started-cli.md)開始使用 Data Lake Analytics

