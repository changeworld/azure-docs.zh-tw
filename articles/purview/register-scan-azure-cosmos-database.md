---
title: 如何)  (SQL API 掃描 Azure Cosmos Database
description: 本操作指南說明如何掃描 Azure Cosmos Database (SQL API) 的詳細資料。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/9/2020
ms.openlocfilehash: e1d2035b787380d9b93943b92fbe81c09fc6a527
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552224"
---
# <a name="register-and-scan-azure-cosmos-database-sql-api"></a>註冊並掃描 Azure Cosmos Database (SQL API) 

本文概述如何在 Azure 範疇中註冊 Azure Cosmos 資料庫 (SQL API) 帳戶並設定掃描。

## <a name="supported-capabilities"></a>支援的功能

Azure Cosmos Database (SQL API) 支援完整和增量掃描來捕捉中繼資料和架構。 掃描也會根據系統和自訂分類規則自動分類資料。

## <a name="prerequisites"></a>先決條件

- 註冊資料來源之前，請先建立 Azure 範疇帳戶。 如需有關建立範疇帳戶的詳細資訊，請參閱 [快速入門：建立 Azure 範疇帳戶](create-catalog-portal.md)。
- 您必須是 Azure 範疇資料來源管理員

## <a name="setting-up-authentication-for-a-scan"></a>設定掃描的驗證

有一種方式可以設定 Azure Cosmos Database 的驗證 (SQL API) ：

- 帳戶金鑰
 
### <a name="account-key"></a>帳戶金鑰

當選取驗證方法為 **帳戶金鑰** 時，您必須取得存取金鑰，並將其儲存在金鑰保存庫中：

1. 流覽至您在 Azure 入口網站中的 Cosmos DB 帳戶 
1. 選取 **設定** 機  >  **碼** 
1. 複製您的 *金鑰* ，並將其儲存在某處以進行後續步驟
1. 瀏覽至您的金鑰保存庫
1. 選取 **設定 > 秘密**
1. 選取 [ **+ 產生/匯入**]，並輸入 **名稱** 和 **值** 作為儲存體帳戶的 *金鑰*
1. 選取 [ **建立** ] 以完成
1. 如果您的金鑰保存庫尚未連線到範疇，您將需要 [建立新的金鑰保存庫連接](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. 最後，使用金鑰 [建立新的認證](manage-credentials.md#create-a-new-credential) 來設定您的掃描

## <a name="register-an-azure-cosmos-database-sql-api-account"></a> (SQL API) 帳戶註冊 Azure Cosmos 資料庫

若要在您的資料目錄中 (SQL API) 帳戶註冊新的 Azure Cosmos 資料庫，請執行下列動作：

1. 流覽至您的範疇帳戶
1. 選取左側導覽列上的 **來源**
1. 選取 [註冊]
1. 在 [ **註冊來源**] 上，選取 **[AZURE COSMOS DB (SQL API)**
1. 選取 [繼續]

:::image type="content" source="media/register-scan-azure-cosmos-database/register-new-data-source.png" alt-text="註冊新的資料來源" border="true":::

在 [ **註冊來源 (] Azure Cosmos DB (SQL API) # B3** ] 畫面上，執行下列動作：

1. 輸入將在目錄中列出資料來源的 **名稱** 。
1. 選擇您想要指向所需儲存體帳戶的方式：
   1. 從 **azure 訂用** 帳戶選取，從 [ **azure 訂** 用帳戶] 下拉式方塊中選取適當的訂用帳戶，並從 [ **Cosmos DB 帳戶名稱** ] 下拉式清單方塊中選取適當的 cosmosDB 帳戶。
   1. 或者，您也可以選取 [ **手動輸入** ]，然後輸入 (URL) 的服務端點。
1. **完成** 以註冊資料來源。

:::image type="content" source="media/register-scan-azure-cosmos-database/register-sources.png" alt-text="註冊來源選項" border="true":::


[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>後續步驟

- [流覽 Azure 範疇資料目錄](how-to-browse-catalog.md)
- [搜尋 Azure 範疇資料目錄](how-to-search-catalog.md)
