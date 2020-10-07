---
title: Azure DevOps Starter 概觀 | Microsoft Docs
description: 了解 Azure DevOps Starter 如何協助您從 Azure 入口網站的單一檢視啟動、部署及管理您的應用程式。
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
ms.openlocfilehash: 91e62cae242279e1923fc31970c233ec70bf7f11
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "87461335"
---
# <a name="overview-of-azure-devops-starter"></a>Azure DevOps Starter 概觀

 Azure DevOps 入門版可供輕鬆地開始使用 Azure。 其可協助您只透過幾個快速步驟，就能從 Azure 入口網站中，在所選擇的 Azure 服務上啟動您最喜愛的 app。 

 DevOps Starter 會設定您在開發、部署及監控應用程式時所需的一切。 您可以使用 DevOps Starter 儀表板來監控程式碼認可、建置和部署，全部都只要在 Azure 入口網站的單一檢視中就能完成。

## <a name="advantages-of-using-devops-starter"></a>使用 DevOps Starter 的優點

  DevOps Starter 會自動設定對 Azure 的整個持續整合 (CI) 與持續傳遞 (CD) 管線。  您可從現有的程式碼開始，也可使用所提供的其中一個應用程式範例。 然後，您可將該應用程式快速部署到各種 Azure 服務，例如虛擬機器、App Service、Azure Kubernetes Services (AKS)、Azure SQL Database 和 Azure Service Fabric。  

  DevOps Starter 會執行 DevOps 管線初始組態設定的所有工作，包括從設定初始 Git 儲存機制、設定 CI/CD 管線、建立用於監控的 Application Insights 資源、以及提供整個解決方案的單一檢視，包括在 Azure 入口網站中建立 DevOps Projects 儀表板的所有工作。

您可以使用 DevOps Starter 來執行下列作業：

* 將應用程式快速部署至 Azure
* 將 CI/CD 管線的設定自動化
* 檢視並了解如何適當地設定 CI/CD 管線
* 進一步依據您的特定情況自訂發行管線

## <a name="how-to-use-devops-starter"></a>如何使用 DevOps Starter？

  DevOps Starter 可從 Azure 入口網站取得。 您可以從入口網站建立 DevOps Starter 資源，就像建立任何其他 Azure 資源一樣。 DevOps Projects 針對各種組態設定選項提供逐步執行精靈型體驗。  

您可以在初始設定中選擇幾個組態設定選項。 這些選項包含︰

* 使用提供的 app 範例，或使用自己的程式碼
* 選取 app 語言
* 依據語言選擇 app 架構
* 選取 Azure 服務 (部署目標)
* 建立新的 Azure DevOps 組織或使用現有組織 
* 選擇 Azure 訂用帳戶
* 挑選 Azure 服務的位置
* 從 Azure 服務的多種定價層中選擇

使用 DevOps Starter 之後，您也可以在 Azure 入口網站上，從單一位置將所有資源從 DevOps Starter 儀表板中刪除。

## <a name="devops-starter-and-azure-devops-integration"></a>DevOps Starter 與 Azure DevOps 整合

DevOps Starter 由 Azure DevOps 提供技術。 DevOps Starter 可讓 Azure 管線中用來設定 CI/CD 的所需工作全都自動化。 其會在新的或現有的 Azure DevOps 組織中建立 Git 存放庫，然後將應用程式範例或您現有的程式碼認可至新的 Git 存放庫。  

自動化功能也會為組建建立一個 CI 觸發程序，讓每個新的程式碼認可都能起始組建。 DevOps Starter 會建立 CD 觸發程序，且會把每個成功建立的新組建部署到您選擇的 Azure 服務中。  

您可以自訂組建和發行管線以用於其他案例。 此外，您也可以複製組建和發行管線以在其他專案中使用。

建立 DevOps Starter 之後，您可以：

* 自訂組建和發行管線
* 使用提取要求管理程式碼流程，並維持高品質
* 在合併程式碼以提高品質之前，先測試及建置每次認可
* 追蹤您應用程式的待處理項目與問題

## <a name="getting-started-with-devops-starter"></a>開始使用 DevOps Starter

* [開始使用 DevOps Starter](https://docs.microsoft.com/azure/devops-project/azure-devops-project-github)

##  <a name="devops-starter-videos"></a>DevOps Starter 影片

* [使用 DevOps Starter 建立 CI/CD](https://www.youtube.com/watch?v=NuYDAs3kNV8)
