---
title: 查詢效能深入解析
description: 查詢效能監視會識別 Azure SQL Database 中單一和集區資料庫的最多 CPU 耗用和長時間執行的查詢。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: jrasnik, sstein
ms.date: 03/10/2020
ms.openlocfilehash: be7e4a641e5b5ac2ef755037142cfd8063d66b5d
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448883"
---
# <a name="query-performance-insight-for-azure-sql-database"></a>Azure SQL Database 的查詢效能深入解析
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

查詢效能深入解析針對單一和集區資料庫提供智慧型查詢分析。 它可協助您識別工作負載中最重要的資源耗用和長時間執行的查詢。 這可協助您找出要優化的查詢，以提升整體工作負載效能，並有效率地使用您要支付的資源。 查詢效能深入解析藉由提供下列功能，協助您針對資料庫效能花較少的時間進行疑難排解：

* 深入瞭解您的資料庫資源 (DTU) 耗用量
* 依 CPU、持續時間和執行計數的最上層資料庫查詢詳細資料 (可能的微調候選效能改進) 
* 深入瞭解查詢詳細資料的能力，以查看查詢文字和資源使用率的歷程記錄
* 顯示[資料庫顧問](database-advisor-implement-performance-recommendations.md)效能建議的注釋

![查詢效能深入解析](./media/query-performance-insight-use/opening-title.png)

## <a name="prerequisites"></a>必要條件

