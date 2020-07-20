---
title: 如何使用 GitHub 套用 DevOps-LUIS
titleSuffix: Azure Cognitive Services
description: 使用 Language Understanding （LUIS）和 GitHub 來套用 DevOps。
services: cognitive-services
author: andycw
manager: cmayo
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/5/2020
ms.author: anwigley
ms.openlocfilehash: 6df65040277ac61cca5fb4bf7fce5b5a7b2f3afe
ms.sourcegitcommit: 52d2f06ecec82977a1463d54a9000a68ff26b572
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/15/2020
ms.locfileid: "84783794"
---
# <a name="apply-devops-to-luis-app-development-using-github-actions"></a>使用 GitHub 動作將 DevOps 套用至 LUIS 應用程式開發

請移至[LUIS DevOps 範本](https://github.com/Azure-Samples/LUIS-DevOps-Template)存放庫，以取得完整的解決方案，以實現 LUIS 的 DevOps 和軟體工程最佳作法。 您可以使用此範本存放庫，透過內建的 CI/CD 工作流程和實務支援來建立自己的儲存機制，並針對您自己的專案，使用 LUIS 來進行[原始檔控制](luis-concept-devops-sourcecontrol.md)、[自動化組建](luis-concept-devops-automation.md)、[測試](luis-concept-devops-testing.md)和[發行管理](luis-concept-devops-automation.md#release-management)。

## <a name="the-luis-devops-template-repo"></a>LUIS DevOps 範本存放庫

[LUIS DevOps 範本](https://github.com/Azure-Samples/LUIS-DevOps-Template)存放庫會逐步解說如何：

* 複製**範本**存放庫-將範本複製到您自己的 GitHub 儲存機制。
* **設定 LUIS 資源**-在 Azure 中建立可供持續整合工作流程使用的[LUIS 撰寫和預測資源](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-azure-subscription#create-resources-in-azure-cli)。
* **設定 ci/cd 工作流程**-設定 CI/cd 工作流程的參數，並將其儲存在[GitHub 秘密](https://help.github.com/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets)中。
* 逐步解說「 **[開發內部迴圈](https://mitchdenny.com/the-inner-loop/)** 」-開發人員在開發分支中工作時，會對範例 LUIS 應用程式進行更新、測試更新，然後引發提取要求來建議變更並尋求審查核准。
* **執行 CI/CD 工作流程**-執行[持續整合工作流程，以使用 GitHub 動作來建立及測試 LUIS 應用程式](luis-concept-devops-automation.md)。
* **執行自動化測試**-[針對 LUIS 應用程式執行自動化的批次測試](luis-concept-devops-testing.md)，以評估應用程式的品質。
* **部署 LUIS 應用程式**-執行[持續傳遞（CD）工作](luis-concept-devops-automation.md#continuous-delivery-cd)以發佈 LUIS 應用程式。
* **在您自己的專案中使用**存放庫-說明如何搭配您自己的 LUIS 應用程式使用存放庫。

## <a name="next-steps"></a>後續步驟

* 使用[LUIS DevOps 範本](https://github.com/Azure-Samples/LUIS-DevOps-Template)存放庫，將 DevOps 套用至您自己的專案。
* [LUIS 的原始檔控制和分支策略](luis-concept-devops-sourcecontrol.md)
* [測試 LUIS DevOps](luis-concept-devops-testing.md)
* [LUIS DevOps 的自動化工作流程](luis-concept-devops-automation.md)
