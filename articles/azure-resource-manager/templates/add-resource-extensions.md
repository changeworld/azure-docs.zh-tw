---
title: 使用擴充功能進行部署後設定
description: 了解如何使用 Azure Resource Manager 範本擴充功能提供部署後的設定。
author: mumian
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: jgao
ms.openlocfilehash: b3c4110c8761b3e8daf324d65ac7fa1dcbcdf61f
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023492"
---
# <a name="provide-post-deployment-configurations-by-using-extensions"></a>使用擴充功能提供部署後的設定

範本擴充功能是一種小型的應用程式，可對 Azure 資源提供部署後的社及自動化工作。 最常見的是虛擬機器擴充功能。 請參閱[虛擬機器擴充功能和功能 (Windows)](../../virtual-machines/extensions/features-windows.md) 與[虛擬機器擴充功能和功能 (Linux)](../../virtual-machines/extensions/features-linux.md)。

## <a name="extensions"></a>延伸模組

現有的擴充功能有：

- [Microsoft.Compute/virtualMachines/extensions](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachines/extensions)
- [Microsoft.Compute virtualMachineScaleSets/extensions](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions) \(英文\)
- [Microsoft.HDInsight clusters/extensions](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/2018-06-01-preview/clusters) \(英文\)
- [Microsoft.Sql servers/databases/extensions](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions) \(英文\) 
- [Microsoft.Web/sites/siteextensions](https://docs.microsoft.com/azure/templates/microsoft.web/2016-08-01/sites/siteextensions) \(英文\)

若要了解可用的擴充功能，請瀏覽至[範本參考](https://docs.microsoft.com/azure/templates/) \(英文\)。 在 [依標題篩選] 中，輸入**擴充功能**。

若要了解如何使用這些擴充功能，請參閱：

- [教學課程：使用 Azure Resource Manager 範本部署虛擬機器擴充](template-tutorial-deploy-vm-extensions.md)功能。
- [教學課程：使用 Azure Resource Manager 範本匯入 SQL BACPAC 檔案](template-tutorial-deploy-sql-extensions-bacpac.md)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：使用 Azure Resource Manager 範本部署虛擬機器擴充功能](template-tutorial-deploy-vm-extensions.md)