「查詢效能深入解析」要求 [查詢存放區](https://msdn.microsoft.com/library/dn817826.aspx) 在您的資料庫上為作用中狀態。 預設會自動啟用 Azure SQL Database 中的所有資料庫。 如果查詢存放區未執行，Azure 入口網站會提示您加以啟用。

> [!NOTE]
> 如果入口網站中出現「此資料庫的查詢存放區未正確設定」訊息，請參閱[最佳化查詢存放區組態](#optimize-the-query-store-configuration)。

## <a name="permissions"></a>權限

您需要下列 [azure 角色型存取控制 (AZURE RBAC) ](../../role-based-access-control/overview.md) 使用查詢效能深入解析的許可權：

* 檢視最耗用資源的查詢和圖表時，需具備**讀取者**、**擁有者**、**參與者**、**SQL DB 參與者** 或 **SQL Server 參與者**權限。
* 檢視查詢文字時，需具備**擁有者**、**參與者**、**SQL DB 參與者**或 **SQL Server 參與者**權限。

## <a name="use-query-performance-insight"></a>使用查詢效能深入解析

「查詢效能深入解析」很容易使用︰

1. 開啟[Azure 入口網站](https://portal.azure.com/)，然後尋找您想要檢查的資料庫。
2. 從左側功能表中，開啟 [**智慧型效能**  >  **查詢效能深入解析**]。
  
   ![功能表上的查詢效能深入解析](./media/query-performance-insight-use/tile.png)

3. 在第一個索引標籤上，檢閱排名最前面的資源取用查詢清單。
4. 選取個別的查詢來檢視其詳細資料。
5. 開啟**智慧型效能**  >  **效能建議**，並檢查是否有任何可用的效能建議。 如需內建效能建議的詳細資訊，請參閱 [Azure SQL Database Advisor](database-advisor-implement-performance-recommendations.md)。
6. 使用滑桿或縮放圖示來變更觀測的間隔。

   ![效能儀表板](./media/query-performance-insight-use/performance.png)

> [!NOTE]
> 若要讓 Azure SQL Database 在查詢效能深入解析中呈現資訊，查詢存放區需要捕獲幾小時的資料。 如果在某段時間內資料庫沒有活動，或查詢存放區處於非作用中狀態，則在「查詢效能深入解析」顯示該時間範圍時，圖表將是空的。 如果查詢存放區未執行，您可以隨時加以啟用。 如需詳細資訊，請參閱[查詢存放區的最佳做法](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store)。
>

如需資料庫效能的建議，請選取 [查詢效能深入解析] 導覽刀鋒視窗上的[建議](database-advisor-implement-performance-recommendations.md)。

![建議索引標籤](./media/query-performance-insight-use/ia.png)

## <a name="review-top-cpu-consuming-queries"></a>檢閱最耗用 CPU 的查詢

根據預設，「查詢效能深入解析」在您第一次加以開啟時，會顯示最耗用 CPU 的前五個查詢。

1. 使用核取方塊，選取或清除圖表要包含或排除的個別查詢。

   第一行會顯示資料庫的整體 DTU 百分比。 長條圖會顯示選取的查詢在所選間隔期間內耗用的 CPU 百分比。 例如，如果選取 [過去一週]****，則每個長條分別代表一天。

   ![最高排名的查詢](./media/query-performance-insight-use/top-queries.png)

   > [!IMPORTANT]
   > 顯示的 DTU 行會彙總為一小時期間內的最大耗用量值。 這只能用來與查詢執行統計資料進行概略性的比較。 在某些情況下，DTU 使用率與執行的查詢相較時可能會顯得太高，但實際上可能並非如此。
   >
   > 例如，如果查詢達到 DTU 的極限 100% 只有幾分鐘的時間，查詢效能深入解析的 DTU 行仍會將整個小時的耗用量顯示為 100% (最終的最大彙總值)。
   >
   > 如需更精細的比較 (最精細為一分鐘)，請考慮建立自訂 DTU 使用率圖表：
   >
   > 1. 在 [Azure 入口網站中，選取**Azure SQL Database**  >  **監視**]。
   > 2. 選取 [計量]。
   > 3. 選取 [+新增圖表]****。
   > 4. 選取圖表上的 DTU 百分比。
   > 5. 此外，在左上方的功能表上選取 [過去 24 小時]****，並將其變更為一分鐘。
   >
   > 使用資料詳細程度更精細的自訂 DTU 圖表，與查詢執行圖表進行比較。

   底部的方格會顯示可見查詢的彙總資訊：

   * 查詢識別碼，這是查詢在資料庫中的唯一識別碼。
   * 每個查詢在可觀察間隔期間內的 CPU，這取決於彙總函式。
   * 每個查詢的持續時間，這同樣也取決於彙總函式。
   * 特定查詢的總執行次數。

2. 如果您的資料過期了，請選取 [重新整理]**** 按鈕。

3. 使用滑桿和縮放按鈕來變更觀測間隔，並調查耗用量尖峰︰

   ![用來變更間隔的滑桿和縮放按鈕](./media/query-performance-insight-use/zoom.png)

4. 您可以選擇性地選取 [自訂]**** 索引標籤，以自訂下列項目的檢視：

   * 計量 (CPU、持續時間、執行計數)。
   * 時間間隔 (過去 24 小時、過去一週，或過去一個月)。
   * 查詢數目。
   * 彙總函式。
  
   ![自訂索引標籤](./media/query-performance-insight-use/custom-tab.png)
  
5. 選取 [前往 >]**** 按鈕以檢視自訂檢視。

   > [!IMPORTANT]
   > 「查詢效能深入解析」限定為顯示最耗用資源的 5 到 20 個查詢，視您選取的項目而定。 您的資料庫所執行的查詢數可能遠高於最高排名所顯示的，但這些查詢並不會包含在圖表中。
   >
   > 也可能會有某個資料庫工作負載類型有許多較小的查詢，頻繁地執行並使用大多數的 DTU，但並未顯示於最高排名的查詢中。 這些查詢不會出現在效能圖表上。
   >
   > 例如，某個查詢可能已耗用大量 DTU 一段時間，但它在觀察期間內的總耗用量小於其他最耗用資源的查詢。 在此情況下，此查詢的資源使用率並不會出現在圖表上。
   >
   > 如果您需要了解超出查詢效能深入解析限制外的最高排名查詢執行情形，請考慮使用 [Azure SQL 分析](../../azure-monitor/insights/azure-sql.md)，執行進階的資料庫效能監視和疑難排解。
   >

## <a name="view-individual-query-details"></a>檢視個別查詢詳細資料

若要檢視查詢詳細資料︰

1. 選取最高排名查詢清單中的任何查詢。

    ![最高排名查詢清單](./media/query-performance-insight-use/details.png)

   詳細檢視隨即開啟。 其中會顯示一段時間內的 CPU 耗用量、持續時間和執行計數。

2. 選取圖表功能以取得詳細資料。

   * 最上方的圖表會顯示整體資料庫 DTU 百分比的資料行。 長條圖代表選取的查詢所耗用的 CPU 百分比。
   * 第二個圖表會顯示所選查詢的總持續時間。
   * 底層圖表則顯示所選查詢的執行總數。

   ![查詢詳細資料](./media/query-performance-insight-use/query-details.png)

3. (選擇性) 使用滑桿、使用縮放按鈕或選取 [設定]****，來自訂查詢資料的顯示方式或挑選不同的時間範圍。

   > [!IMPORTANT]
   > 「查詢效能深入解析」不會擷取任何 DDL 查詢。 在某些情況下，它可能不會擷取所有的臨機操作查詢。
   >

## <a name="review-top-queries-per-duration"></a>針對每段持續時間檢閱排名最前面的查詢

「查詢效能深入解析」有兩項計量可協助您找出潛在的瓶頸︰持續時間和執行計數。

長時間執行的查詢最有可能會鎖定資源較長的時間、封鎖其他使用者，以及限制擴充性。 它們也是最適合進行最佳化的項目。

若要識別長時間執行的查詢：

1. 針對選取的資料庫，在 [查詢效能深入解析] 中開啟 [自訂]**** 索引標籤。
2. 將計量變更為 [持續時間]****。
3. 選取查詢數目和觀測間隔。
4. 選取彙總函式：

   * **Sum** 會加總整個觀測間隔內的所有查詢執行時間。
   * **Max** 會尋找在整個觀測間隔內執行時間最久的查詢。
   * **Avg** 會尋找所有查詢執行的平均執行時間，並顯示這些平均值中排名最高者。

   ![查詢持續時間](./media/query-performance-insight-use/top-duration.png)

5. 選取 [前往 >]**** 按鈕以檢視自訂檢視。

   > [!IMPORTANT]
   > 調整查詢檢視並不會更新 DTU 行。 DTU 行一律會顯示間隔內的最大耗用量值。
   >
   > 若要更詳盡地了解資料庫的 DTU 耗用量 (最詳細為一分鐘)，請考慮在 Azure 入口網站中建立自訂圖表：
   >
   > 1. 選取**Azure SQL Database**  >  **監視**]。
   > 2. 選取 [計量]。
   > 3. 選取 [+新增圖表]****。
   > 4. 選取圖表上的 DTU 百分比。
   > 5. 此外，在左上方的功能表上選取 [過去 24 小時]****，並將其變更為一分鐘。
   >
   > 建議您使用自訂 DTU 圖表與查詢效能圖表相比較。
   >

## <a name="review-top-queries-per-execution-count"></a>針對每個執行計數檢閱排名最前面的查詢

即便執行數目很高可能不會影響資料庫本身，且資源使用量也很低，但使用資料庫的應用程式可能會變慢。

在某些情況下，很高的執行計數可能會導致網路往返次數增加。 往返次數會影響效能。 它們會受限於網路延遲和下游伺服器延遲。

例如，許多資料導向的網站會針對每個使用者要求大量存取資料庫。 雖然連接共用有助於，但是伺服器上增加的網路流量和處理負載可能會降低效能。 一般情況下，應盡可能壓低往返次數。

若要識別經常執行的 ( 「多對話」 ) 查詢：

1. 針對選取的資料庫，在 [查詢效能深入解析] 中開啟 [自訂]**** 索引標籤。
2. 將計量變更為 [執行計數]****。
3. 選取查詢數目和觀測間隔。
4. 選取 [前往 >]**** 按鈕以檢視自訂檢視。

   ![查詢執行計數](./media/query-performance-insight-use/top-execution.png)

## <a name="understand-performance-tuning-annotations"></a>了解效能調整註釋

在「查詢效能深入解析」中瀏覽您的工作負載時，您可能會注意到圖表最上方含有垂直線的圖示。

這些圖示是註釋。 它們會顯示 [Azure SQL Database Advisor](database-advisor-implement-performance-recommendations.md)的效能建議。 將滑鼠停留在註釋上方，可取得關於效能建議的摘要資訊。

   ![查詢註釋](./media/query-performance-insight-use/annotation.png)

如果您想要了解更多資訊或套用建議程式的建議，請選取圖示以開啟建議動作的詳細資料。 如果是有效的建議，您就可以從入口網站立即加以套用。

   ![查詢註釋詳細資料](./media/query-performance-insight-use/annotation-details.png)

在某些情況下，由於縮放層級的關係，彼此接近的註釋可能會摺疊成單一註釋。 「查詢效能深入解析」會將其顯示為群組註釋圖示。 選取群組註釋圖示將會開啟新的刀鋒視窗，而列出註釋。

使查詢和效能調整動作相互關聯，有助於深入了解您的工作負載。

## <a name="optimize-the-query-store-configuration"></a>優化查詢存放區設定

在使用查詢效能深入解析時，您可能會看到下列查詢存放區錯誤訊息：

* 「此資料庫的查詢存放區未正確設定。 請按一下這裡深入了解。」
* 「此資料庫的查詢存放區未正確設定。 請按一下這裡變更設定。」

這些訊息通常會在查詢存放區無法收集新資料時出現。

當查詢存放區處於唯讀狀態並且以最佳方式設定參數時，就會發生第一種狀況。 您可以藉由增加資料存放區的大小或清除查詢存放區，來修正此問題。 (如果您清除查詢存放區，所有先前收集到的遙測資料都將遺失。)

   ![查詢存放區詳細資料](./media/query-performance-insight-use/qds-off.png)

當查詢存放區未啟用，或參數並不是以最佳方式所設定時，就會發生第二種狀況。 您可以執行 [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 或 Azure 入口網站所提供的下列命令，以變更保留和擷取原則，以及啟用查詢存放區。

### <a name="recommended-retention-and-capture-policy"></a>建議使用的保留期和擷取原則

保留期原則有兩種：

* **大小**：如果此原則設定為 [ **自動**]，則會在達到接近最大值時自動清除資料。
* 以**時間為基礎：根據**預設，此原則設為30天。 如果查詢存放區的空間用盡，就會刪除 30 天之前的查詢資訊。

您可以將擷取原則設為：

* **All**：查詢存放區會捕捉所有查詢。
* **Auto**：查詢存放區忽略無意義的編譯和執行期間的不頻繁查詢和查詢。 執行次數、編譯持續時間和執行階段持續時間的閾值皆由內部決定。 這是預設選項。
* **無**：查詢存放區停止捕捉新的查詢，但仍會收集已捕捉的查詢執行時間統計資料。

建議您從 [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 或 Azure 入口網站執行下列命令，將所有原則設為 [自動]****，並將清除原則設為 30 天。 (請將 `YourDB` 取代為資料庫名稱)。

```sql
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);
```

透過 [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 或 Azure 入口網站連線到資料庫，並執行下列查詢，以增加查詢存放區的大小。 (請將 `YourDB` 取代為資料庫名稱)。

```SQL
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);
```

套用這些設定，最終會使查詢存放區收集新查詢的遙測資料。 如果您需要立即執行查詢存放區，可以選擇透過 SSMS 或 Azure 入口網站執行下列查詢，以清除查詢存放區。 (請將 `YourDB` 取代為資料庫名稱)。

> [!NOTE]
> 執行下列查詢，將會從查詢存放區中刪除所有先前收集到的受監視遙測資料。

```SQL
    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;
```

## <a name="next-steps"></a>後續步驟

請考慮使用 [Azure SQL 分析](../../azure-monitor/insights/azure-sql.md) ，以進行大量單一和集區資料庫、彈性集區、受控實例和實例資料庫的先進效能監視。
