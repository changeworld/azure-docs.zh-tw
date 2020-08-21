---
title: 'Azure SQL 中的 Machine Learning 服務受控執行個體 (preview) '
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
ms.openlocfilehash: ad25a9a62757e1e031ce8b93a44e6f2ada4d9964
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689502"
---
# <a name="machine-learning-services-in-azure-sql-managed-instance-preview"></a>Azure SQL 中的 Machine Learning 服務受控執行個體 (preview) 

Machine Learning Services 是 Azure SQL 受控執行個體 (preview) 的功能，可提供資料庫內機器學習，同時支援 Python 和 R 腳本。 此功能包含適用于高效能預測性分析和機器學習的 Microsoft Python 和 R 套件。 您可以透過預存程式、包含 Python 或 R 語句的 T-sql 腳本，或包含 T-sql 的 Python 或 R 程式碼，在腳本中使用關聯式資料。

> [!IMPORTANT]
> 機器學習服務是 Azure SQL 受控執行個體的功能，目前處於公開預覽狀態。
> 這項預覽功能一開始僅在美國、歐洲和澳大利亞的區域數量有限，並在稍後新增其他區域。
>
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>
> 請至下方[註冊預覽版](#signup)。

## <a name="what-is-machine-learning-services"></a>什麼是機器學習服務？

Azure SQL 受控執行個體中的 Machine Learning 服務可讓您在資料庫中執行 Python 和 R 腳本。 您可以使用此服務來準備和清除資料、進行特徵工程，然後在資料庫內訓練、評估，以及部署機器學習模型。 此功能可讓您在資料所在的位置執行指令碼，而無須透過網路將資料傳輸至另一部伺服器。

使用 Machine Learning 服務搭配 Azure SQL 受控執行個體中的 R/Python 支援來：

- 執行**r 和 Python 腳本以進行資料準備和一般用途的資料處理**-您現在可以將 R/python 腳本帶到 Azure SQL 受控執行個體您的資料所在位置，而不需要將資料移到其他伺服器來執行 R 和 Python 腳本。 您可以消除與延遲、安全性和合規性相關的資料移動和相關問題的需求。

- **在資料庫中訓練機器學習模型** -您可以使用任何開放原始碼演算法來定型模型。 您可以輕鬆地將訓練調整至整個資料集，而不需依賴從資料庫提取的範例資料集。

- **在預存程式中將模型和腳本部署到生產環境中** ，只要在 t-sql 預存程式中內嵌腳本和定型的模型就可以實際運作。 連接到 Azure SQL 受控執行個體的應用程式可以藉由直接呼叫預存程式，在這些模型中受益于預測和智慧。 您也可以使用原生 T-sql PREDICT 函數來讓模型，以在高度並行的即時評分案例中快速評分。

機器學習服務包含了 Python 和 R 的基本發行版。 除了適用於 Python 的 [revoscalepy](https://docs.microsoft.com/sql/advanced-analytics/python/ref-py-revoscalepy) 和 [microsoftml](https://docs.microsoft.com/sql/advanced-analytics/python/ref-py-microsoftml)，以及適用於 R 的 [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler)、[MicrosoftML](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml)、[olapR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-olapr) 和 [sqlrutils](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-sqlrutils) 這些上述 Microsoft 套件以外，您還可以安裝並使用開放原始碼套件和架構，例如 PyTorch、TensorFlow 和 scikit-learn。

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>註冊預覽版

此有限公開預覽版受限於 [Azure 預覽條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 

如果您想要加入預覽計畫並接受這些條款，您可以在上建立 Azure 支援票證以要求註冊 [**https://azure.microsoft.com/support/create-ticket/**](https://azure.microsoft.com/support/create-ticket/) 。 

1. 選取下列選項：
   - 問題類型- **技術**
   - 訂用帳戶-*選取您的訂*用帳戶
   - 服務- **SQL Database 受控執行個體**
   - 摘要- *輸入要求的簡短描述*
   - 問題類型- **Machine Learning Services FOR SQL 受控執行個體 (Preview) **
   - 問題子類型-**其他問題或**「操作說明」問題

1. 按一下 **[下一步：方案]**。

1. 閱讀預覽的相關資訊，然後按一下 [ **詳細資料**]。

1. 在 [ **描述**] 中，輸入您的要求的詳細資訊，包括您想要在預覽版中註冊的邏輯伺服器名稱、區域和訂用帳戶識別碼。 視需要輸入其他詳細資料。

1. 當您完成時，請按 **[下一步]： [檢查 + 建立]**，然後按一下 [ **建立**]。

註冊方案後，Microsoft 即會將您加入公開預覽版，並為您現有或新的資料庫啟用機器學習服務。

在公開預覽期間，請勿將 SQL 受控執行個體中的機器學習服務用於生產工作負載。

## <a name="next-steps"></a>後續步驟

- 請參閱 [SQL Server Machine Learning 服務的主要差異](machine-learning-services-differences.md)。
- 若要瞭解如何在 Machine Learning 服務中使用 Python，請參閱 [執行 python 腳本](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15)。
- 若要瞭解如何在 Machine Learning 服務中使用 R，請參閱 [執行 r 腳本](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15)。
- 如需有關其他 SQL 平臺機器學習的詳細資訊，請參閱 [SQL machine learning 檔](https://docs.microsoft.com/sql/machine-learning/)。
