---
title: 建立 Azure Synapse 工作區 Azure Resource Manager 範本
description: 了解如何使用 Azure Resource Manager 範本建立 Synapse 工作區。
services: azure-resource-manager
author: julieMSFT
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: jrasnick
ms.date: 08/07/2020
ms.openlocfilehash: c64f3d835eeede79f937bbaadb0a54992176438d
ms.sourcegitcommit: 206629373b7c2246e909297d69f4fe3728446af5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2020
ms.locfileid: "89500650"
---
# <a name="quickstart-create-an-azure-synapse-workspace-using-a-deployment-template"></a>快速入門：使用部署範本建立 Azure Synapse 工作區

此 Azure Resource Manager 範本 (ARM 範本) 會建立具有基礎 Data Lake Storage 的 Azure Synapse 工作區。 Azure Synapse 工作區是 Azure Synapse Analytics 中分析程序的安全性實體共同作業界限。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果您的環境符合必要條件，而且您很熟悉 ARM 範本，請選取 [部署至 Azure] 按鈕。 範本會在 Azure 入口網站中開啟。

[![部署至 Azure 1](../media/template-deployments/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>檢閱範本

您可以選取**視覺化**連結來檢閱範本，如下所示：

[![視覺化](../media/template-deployments/template-visualize-button.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

此範本會定義兩個資源：

- 儲存體帳戶
- 工作區

## <a name="deploy-the-template"></a>部署範本

1. 選取以下影像來登入 Azure 並開啟範本。 此範本會建立 Synapse 工作區。
   
   [![部署至 Azure 2](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

1. 輸入或更新下列值：

   * 訂用帳戶：選取 Azure 訂用帳戶。
   * **資源群組**：選取 [新建]，並輸入資源群組的唯一名稱，然後選取 [確定]。 新的資源群組將有助於清除資源。
   * **區域**：選取區域。  例如，**美國中部**。
   * **Name**：輸入工作區的名稱。
   * **SQL 管理員登入**：輸入 SQL Server 的管理員使用者名稱。
   * **SQL 管理員密碼**：輸入 SQL Server 的管理員密碼。
   * **標籤值**：接受預設值。 
   * **檢閱並建立**：選取。
   * **建立**：選取。

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure Synapse Analytics 和 Azure Resource Manager，請繼續閱讀下列文章。

- 閱讀 [Azure Synapse Analytics 的概觀](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 
- 深入了解 [Azure Resource Manager](../azure-resource-manager/management/overview.md)
- [建立及部署您的第一個 ARM 範本](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
