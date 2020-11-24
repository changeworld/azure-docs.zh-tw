---
title: 在 Azure Migrate 中新增遷移工具
description: 瞭解如何在 Azure Migrate 中新增遷移工具。
ms.topic: article
ms.date: 11/23/2020
ms.openlocfilehash: 5ff5fb54e077896fb6169ad53ce29483cd2c2f89
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95545289"
---
# <a name="add-migration-tools"></a>新增移轉工具

本文說明如何在 [Azure Migrate](./migrate-services-overview.md)中新增遷移工具。

- 如果您想要新增遷移工具，但尚未設定 Azure Migrate 專案，請遵循這 [篇文章](create-manage-projects.md)。
- 如果您已新增用於遷移的 ISV 工具，請 [遵循下列步驟](prepare-isv-movere.md)來準備使用此工具。

## <a name="select-a-migration-scenario"></a>選取遷移案例

1. 在 Azure Migrate 專案中，按一下 [概觀]。
2. 選取您要使用的遷移案例：

    - 若要將機器和工作負載遷移至 Azure，請選取 [ **評估和遷移伺服器**]。
    - 若要遷移內部部署資料庫，請選取 [ **評估和遷移資料庫**]。
    - 若要遷移內部部署 web 應用程式，請選取 [**探索更多**  >  **Web Apps**。
    - 若要使用資料箱將資料移轉至 Azure，請選取 [**探索更多**  >  **資料箱**]。

    ![選取遷移案例的選項](./media/how-to-migrate/migrate-scenario.png)


## <a name="select-a-server-migration-tool"></a>選取伺服器遷移工具

1. 新增工具：

    - 如果您使用入口網站中的 [ **評估和遷移伺服器** ] 選項來建立 Azure Migrate 專案，則會自動將 Azure Migrate 伺服器遷移工具新增至專案。 若要新增其他遷移工具，請在 [ **伺服器**] 中，選取 [ **遷移工具**] 旁的 [ **新增其他工具**]。
    
         ![按鈕以新增其他遷移工具](./media/how-to-migrate/add-migration-tools.png)

    - 如果您使用不同的選項來建立專案，但還沒有任何遷移工具，請選取 [**伺服器**  >  **遷移工具**] 中的 [**按一下這裡**]。

    ![按鈕以新增第一個遷移工具](./media/how-to-migrate/no-migration-tool.png)

2. 在 **Azure Migrate**  >  **新增工具**] 中，選取您要新增的工具。 然後選取 [ **新增工具**]。

    ![從清單中選取評量工具](./media/how-to-migrate/select-migration-tool.png)


## <a name="select-a-database-migration-tool"></a>選取資料庫移轉工具

如果您使用入口網站中的 [ **評估和遷移資料庫** ] 選項來建立 Azure Migrate 專案，資料庫移轉工具會自動加入至專案。 

1. 如果資料庫移轉工具不在專案中，請選取 [**資料庫**  >  **評估工具**] 中的 [**按一下這裡**]。
    
    ![新增資料庫移轉工具](./media/how-to-migrate/no-database-migration-tool.png)


2. 在 **Azure Migrate**  >  **新增工具**] 中，選取資料庫移轉工具。 然後選取 [ **新增工具**]。

    ![從清單中選取資料庫移轉工具](./media/how-to-migrate/select-database-migration-tool.png)

    

## <a name="select-a-web-app-migration-tool"></a>選取 web 應用程式遷移工具

如果您使用入口網站中的 [**探索更多**  >  **WebApps** ] 選項建立 Azure Migrate 專案，則會自動將 Web 應用程式遷移工具新增至專案。 

1. 如果 web 應用程式遷移工具不在專案中，請選取 [ **web 應用程式**  >  **評估工具**] 中的 [**按一下這裡**]。

    ![新增 web 應用程式遷移工具](./media/how-to-migrate/no-web-app-migration-tool.png)
 

2. 在 **Azure Migrate**  >  **新增工具**] 中，選取 Web 應用程式遷移工具。 然後選取 [ **新增工具**]。

    ![從清單中選取 webapp 評定工具](./media/how-to-migrate/select-web-app-migration-tool.png)


## <a name="order-an-azure-data-box"></a>訂購 Azure 資料箱

若要將大量資料移轉至 Azure，您可以訂購 Azure 資料箱進行離線資料傳輸。

1. 若要深入瞭解，請選取 **[****探索更多**]。
2. 若要深入 **瞭解**，請選取 [ **資料箱**]。
3. 在 [ **開始使用資料**] 方塊中，選取訂購資料箱時要使用的訂用帳戶和資源群組。
4. **傳輸類型是匯** 入至 Azure。 指定資料所在的國家/地區，以及您想要將資料傳輸至其中的 Azure 區域。 
5. 按一下 **[** 套用] 以儲存設定。

## <a name="next-steps"></a>後續步驟

使用適用于 [hyper-v](tutorial-migrate-hyper-v.md) 或 [VMware](tutorial-migrate-vmware.md) Vm 的 Azure Migrate Server 遷移工具來嘗試遷移。
