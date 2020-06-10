---
title: 建立伺服器層級防火牆規則
description: 建立伺服器層級防火牆規則
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: vanto, carlrab
ms.date: 02/11/2019
ms.openlocfilehash: 8b340bdbd4bea4fcd865d6c27d688e5dbed86bb8
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "84052339"
---
# <a name="quickstart-create-a-server-level-firewall-rule-using-the-azure-portal"></a>快速入門：使用 Azure 入口網站建立伺服器層級的防火牆規則
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

本快速入門會逐步解說如何使用 Azure 入口網站，在 Azure SQL Database 中建立[伺服器層級防火牆規則](firewall-configure.md)，以便您能連線到[邏輯 SQL Server](logical-servers.md)、單一資料庫，以及彈性集區及其資料庫。 需要有防火牆規則，才能從其他 Azure 資源和從內部部署資源進行連線。 伺服器層級防火牆規則不適用於 Azure SQL 受控執行個體。

## <a name="prerequisites"></a>必要條件

本快速入門將以[使用 Azure 入口網站建立單一資料庫](single-database-create-quickstart.md)中建立的資源作為起始點。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="create-a-server-level-ip-firewall-rule"></a>建立伺服器層級 IP 防火牆規則

 SQL Database 會在伺服器層級建立單一和集區資料庫的防火牆。 除非您建立 IP 防火牆規則來開啟防火牆，否則此防火牆會防止用戶端應用程式連線到伺服器或其任何單一或集區資料庫。 若要從 Azure 外部的 IP 位址連線，請針對您想要能夠連線的特定 IP 位址或位址範圍建立防火牆規則。 如需伺服器層級和資料庫層級 IP 防火牆規則的詳細資訊，請參閱 [伺服器層級和資料庫層級 IP 防火牆規則](firewall-configure.md)。

> [!NOTE]
> Azure SQL Database 會透過連接埠 1433 通訊。 如果您嘗試從公司網路內進行連線，您網路的防火牆可能不允許透過連接埠 1433 的輸出流量。 若是如此，除非 IT 部門開啟連接埠 1433，否則您無法連線到您的伺服器。
> [!IMPORTANT]
> 0\.0.0.0 的防火牆規則可讓所有 Azure 服務通過伺服器層級防火牆規則，並嘗試透過伺服器連線到單一或集區資料庫。

請遵循下列步驟，為您用戶端的 IP 位址建立伺服器層級 IP 防火牆規則，並且讓外部連線僅能透過 Azure SQL Database 防火牆存取該 IP 位址。

1. 完成[資料庫](#prerequisites)部署之後，選取左側功能表中的 [SQL 資料庫]，然後選擇 [SQL 資料庫] 頁面上的 [mySampleDatabase]。 資料庫的概觀頁面隨即開啟，其中會顯示完整伺服器名稱 (例如 **mynewserver-20170824.database.windows.net**)，並提供進一步的組態選項。

2. 在其他快速入門中，請複製此完整伺服器名稱，以在連線到伺服器及其資料庫時使用。

   ![伺服器名稱](./media/firewall-create-server-level-portal-quickstart/server-name.png)

3. 在工具列上選取 [設定伺服器防火牆]。 伺服器的 [防火牆設定] 頁面隨即開啟。

   ![伺服器層級 IP 防火牆規則](./media/firewall-create-server-level-portal-quickstart/server-firewall-rule.png)

4. 選擇工具列上的 [新增用戶端 IP]，將您目前的 IP 位址新增至新的伺服器層級 IP 防火牆規則。 伺服器層級 IP 防火牆規則可以針對單一 IP 位址或 IP 位址範圍開啟連接埠 1433。

   > [!IMPORTANT]
   > 根據預設，已對所有 Azure 服務停用透過 Azure SQL Database 防火牆存取。 如果您想要啟用所有 Azure 服務的存取權，請在此頁面上選擇 [開啟]。
   >

5. 選取 [儲存]。 系統便會為目前的 IP 位址建立伺服器層級 IP 防火牆規則，以便在伺服器上開啟連接埠 1433。

6. 關閉 [防火牆設定] 頁面。

您現在可以利用 SQL Server Management Studio 或您所選的其他工具，使用先前建立的伺服器管理帳戶從這個 IP 位址連線至伺服器及其資料庫。

## <a name="clean-up-resources"></a>清除資源

如果您想移至[後續步驟](#next-steps)並了解如何使用各種不同方法來連線及查詢您的資料庫，請儲存這些資源。 不過，如果您要刪除在此快速入門中建立的資源，請使用下列步驟。

1. 從 Azure 入口網站的左側功能表中，依序選取 [資源群組] 和 [myResourceGroup]。
2. 在資源群組頁面上，選取 [刪除]，在文字方塊中輸入 **myResourceGroup**，然後選取 [刪除]。

## <a name="next-steps"></a>後續步驟

- 您現在具有資料庫，您可使用任何一個慣用工具或語言進行[連線和查詢](connect-query-content-reference-guide.md)，包括
  - [使用 SQL Server Management Studio 進行連線和查詢](connect-query-ssms.md)
  - [使用 Azure Data Studio 進行連線及查詢](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- 若要了解如何設計您的第一個資料庫、建立資料表及插入資料，請參閱下列其中一個教學課程：
  - [使用 SSMS 在 Azure SQL Database 中設計您的第一個單一資料庫](design-first-database-tutorial.md)
  - [使用 C# 和 ADO.NET 在 Azure SQL Database 中設計單一資料庫並連線](design-first-database-csharp-tutorial.md)
