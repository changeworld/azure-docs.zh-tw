---
title: 同步處理 Azure App Service 的網路設定
titleSuffix: Azure SQL Managed Instance
description: 本文討論如何將 Azure App Service 主控方案的網路設定與您的 Azure SQL 受控執行個體同步。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 12/13/2018
ms.openlocfilehash: a0192f204fdb0797d98947a174bf6d669034b666
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84695448"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan-with-azure-sql-managed-instance"></a>使用 Azure SQL 受控執行個體同步 Azure App Service 主控方案的網路設定
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

雖然您[已將應用程式與 Azure 虛擬網路整合](../../app-service/web-sites-integrate-with-vnet.md)，但無法建立與 SQL 受控執行個體的連接。 重新整理或同步處理服務方案的網路設定可以解決此問題。 

## <a name="sync-network-configuration"></a>同步網路組態 

若要這樣做，請遵循下列步驟：  

1. 移至您的 Web 應用程式 App Service 方案。

   ![App Service 方案的螢幕擷取畫面](./media/azure-app-sync-network-configuration/app-service-plan.png)

2. 選取 [**網路**]，然後選取 [**按一下這裡以管理**]。

   ![管理服務方案的螢幕擷取畫面](./media/azure-app-sync-network-configuration/manage-plan.png)

3. 選取您的 VNet****，按一下 [同步處理網路]****。

   ![同步網路的螢幕擷取畫面](./media/azure-app-sync-network-configuration/sync.png)

4. 等待同步處理完成。
  
   ![同步處理完成的螢幕擷取畫面](./media/azure-app-sync-network-configuration/sync-done.png)

您現在已準備好嘗試重新建立與 SQL 受控執行個體的連線。

## <a name="next-steps"></a>後續步驟

- 如需有關為 SQL 受控執行個體設定 VNet 的詳細資訊，請參閱[sql 受控執行個體 VNet 架構](connectivity-architecture-overview.md)和[如何設定現有的 vnet](vnet-existing-add-subnet.md)。
