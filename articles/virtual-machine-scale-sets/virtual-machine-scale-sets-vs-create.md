---
title: 使用 Visual Studio 部署虛擬機器調整集
description: 使用 Visual Studio 和 Resource Manager 範本部署虛擬機器調整集
ms.custom: vs-azure, H1Hack27Feb2017
ms.workload: azure-vs
author: mayanknayar
tags: azure-resource-manager
ms.assetid: ed0786b8-34b2-49a8-85b5-2a628128ead6
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: manayar
ms.openlocfilehash: c49b4f42bc726c68880bdd4d6f58956936e83177
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066966"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>如何使用 Visual Studio 建立虛擬機器擴展集

本文介紹如何使用視覺化工作室資源組部署部署 Azure 虛擬機器縮放集。

[Azure 虛擬機器縮放集](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/)是一種 Azure 計算資源，用於部署和管理具有自動縮放和負載平衡的類似虛擬機器的集合。 您可以使用 [Azure Resource Manager 範本 (英文)](https://github.com/Azure/azure-quickstart-templates) 來佈建和部署虛擬機器擴展集。 可以使用 Azure CLI、PowerShell、REST 以及直接從 Visual Studio 部署 Azure 資源管理器範本。 Visual Studio 提供了一組示例範本，您可以將這些範本部署為 Azure 資源組部署專案的一部分。

Azure 資源群組部署是一種方式，可在單一部署作業中將一組相關的 Azure 資源群組在一起並加以發佈。 有關詳細資訊，請參閱通過[Visual Studio 創建和部署 Azure 資源組](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)。

## <a name="prerequisites"></a>Prerequisites

要開始在視覺化工作室中部署虛擬機器縮放集，您需要以下先決條件：

* Visual Studio 2013 或更新版本
* Azure SDK 2.7、2.8 或 2.9

>[!NOTE]
>本文使用 Visual Studio 2019 與[Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)。

## <a name="create-a-project"></a>創建專案<a name="creating-a-project"></a> 

1. 開啟 Visual Studio，然後選取 [建立新專案]****。

1. 在**創建新專案中**，為 C# 選擇**Azure 資源組**，然後選擇 **"下一步**"。

1. 在 **"配置新專案"** 中，輸入名稱並選擇 **"創建**"。

    ![命名並創建專案](media/virtual-machine-scale-sets-vs-create/configure-azure-resource-group.png)

1. 從範本清單中，選擇**Windows 虛擬機器縮放集**或**Linux 虛擬機器縮放集**範本。 選取 [確定]****。

   ![選擇虛擬機器範本](media/virtual-machine-scale-sets-vs-create/select-vm-template.png)

創建專案後，**解決方案資源管理器**包含 PowerShell 部署腳本、Azure 資源管理器範本和虛擬機器縮放集的參數檔。

## <a name="customize-your-project"></a>自訂您的專案

現在，您可以編輯範本以自訂它以滿足應用程式的需求。 您可以添加虛擬機器擴充屬性或編輯負載平衡規則。 預設情況下，虛擬機器縮放集範本配置為部署**Azure 診斷**擴展，這使得添加自動縮放規則變得容易。 範本還部署帶有公共 IP 位址的負載等化器，該位址配置有入站 NAT 規則。

負載等化器允許您使用 SSH （Linux） 或 RDP （Windows） 連接到虛擬機器實例。 前端連接埠範圍是從 50000 開始。 對於 Linux，如果 SSH 要移植 50000，則負載平衡將路由您到 Scale 集中的第一個虛擬機器的埠 22。 連接到埠 50001 路由到第二個虛擬機器的埠 22，等等。

 使用 Visual Studio 編輯範本的一個好方法是使用**JSON 大綱**。 您可以組織參數、變數和資源。 通過瞭解架構，Visual Studio 可以在部署範本之前指出範本中的錯誤。

![JSON 總管](media/virtual-machine-scale-sets-vs-create/json-explorer.png)

## <a name="deploy-the-project"></a>部署專案

部署 Azure 資源管理器範本以創建虛擬機器縮放集資源：

1. 在**解決方案資源管理器**中，按右鍵專案並選擇 **"部署** > **New**"。

    ![部署專案](media/virtual-machine-scale-sets-vs-create/deploy-new-project.png)

1. 在 **"部署到資源組"** 中，選擇要使用哪個訂閱並選擇資源組。 如有必要，可以創建資源組。

1. 接下來，選擇 **"編輯參數"** 以輸入傳遞給範本的參數。

   ![輸入訂閱和資源組](media/virtual-machine-scale-sets-vs-create/deploy-to-resource-group.png)

1. 提供作業系統的使用者名和密碼。 創建部署需要這些值。 如果您沒有安裝適用于 Visual Studio 的 PowerShell 工具，請選擇 **"保存密碼**以避免隱藏 PowerShell 命令提示"，或使用[金鑰保存庫支援](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/)。 選擇 **"保存"** 以繼續。

    ![編輯部署參數](media/virtual-machine-scale-sets-vs-create/edit-deployment-parameters.png)

1. 在 **"部署到資源組**"中，選擇 **"部署**"。 該操作運行**部署 Azure 資源組.ps1**腳本。 [輸出]**** 視窗會顯示部署進度。

   ![輸出顯示結果](media/virtual-machine-scale-sets-vs-create/deployment-output.png)

## <a name="explore-your-virtual-machine-scale-set"></a>探索虛擬機器縮放集<a name="exploring-your-virtual-machine-scale-set"></a>

選擇 **"查看** > **雲資源管理器**"以查看新的虛擬機器規模集。 如有必要，請使用 **"全部刷新**"。

![雲端總管](media/virtual-machine-scale-sets-vs-create/cloud-explorer.png)

**雲資源管理器**允許您在開發應用程式時在視覺化工作室中管理 Azure 資源。 您也可以在 [Azure 入口網站](https://portal.azure.com)和 [Azure 資源總管 (英文)](https://resources.azure.com/) 中檢視虛擬機器擴展集。

 門戶提供了使用 Web 瀏覽器管理 Azure 基礎結構的最佳方式。 Azure 資源資源管理器提供了一種探索和調試 Azure 資源的簡便方法。 Azure 資源資源管理器提供實例視圖，並顯示您所查看資源的 PowerShell 命令。

## <a name="next-steps"></a>後續步驟

通過 Visual Studio 成功部署虛擬機器縮放集後，您可以進一步自訂專案以滿足應用程式要求。 例如，通過添加**見解**資源來配置自動縮放。 您可以將基礎結構添加到範本（如獨立虛擬機器）或使用自訂腳本擴展部署應用程式。 好的示例範本可以在[Azure 快速入門範本](https://github.com/Azure/azure-quickstart-templates)GitHub 存儲庫中找到。 搜尋 `vmss`。
