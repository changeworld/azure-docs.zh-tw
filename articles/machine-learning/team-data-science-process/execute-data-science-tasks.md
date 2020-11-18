---
title: 執行資料科學工作 - Team Data Science Process
description: 資料科學家如何以可追蹤、版本控制，以及共同作業的方式執行資料科學專案。
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/17/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e47dad8498c48a5da5307517efe493fa5c1aa590
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94748058"
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>執行資料科學工作：探索、模型化和部署

一般資料科學工作包括資料探索、模型化和部署。 本文概述完成數個常見的資料科學工作，例如互動式資料探索、資料分析、報告和模型建立的工作。 將模型部署到生產環境的選項可能包括：

- [Azure Machine Learning](../index.yml)
- [SQL-Server 與 ML 服務](/sql/advanced-analytics/r/r-services)
- [Microsoft Machine Learning Server](/machine-learning-server/what-is-machine-learning-server)


## <a name="1--exploration"></a>1. <a name='DataQualityReportUtility-1'></a> 探索 

資料科學家可以利用許多方式執行探索和報告：使用 Python 可用的程式庫和套件 (例如 matplotlib) 或使用 R (例如 ggplot 或 lattice)。 資料科學家可以自訂這類程式碼，以符合特定情節的資料探索需求。 處理結構化資料與非結構化資料的需求不同，例如文字或影像。 

產品（例如 Azure Machine Learning）也提供資料整頓和探索的 [先進資料準備](../how-to-create-register-datasets.md) ，包括功能建立。 使用者應決定最符合其需求的工具、程式庫和套件。 

在此階段結尾處的交付項目是資料探索報告。 報告應該提供相當完整的資料檢視，以用於模型化及評估資料是否適合繼續進行模型化步驟。 

## <a name="2--modeling"></a>2. <a name='ModelingUtility-2'></a> 模型化

以各種不同的語言提供多種定型模型的工具組和套件。 資料科學家應該可以自由使用任何其習慣的項目，只要針對相關商務使用案例與實際執行情節符合有關精確度和延遲的效能考量。

### <a name="model-management"></a>模型管理
在多個模型都已建置完成之後，您通常需要有可註冊和管理模型的系統。 通常您需要指令碼或 API 以及後端資料庫或版本控制系統的組合。 針對這些管理工作，您可以考慮以下的一些選項：

1. [Azure Machine Learning - 模型管理服務](../index.yml)
2. [從 MIT ModelDB](http://modeldb.csail.mit.edu:3000/projects) 
3. [SQL-Server 即模型管理系統](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/) \(英文\)
4. [Microsoft Machine Learning Server](/sql/advanced-analytics/r/r-server-standalone)

## <a name="3--deployment"></a>3. <a name='Deployment-3'></a> 部署

生產環境部署可讓模型在企業中扮演主動角色。 從已部署模型的預測可用於商業決策。

### <a name="production-platforms"></a>生產環境平台
有許多方法和平台可將模型置入生產環境。 以下提供一些選項：


- [在 Azure Machine Learning 中的模型部署](../how-to-deploy-and-where.md)
- [在 SQL-Server 中部署模型](/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](/sql/advanced-analytics/r/r-server-standalone)

> [!NOTE]
> 在部署之前，必須確保模型計分的延遲夠低，以便在生產環境中使用。
>
>

逐步解說還會提供進一步範例，說明 **特定情節** 之程序中的所有步驟。 [範例逐步解說](walkthroughs.md)文章中會列出這些逐步解說以及簡短說明的連結。 這些逐步解說說明如何將雲端、內部部署工具及服務組合成工作流程或管線，以建立智慧型應用程式。

> [!NOTE]
> 如需使用 Azure Machine Learning Studio 進行部署，請參閱[部署 Azure Machine Learning Web 服務](../classic/deploy-a-machine-learning-web-service.md)。
>
>

### <a name="ab-testing"></a>A/B 測試
多個模型在生產環境中時，執行 [A/B 測試](https://en.wikipedia.org/wiki/A/B_testing)來比較模型的效能會很有用。 

 
## <a name="next-steps"></a>後續步驟

[追蹤資料科學專案的進度](track-progress.md)會顯示資料科學家可如何追蹤資料科學專案的進度。

[模型作業和 CI/CD](ci-cd-flask.md) 會示範如何使用開發模型執行 CI/CD。
