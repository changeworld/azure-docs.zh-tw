---
title: 如何針對 Azure Data Lake Analytics 的 U-SQL 執行時間失敗進行疑難排解
description: 瞭解如何針對 U-SQL 執行時間失敗進行疑難排解。
services: data-lake-analytics
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/10/2019
ms.openlocfilehash: 54524b0528f94ca9386c2d0d45ba4393c965fa88
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87128806"
---
# <a name="learn-how-to-troubleshoot-u-sql-runtime-failures-due-to-runtime-changes"></a>瞭解如何針對因為執行時間變更而發生的 U-SQL 執行時間失敗進行疑難排解

Azure Data Lake 的 U-SQL 執行時間（包括編譯器、優化工具和作業管理員）會處理您的 U-SQL 程式碼。

## <a name="choosing-your-u-sql-runtime-version"></a>選擇您的 U-SQL 執行階段版本

當您從 Visual Studio、ADL SDK 或 Azure Data Lake Analytics 入口網站提交 U-SQL 作業時，您的作業將會使用目前可用的預設執行時間。 新版本的 U-SQL 執行時間會定期發行，同時包含次要更新和安全性修正程式。

您也可以選擇自訂執行階段版本;可能是因為您想要試用新的更新、需要停留在舊版的執行時間，或是針對回報的問題提供一個修正程式，而您無法等候週期性新更新。

> [!CAUTION]
> 選擇與預設值不同的執行時間可能會中斷您的 U-SQL 作業。 請改用這些其他版本來進行測試。

在罕見的情況下，Microsoft 支援服務可能會將不同版本的執行時間釘選為您帳戶的預設值。 請確定您已儘快還原此 pin。 如果您持續釘選到該版本，它會在稍後的日期到期。

### <a name="monitoring-your-jobs-u-sql-runtime-version"></a>監視您的作業 U-SQL 執行階段版本

您可以透過 Visual Studio 的作業瀏覽器或 Azure 入口網站的作業歷程記錄，查看您過去作業在帳戶的作業歷程記錄中使用的執行階段版本歷程記錄。

1. 在 Azure 入口網站中，移至您的 Data Lake Analytics 帳戶。
2. 選取 [**查看所有作業**]。 帳戶中所有作用中和最近完成的作業清單隨即出現。
3. 選擇性，按一下 [篩選]**** 可協助您依照 [時間範圍]****、[作業名稱]**** 和 [作者]**** 值尋找作業。
4. 您可以查看已完成的工作中所使用的執行時間。

![顯示過去作業的執行階段版本](./media/runtime-troubleshoot/prior-job-usql-runtime-version-.png)

可用的執行階段版本會隨著時間變更。 預設的執行時間一律稱為「預設」，而且我們會保留至少舊版的執行時間，並且讓特殊執行時間可供各種不同的原因使用。 明確命名的執行時間通常會遵循下列格式（針對變數部分使用斜體，而 [] 表示選擇性的部分）：

release_YYYYMMDD_adl_buildno [_modifier]

例如，release_20190318_adl_3394512_2 表示執行時間版本3月 18 2019 的第二版組建3394512，而 release_20190318_adl_3394512_private 表示相同版本的私用組建。 注意：當上次簽入已針對該發行，而不一定是官方發行日期時，該日期與相關。

以下是目前可用的執行階段版本。

- release_20190318_adl_3394512
- release_20190318_adl_5832669 目前的預設值
- release_20190703_adl_4713356

## <a name="troubleshooting-u-sql-runtime-version-issues"></a>針對 U-SQL 執行時間版本問題進行疑難排解

您可能會遇到兩個可能的執行階段版本問題：

1. 腳本或部分使用者程式碼會將行為從某個版本變更為下一個版本。 這類重大變更通常會在發行的版本資訊發佈之前先行傳遞。 如果您遇到這種重大變更，請洽詢 Microsoft 支援服務以回報此中斷行為（如果尚未記載），並針對舊版執行階段版本提交您的作業。

2. 當您已將非預設的執行時間釘選到您的帳戶時，您已明確或隱含地使用它，而且該執行時間已在一段時間後移除。 如果您遇到遺失的執行時間，請升級您的腳本，以使用目前的預設執行時間來執行。 如果您需要額外的時間，請聯絡 Microsoft 支援服務

## <a name="see-also"></a>請參閱

- [Azure 資料湖分析概觀](data-lake-analytics-overview.md)
- [使用 Azure 入口網站管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-portal.md)
- [使用 Azure 入口網站監視 Azure Data Lake Analytics 中的作業](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
