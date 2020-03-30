---
title: 使用範本創建 Apache Hadoop 群集 - Azure HDInsight
description: 了解如何使用 Resource Manager 範本建立 HDInsight 的叢集
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/18/2019
ms.openlocfilehash: 9498f2cf56f0bfe20d0806e5dc9872403dabb180
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979099"
---
# <a name="create-apache-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>使用 Resource Manager 範本在 HDInsight 中建立 Apache Hadoop 叢集

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

在本文中，您會學習使用 Azure Resource Manager 範本建立 Azure HDInsight 叢集的數種方式。 如需詳細資訊，請參閱 [使用 Azure Resource Manager 範本部署應用程式](../azure-resource-manager/templates/deploy-powershell.md)。 若要了解其他叢集建立工具和功能，請按一下此頁面頂端的索引標籤選取器，或參閱[叢集建立方法](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods)。

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Prerequisites

* [Azure 訂閱](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* Azure PowerShell 和/或 Azure CLI。

### <a name="resource-manager-templates"></a>Resource Manager 範本

資源管理器範本便於在單個協調的操作中為應用程式創建以下資源：
* HDInsight 叢集及其相依資源 (例如，預設儲存體帳戶)。
* 其他資源 (例如，使用 [Apache Sqoop](https://sqoop.apache.org/) \(英文\) 的 Azure SQL Database)。

在範本中，您會定義應用程式所需的資源。 您也可以指定部署參數，以便為不同的環境輸入值。 範本由 JSON 與運算式所組成，可讓您用來為部署建構值。

您可以在[Azure 快速入門範本](https://azure.microsoft.com/resources/templates/?term=hdinsight)中找到 HDInsight 範本示例。 使用跨平台 [Visual Studio Code](https://code.visualstudio.com/#alt-downloads) (具有 [Resource Manager 擴充功能](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)) 或文字編輯器，將範本儲存至您工作站上的檔案。

如需 Resource Manager 範本的詳細資訊，請參閱下列文章和範例：

* [編寫 Azure Resource Manager 範本](../azure-resource-manager/templates/template-syntax.md)
* [使用 Azure Resource Manager 範本部署應用程式](../azure-resource-manager/templates/deploy-powershell.md)
* [Microsoft.HDInsight/clusters](/azure/templates/microsoft.hdinsight/allversions) 範本參考
* [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular)

## <a name="generate-templates"></a>產生範本

Resource Manager 可讓您使用不同工具，從您的訂用帳戶中現有的資源匯出 Resource Manager 範本。 您可以使用產生的範本了解範本語法，或視需要自動重新部署解決方案。 有關詳細資訊，請參閱[匯出範本](../azure-resource-manager/templates/export-template-portal.md)。

## <a name="deploy-using-the-portal"></a>使用入口網站進行部署

您可以使用 Azure 入口網站部署 Resource Manager 範本。 如需詳細資訊，請參閱[從自訂範本部署資源](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)。

## <a name="deploy-using-powershell"></a>使用 PowerShell 進行部署

您可以使用 Azure PowerShell 部署 Resource Manager 範本。 如需詳細資訊，請參閱[使用 Resource Manager 範本與 Azure PowerShell 來部署資源](../azure-resource-manager/templates/deploy-powershell.md)和[使用 SAS 權杖和 Azure PowerShell 部署私用 Resource Manager 範本](../azure-resource-manager/resource-manager-powershell-sas-token.md)。

## <a name="deploy-using-azure-cli"></a>使用 Azure CLI 部署

您可以使用 Azure CLI 部署 Resource Manager 範本。 如需詳細資訊，請參閱[使用 Resource Manager 範本與 Azure CLI 來部署資源](../azure-resource-manager/templates/deploy-cli.md)和[使用 SAS 權杖和 Azure CLI 部署私用 Resource Manager 範本](../azure-resource-manager/resource-manager-cli-sas-token.md)。

## <a name="deploy-using-the-rest-api"></a>使用 REST API 進行部署

您可以使用 REST API 部署 Resource Manager 範本。 如需詳細資訊，請參閱[使用 Resource Manager 範本與 Resource Manager REST API 來部署資源](../azure-resource-manager/templates/deploy-rest.md)。

## <a name="deploy-with-visual-studio"></a>透過 Visual Studio 部署

 使用 Visual Studio 來透過使用者介面建立資源群組專案，並將其部署至 Azure。 您選取要包含在您的專案中的資源類型。 這些資源會自動新增至 Resource Manager 範本。 該專案也提供 PowerShell 指令碼來部署範本。

如需搭配資源群組使用 Visual Studio 的簡介，請參閱 [透過 Visual Studio 建立和部署 Azure 資源群組](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)。

## <a name="troubleshoot"></a>疑難排解

如果您在建立 HDInsight 叢集時遇到問題，請參閱[存取控制需求](hdinsight-hadoop-customize-cluster-linux.md#access-control)。

## <a name="next-steps"></a>後續步驟

在本文中，您學到幾種建立 HDInsight 叢集的方法。 如需詳細資訊，請參閱下列文章：

* 有關更多 HDInsight 相關範本，請參閱[Azure 快速入門範本](https://azure.microsoft.com/resources/templates/?term=hdinsight)。
* 如需透過 .NET 用戶端程式庫部署資源的範例，請參閱[使用 .NET 程式庫與範本部署資源](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
* 如需部署應用程式的深入範例，請參閱 [透過可預測方式在 Azure 中佈建和部署微服務](../app-service/deploy-complex-application-predictably.md)。
* 如需將您的方案部署到不同環境的指引，請參閱 [Microsoft Azure 中的開發和測試環境](../solution-dev-test-environments.md)。
* 若要了解 Azure Resource Manager 範本的區段，請參閱 [編寫範本](../azure-resource-manager/templates/template-syntax.md)。
* 如需可在 Azure Resource Manager 範本中使用的函式清單，請參閱 [範本函式](../azure-resource-manager/templates/template-functions.md)。
