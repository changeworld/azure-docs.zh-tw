---
title: 在生產環境中部署模型 - Team Data Science Process
description: 如何將模型部署到生產環境，好讓這些模型能夠在進行業務決策時扮演主動角色。
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e3e2ef3340ca836f56176c21cf3d221f0bf172b9
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722232"
---
# <a name="deploy-models-to-production-to-play-an-active-role-in-making-business-decisions"></a>將模型部署到生產環境以便在進行業務決策時扮演主動角色

生產環境部署可讓模型在企業中扮演主動角色。 從已部署模型的預測可用於商業決策。

## <a name="production-platforms"></a>生產環境平台

有許多方法和平台可將模型置入生產環境。 以下提供一些選項：

- [使用 Azure Machine Learning 部署模型的位置](../how-to-deploy-and-where.md)
- [在 SQL-Server 中部署模型](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

>[!NOTE]
>在部署之前，必須確保模型計分的延遲夠低，以便在生產環境中使用。
>

>[!NOTE]
>如需使用 Azure Machine Learning Studio 進行部署，請參閱[部署 Azure Machine Learning Web 服務](../studio/deploy-a-machine-learning-web-service.md)。
>

## <a name="ab-testing"></a>A/B 測試

當生產環境中有多個模型時，可以使用[A/B 測試](https://en.wikipedia.org/wiki/A/B_testing)來比較模型效能。 
 
## <a name="next-steps"></a>後續步驟

也會提供逐步解說，說明**特定案例**之程序中的所有步驟。 [範例逐步解說](walkthroughs.md)文章中會列出這些逐步解說以及簡短說明的連結。 這些逐步解說說明如何將雲端、內部部署工具及服務組合成工作流程或管線，以建立智慧型應用程式。 
