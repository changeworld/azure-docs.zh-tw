---
title: 如何排除 Azure 資料湖分析 U-SQL 運行時故障
description: 瞭解如何排除 U-SQL 運行時故障。
services: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/10/2019
ms.openlocfilehash: 1e3fb218e6cda5619bfa1a0936e07d6731a9cc93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73648450"
---
# <a name="learn-how-to-troubleshoot-u-sql-runtime-failures-due-to-runtime-changes"></a>瞭解如何排除由於運行時更改而導致的 U-SQL 運行時故障

Azure 資料湖 U-SQL 運行時（包括編譯器、優化器和作業管理器）是處理 U-SQL 代碼的內容。

## <a name="choosing-your-u-sql-runtime-version"></a>選擇 U-SQL 執行階段版本

當您從 Visual Studio、ADL SDK 或 Azure 資料湖分析門戶提交 U-SQL 作業時，作業將使用當前可用的預設運行時。 新版本的 U-SQL 運行時定期發佈，包括次要更新和安全修補程式。

您還可以選擇自訂執行階段版本;也可以選擇自訂執行階段版本。或者因為您想要嘗試新的更新，需要保留舊版本的運行時，或者為報告的問題提供了修補程式，而您無法等待常規的新更新。

> [!CAUTION]
> 選擇與預設值不同的運行時可能會中斷 U-SQL 作業。 僅使用這些其他版本進行測試。

在極少數情況下，Microsoft 支援可能會將不同版本的運行時固定為您的帳戶的預設值。 請確保儘快恢復此引腳。 如果仍固定到該版本，它將在稍後某個日期過期。

### <a name="monitoring-your-jobs-u-sql-runtime-version"></a>監視作業 U-SQL 執行階段版本

您可以通過 Visual Studio 的作業瀏覽器或 Azure 門戶的作業歷史記錄查看過去作業在帳戶作業歷史記錄中使用的執行階段版本的歷史記錄。

1. 在 Azure 入口網站中，移至您的 Data Lake Analytics 帳戶。
2. 選擇 **"查看所有作業**"。 將顯示帳戶中所有活動作業和最近完成作業的清單。
3. 選擇性，按一下 [篩選]**** 可協助您依照 [時間範圍]****、[作業名稱]**** 和 [作者]**** 值尋找作業。
4. 您可以看到已完成作業中使用的運行時。

![顯示過去作業的執行階段版本](./media/runtime-troubleshoot/prior-job-usql-runtime-version-.png)

可用的執行階段版本會隨時間而變化。 預設運行時始終稱為"預設"，我們至少保留以前的運行時可用一段時間，以及使特殊運行時可用的原因有多種。 顯式命名的運行時通常遵循以下格式（斜體用於可變零件，[] 表示可選部件）：

release_YYYYMMDD_adl_buildno[_modifier]

例如，release_20190318_adl_3394512_2 是指 2019 年 3 月 18 日執行階段版本的生成 3394512 的第二個版本，release_20190318_adl_3394512_private是指同一版本的私有生成。 注意：日期與該版本上次簽入時相關，不一定是正式發佈日期。

以下是當前可用的執行階段版本。

- release_20190318_adl_3394512
- release_20190318_adl_5832669當前預設值
- release_20190703_adl_4713356

## <a name="troubleshooting-u-sql-runtime-version-issues"></a>解決 U-SQL 執行階段版本問題

您可能會遇到兩個可能的執行階段版本問題：

1. 腳本或某些使用者代碼正在將行為從一個版本更改為下一個版本。 此類重大更改通常隨著版本資訊的發佈而提前傳達。 如果您遇到此類重大更改，請聯繫 Microsoft 支援部門報告此突發行為（如果尚未記錄），並針對較舊的執行階段版本提交作業。

2. 在非預設運行時已顯式或隱式使用時，該運行時已固定到您的帳戶，並且該運行時在一段時間後已被刪除。 如果遇到缺少運行時，請升級腳本以使用當前預設運行時運行。 如果您需要更多時間，請聯繫 Microsoft 支援

## <a name="see-also"></a>另請參閱

- [Azure 資料湖分析概觀](data-lake-analytics-overview.md)
- [使用 Azure 門戶管理 Azure 資料湖分析](data-lake-analytics-manage-use-portal.md)
- [使用 Azure 入口網站監視 Azure Data Lake Analytics 中的作業](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
