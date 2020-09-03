---
title: 教學課程 - 在 Azure 中監視 Linux 虛擬機器
description: 在本教學課程中，您將了解如何監視在 Linux 虛擬機器上執行的效能和探索到的應用程式元件。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: mgoedtel
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/30/2019
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: c8d7b13f9e35a41a414a44c908997cfcc550af41
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89011730"
---
# <a name="tutorial-monitor-a-linux-virtual-machine-in-azure"></a>教學課程：在 Azure 中監視 Linux 虛擬機器

Azure 監視會使用代理程式從 Azure VM 收集開機和效能資料，將此資料儲存在 Azure 儲存體，並讓資料可透過入口網站、Azure PowerShell 模組和 Azure CLI 存取。 透過適用於 VM 的 Azure 監視器可提供進階監視，具體方法是，收集效能計量、探索安裝在 VM 上的應用程式元件，以及納入效能圖表和相依性對應。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 啟用 VM 上的開機診斷
> * 檢視開機診斷
> * 檢視 VM 主機計量
> * 啟用適用於 VM 的 Azure 監視器
> * 檢視 VM 效能計量
> * 建立警示

## <a name="launch-azure-cloud-shell"></a>啟動 Azure Cloud Shell

Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 

若要開啟 Cloud Shell，只要選取程式碼區塊右上角的 [試試看]  即可。 您也可以移至 [https://shell.azure.com/powershell](https://shell.azure.com/powershell)，從另一個瀏覽器索引標籤啟動 Cloud Shell。 選取 [複製]  即可複製程式碼區塊，將它貼到 Cloud Shell 中，然後按 enter 鍵加以執行。

如果您選擇在本機安裝和使用 CLI，本教學課程會要求您執行 Azure CLI 2.0.30 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-vm"></a>建立 VM

若要查看作用中的診斷和計量，您需要 VM。 首先，使用 [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create) 建立資源群組。 下列範例會在 eastus 位置建立名為 myResourceGroupMonitor 的資源群組。

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

現在，使用 [az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create) 建立 VM。 下列範例會建立名為 myVM 的 VM，並產生 SSH 金鑰 (如果 ~/.ssh/ 中沒有這些金鑰的話)︰

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="enable-boot-diagnostics"></a>啟用開機診斷

當 Linux VM 開機，開機診斷擴充功能會擷取開機輸出，並儲存在 Azure 儲存體。 這項資料可以用於 VM 開機問題的疑難排解。 當您使用 Azure CLI 建立 Linux VM 時，開機診斷不會自動啟用。

啟用開機診斷之前，需要建立儲存體帳戶用來儲存開機記錄。 儲存體帳戶必須具有全域唯一的名稱，介於 3 到 24 個字元的長度，而且只能包含數字和小寫字母。 使用 [az storage account create](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) 建立儲存體帳戶。 在此範例中，使用隨機字串來建立唯一的儲存體帳戶名稱。

```azurecli-interactive
storageacct=mydiagdata$RANDOM

az storage account create \
  --resource-group myResourceGroupMonitor \
  --name $storageacct \
  --sku Standard_LRS \
  --location eastus
```

當啟用開機診斷時，需要 blob 儲存體容器的 URI。 以下命令會查詢儲存體帳戶傳回此 URI。 URI 值儲存在名為 bloburi 的變數，下一個步驟會使用此變數。

```azurecli-interactive
bloburi=$(az storage account show --resource-group myResourceGroupMonitor --name $storageacct --query 'primaryEndpoints.blob' -o tsv)
```

現在，使用 [az vm boot-diagnostics enable](/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-enable) 啟用開機診斷。 `--storage` 值是在上一個步驟收集到的 blob URI。

```azurecli-interactive
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```

## <a name="view-boot-diagnostics"></a>檢視開機診斷

開機診斷啟用後，每次您停止並啟動 VM 時，開機程序的相關資訊便會寫入記錄檔。 在此範例中，先使用 [az vm deallocate](/cli/azure/vm?view=azure-cli-latest#az-vm-deallocate) 命令將 VM 解除配置，如下所示：

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

現在，使用 [az vm start](/cli/azure/vm?view=azure-cli-latest#az-vm-start) 命令啟動 VM，如下所示：

```azurecli-interactive
az vm start --resource-group myResourceGroupMonitor --name myVM
```

您可以使用 [az vm boot-diagnostics get-boot-log](/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-get-boot-log) 命令取得 myVM 的開機診斷資料，如下所示︰

```azurecli-interactive
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```

## <a name="view-host-metrics"></a>檢視主機計量

在 Azure 中有 Linux VM 專用的主機與它互動。 系統會自動收集主機的計量，而且可以在 Azure 入口網站中檢視這些計量，如下所示︰

1. 從 Azure 入口網站中，選取 [資源群組]，選擇 [myResourceGroupMonitor]，然後選取資源清單中的 [myVM]。
1. 若要查看主機 VM 的執行狀況，選取 VM 視窗上的 [計量]，然後選擇 [可用的計量] 下的任何 [主機] 計量。

    ![檢視主機計量](./media/tutorial-monitoring/monitor-host-metrics.png)

## <a name="enable-advanced-monitoring"></a>啟用進階監視

若要使用適用於 VM 的 Azure 監視器對 Azure VM 啟用監視：

1. 從 Azure 入口網站中，按一下 [資源群組]，選取 [myResourceGroupMonitor]，然後選取資源清單中的 [myVM]。

2. 在 VM 頁面的 [監視] 區段中，選取 [Insights (預覽)]。

3. 在 [Insights (預覽)] 頁面上，選取 [立即試用]。

    ![為 VM 啟用適用於 VM 的 Azure 監視器](../../azure-monitor/insights/media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

4. 在 [將 Azure 監視器 Insights 上線] 頁面上，如果您目前在同一個訂用帳戶中有 Log Analytics 工作區，請在下拉式清單中選取它。  

    清單會預先選取用來在訂用帳戶中部署 VM 的預設工作區和位置。 

    >[!NOTE]
    >若要建立新的 Log Analytics 工作區以儲存來自 VM 的監視資料，請參閱[建立 Log Analytics 工作區](../../azure-monitor/learn/quick-create-workspace.md)。 工作區必須屬於其中一個[支援的區域](../../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions)。

啟用監視之後，您可能需要等待數分鐘，才能檢視 VM 的效能計量。

![啟用適用於 VM 的 Azure 監視器來監視部署處理](../../azure-monitor/insights/media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="view-vm-performance-metrics"></a>檢視 VM 效能計量

適用於 VM 的 Azure 監視器包含一組以關鍵效能指標 (KPI) 為目標的效能圖表，可協助您判斷虛擬機器的執行狀況。 若要從 VM 存取，請執行下列步驟。

1. 從 Azure 入口網站中，按一下 [資源群組]，選取 [myResourceGroupMonitor]，然後選取資源清單中的 [myVM]。

2. 在 VM 頁面的 [監視] 區段中，選取 [Insights (預覽)]。

3. 選取 [效能] 索引標籤。

此頁面不僅包含效能使用率圖表，也是一個表格，可顯示已找到的每個邏輯磁碟、其容量、使用率，以及每個量值的總平均。

## <a name="create-alerts"></a>建立警示

您可以根據特定效能計量來建立警示。 舉例來說，可使用警示來通知您平均 CPU 使用量超過特定臨界值、或是可用磁碟空間降到低於某個量。 警示會顯示在 Azure 入口網站，或是可以透過電子郵件傳送。 您也可以觸發 Azure 自動化 Runbook 或 Azure Logic Apps 以回應產生的警示。

以下範例會建立平均 CPU 使用量的警示。

1. 從 Azure 入口網站中，按一下 [資源群組]，選取 [myResourceGroupMonitor]，然後選取資源清單中的 [myVM]。

2. 按一下VM 刀鋒視窗中的 [警示規則]，按一下橫跨在警示刀鋒視窗上方的 [新增計量警示]。

3. 提供警示的 [名稱]，例如 myAlertRule。

4. 若要在 CPU 百分比連續五分鐘超過 1.0 時觸發警示，保留所有其他已選取的預設值。

5. (選擇性) 選取 [電子郵件的擁有者、參與者及讀者] 核取方塊以傳送電子郵件通知。 預設動作是在入口網站中顯示通知。

6. 按一下 [確定] 按鈕。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已設定並檢視 VM 的效能。 您已了解如何︰

> [!div class="checklist"]
> * 建立資源群組和 VM
> * 啟用 VM 上的開機診斷
> * 檢視開機診斷
> * 檢視主機計量
> * 啟用適用於 VM 的 Azure 監視器
> * 檢視 VM 計量
> * 建立警示

請前進到下一個教學課程，以了解 Azure 資訊安全中心。

> [!div class="nextstepaction"]
> [管理 VM 安全性](tutorial-azure-security.md)
