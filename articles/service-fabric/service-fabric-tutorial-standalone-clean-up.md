---
title: 清除獨立叢集
description: 在本教學課程中，您將了解如何刪除獨立 Service Fabric 叢集的 AWS 或 Azure 資源。
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: 0d46e9068a311594f779411c3ccee2b408febb3f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91842881"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>本教學課程：清除獨立叢集

Service Fabric 獨立叢集可讓您選擇自己的環境來裝載 Service Fabric。 在此教學課程系列中，您將建立裝載於 AWS 或 Azure 的獨立叢集，並在其中部署應用程式。

本教學課程是一個系列的第四部分。 此部分的教學課程將說明如何刪除您先前建立用以裝載 Service Fabric 叢集的 AWS 或 Azure 資源。

在本文中，您將了解如何：

> [!div class="checklist"]
> * 移除 Service Fabric 叢集
> * 刪除 AWS 或 Azure 資源

## <a name="remove-a-service-fabric-cluster"></a>移除 Service Fabric 叢集

1. 透過 RDP 進入您對已安裝的 Service Fabric 使用的 VM。
2. 開啟 PowerShell。
3. 將目錄切換至第二個教學課程中的解壓縮資料夾。
4. 執行下列命令以移除 Service Fabric 叢集：

  ```powershell
  .\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
  ```

5. 在出現提示時輸入 `Y`。 如果作業已順利完成，輸出將會如下所示 (替換為您自己的 IP 位址)：

  ```powershell
  Best Practices Analyzer completed successfully.
  Removing configuration from machine 172.31.21.141
  Removing configuration from machine 172.31.27.1
  Removing configuration from machine 172.31.20.163
  Configuration removed from machine 172.31.21.141
  Configuration removed from machine 172.31.27.1
  Configuration removed from machine 172.31.20.163
  The cluster is successfully removed.
  ```

## <a name="delete-aws-resources"></a>刪除 AWS 資源

1. 登入您的 AWS 帳戶。
2. 移至 EC2 主控台。
3. 選取您在教學課程的第一個單元中建立的三個節點。
4. 選取 [動作] > [執行個體狀態] > [終止]。

## <a name="delete-azure-resources"></a>刪除 Azure 資源

1. 登入 Azure 入口網站。
2. 移至 [虛擬機器]**** 區段。
3. 選取您在教學課程第一個單元中建立的三個節點核取方塊。
4. 選取 [刪除]。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何刪除先前步驟中建立的資源。

> [!div class="checklist"]
> * 清除資源

> [!div class="nextstepaction"]
> [回到開頭處](service-fabric-tutorial-standalone-create-infrastructure.md)
