---
title: 快速入門：使用 Azure 入口網站建立執行個體
titleSuffix: Azure Database Migration Service
description: 使用 Azure 入口網站來建立 Azure 資料庫移轉服務的執行個體。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: quickstart
ms.date: 01/08/2020
ms.openlocfilehash: 7d27d67cfa46eb4554f6ca3d66df76e393b18da0
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/29/2020
ms.locfileid: "84187532"
---
# <a name="quickstart-create-an-instance-of-the-azure-database-migration-service-by-using-the-azure-portal"></a>快速入門：使用 Azure 入口網站建立 Azure 資料庫移轉服務的執行個體

在本快速入門中，使用 Azure 入口網站來建立 Azure 資料庫移轉服務的執行個體。  建立執行個體之後，就可以用於將資料從 SQL Server 移轉到 Azure SQL Database。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

開啟網頁瀏覽器，瀏覽至 [Microsoft Azure 入口網站](https://portal.azure.com/)，然後輸入您的認證來登入入口網站。

預設檢視是您的服務儀表板。

## <a name="register-the-resource-provider"></a>註冊資源提供者

您必須在建立第一個資料庫移轉服務執行個體之前，註冊 Microsoft.DataMigration 資源提供者。

1. 在 Azure 入口網站中，選取 [所有服務]，然後選取 [訂用帳戶]。

2. 選取您要在其中建立 Azure 資料庫移轉服務執行個體的訂用帳戶，然後選取 [資源提供者]。

3. 搜尋移轉，然後在 [Microsoft.DataMigration] 的右邊，選取 [註冊]。

    ![註冊資源提供者](media/quickstart-create-data-migration-service-portal/dms-register-provider.png)

## <a name="create-an-instance-of-the-service"></a>建立服務執行個體

1. 選取 [+ 建立資源]，以建立 Azure 資料庫移轉服務的執行個體。

2. 搜尋適合「移轉」的市集，選取 [Azure 資料庫移轉服務]，並在 **Azure 資料庫移轉服務**畫面中，選取 [建立]。

3. 在 [建立移轉服務] 畫面上：

    - 選擇易記又獨特的**服務名稱**，以識別您的 Azure 資料庫移轉服務執行個體。
    - 選取您要建立執行個體的 Azure **訂用帳戶**。
    - 選取現有的 [資源群組] 或建立新群組。
    - 選擇最接近您的來源或目標伺服器的**位置**。
    - 選取現有的**虛擬網路**或建立一個。

        虛擬網路會為 Azure 資料庫移轉服務提供來源資料庫和目標環境的存取權。

        如需如何在 Azure 入口網站中建立虛擬網路的詳細資訊，請參閱[使用 Azure 入口網站建立虛擬網路](https://aka.ms/vnet)一文。

    - 選取 [基本:1 個 vCore] 作為 [定價層]。

        ![建立移轉服務](media/quickstart-create-data-migration-service-portal/dms-create-service1.png)

4. 選取 [建立]。

    數分鐘之後，您的 Azure 資料庫移轉服務執行個體就會建立且可供使用。 Azure 資料庫移轉服務隨即顯示，如下圖所示：

    ![已建立移轉服務](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>清除資源

您可以刪除 [Azure 資源群組](../azure-resource-manager/management/overview.md)，清除您在快速入門中建立的資源。 若要刪除資源群組，請瀏覽至您建立的 Azure 資料庫移轉服務執行個體。 選取**資源群組**名稱，然後選取 [刪除資源群組]。 這個動作會刪除資源群組中的所有資產，以及群組本身。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [將 SQL Server 移轉至 Azure SQL Database](tutorial-sql-server-to-azure-sql.md)
