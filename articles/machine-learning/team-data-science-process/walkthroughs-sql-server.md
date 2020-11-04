---
title: R、Python 和 T-SQL 的 SQL Server 上分析 - Team Data Science Process
description: 逐步解說在 SQL Server 中使用 R、Python 和 T-sql 來執行預測性分析的範例。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a8789050257b8bc67649233760baefe7e2ee9adf
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93320157"
---
# <a name="sql-server-data-science-walkthroughs-using-r-python-and-t-sql"></a>使用 R、Python 和 T-sql SQL Server 資料科學逐步解說

這些逐步解說會使用 SQL Server、SQL Server R Services 和 SQL Server Python Services 來執行預測性分析。 R 和 Python 程式碼會部署在預存程序中。 其遵循 Team Data Science Process 中所述的步驟。 如需 Team Data Science Process 的概觀，請參閱 [Data Science Process](overview.md)。 

執行 Team Data 科學程式的其他資料科學逐步解說，會依其使用的 **平臺** 進行分組。 如需這些範例的列述，請參閱[執行 Team Data Science Process 的逐步解說](walkthroughs.md)。


## <a name="predict-taxi-tips-using-python-and-sql-queries-with-sql-server"></a>使用 Python 和 SQL 查詢搭配 SQL Server 來預測計程車小費 

[使用 SQL Server](sql-walkthrough.md)逐步解說顯示如何建立和部署機器學習服務分類和回歸模型。  資料是公開可用的 NYC 計程車行程和費用資料集。


## <a name="predict-taxi-tips-using-microsoft-r-with-sql-server"></a>使用 Microsoft R 搭配 SQL Server 來預測計程車小費 

[使用 SQL Server R Services](/sql/machine-learning/tutorials/walkthrough-data-science-end-to-end-walkthrough)逐步解說會示範如何建立 R 模型，並將其部署至 SQL Server。 此逐步解說是為了向 R 開發人員介紹 R Services (資料庫內) 所設計。


## <a name="predict-taxi-tips-using-r-from-t-sql-or-stored-procedures-with-sql-server"></a>使用 T-SQL 或預存程序中的 R 搭配 SQL Server 來預測計程車小費

[Data science walkthrough for R and SQL Server](/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough) (適用於 R 和 SQL Server 的資料科學逐步解說) 會對 SQL 程式設計人員提供使用 SQL Server R Services 來建置搭配 Transact-SQL 的進階分析解決方案的體驗，以操作 R 解決方案。 


## <a name="predict-taxi-tips-using-python-in-sql-server-stored-procedures"></a>使用 SQL Server 預存程序中的 Python 來預測計程車小費

[使用 T-SQL 與 SQL Server Python Services](/sql/advanced-analytics/tutorials/sqldev-in-database-python-for-sql-developers) 逐步解說可向 SQL 程式設計師提供在 SQL Server 中建置機器學習解決方案的經驗。 該逐步解說會說明如何透過將 Python 程式碼新增至預存程序，來將 Python 合併到應用程式中。


## <a name="next-steps"></a>後續步驟

如需組成 Team Data Science Process 之主要元件的討論，請參閱 [Team Data Science Process 概觀](overview.md)。

如需您可以用來結構化資料科學專案之 Team Data Science Process 生命週期的討論，請參閱 [Team Data Science Process 生命週期](lifecycle.md)。 生命週期會概述專案在執行時通常會遵循之從開始到完成的步驟。