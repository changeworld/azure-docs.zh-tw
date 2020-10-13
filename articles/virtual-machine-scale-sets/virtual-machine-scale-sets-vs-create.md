---
title: 使用 Visual Studio 部署虛擬機器調整集
description: 使用 Visual Studio 和 Resource Manager 範本部署虛擬機器調整集
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: visual-studio
ms.date: 09/09/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 2db281f1b3278e37deca8486971a7f7a83f85ccf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86503076"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>如何使用 Visual Studio 建立虛擬機器擴展集

本文說明如何使用 Visual Studio 資源群組部署來部署 Azure 虛擬機器擴展集。

[Azure 虛擬機器擴展集](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) 是一項 Azure 計算資源，可透過自動調整和負載平衡來部署和管理類似虛擬機器的集合。 您可以使用 [Azure Resource Manager 範本 (英文)](https://github.com/Azure/azure-quickstart-templates) 來佈建和部署虛擬機器擴展集。 Azure Resource Manager 範本可以使用 Azure CLI、PowerShell、REST，也可以直接從 Visual Studio 來部署。 Visual Studio 提供一組範例範本，您可以將其部署為 Azure 資源群組部署專案的一部分。

Azure 資源群組部署是一種方式，可在單一部署作業中將一組相關的 Azure 資源群組在一起並加以發佈。 如需詳細資訊，請參閱 [透過 Visual Studio 建立和部署 Azure 資源群組](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)。

## <a name="prerequisites"></a>Prerequisites

若要開始在 Visual Studio 中部署虛擬機器擴展集，您需要下列必要條件：

* Visual Studio 2013 或更新版本
* Azure SDK 2.7、2.8 或 2.9

>[!NOTE]
>本文使用 Visual Studio 2019 搭配 [AZURE SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)。

## <a name="create-a-project"></a>建立專案 <a name="creating-a-project"></a> 

1. 開啟 Visual Studio，然後選取 [建立新專案]  。

1. 在 [ **建立新專案**] 中，選擇 [c # 的 **Azure 資源群組** ]，然後選取 **[下一步]**。

1. 在 [ **設定新專案**] 中輸入名稱，然後選取 [ **建立**]。

    ![命名及建立您的專案](media/virtual-machine-scale-sets-vs-create/configure-azure-resource-group.png)

1. 從範本清單中，選擇 [ **Windows 虛擬機器擴展集** ] 或 [ **Linux 虛擬機器擴展集** ] 範本。 選取 [確定]  。

   ![選取虛擬機器範本](media/virtual-machine-scale-sets-vs-create/select-vm-template.png)

建立專案之後， **方案總管** 包含 PowerShell 部署腳本、Azure Resource Manager 範本，以及虛擬機器擴展集的參數檔案。

## <a name="customize-your-project"></a>自訂您的專案

現在您可以編輯範本，以針對您的應用程式需求進行自訂。 您可以新增虛擬機器擴充功能屬性或編輯負載平衡規則。 根據預設，虛擬機器擴展集範本會設定為部署 **AzureDiagnostics** 延伸模組，這可讓您輕鬆地新增自動調整規則。 範本也會部署具有公用 IP 位址的負載平衡器，並以輸入 NAT 規則設定。

負載平衡器可讓您使用 SSH (Linux) 或 RDP (Windows) 連接到虛擬機器實例。 前端連接埠範圍是從 50000 開始。 針對 Linux，如果您透過 SSH 連線到埠50000，負載平衡會將您路由傳送至擴展集中第一部虛擬機器的埠22。 連接到埠50001會路由傳送至第二部虛擬機器的埠22，依此類推。

 使用 Visual Studio 編輯範本的好方法是使用 **JSON 大綱**。 您可以組織參數、變數和資源。 瞭解架構之後，Visual Studio 可以在部署之前指出您範本中的錯誤。

![JSON 總管](media/virtual-machine-scale-sets-vs-create/json-explorer.png)

## <a name="deploy-the-project"></a>部署專案

部署 Azure Resource Manager 範本，以建立虛擬機器擴展集資源：

1. 在**方案總管**中，以滑鼠右鍵按一下專案，然後選擇 [**部署**  >  **新**專案]。

    ![部署您的專案](media/virtual-machine-scale-sets-vs-create/deploy-new-project.png)

1. 在 [ **部署至資源群組**] 中，選擇要使用的訂用帳戶，然後選取資源群組。 如有必要，您可以建立資源群組。

1. 接下來，選取 [ **編輯參數** ] 以輸入傳遞給範本的參數。

   ![輸入訂用帳戶和資源群組](media/virtual-machine-scale-sets-vs-create/deploy-to-resource-group.png)

1. 提供作業系統的使用者名稱和密碼。 建立部署時需要這些值。 如果您未安裝 PowerShell Tools for Visual Studio，請選取 [ **儲存密碼** ] 以避免隱藏的 PowerShell 命令提示字元，或使用 [Key Vault 支援](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/)。 選取 [ **儲存** ] 以繼續。

    ![編輯部署參數](media/virtual-machine-scale-sets-vs-create/edit-deployment-parameters.png)

1. 在 [ **部署至資源群組**] 中，選取 [ **部署**]。 動作會執行 **Deploy-AzureResourceGroup.ps1** 腳本。 [輸出]**** 視窗會顯示部署進度。

   ![輸出會顯示結果](media/virtual-machine-scale-sets-vs-create/deployment-output.png)

## <a name="explore-your-virtual-machine-scale-set"></a>探索您的虛擬機器擴展集 <a name="exploring-your-virtual-machine-scale-set"></a>

選取 [ **view**  >  **Cloud Explorer** ] 以查看新的虛擬機器擴展集。 如有必要，請使用 [ **全部**重新整理]。

![Cloud Explorer](media/virtual-machine-scale-sets-vs-create/cloud-explorer.png)

**Cloud Explorer** 可讓您在開發應用程式時，在 Visual Studio 中管理 Azure 資源。 您也可以在 [Azure 入口網站](https://portal.azure.com)和 [Azure 資源總管 (英文)](https://resources.azure.com/) 中檢視虛擬機器擴展集。

 入口網站提供使用網頁瀏覽器來管理 Azure 基礎結構的最佳方式。 Azure 資源總管提供簡單的方式來探索及偵測 Azure 資源。 Azure 資源總管提供實例視圖，也會顯示您所查看資源的 PowerShell 命令。

## <a name="next-steps"></a>接下來的步驟

當您透過 Visual Studio 成功部署虛擬機器擴展集之後，您可以進一步自訂您的專案，以符合您的應用程式需求。 例如，藉由新增 **深入** 解析資源來設定自動調整。 您可以將基礎結構新增至您的範本（例如獨立虛擬機器），或使用自訂腳本擴充功能來部署應用程式。 您可以在 [Azure 快速入門範本](https://github.com/Azure/azure-quickstart-templates) GitHub 存放庫中找到良好的範例範本。 搜尋 `vmss`。
