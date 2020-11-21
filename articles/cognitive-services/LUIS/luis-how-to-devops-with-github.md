---
title: 如何套用 DevOps 與 GitHub-LUIS
titleSuffix: Azure Cognitive Services
description: 將 DevOps 套用至 Language Understanding (LUIS) 和 GitHub。
services: cognitive-services
author: andycw
manager: cmayo
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/5/2020
ms.author: anwigley
ms.openlocfilehash: b733e90b69be4e2bd458be5486564747ed4cca78
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018866"
---
# <a name="apply-devops-to-luis-app-development-using-github-actions"></a>使用 GitHub Actions 將 DevOps 套用至 LUIS 應用程式開發

移至 [LUIS DevOps 範本](https://github.com/Azure-Samples/LUIS-DevOps-Template) 存放庫，取得完整的解決方案，以實行 LUIS 的 DevOps 和軟體工程最佳做法。 您可以使用此範本存放庫來建立自己的存放庫，並內建支援 CI/CD 工作流程和實務，以針對您自己的專案，使用 LUIS 進行 [原始檔控制](luis-concept-devops-sourcecontrol.md)、 [自動化組建](luis-concept-devops-automation.md)、 [測試](luis-concept-devops-testing.md)和 [發行管理](luis-concept-devops-automation.md#release-management) 。

## <a name="the-luis-devops-template-repo"></a>LUIS DevOps 範本存放庫

[LUIS DevOps 範本](https://github.com/Azure-Samples/LUIS-DevOps-Template)存放庫逐步解說如何：

* 複製 **範本** 存放庫-將範本複製到您自己的 GitHub 存放庫。
* **設定 LUIS 資源** -在 Azure 中建立將由持續整合工作流程使用的 [LUIS 撰寫和預測資源](./luis-how-to-azure-subscription.md) 。
* **設定 ci/cd 工作流程** -設定 CI/cd 工作流程的參數，並將其儲存在 [GitHub 秘密](https://help.github.com/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets)中。
* 逐步解說「 **[開發內部迴圈](https://mitchdenny.com/the-inner-loop/)** 」-開發人員在開發分支中工作時，會更新範例 LUIS 應用程式、測試更新，然後引發提取要求以建議變更並搜尋審核核准。
* **執行 CI/CD 工作流程** -執行 [持續整合工作流程，以使用 GitHub Actions 建立及測試 LUIS 應用程式](luis-concept-devops-automation.md) 。
* **執行自動化測試** -對 [LUIS 應用程式執行自動化批次測試](luis-concept-devops-testing.md) ，以評估應用程式的品質。
* **部署 LUIS 應用程式** -執行 [持續傳遞 (CD) 作業](luis-concept-devops-automation.md#continuous-delivery-cd) 來發佈 LUIS 應用程式。
* 使用存放庫搭配 **您自己的專案**-說明如何搭配您自己的 LUIS 應用程式來使用存放庫。

## <a name="next-steps"></a>下一步

* 使用 [LUIS DevOps 範本](https://github.com/Azure-Samples/LUIS-DevOps-Template) 存放庫將 DevOps 套用至您自己的專案。
* [LUIS 的原始程式碼控制和分支策略](luis-concept-devops-sourcecontrol.md)
* [測試 LUIS DevOps](luis-concept-devops-testing.md)
* [適用于 LUIS DevOps 的自動化工作流程](luis-concept-devops-automation.md)
