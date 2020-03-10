---
title: 使用 R Machine Learning 服務（預覽）
description: 本文說明 Azure SQL Database Machine Learning 服務（使用 R），並說明其運作方式。
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 11/20/2019
ms.openlocfilehash: ca223de2bc0b26e4968d400ea418761a399dacae
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78364593"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>使用 R Azure SQL Database Machine Learning 服務（預覽）

機器學習服務是 Azure SQL Database 的功能，用來執行資料庫內 R 指令碼。 此功能包含高效能預測性分析和機器學習服務的 Microsoft R 套件。 透過預存程序、包含 R 陳述式的 T-SQL 指令碼或包含 T-SQL 的 R 程式碼，關聯式資料可在 R 指令碼中使用。

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

> [!NOTE]
> 預覽適用于使用**一般用途**和**業務關鍵**服務層級中以 vCore 為基礎的購買模型的單一資料庫和彈性集區。 在此初始預覽版本中，不支援**超大規模資料庫**服務層級和**受控實例**部署選項。 目前，R 是唯一支援的語言。 目前不支援 Python。
>
> 預覽目前適用于下欄區域：西歐、北歐、美國西部2、美國東部、美國中南部、美國中北部、加拿大中部、東南亞、印度南部及澳大利亞東南部。

## <a name="what-you-can-do-with-r"></a>R 的用途

使用 R 語言的強大功能可提供在資料庫內部進行的進階分析和機器學習。 此功能可讓您在資料所在之處導入計算和處理功能，而無須透過網路提取資料。 此外，您也可以利用企業 R 套件的強大功能，大規模地傳遞先進的分析。

機器學習服務包含 R 的基底散發套件，並與來自 Microsoft 的企業 R 套件覆疊。 Microsoft 的 R 函式和演算法同時適用於大規模和公用程式，以提供預測性分析、統計模型、資料視覺效果，以及頂尖的機器學習演算法。

### <a name="r-packages"></a>R 套件

最常見的開放原始碼 R 套件會預先安裝在 Machine Learning 服務中。 此外，也包含下列來自 Microsoft 的 R 套件：

| R 封裝 | 描述|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | Microsoft R Open 是 Microsoft 提供的增強版 R。 其為一個適用於統計分析與資料科學的完整開放原始碼平台， 並以 R 為基礎而且與其 100% 相容，且包含可提升效能和重現性的額外功能。 |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | RevoScaleR 是擴充 R 的主要程式庫，此程式庫的函式最廣泛受到使用。 在這些程式庫中，可以找到資料轉換和操作、統計摘要、視覺效果，以及多種形式的模型與分析。 此外，這些程式庫中的函式會自動將工作負載發散到可用的核心進行平行處理，且能夠在計算引擎協調與管理的資料區塊上運作。 |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | MicrosoftML 新增機器學習演算法，可用來建立文字分析、影像分析和情緒分析的自訂模型。 |

除了預先安裝的套件之外，您還可以[安裝其他套件](sql-database-machine-learning-services-add-r-packages.md)。

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>註冊預覽版

> [!IMPORTANT]
> 註冊 Azure SQL Database Machine Learning 服務（預覽）目前已關閉。

在預覽期間，不建議針對生產環境工作負載使用 R 的 Machine Learning 服務。

## <a name="next-steps"></a>後續步驟

- 請參閱[SQL Server Machine Learning 服務的主要差異](sql-database-machine-learning-services-differences.md)。
- 若要瞭解如何使用 R 來查詢 Azure SQL Database Machine Learning 服務（預覽），請參閱[快速入門手冊](sql-database-connect-query-r.md)。
- 若要開始使用一些簡單的 R 腳本，請參閱[在 Azure SQL Database Machine Learning 服務（預覽）中建立和執行簡單的 r 腳本](sql-database-quickstart-r-create-script.md)。
