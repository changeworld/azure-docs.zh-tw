---
title: 使用 Visual Studio 部署虛擬機器調整集
description: 使用 Visual Studio 和 Resource Manager 範本部署虛擬機器調整集
ms.custom: vs-azure, H1Hack27Feb2017
ms.workload: azure-vs
author: mimckitt
tags: azure-resource-manager
ms.assetid: ed0786b8-34b2-49a8-85b5-2a628128ead6
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: mimckitt
ms.openlocfilehash: adc91d5f4f79be8a85dfed7d10a882493f6427b0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81273336"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>如何使用 Visual Studio 建立虛擬機器擴展集

本文說明如何使用 Visual Studio 資源群組部署來部署 Azure 虛擬機器擴展集。

[Azure 虛擬機器擴展集](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/)是一項 azure 計算資源，可透過自動調整和負載平衡來部署和管理類似虛擬機器的集合。 您可以使用 [Azure Resource Manager 範本 (英文)](https://github.com/Azure/azure-quickstart-templates) 來佈建和部署虛擬機器擴展集。 Azure Resource Manager 範本可以使用 Azure CLI、PowerShell、REST 進行部署，也可直接從 Visual Studio 進行部署。 Visual Studio 提供一組範例範本，您可以將其部署為 Azure 資源群組部署專案的一部分。

Azure 資源群組部署是一種方式，可在單一部署作業中將一組相關的 Azure 資源群組在一起並加以發佈。 如需詳細資訊，請參閱[透過 Visual Studio 建立和部署 Azure 資源群組](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)。

## <a name="prerequisites"></a>先決條件

若要開始在 Visual Studio 中部署虛擬機器擴展集，您需要下列必要條件：

* Visual Studio 2013 或更新版本
* Azure SDK 2.7、2.8 或 2.9

>[!NOTE]
>本文使用 Visual Studio 2019 搭配[AZURE SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)。

## <a name="create-a-project"></a>建立專案<a name="creating-a-project"></a> 

1. 開啟 Visual Studio，然後選取 [建立新專案]  。

1. 在 [**建立新專案**] 中，選擇 [c # 的**Azure 資源群組**]，然後選取 **[下一步]**。

1. 在 [**設定您的新專案**] 中輸入名稱，然後選取 [**建立**]。

    ![命名並建立您的專案](media/virtual-machine-scale-sets-vs-create/configure-azure-resource-group.png)

1. 從範本清單中，選擇 [ **Windows 虛擬機器擴展集**] 或 [ **Linux 虛擬機器擴展集**] 範本。 選取 [確定]  。

   ![選取虛擬機器範本](media/virtual-machine-scale-sets-vs-create/select-vm-template.png)

建立專案之後，**方案總管**包含 PowerShell 部署腳本、Azure Resource Manager 範本，以及虛擬機器擴展集的參數檔。

## <a name="customize-your-project"></a>自訂您的專案

現在您可以編輯範本，以依據應用程式的需求進行自訂。 您可以新增虛擬機器擴充功能屬性或編輯負載平衡規則。 根據預設，虛擬機器擴展集範本會設定為部署**AzureDiagnostics**延伸模組，讓您可以輕鬆地新增自動調整規則。 範本也會部署具有公用 IP 位址的負載平衡器，並以輸入 NAT 規則進行設定。

負載平衡器可讓您使用 SSH （Linux）或 RDP （Windows）連接到虛擬機器實例。 前端連接埠範圍是從 50000 開始。 針對 Linux，如果您透過 SSH 連線到埠50000，負載平衡會將您路由至擴展集中第一部虛擬機器的埠22。 連接到埠50001會路由至第二部虛擬機器的埠22，依此類推。

 使用 [Visual Studio] 來編輯範本的好方法是使用 [ **JSON 大綱**]。 您可以組織參數、變數和資源。 瞭解架構之後，Visual Studio 可以在您的範本中指出錯誤，然後再進行部署。

![JSON 總管](media/virtual-machine-scale-sets-vs-create/json-explorer.png)

## <a name="deploy-the-project"></a>部署專案

部署 Azure Resource Manager 範本來建立虛擬機器擴展集資源：

1. 在**方案總管**中，以滑鼠右鍵按一下專案，然後選擇 [**部署** > **新**的]。

    ![部署專案](media/virtual-machine-scale-sets-vs-create/deploy-new-project.png)

1. 在 [**部署至資源群組**] 中，選擇要使用的訂用帳戶，然後選取資源群組。 如有必要，您可以建立資源群組。

1. 接下來，選取 [**編輯參數**] 以輸入傳遞至範本的參數。

   ![輸入訂用帳戶和資源群組](media/virtual-machine-scale-sets-vs-create/deploy-to-resource-group.png)

1. 提供作業系統的使用者名稱和密碼。 需要這些值才能建立部署。 如果您未安裝 PowerShell Tools for Visual Studio，請選取 [**儲存密碼**] 以避免隱藏的 PowerShell 命令提示字元，或使用[Key Vault 支援](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/)。 選取 [**儲存**] 以繼續。

    ![編輯部署參數](media/virtual-machine-scale-sets-vs-create/edit-deployment-parameters.png)

1. 在 [**部署至資源群組**] 中，選取 [**部署**]。 動作會執行**deploy-azureresourcegroup.ps1**腳本。 [輸出]**** 視窗會顯示部署進度。

   ![輸出顯示結果](media/virtual-machine-scale-sets-vs-create/deployment-output.png)

## <a name="explore-your-virtual-machine-scale-set"></a>探索您的虛擬機器擴展集<a name="exploring-your-virtual-machine-scale-set"></a>

選取 [ **view** > **Cloud Explorer** ] 以查看新的虛擬機器擴展集。 如有必要，請使用 [**全部**重新整理]。

![Cloud Explorer](media/virtual-machine-scale-sets-vs-create/cloud-explorer.png)

**Cloud Explorer**可讓您在開發應用程式時，在 Visual Studio 中管理 Azure 資源。 您也可以在 [Azure 入口網站](https://portal.azure.com)和 [Azure 資源總管 (英文)](https://resources.azure.com/) 中檢視虛擬機器擴展集。

 入口網站提供使用網頁瀏覽器來管理 Azure 基礎結構的最佳方式。 Azure 資源總管提供簡單的方式來探索和分析 Azure 資源。 Azure 資源總管提供實例視圖，也會顯示您所查看資源的 PowerShell 命令。

## <a name="next-steps"></a>後續步驟

成功透過 Visual Studio 部署虛擬機器擴展集之後，您可以進一步自訂您的專案，以符合您的應用程式需求。 例如，藉由新增**深入**解析資源來設定自動調整。 您可以將基礎結構新增至您的範本（例如獨立虛擬機器），或使用自訂腳本擴充功能部署應用程式。 您可以在[Azure 快速入門範本](https://github.com/Azure/azure-quickstart-templates)GitHub 存放庫中找到良好的範例範本。 搜尋 `vmss`。
