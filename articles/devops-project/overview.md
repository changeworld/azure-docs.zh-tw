---
title: Azure DevOps Starter 的總覽 |Microsoft Docs
description: 瞭解 Azure DevOps Starter 的價值
services: devops-project
documentationcenter: ''
author: mlearned
manager: gwallace
editor: mlearned
ms.assetid: ''
ms.service: az-devops-project
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 03/24/2020
ms.author: mlearned
ms.openlocfilehash: 99a1fdb8caff9953041c996d0f5581318ce11c66
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82233681"
---
# <a name="overview-of-azure-devops-starter"></a>Azure DevOps Starter 的總覽

 Azure DevOps 入門版可供輕鬆地開始使用 Azure。 其可協助您只透過幾個快速步驟，就能從 Azure 入口網站中，在所選擇的 Azure 服務上啟動您最喜愛的 app。 

 DevOps Starter 設定了開發、部署和監視應用程式所需的所有專案。 您可以使用 DevOps 入門儀表板來監視程式碼認可、組建和部署，全都是從 Azure 入口網站中的單一視圖來進行。

## <a name="advantages-of-using-devops-starter"></a>使用 DevOps Starter 的優點

  DevOps Starter 會自動將整個持續整合（CI）和持續傳遞（CD）管線設定為 Azure。  您可從現有的程式碼開始，也可使用所提供的其中一個應用程式範例。 然後，您可將該應用程式快速部署到各種 Azure 服務，例如虛擬機器、App Service、Azure Kubernetes Services (AKS)、Azure SQL Database 和 Azure Service Fabric。  

  DevOps Starter 會針對 DevOps 管線的初始設定執行所有工作，包括從設定初始 Git 存放庫、設定 CI/CD 管線、建立用於監視的 Application Insights 資源，以及透過在 Azure 入口網站中建立 DevOps Projects 儀表板來提供整個解決方案的單一視圖。

您可以使用 DevOps Starter 來執行下列動作：

* 將應用程式快速部署至 Azure
* 將 CI/CD 管線的設定自動化
* 檢視並了解如何適當地設定 CI/CD 管線
* 進一步依據您的特定情況自訂發行管線

## <a name="how-to-use-devops-starter"></a>如何使用 DevOps Starter？

  DevOps Starter 可從 Azure 入口網站取得。 您可以建立 DevOps 入門資源，就像您從入口網站建立任何其他 Azure 資源一樣。 DevOps Projects 針對各種組態設定選項提供逐步執行精靈型體驗。  

您可以在初始設定中選擇幾個組態設定選項。 這些選項包含︰

* 使用提供的 app 範例，或使用自己的程式碼
* 選取 app 語言
* 依據語言選擇 app 架構
* 選取 Azure 服務 (部署目標)
* 建立新的 Azure DevOps 組織或使用現有組織 
* 選擇 Azure 訂用帳戶
* 挑選 Azure 服務的位置
* 從 Azure 服務的多種定價層中選擇

使用 DevOps Starter 之後，您也可以從 [Azure 入口網站上的 [DevOps 入門儀表板] 中，刪除單一位置的所有資源。

## <a name="devops-starter-and-azure-devops-integration"></a>DevOps Starter 和 Azure DevOps 整合

DevOps Starter 的支援 Azure DevOps。 DevOps Starter 會將 Azure Pipelines 所需的所有工作自動化，以設定 CI/CD 管線。 其會在新的或現有的 Azure DevOps 組織中建立 Git 存放庫，然後將應用程式範例或您現有的程式碼認可至新的 Git 存放庫。  

自動化功能也會為組建建立一個 CI 觸發程序，讓每個新的程式碼認可都能起始組建。 DevOps Starter 會建立 CD 觸發程式，並將每個成功的新組建部署到您選擇的 Azure 服務。  

您可以自訂組建和發行管線以用於其他案例。 此外，您也可以複製組建和發行管線以在其他專案中使用。

建立 DevOps Starter 之後，您可以：

* 自訂組建和發行管線
* 使用提取要求管理程式碼流程，並維持高品質
* 在合併程式碼以提高品質之前，先測試及建置每次認可
* 追蹤您應用程式的待處理項目與問題

## <a name="getting-started-with-devops-starter"></a>開始使用 DevOps Starter

* [開始使用 DevOps Starter](https://docs.microsoft.com/azure/devops-project/azure-devops-project-github)

##  <a name="devops-starter-videos"></a>DevOps 入門影片

* [使用 Azure DevOps Starter 建立 CI/CD](https://www.youtube.com/watch?v=NuYDAs3kNV8)
