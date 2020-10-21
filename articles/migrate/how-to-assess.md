---
title: 在 Azure Migrate 中新增評定工具
description: 瞭解如何在 Azure Migrate 中新增評量工具。
ms.topic: how-to
ms.date: 04/26/2020
ms.openlocfilehash: 5dee9ec2210bf6e04255b07cf641ced6807c8b5a
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92317356"
---
# <a name="add-assessment-tools"></a>新增評量工具

本文說明如何在 [Azure Migrate](./migrate-services-overview.md)中新增評定工具。 

- 如果您想要新增評量工具，但還沒有 Azure Migrate 專案，請遵循這 [篇文章](how-to-add-tool-first-time.md)。
- 如果您已新增 ISV 工具或 Movere，以進行評量，請 [遵循下列步驟](prepare-isv-movere.md)來準備使用此工具。

## <a name="select-an-assessment-scenario"></a>選取評量案例

1. 在 Azure Migrate 專案中，按一下 [概觀]****。
2. 選取您要使用的評定案例：

    - 若要探索及評估要遷移至 Azure 的機器和工作負載，請選取 [ **評估和遷移伺服器**]。
    - 若要評估內部部署 SQL Server 資料庫，請選取 [ **評估和遷移資料庫**]。
    - 若要評估內部部署 web 應用程式，請選取 [ **評定及遷移 web 應用程式**]。

    ![評定案例](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>選取伺服器評量工具 

1. 按一下 [ **評估和遷移伺服器**]。
2. 在 **Azure Migrate-伺服器**中，如果您尚未新增評量工具，請選取 [ **評定工具**] 底下的 [ **按一下這裡新增評定工具**]。 如果您已經新增評量工具，請在 [ **新增更多評量工具**] 中選取 [ **變更**]。

    > [!NOTE]
    > 如果您需要流覽至不同的專案，請在 [ **Azure Migrate-伺服器**] 中，按一下 [ **查看不同遷移專案的詳細資料**] 旁的 [ **按一下這裡**]。

3. 在 **Azure Migrate**中，選取您要使用的評量工具。

    - 如果您使用 Azure Migrate Server 評量，您可以直接在 Azure Migrate 專案中設定、執行和查看評量。
    - 如果您使用不同的評量工具，請流覽至為其網站提供的連結，並根據所提供的指示執行評量。


## <a name="select-a-database-assessment-tool"></a>選取資料庫評估工具

1. 按一下 [**評估和遷移資料庫**]
2. 在 [ **資料庫**] 中，按一下 [ **新增工具**]。
3. 在 [新增工具] > **選取 [評定工具**] 中，選取您要用來評估資料庫的工具。

## <a name="select-a-web-app-assessment-tool"></a>選取 web 應用程式評估工具

1. 按一下 [ **評估和遷移 web 應用程式**]。
2. 遵循 Azure App Service 遷移工具的連結。 使用遷移工具：

    - **線上評估應用程式**：您可以使用 Azure App Service Migration Assistant，在線上評估具有公用 URL 的應用程式。
    - **.Net/PHP**：針對內部 .NET 和 php 應用程式，您可以下載並執行 Migration Assistant。



## <a name="next-steps"></a>後續步驟

試用針對[VMware](./tutorial-discover-vmware.md) Vm、 [hyper-v](./tutorial-discover-hyper-v.md)或[實體伺服器](./tutorial-discover-physical.md)使用 Azure Migrate 伺服器評量的評量