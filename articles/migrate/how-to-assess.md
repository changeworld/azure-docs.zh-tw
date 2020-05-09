---
title: 在 Azure Migrate 中新增評估工具
description: 瞭解如何在 Azure Migrate 中新增評定工具。
ms.topic: how-to
ms.date: 04/26/2020
ms.openlocfilehash: 7e7d7cf26cab30cd7449e4643fdfb59faece47f2
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2020
ms.locfileid: "82901040"
---
# <a name="add-assessment-tools"></a>新增評量工具

本文說明如何在[Azure Migrate](migrate-overview.md)中新增評定工具。 

- 如果您想要新增評估工具，但還沒有 Azure Migrate 專案，請遵循這[篇文章](how-to-add-tool-first-time.md)。-
- 如果您已新增 ISV 工具或 Movere 以進行評估，請[遵循下列步驟](prepare-isv-movere.md)來準備使用此工具。

## <a name="select-an-assessment-scenario"></a>選取評估案例

1. 在 Azure Migrate 專案中，按一下 [概觀]  。
2. 選取您想要使用的評估案例：

    - 若要探索及評估要遷移至 Azure 的機器和工作負載，請選取 [**評估和遷移伺服器**]。
    - 若要評估內部部署的 SQL 機器，請選取 [**評估和遷移資料庫**]。
    - 若要評估內部部署 web 應用程式，請選取 [**評估和遷移 web 應用程式**]。

    ![評定案例](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>選取伺服器評估工具 

1. 按一下 [**評估和遷移伺服器**]。
2. 在 [ **Azure Migrate-伺服器**] 中，如果您尚未新增評估工具，請在 [**評估工具**] 底下，選取 [**按一下這裡新增評估工具**]。 如果您已新增評估工具，請在 [**新增更多評估工具**] 中選取 [**變更**]。

    > [!NOTE]
    > 如果您需要流覽至不同的專案，請在 [ **Azure Migrate 伺服器**] 中，按一下 [**查看不同遷移專案的詳細資料**] 旁的 [**按一下這裡**]。

3. 在 [ **Azure Migrate**中，選取您想要使用的評估工具。

    - 如果您使用 Azure Migrate Server 評估，您可以直接在 Azure Migrate 專案中設定、執行及查看評量。
    - 如果您使用不同的評估工具，請流覽至針對其網站提供的連結，並根據所提供的指示執行評估。


## <a name="select-a-database-assessment-tool"></a>選取資料庫評估工具

1. 按一下 [**評估並遷移資料庫**]
2. 在 [**資料庫**] 中，按一下 [**加入工具**]。
3. 在 [新增工具] 中 >**選取 [評估工具**]，選取您要用來評估資料庫的工具。

## <a name="select-a-web-app-assessment-tool"></a>選取 web 應用程式評估工具

1. 按一下 [**評估和遷移 web 應用程式**]。
2. 遵循 Azure App Service 的遷移工具連結。 使用遷移工具來執行下列動作：

    - **線上評估應用程式**：您可以使用 Azure App Service 移轉小幫手，在線上評估具有公用 URL 的應用程式。
    - **.Net/PHP**：若是內部 .NET 和 php 應用程式，您可以下載並執行移轉小幫手。



## <a name="next-steps"></a>後續步驟

試用針對[VMware](tutorial-prepare-vmware.md) Vm、 [hyper-v](tutorial-prepare-hyper-v.md)或[實體伺服器](tutorial-prepare-physical.md)使用 Azure Migrate Server 評估的評量
