---
title: 透過 Kubernetes 儀表板監視您的 Azure Stack Edge Pro 裝置 |Microsoft Docs
description: 說明如何存取和使用 Kubernetes 儀表板來監視您的 Azure Stack Edge Pro 裝置。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/22/2020
ms.author: alkohli
ms.openlocfilehash: 137cff47d49be1405f60bc47cd16f7f027ab63a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91320824"
---
# <a name="use-kubernetes-dashboard-to-monitor-your-azure-stack-edge-pro-gpu-device"></a>使用 Kubernetes 儀表板來監視您的 Azure Stack Edge Pro GPU 裝置

本文說明如何存取和使用 Kubernetes 儀表板來監視您的 Azure Stack Edge Pro GPU 裝置。 若要監視您的裝置，您可以使用 Azure 入口網站中的圖表、觀看 Kubernetes 儀表板，或透過 `kubectl` 裝置的 PowerShell 介面執行命令。 

本文僅著重于可在 Kubernetes 儀表板上執行的監視工作。

在本文中，您將學會如何：

> [!div class="checklist"]
>
> * 存取您裝置上的 Kubernetes 儀表板
> * 查看裝置上部署的模組
> * 取得部署在裝置上的應用程式 IP 位址
> * 查看裝置上部署之模組的容器記錄


## <a name="about-kubernetes-dashboard"></a>關於 Kubernetes 儀表板

Kubernetes 儀表板是一個 web 型使用者介面，可讓您用來針對容器化應用程式進行疑難排解。 Kubernetes 儀表板是 Kubernetes 命令列以 UI 為基礎的替代方案 `kubectl` 。 如需詳細資訊，請參閱 [Kubernetes 儀表板](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)。 

在您的 Azure Stack Edge Pro 裝置上，您可以在 *唯讀* 模式中使用 Kubernetes 儀表板，以深入瞭解在 Azure Stack Edge Pro 裝置上執行的應用程式、查看 Kubernetes 叢集資源的狀態，以及查看裝置上發生的任何錯誤。

## <a name="access-dashboard"></a>存取儀表板

Kubernetes 儀表板是 *唯讀* 的，會在埠31000的 Kubernetes 主要節點上執行。 遵循下列步驟以存取儀表板： 

1. 在裝置的本機 UI 中，移至 [ **裝置** ]，然後移至 **裝置端點**。 
1. 選取 [ **下載** 設定] 以下載 `kubeconfig` ，可讓您存取儀表板。 將檔案儲存 `config.json` 在本機系統上。
1. 選取 Kubernetes 儀表板 URL，以在瀏覽器中開啟儀表板。

    ![在本機 UI 的 [裝置] 頁面中 Kubernetes 儀表板 URL](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-url-local-ui-1.png)

1. 在 [ **Kubernetes 儀表板** ] 登入頁面上：
    
    1. 選取 [ **kubeconfig**]。 
        ![選取 kubeconfig 選項](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-sign-in-1.png) 
    1. 選取省略號 **...**。流覽並指向您稍 `kubeconfig` 早在本機系統上下載的。 選取 [登入]。
        ![流覽至 kubeconfig 檔案](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-sign-in-2.png)    

6. 您現在可以在唯讀模式中，查看 Azure Stack Edge Pro 裝置的 Kubernetes 儀表板。

    ![Kubernetes 儀表板主頁面](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-main-page-1.png)

## <a name="view-module-status"></a>查看模組狀態

計算模組是已實行商務邏輯的容器。 您可以使用儀表板來確認是否已成功在您的 Azure Stack Edge Pro 裝置上部署計算模組。

若要查看模組狀態，請在儀表板上執行下列步驟：

1. 在 [儀表板] 的左窗格中，移至 [ **命名空間**]。 依命名空間篩選 IoT Edge 模組的顯示位置，在此案例中為 **iotedge**。
1. 在左窗格中，移至 [ **工作負載 > 部署**]。
1. 在右窗格中，您會看到部署在裝置上的所有模組。 在此情況下，GettingStartedWithGPU 模組會部署在 Azure Stack Edge Pro 上。 您可以看到模組已部署。

    ![查看模組部署](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-module-deployment-1.png)

 
## <a name="get-ip-address-for-services-or-modules"></a>取得服務或模組的 IP 位址

您可以使用儀表板來取得您想要在 Kubernetes 叢集外部公開之服務或模組的 IP 位址。 

您可以透過 [ **計算網路設定** ] 頁面中裝置的本機 web UI，指派這些外部服務的 IP 範圍。 部署 IoT Edge 模組之後，您可能會想要取得指派給特定模組或服務的 IP 位址。 

若要取得 IP 位址，請在儀表板上執行下列步驟：

1. 在 [儀表板] 的左窗格中，移至 [ **命名空間**]。 依部署外部服務的命名空間篩選，在此案例中為 **iotedge**。
1. 在左窗格中，移至 [ **探索和負載平衡 > 服務**]。
1. 在右窗格中，您會看到在 `iotedge` Azure Stack Edge Pro 裝置上的命名空間中執行的所有服務。

    ![取得外部服務的 IP](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-get-ip-external-service-1.png)

## <a name="view-container-logs"></a>檢視容器記錄

在某些情況下，您需要查看容器記錄。 您可以使用儀表板來取得您已在 Kubernetes 叢集上部署之特定容器的記錄。

若要查看容器記錄，請在儀表板上執行下列步驟：

1. 在 [儀表板] 的左窗格中，移至 [ **命名空間**]。 依部署 IoT Edge 模組的命名空間進行篩選，在此案例中為 **iotedge**。
1. 在左窗格中，移至 > pod 的 **工作負載**。
1. 在右窗格中，您會看到所有在您的裝置上執行的 pod。 識別正在執行您想要查看其記錄之模組的 pod。 選取您所識別之 pod 的垂直省略號，然後從內容功能表中選取 [ **記錄**]。

    ![View container logs 1](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-container-logs-1.png)

1. 記錄檔會顯示在儀表板內建的記錄檢視器中。 您也可以下載記錄檔。

    ![查看容器記錄2](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-container-logs-1.png)
    

## <a name="view-cpu-memory-usage"></a>查看 CPU、記憶體使用量

Azure Stack Edge Pro 裝置的 Kubernetes 儀表板也有 [計量伺服器附加](https://kubernetes.io/docs/tasks/debug-application-cluster/resource-metrics-pipeline/) 元件，可匯總 Kubernetes 資源之間的 CPU 和記憶體使用量。
 
例如，您可以在所有命名空間中，查看整個部署所耗用的 CPU 和記憶體。 

![查看所有部署之間的 CPU 和記憶體使用量](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/view-cpu-memory-all-1.png)

您也可以依特定命名空間進行篩選。 在下列範例中，您可以只針對 Azure Arc 部署來查看 CPU 和記憶體耗用量。  

![查看 Azure Arc 部署的 CPU 和記憶體使用量](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/view-cpu-memory-azure-arc-1.png)

Kubernetes 計量伺服器提供自動調整管線，例如 [水準 Pod 自動調整程式](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)。


## <a name="next-steps"></a>後續步驟

瞭解如何針對 [裝置問題進行疑難排解](azure-stack-edge-gpu-troubleshoot.md)。
