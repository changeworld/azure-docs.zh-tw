---
title: Azure SQL 受控執行個體中的 Machine Learning Services （預覽）
description: 本文提供 Azure SQL 受控執行個體中的總覽或 Machine Learning 服務。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: carlrab, davidph
manager: cgronlun
ms.date: 06/03/2020
ms.openlocfilehash: b5daf283df1ef5d6b42da5bf0a4652aedf2f6284
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708734"
---
# <a name="machine-learning-services-in-azure-sql-managed-instance-preview"></a>Azure SQL 受控執行個體中的 Machine Learning Services （預覽）

Machine Learning Services 是 Azure SQL 受控執行個體（預覽）的一項功能，可提供資料庫內機器學習服務，同時支援 Python 和 R 腳本。 此功能包括適用于高效能預測性分析和機器學習的 Microsoft Python 和 R 套件。 關聯式資料可以透過預存程式、包含 Python 或 R 語句的 T-sql 腳本，或包含 T-sql 的 Python 或 R 程式碼，在腳本中使用。

> [!IMPORTANT]
> 機器學習服務是 Azure SQL 受控執行個體的功能，目前處於公開預覽狀態。
> 這項預覽功能一開始是在美國、東南亞和澳大利亞的有限數量區域中提供，稍後再新增其他區域。
>
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>
> 請至下方[註冊預覽版](#signup)。

## <a name="what-is-machine-learning-services"></a>什麼是機器學習服務？

Azure SQL 受控執行個體中的 Machine Learning 服務可讓您在資料庫內執行 Python 和 R 腳本。 您可以使用此服務來準備和清除資料、進行特徵工程，然後在資料庫內訓練、評估，以及部署機器學習模型。 此功能可讓您在資料所在的位置執行指令碼，而無須透過網路將資料傳輸至另一部伺服器。

搭配 Azure SQL 受控執行個體中的 R/Python 支援使用 Machine Learning 服務，以執行下列動作：

- 執行**r 和 python 腳本來進行資料準備和一般用途資料處理**-您現在可以將 R/Python 腳本帶入您的資料所在的 Azure SQL 受控執行個體，而不需要將資料移出至其他伺服器來執行 R 和 Python 腳本。 您可以避免資料移動的需求，以及與延遲、安全性和合規性相關的相關問題。

- **在資料庫中訓練機器學習模型**-您可以使用任何開放原始碼演算法來定型模型。 您可以輕鬆地將訓練調整為整個資料集，而不是依賴從資料庫提取的範例資料集。

- **在預存程式中將您的模型和腳本部署到生產環境**-只要在 t-sql 預存程式中內嵌腳本和定型模型，就可以運作它們。 連線到 Azure SQL 受控執行個體的應用程式可以透過呼叫預存程式，受益于這些模型中的預測和智慧。 您也可以使用原生 T-sql PREDICT 函式來讓模型，以在高度並行的即時評分案例中快速計分。

機器學習服務包含了 Python 和 R 的基本發行版。 除了適用於 Python 的 [revoscalepy](https://docs.microsoft.com/sql/advanced-analytics/python/ref-py-revoscalepy) 和 [microsoftml](https://docs.microsoft.com/sql/advanced-analytics/python/ref-py-microsoftml)，以及適用於 R 的 [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler)、[MicrosoftML](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml)、[olapR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-olapr) 和 [sqlrutils](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-sqlrutils) 這些上述 Microsoft 套件以外，您還可以安裝並使用開放原始碼套件和架構，例如 PyTorch、TensorFlow 和 scikit-learn。

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>註冊預覽版

此有限公開預覽版受限於[Azure 預覽條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 

如果您想要加入預覽計畫並接受這些條款，您可以在上建立 Azure 支援票證以要求註冊 [https://azure.microsoft.com/support/create-ticket/](https://azure.microsoft.com/support/create-ticket/) 。 針對 [問題類型]，選取 [技術]，針對 [服務] 選取 [SQL Database 受控執行個體]，並針對 [問題類型] 選取 [其他]。 請在要求中，說明想註冊適用於 SQL 受控執行個體的機器學習之有限公開預覽版本，並提供下列詳細資料：邏輯伺服器名稱、區域和訂用帳戶識別碼。

註冊方案後，Microsoft 即會將您加入公開預覽版，並為您現有或新的資料庫啟用機器學習服務。

在公開預覽期間，請勿將 SQL 受控執行個體中的機器學習服務用於生產工作負載。

## <a name="next-steps"></a>後續步驟

- 請參閱[SQL Server Machine Learning 服務的主要差異](machine-learning-services-differences.md)。
- 若要瞭解如何在 Machine Learning 服務中使用 Python，請參閱[執行 python 腳本](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15)。
- 若要瞭解如何在 Machine Learning 服務中使用 R，請參閱[執行 r 腳本](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15)。
- 如需有關其他 SQL 平臺上機器學習的詳細資訊，請參閱[SQL 機器學習檔](https://docs.microsoft.com/sql/machine-learning/)。
