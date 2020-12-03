---
title: 掃描防火牆後方的 Azure SQL DB
description: 瞭解如何使用 Azure 範疇入口網站來掃描防火牆後方的資源。
author: sudhandkumar
ms.author: kumarsud
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/16/2020
ms.openlocfilehash: 4e1e76aaae2951e6d65fd1ebaaa5798ff417daf9
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551989"
---
# <a name="scan-azure-sql-db-behind-a-firewall-in-azure-purview"></a>在 Azure 範疇中掃描防火牆後方的 Azure SQL DB

在本文中，您會瞭解如何使用 Azure 範疇來掃描防火牆後方的資源。

## <a name="prerequisites"></a>Prerequisites

* 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 您自己的 [Azure Active Directory 租](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)使用者。

## <a name="sign-in-to-azure"></a>登入 Azure

使用您的 Azure 帳戶登入 [Azure 入口網站](https://portal.azure.com) 。

## <a name="set-up-sql-storage-behind-a-firewall"></a>設定防火牆後方的 SQL 儲存體

第一個步驟是透過 [註冊並掃描 Azure SQL Database](register-scan-azure-sql-database.md)，將目錄的 MSI 新增至 AZURE SQL DB。

## <a name="scan-sql-db-from-purview"></a>從範疇掃描 SQL DB

1. 流覽至 [範疇] 首頁。

1. 選取左側導覽列上的 [ **管理中心** ]。

1. 選取 [來源] 底下的 [ **資料來源** ] **和 [掃描**]。

1. 選取 [ **+ 新增** ] 以新增資料來源。

1. 選取 **Azure SQL Database**。

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-source.png" alt-text="選取 [sql server] 的螢幕擷取畫面。":::

1. 提供新資料來源的名稱，選取 [ **從 Azure 訂用** 帳戶]，然後從下拉式清單中選取您的訂用帳戶和伺服器名稱。

   選取 **[完成]** 以完成註冊。 

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-register.png" alt-text="完成選取專案的螢幕擷取畫面":::

1. 針對位於防火牆後方並測試連線的儲存體選取 [ **設定掃描** ]。 連接表示成功。 

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-setscan.png" alt-text="選取專案 t0 設定掃描的螢幕擷取畫面。":::

1. 設定掃描頻率，然後選取 [ **繼續**]。

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-once.png" alt-text="啟動 sql 掃描之選取範圍的螢幕擷取畫面。":::

1.  掃描完成後，資料來源清單中的狀態就會更新。

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-success.png" alt-text="訊息掃描已完成的螢幕擷取畫面":::
   
## <a name="next-steps"></a>後續步驟

接下來，您可以設定掃描規則集建立 [掃描規則集](create-a-scan-rule-set.md) ，以將掃描群組在一起。
