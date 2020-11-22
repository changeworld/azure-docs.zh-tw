---
title: 如何針對 Azure Data Lake Analytics 的 U-SQL 執行時間失敗進行疑難排解
description: 瞭解如何針對 U-SQL 執行時間失敗進行疑難排解。
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.date: 10/10/2019
ms.openlocfilehash: 41b7c80c85331f288343351749e6b2e5292b30c6
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2020
ms.locfileid: "95241602"
---
# <a name="learn-how-to-troubleshoot-u-sql-runtime-failures-due-to-runtime-changes"></a>瞭解如何針對執行時間變更所造成的非 SQL 執行時間失敗進行疑難排解

Azure Data Lake U-SQL 執行階段 (包括編譯器、最佳化工具和作業管理員) 會處理您的 U-SQL 程式碼。

## <a name="choosing-your-u-sql-runtime-version"></a>選擇您的 U SQL 執行階段版本

當您從 Visual Studio、ADL SDK 或 Azure Data Lake Analytics 入口網站提交 U SQL 工作時，您的作業將會使用目前可用的預設執行時間。 新版本的 U SQL 執行時間會定期發行，並同時包含次要更新和安全性修正。

您也可以選擇自訂執行階段版本;這可能是因為您想要試用新的更新、需要保持在舊版執行時間，或是在您無法等候定期新更新的回報問題上提供修正程式。

> [!CAUTION]
> 選擇與預設值不同的執行階段可能會中斷您的 U-SQL 作業。 使用這些其他版本僅供測試之用。

在罕見的情況下，Microsoft 支援服務可能會將不同版本的執行時間釘選為您帳戶的預設值。 請務必儘快還原此 pin。 如果您持續釘選到該版本，則會在稍後的日期到期。

### <a name="monitoring-your-jobs-u-sql-runtime-version"></a>監視您的作業 U-SQL 執行階段版本

您可以透過 Visual Studio 的工作瀏覽器或 Azure 入口網站的作業歷程記錄，查看您過去的工作在帳戶的作業歷程記錄中使用的執行階段版本歷程記錄。

1. 在 Azure 入口網站中，移至您的 Data Lake Analytics 帳戶。
2. 選取 [ **查看所有作業**]。 帳戶中的所有作用中和最近完成的工作清單隨即出現。
3. 選擇性，按一下 [篩選] 可協助您依照 [時間範圍]、[作業名稱] 和 [作者] 值尋找作業。
4. 您可以看到已完成的作業中所使用的執行時間。

![顯示過去作業的執行階段版本](./media/runtime-troubleshoot/prior-job-usql-runtime-version-.png)

可用的執行階段版本會隨時間變更。 預設的執行時間一律稱為「預設」，且我們至少會保留先前的執行時間以供使用，並讓有許多原因的特殊執行時間可供使用。 明確命名的執行時間通常會遵循下列格式 (斜體用於變數部分，而 [] 表示選擇性的部分) ：

release_YYYYMMDD_adl_buildno [_modifier]

例如，release_20190318_adl_3394512_2 表示執行時間3月 18 2019 的組建3394512的第二個版本，而 release_20190318_adl_3394512_private 表示相同版本的私用組建。 注意：此日期與上次簽入該版本的時間有關，而不一定是官方發行日期。


## <a name="troubleshooting-u-sql-runtime-version-issues"></a>疑難排解 U-SQL 執行階段版本問題

有兩個可能的執行階段版本問題可能會發生：

1. 腳本或某些使用者程式碼正在將行為從某個版本變更為下一個版本。 這類的重大變更通常會在發行的版本資訊發行時預先進行通訊。 如果您遇到這類重大變更，請聯絡 Microsoft 支援服務來回報此中斷行為 (如果尚未) 記載該行為，請將您的作業提交至舊版的執行階段版本。

2. 當您已將非預設執行時間釘選到您的帳戶，且該執行時間已在一段時間後移除時，您就會明確或隱含地使用非預設執行時間。 如果您遇到遺失的執行時間，請升級您的腳本，以使用目前的預設執行時間來執行。 如果您需要更多時間，請聯絡 Microsoft 支援服務

## <a name="known-issues"></a>已知問題

* 在檔案版本12.0.3 或 .USQL 腳本中的之後參考 Newtonsoft.Js，將會導致下列編譯失敗：

    *「很抱歉;在 Data Lake Analytics 帳戶中執行的作業可能會執行得更慢或無法完成。發生未預期的問題，導致我們無法將這項功能自動還原到您的 Azure Data Lake Analytics 帳戶。已聯絡 Azure Data Lake 工程師進行調查」。*  

    呼叫堆疊將包含的位置：  
    `System.IndexOutOfRangeException: Index was outside the bounds of the array.`  
    `at Roslyn.Compilers.MetadataReader.PEFile.CustomAttributeTableReader.get_Item(UInt32 rowId)`  
    `...`

    **解決方案**：請在 file v 12.0.2 或更低版本上使用 Newtonsoft.Js。


## <a name="see-also"></a>另請參閱

- [Azure 資料湖分析概觀](data-lake-analytics-overview.md)
- [使用 Azure 入口網站管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-portal.md)
- [使用 Azure 入口網站監視 Azure Data Lake Analytics 中的作業](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
