---
title: 建立以應用程式為中心的環境-Azure
description: 本文示範如何使用 Cloud Shell Colony 和 Microsoft Azure 來建立以應用程式為中心的環境。
ms.topic: how-to
ms.date: 11/26/2020
ms.openlocfilehash: 88244f268d5ed038e9bb7082d3d5cc1179e5ec4e
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2020
ms.locfileid: "94918097"
---
# <a name="create-an-application-centric-environment"></a>建立以應用程式為中心的環境

[Quali 的 CloudShell Colony](https://azuremarketplace.microsoft.com/marketplace/apps/quali_systems.cloudshell_colony?tab=Overview) 是 SaaS 平臺，可針對雲端技術（包括 Azure 和 Kubernetes）上以應用程式為中心的複雜環境大規模提供基礎結構自動化。 CloudShell Colony 補充 Azure DevTest Labs，協助開發人員團隊在整個價值串流中部署複雜的應用程式，一直到生產環境。

本文示範如何使用 CloudShell Colony 和 Microsoft Azure 來建立以應用程式為中心的環境。

## <a name="set-up-the-environment-with-cloudshell-colony-and-microsoft-azure"></a>使用 CloudShell Colony 和 Microsoft Azure 設定環境

1. 註冊免費試用 [Colony](https://azuremarketplace.microsoft.com/marketplace/apps/quali_systems.cloudshell_colony?tab=Overview)。

    :::image type="content" source="./media/create-application-centric-environment/free-trial.png" alt-text="註冊免費試用":::    
1. 將您的 Azure 帳戶 (view) 的 [步驟](https://colonysupport.quali.com/hc/articles/360008222234) 連結。

    :::image type="content" source="./media/create-application-centric-environment/welcome.png" alt-text="歡迎使用 Colony":::     
1. 邀請使用者加入您的空間。
1. 使用 YAML 檔建立您的第一個藍圖， ([在這裡) 查看步驟](https://colonysupport.quali.com/hc/articles/360001680807-Steps-to-Developing-a-Blueprint) 。
    1. 將 GitHub 或 BitBucket 中的藍圖存放庫連結至 Colony。
    1. 使用 Colony 範例藍圖作為基礎，並視需要進行修改。

        :::image type="content" source="./media/create-application-centric-environment/performance-stress-tests.png" alt-text="壓力測試":::    
    1. 將您的藍圖發佈給其他人使用。
1. 使用 Colony 將您的應用程式環境啟動至沙箱。

    :::image type="content" source="./media/create-application-centric-environment/blueprints.png" alt-text="使用 Colony 將您的應用程式環境啟動至沙箱":::    

> [!NOTE]
> 您也可以將藍圖整合為 Azure DevOps 中的 CI/CD 工作流程的一部分 (在此) 中 [查看步驟](https://colonysupport.quali.com/hc/articles/360008464234) 。

:::image type="content" source="./media/create-application-centric-environment/devops-pipeline.png" alt-text="連接到您的 Azure DevOps 管線":::    

## <a name="next-steps"></a>後續步驟

[要求 Colony 的示範](https://info.quali.com/cloudshell-colony-demo-request)
