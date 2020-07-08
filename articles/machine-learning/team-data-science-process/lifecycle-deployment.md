---
title: Team Data Science Process 生命週期的部署階段
description: 資料科學專案部署階段的目標、工作和交付項目
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
ms.openlocfilehash: 1138c95274c769186a9a29aa4d35517e378baeae
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "76720481"
---
# <a name="deployment-stage-of-the-team-data-science-process-lifecycle"></a>Team Data Science Process 生命週期的部署階段

本文將概要說明與 Team Data Science Process (TDSP) 部署相關聯的目標、工作和交付項目。 此流程會提供建議的生命週期，供您建構資料科學專案。 生命週期可描繪出專案一般執行時 (通常會反覆進行) 的主要階段：

   1. **了解商務**
   2. **資料取得與認知**
   3. **模型**
   4. **部署**
   5. **客戶接受度**

以下是 TDSP 生命週期的視覺表示法： 

![TDSP 生命週期](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>目標
將有資料管線的模型部署至生產環境或類生產環境，以進行最後的使用者接受度測試。 

## <a name="how-to-do-it"></a>如何執行此動作
此階段解決的主要工作︰

**實作模型**︰將模型和管線部署到生產環境或類生產環境以供取用應用程式。

### <a name="operationalize-a-model"></a>模型運作
在您擁有一組妥善執行的模型後，就可以運作這些模型，供其他應用程式取用。 預測可根據商業需求，以即時或批次的形式執行。 若要部署模型，您要使用開放的 API 介面公開模型。 介面可讓模型能從各種應用程式輕鬆地取用，例如：

   * 線上網站
   * 試算表 
   * 儀表板
   * 企業營運應用程式 
   * 後端應用程式 

如需使用 Azure Machine Learning Web 服務實作模型的範例，請參閱[部署 Azure Machine Learning Web 服務](../studio/deploy-a-machine-learning-web-service.md)。 對於您部署的生產模型和資料管線，建議您在其中建置遙測和監視。 這種做法有助於後續的系統狀態報告和疑難排解。  

## <a name="artifacts"></a>Artifacts

* 顯示系統健康情況和重要計量的狀態儀表板
* 具有部署詳細資料的最終模型報告
* 最終的方案架構文件


## <a name="next-steps"></a>後續步驟

以下是 TDSP 生命週期中每個步驟的連結：

   1. [了解商務](lifecycle-business-understanding.md)
   2. [資料取得與認知](lifecycle-data.md)
   3. [模型](lifecycle-modeling.md)
   4. [部署](lifecycle-deployment.md)
   5. [客戶接受度](lifecycle-acceptance.md)

我們會提供完整的逐步解說，以示範特定案例之程式中的所有步驟。 [範例逐步解說](walkthroughs.md)一文提供有連結和縮圖描述的案例清單。 這些逐步解說示範如何將雲端、內部部署工具與服務組合成工作流程或管線，以建立智慧型應用程式。 

如需如何在使用 Azure Machine Learning Studio 的 TDSP 中執行步驟的範例，請參閱[配合使用 Team Data Science Process 與 Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)。
