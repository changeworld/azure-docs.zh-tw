---
title: 建立內部負載平衡器 - Azure 範本
titleSuffix: Azure Load Balancer
description: 了解如何使用資源管理員中的範本建立內部負載平衡器
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 0d7cc4d571ddeb0b57fd4f025b8cbf7b204f61e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456959"
---
# <a name="create-an-internal-load-balancer-using-a-template"></a>使用範本建立內部負載平衡器

> [!div class="op_single_selector"]
> * [Azure 門戶](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [電源外殼](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [範本](../load-balancer/load-balancer-get-started-ilb-arm-template.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="deploy-the-template-by-using-click-to-deploy"></a>使用按一下即部署來部署範本

公用儲存機制中可用的範例範本會使用一個包含預設值的參數檔案，這些預設值可用來產生上述案例。 若要使用「按一下即部署」來部署此範本，請依循[此連結](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-internal-load-balancer)，按一下 [部署至 Azure]****，視情況取代預設參數值，再依循入口網站中的指示。

## <a name="deploy-the-template-by-using-powershell"></a>使用 PowerShell 部署範本

若要使用 PowerShell 部署您下載的範本，請依照下列步驟執行。

1. 如果您從未用過 Azure PowerShell，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azure/overview) ，並遵循其中的所有指示登入 Azure，然後選取您的訂用帳戶。
2. 將參數檔案下載至本機磁碟。
3. 編輯並儲存檔案。
4. 運行**New-AzResourceGroup 部署**Cmdlet 以使用範本創建資源組。

    ```azurepowershell-interactive
    New-AzResourceGroupDeployment -Name TestRG -Location westus `
        -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json' `
        -TemplateParameterFile 'C:\temp\azuredeploy.parameters.json'
    ```

## <a name="deploy-the-template-by-using-the-azure-cli"></a>使用 Azure CLI 部署範本

若要使用 Azure CLI 部署範本，請依照下列步驟執行。

1. 如果您從未使用過 Azure CLI，請參閱[安裝和配置 Azure CLI，](../cli-install-nodejs.md)並按照說明操作，以選擇 Azure 帳戶和訂閱。
2. 轉到[https://shell.azure.com](https://shell.azure.com)在瀏覽器中打開雲外殼。 執行 **azure config mode** 命令，以切換為資源管理員模式，如下所示。

    ```console
    azure config mode arm
    ```

    此為上述命令的預期輸出內容：

        info:    New mode is arm

3. 開啟參數檔案，選取其內容，然後將該內容儲存至您電腦中的一個檔案。 在此範例中，我們將參數檔案儲存為 *parameters.json*。
4. 執行 **azure group deployment create** 命令，使用先前下載並修改的範本和參數檔案來部署新的內部負載平衡器。 輸出後顯示的清單可說明所使用的參數。

    ```console
    azure group create --name TestRG --location westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json --parameters-file parameters.json
    ```

## <a name="next-steps"></a>後續步驟

[使用來源 IP 同質性設定負載平衡器分配模式](load-balancer-distribution-mode.md)

[設定負載平衡器的閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)

如需範本中負載平衡器的 JSON 語法和屬性，請參閱 [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers)。