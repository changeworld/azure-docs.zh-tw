---
title: 使用 ARM 範本進行 Azure API 管理的 DevOps
description: Azure API 管理的 API DevOps 簡介，使用 Azure Resource Manager 範本來管理 CI/CD 管線中的 API 部署
services: api-management
author: miaojiang
ms.service: api-management
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: apimpm
ms.openlocfilehash: 144baa0944451adaf08f0df84325b58079e19d65
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92209526"
---
# <a name="cicd-for-api-management-using-azure-resource-manager-templates"></a>使用 Azure Resource Manager 範本進行 API 管理的 CI/CD

本文介紹如何使用 Azure Resource Manager 範本，透過 Azure API 管理來 DevOps API。 有了 Api 的策略性價值，持續整合 (CI) 和持續部署 (CD) 管線已成為 API 開發的重要層面。 這項功能可讓組織自動進行 API 變更的部署，而不需要容易出錯的手動步驟、偵測先前的問題，並以更快的速度提供價值給終端使用者。 

如需執行本文所述之 DevOps 方法的詳細資訊、工具和程式碼範例，請參閱 GitHub 中的開放原始碼 [AZURE API 管理 DevOps 資源套件](https://github.com/Azure/azure-api-management-devops-resource-kit) 。 由於客戶採用各種不同的工程文化特性和現有的自動化解決方案，因此這個方法並不適合全部適用的解決方案。

## <a name="the-problem"></a>問題

現今的組織通常會有多個部署環境 (例如開發、測試、生產) ，並且針對每個環境使用不同的 API 管理實例。 有些實例是由多個開發小組所共用，而這些小組負責不同版本步調的不同 Api。

因此，客戶面臨下列挑戰：

* 如何將 Api 的部署自動化至 API 管理？
* 如何將設定從一個環境遷移到另一個環境？
* 如何避免不同開發小組間共用相同 API 管理實例的干擾？

## <a name="manage-configurations-in-resource-manager-templates"></a>管理 Resource Manager 範本中的設定

下圖說明建議的方法。 

:::image type="content" source="media/devops-api-development-templates/apim-devops.png" alt-text="使用 API 管理的 DevOps":::

在此範例中，有兩個部署環境： *開發* 和 *生產*環境。 每個都有自己的 API 管理實例。 

* API 開發人員可存取開發實例，並可用於開發和測試其 Api。 
* 實際執行實例是由稱為 *API 發行者*的指定小組所管理。

此建議方法中的關鍵是將所有 API 管理設定保留在 [Azure Resource Manager 範本](../azure-resource-manager/resource-group-authoring-templates.md)中。 這些範本應該保留在原始檔控制系統（例如 Git）中。 如圖中所示，有一個發行者存放庫，其中包含生產 API 管理實例在樣板集合中的所有設定：

|範本  |描述  |
|---------|---------|
|服務範本     | API 管理實例的服務層級設定，例如定價層和自訂網域         |
|共用範本     |  API 管理實例中的共用資源，例如群組、產品和記錄器    |
|API 範本     |  Api 和其子資源的設定：作業、原則、診斷設定        |
|主要) 範本的主要 (     |   藉由 [連結](../azure-resource-manager/resource-group-linked-templates.md) 至所有範本並依序部署，將所有專案結合在一起。 若要將所有設定部署至 API 管理實例，請部署主要範本。 您也可以個別部署每個範本。       |

API 開發人員會將發行者存放庫派生給開發人員存放庫，並處理其 Api 的變更。 在大部分情況下，他們會將焦點放在其 Api 的 API 範本，而不需要變更共用或服務範本。

## <a name="migrate-configurations-to-templates"></a>將設定遷移至範本
使用 Resource Manager 範本時，API 開發人員會面臨一些挑戰：

* API 開發人員通常會使用 [OpenAPI 規格](https://github.com/OAI/OpenAPI-Specification) ，而且可能不熟悉 Resource Manager 架構。 手動編寫範本可能容易出錯。 

   資源套件中名為 [**Creator**](https://github.com/Azure/azure-api-management-devops-resource-kit/blob/master/src/APIM_ARMTemplate/README.md#Creator) 的公用程式工具可協助您根據開啟的 api 規格檔案，自動建立 API 範本。 此外，開發人員可以使用 XML 格式來提供 api 的 api 管理原則。 

* 對於已經在使用 API 管理的客戶，另一項挑戰是將現有的設定解壓縮到 Resource Manager 範本中。 對於這些客戶而言，資源套件中稱為「 [**解壓縮**](https://github.com/Azure/azure-api-management-devops-resource-kit/blob/master/src/APIM_ARMTemplate/README.md#extractor) 程式」的工具可從其 API 管理實例中解壓縮設定，以協助產生範本。  

## <a name="workflow"></a>工作流程

* 一旦 API 開發人員完成開發和測試 API，並產生 API 範本之後，他們就可以提交提取要求，將變更合併至發行者存放庫。 

* API 發行者可以驗證提取要求，並確保變更安全且符合規範。 例如，他們可以檢查是否只允許 HTTPS 與 API 通訊。 大部分的驗證都可以自動化為 CI/CD 管線中的一個步驟。

* 一旦成功核准併合並變更，API 發行者可以選擇依排程或依需求將它們部署到生產環境實例。 範本的部署可以使用 [GitHub Actions](https://github.com/Azure/apimanagement-devops-samples)、 [Azure Pipelines](/devops/pipelines/)、 [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)、 [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)或其他工具來自動化。

使用此方法時，api 管理實例中的 API 變更部署可以自動化，而且可以輕鬆地將變更從一個環境升級至另一個環境。 由於不同的 API 開發小組將會處理不同組的 API 範本和檔案，因此可防止不同小組之間的干擾。

## <a name="video"></a>影片

> [!VIDEO https://www.youtube.com/embed/4Sp2Qvmg6j8]

## <a name="next-steps"></a>後續步驟

- 如需其他資訊、工具和範例範本，請參閱開放原始碼 [AZURE API 管理 DevOps 資源套件](https://github.com/Azure/azure-api-management-devops-resource-kit) 。