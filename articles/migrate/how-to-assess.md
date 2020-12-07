---
title: 在 Azure Migrate 中新增評定工具
description: 瞭解如何在 Azure Migrate 中新增評量工具。
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 9a4ed5542945b8d281ec750d9bbd3a8f444e44d2
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96752255"
---
# <a name="add-assessment-tools"></a>新增評量工具

本文說明如何在 [Azure Migrate](./migrate-services-overview.md)中新增評定工具。 

- 如果您想要新增評量工具，但還沒有 Azure Migrate 專案，請遵循這 [篇文章](create-manage-projects.md)。
- 如果您已新增 ISV 工具或 Movere，以進行評量，請 [遵循下列步驟](prepare-isv-movere.md)來準備使用此工具。

## <a name="select-an-assessment-scenario"></a>選取評量案例

1. 在 Azure Migrate 專案中，按一下 [概觀]。
2. 選取評定案例：

    - 若要探索及評估要遷移至 Azure 的機器和工作負載，請選取 [ **評估和遷移伺服器**]。
    - 若要評估內部部署 SQL Server 資料庫，請選取 [ **評估和遷移資料庫**]。
    - 若要評估或遷移內部部署 web 應用程式，請選取 [**探索更多**  >  **Web Apps**。
    - 若要評估您的虛擬桌面基礎結構，請選取 [**探索更多**  >  **虛擬桌面基礎結構**]。

    ![選取評量案例的選項](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>選取伺服器評量工具 


1. 新增工具：

    - 如果您使用入口網站中的 [ **評估和遷移伺服器** ] 選項來建立 Azure Migrate 專案，Azure Migrate Server 評定工具會自動新增至專案。 若要新增其他評估工具，請在 [ **伺服器**] 中的 [ **評估工具**] 旁邊，選取 [ **新增更多工具**]。
    
         ![按鈕以新增其他評估工具](./media/how-to-assess/add-assessment-tool.png)

    - 如果您使用不同的選項來建立專案，但還沒有任何評量工具，請選取 [**伺服器** 評量工具] 中的 [  >  ******按一下這裡**]。

        ![按鈕以新增第一個評估工具](./media/how-to-assess/no-assessment-tool.png)

2. 在 **Azure Migrate**  >  **新增工具**] 中，選取您要新增的工具。 然後選取 [ **新增工具**]。

    ![從清單中選取評量工具](./media/how-to-assess/select-assessment-tool.png)



## <a name="select-a-database-assessment-tool"></a>選取資料庫評估工具

1. 新增工具：

    - 如果您使用入口網站中的 [ **評估和遷移資料庫** ] 選項來建立 Azure Migrate 專案，則會自動將資料庫評估工具新增至專案。 若要在 **資料庫** 中新增其他評估工具，請在 [ **評量工具**] 旁邊選取 [ **新增更多工具**]。

    - 如果您使用不同的選項來建立專案，但還沒有任何資料庫評估工具，請選取 [**資料庫**  >  **評估工具**] 中的 [**按一下這裡**]。

2. 在 **Azure Migrate**  >  **新增工具**] 中，選取您要新增的工具。 然後選取 [ **新增工具**]。

    ![從清單中選取資料庫評估工具](./media/how-to-assess/select-database-assessment-tool.png)


## <a name="select-a-web-app-assessment-tool"></a>選取 web 應用程式評估工具

如果您使用入口網站中的 [**探索更多**  >  **WebApps** ] 選項建立 Azure Migrate 專案，則會自動將 Web 應用程式評估工具新增至專案。 


1. 如果 Web 應用程式評估工具不在專案中，請在 **Web Apps**  >  **評定工具**] 中，選取 [**按一下這裡**]。
    
    ![新增 web 應用程式評估工具](./media/how-to-assess/no-web-app-assessment-tool.png)


2. 在 **Azure Migrate**  >  **新增工具**] 中，選取 [Web 應用程式評定] 工具。 然後選取 [ **新增工具**]。

    ![從清單中選取資料庫移轉工具](./media/how-to-assess/select-web-app-assessment-tool.png)

 


## <a name="next-steps"></a>後續步驟

探索使用適用于[VMware](./tutorial-discover-vmware.md) Vm、 [hyper-v](./tutorial-discover-hyper-v.md)或[實體伺服器](./tutorial-discover-physical.md)Azure Migrate Server 評定工具進行評量的內部部署機器