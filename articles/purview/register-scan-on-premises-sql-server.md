---
title: 註冊和掃描內部部署 SQL server
description: 本教學課程說明如何使用自我裝載 IR 來掃描內部內部部署 SQL server。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/18/2020
ms.openlocfilehash: 9003366ec0d64057ca7426d5b6b99986bc21fc9d
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96920292"
---
# <a name="register-and-scan-an-on-premises-sql-server"></a>註冊和掃描內部部署 SQL server

本文概述如何在範疇中註冊 SQL server 資料來源，並在其上設定掃描。

## <a name="supported-capabilities"></a>支援的功能

SQL server 內部部署資料來源支援下列功能：

- **完整和增量掃描** ，可在內部部署網路或安裝在 Azure VM 上的 SQL server 中，捕捉中繼資料和分類。

- ADF 複製/資料流程活動的資料 **資產之間的** 歷程

SQL server 內部部署資料來源支援：

- 從 SQL server 2019 到 SQL server 2000 的每個 SQL 版本

- 驗證方法： SQL 驗證

### <a name="known-limitations"></a>已知限制

Azure 範疇不支援在 SQL Server 中掃描 [視圖](https://docs.microsoft.com/sql/relational-databases/views/views?view=sql-server-ver15) 。 

## <a name="prerequisites"></a>必要條件

- 註冊資料來源之前，請先建立 Azure 範疇帳戶。 如需有關建立範疇帳戶的詳細資訊，請參閱 [快速入門：建立 Azure 範疇帳戶](create-catalog-portal.md)。

- 設定自我裝載 [整合運行](manage-integration-runtimes.md) 時間來掃描資料來源。

## <a name="setting-up-authentication-for-a-scan"></a>設定掃描的驗證

有一種方式可以設定 SQL server 內部部署的驗證：

- SQL 驗證

### <a name="sql-authentication"></a>SQL 驗證

SQL 身分識別必須具有主資料庫的存取權。 這個位置是 `sys.databases` 儲存位置。 範疇掃描器必須列舉，才能 `sys.databases` 找出伺服器中的所有 SQL DB 實例。

#### <a name="using-an-existing-server-administrator"></a>使用現有的伺服器管理員

如果您打算使用現有的伺服器管理員 (sa) 使用者來掃描您的內部部署 SQL server，請確定下列各項：

1. `sa` 不是 Windows 驗證類型。

2. 您打算使用的伺服器層級使用者必須擁有 public 和 sysadmin 的伺服器角色。 您可以藉由流覽至 SQL Server Management Studio (SSMS) 、連接到伺服器、流覽至 [安全性]、選取您打算使用的登入、以滑鼠右鍵按一下 [ **屬性** ]，然後選取 [ **伺服器角色**]，來確認這一點。

   :::image type="content" source="media/register-scan-on-premises-sql-server/server-level-login.png" alt-text="伺服器層級登入。":::

3. 資料庫會對應至每個資料庫至少有 db_datareader 層級存取權的使用者。

   :::image type="content" source="media/register-scan-on-premises-sql-server/user-mapping-sa.png" alt-text="sa 的使用者對應。":::

#### <a name="creating-a-new-login-and-user"></a>建立新的登入和使用者

如果您想要建立新的登入和使用者，以掃描您的 SQL server，請遵循下列步驟：

1. 流覽至 SQL Server Management Studio (SSMS) ，連接到伺服器，流覽至 [安全性]，以滑鼠右鍵按一下 [登入]，然後建立新的登入。 請務必選取 [SQL 驗證]。

   :::image type="content" source="media/register-scan-on-premises-sql-server/create-new-login-user.png" alt-text="建立新的登入和使用者。":::

2. 選取左側導覽列上的 [伺服器角色]，然後選取 [公用] 和 [系統管理員]。

3. 選取左側導覽列上的 [使用者對應]，然後選取對應中的所有資料庫。

   :::image type="content" source="media/register-scan-on-premises-sql-server/user-mapping.png" alt-text="使用者對應。":::

4. 按一下 [確定] 來進行儲存。

5. 以滑鼠右鍵按一下並選取 [ **屬性**]，再次流覽至您所建立的使用者。 輸入新密碼並加以確認。 選取 [指定舊密碼]，然後輸入舊密碼。 **一旦您建立新的登入，就必須立即變更您的密碼。**

   :::image type="content" source="media/register-scan-on-premises-sql-server/change-password.png" alt-text="變更密碼。":::

#### <a name="storing-your-sql-login-password-in-a-key-vault-and-creating-a-credential-in-purview"></a>將您的 SQL 登入密碼儲存在金鑰保存庫中，並在範疇中建立認證

1. 在 Azure 入口網站中流覽至您的金鑰保存庫
1. 選取 **設定 > 秘密**
1. 選取 [ **+ 產生/匯入**]，並輸入 **名稱** 和 **值** 做為您的 SQL server 登入的 *密碼*
1. 選取 [ **建立** ] 以完成
1. 如果您的金鑰保存庫尚未連線到範疇，您將需要 [建立新的金鑰保存庫連接](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. 最後，使用使用者 **名稱** 和 **密碼** 來 [建立新的認證](manage-credentials.md#create-a-new-credential)，以設定您的掃描

## <a name="register-a-sql-server-data-source"></a>註冊 SQL server 資料來源

1. 流覽至您的範疇帳戶

1. 在左側導覽中的 [來源] 和 [掃描] 下，選取 [ **整合運行** 時間]。 請確定已設定自我裝載整合執行時間。 如果未設定，請依照 [此處](manage-integration-runtimes.md) 所述的步驟來建立自我裝載整合執行時間，以便在可存取內部部署網路的內部部署或 Azure VM 上進行掃描。

1. 選取左側導覽列上的 **來源**

1. 選取 [註冊]

1. 選取 **[SQL server** ]，然後 **繼續**

   :::image type="content" source="media/register-scan-on-premises-sql-server/set-up-sql-data-source.png" alt-text="設定 SQL 資料來源。":::

5. 提供易記的名稱和伺服器端點，然後選取 **[完成]** 以註冊資料來源。 例如，如果您的 SQL server FQDN 是 **foobar.database.windows.net**，請輸入 *foobar* 做為伺服器端點。

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>後續步驟

- [流覽 Azure 範疇資料目錄](how-to-browse-catalog.md)
- [搜尋 Azure 範疇資料目錄](how-to-search-catalog.md)
