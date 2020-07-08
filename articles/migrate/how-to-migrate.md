---
title: 在 Azure Migrate 中新增遷移工具
description: 瞭解如何在 Azure Migrate 中新增遷移工具。
ms.topic: article
ms.date: 04/26/2020
ms.openlocfilehash: 670341adca7ab2958d43132aab164d7bba0f87d0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84195814"
---
# <a name="add-migration-tools"></a>新增移轉工具

本文說明如何在[Azure Migrate](migrate-overview.md)中新增遷移工具。

- 如果您想要新增遷移工具，但尚未設定 Azure Migrate 專案，請遵循這[篇文章](how-to-add-tool-first-time.md)。
- 如果您已新增 ISV 工具來進行遷移，請[遵循下列步驟](prepare-isv-movere.md)來準備使用此工具。

## <a name="select-a-migration-scenario"></a>選取遷移案例

1. 在 Azure Migrate 專案中，按一下 [概觀]****。
2. 選取您想要使用的遷移案例：

    - 若要將機器和工作負載遷移至 Azure，請選取 [**評估和遷移伺服器**]。
    - 若要遷移內部部署 SQL Server 資料庫，請選取 [**評估和遷移資料庫**]。
    - 若要遷移內部部署 web 應用程式，請選取 [**評估和遷移 web 應用程式**]。
    - 若要在離線模式中將大量內部部署資料移轉至 Azure，請選取 [**訂購資料箱**]。

    ![評定案例](./media/how-to-migrate/assess-scenario.png)

## <a name="select-a-server-migration-tool"></a>選取伺服器遷移工具

1. 按一下 [**評估和遷移伺服器**]。
2. 在 [ **Azure Migrate-伺服器**] 中，如果您尚未新增遷移工具，請在 [**遷移工具**] 底下，選取 [**按一下這裡新增遷移工具**]。 如果您已新增遷移工具，請在 [**新增更多遷移工具**] 中選取 [**變更**]。

    > [!NOTE]
    > 如果您需要流覽至不同的專案，請在 [ **Azure Migrate 伺服器**] 中，按一下 [**查看不同遷移專案的詳細資料**] 旁的 [**按一下這裡**]。

3. 在 [ **Azure Migrate**中，選取您想要使用的遷移工具。
    - 如果您使用 Azure Migrate Server 遷移，則可以直接在 Azure Migrate 專案中設定和執行遷移。
    - 如果您使用協力廠商評估工具，請流覽至針對 ISV 提供的連結，並根據所提供的指示執行遷移。

## <a name="select-a-database-migration-tool"></a>選取資料庫移轉工具

1. 按一下 [**評估並遷移資料庫**]
2. 在 [**資料庫**] 中，按一下 [**加入工具**]。
3. 在 [新增工具] 中 >**選取 [遷移工具**]，然後選取您要用來遷移資料庫的工具。

## <a name="select-a-web-app-migration-tool"></a>選取 web 應用程式遷移工具

1. 按一下 [**評估和遷移 web 應用程式**]。
2. 遵循 Azure App Service 的遷移工具連結。 使用遷移工具來執行下列動作：

    - **線上評估應用程式**：您可以使用 Azure App Service 移轉小幫手，線上評估和遷移具有公用 URL 的應用程式。
    - **.Net/PHP**：若是內部 .NET 和 php 應用程式，您可以下載並執行移轉小幫手。

## <a name="order-an-azure-data-box"></a>訂購 Azure 資料箱

若要將大量資料移轉至 Azure，您可以訂購 Azure 資料箱進行離線資料傳輸。

1. 按一下 [**訂購資料箱**]。
2. 在 [**選取您的 Azure 資料箱**中，指定您的訂用帳戶。 
3. 傳輸將會匯入至 Azure。 指定資料來源，以及資料的 Azure 區域目的地。

## <a name="next-steps"></a>後續步驟

嘗試使用適用于[hyper-v](tutorial-migrate-hyper-v.md)或[VMware](tutorial-migrate-vmware.md) Vm 的 Azure Migrate Server 遷移來進行遷移。
