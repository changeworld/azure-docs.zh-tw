---
title: SQL Server 版本的就地變更
description: 瞭解如何在 Azure 中變更您 SQL Server 的虛擬機器版本。
services: virtual-machines-windows
documentationcenter: na
author: ramakoni1
manager: ramakoni1
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/08/2020
ms.author: RamaKoni
ms.reviewer: sqlblt, daleche
ms.custom: seo-lt-2019
ms.openlocfilehash: a57a432a5f0f8e5a6bd802ec08b18350da3a77b3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91293368"
---
# <a name="in-place-change-of-sql-server-version-on-azure-vm"></a>在 Azure VM 上就地變更 SQL Server 版本

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文說明如何在 Microsoft Azure 中 (VM) 變更 Windows 虛擬機器上的 Microsoft SQL Server 版本。

## <a name="prerequisites"></a>必要條件

若要執行 SQL Server 的就地升級，請遵循下列條件：

- 需要 SQL Server 所需版本的安裝媒體。 具備[軟體保證](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)的客戶可從[大量授權中心](https://www.microsoft.com/Licensing/servicecenter/default.aspx)取得安裝媒體。 沒有軟體保證的客戶可以從具有較新版本 SQL Server (（通常位於 C:\SQLServerFull) ）的 Azure Marketplace SQL Server VM 映射使用安裝媒體。
- 版本升級應遵循 [支援的升級路徑](https://docs.microsoft.com/sql/database-engine/install-windows/supported-version-and-edition-upgrades-version-15?view=sql-server-ver15)。

## <a name="planning-for-version-change"></a>規劃版本變更

建議您先檢查下列專案，再進行版本變更：

1. 檢查您打算升級到的版本中有哪些新功能：

   - [SQL 2019](https://docs.microsoft.com/sql/sql-server/what-s-new-in-sql-server-ver15?view=sql-server-ver15)的新功能
   - [SQL 2017](https://docs.microsoft.com/sql/sql-server/what-s-new-in-sql-server-2017?view=sql-server-ver15)的新功能
   - [SQL 2016](https://docs.microsoft.com/sql/sql-server/what-s-new-in-sql-server-2016?view=sql-server-ver15)的新功能
   - [SQL 2014](https://docs.microsoft.com/sql/sql-server/what-s-new-in-sql-server-2016?view=sql-server-2014)的新功能

1. 建議您檢查即將變更之版本的 [相容性](https://docs.microsoft.com/sql/database-engine/install-windows/compatibility-certification?view=sql-server-ver15) 憑證，以便您可以使用資料庫相容性模式，將升級的影響降至最低。
1. 您可以參閱下列文章，以協助確保成功的結果：

   - [影片：現代化 SQL Server |Pam Lahoud & Pedro Lopes |20年的 PASS](https://www.youtube.com/watch?v=5RPkuQHcxxs&feature=youtu.be)
   - [AB 測試的資料庫測試助理](https://docs.microsoft.com/sql/dea/database-experimentation-assistant-overview?view=sql-server-ver15)
   - [使用查詢調整小幫手來升級資料庫](https://docs.microsoft.com/sql/relational-databases/performance/upgrade-dbcompat-using-qta?view=sql-server-ver15)
   - [變更資料庫相容性層級並使用查詢存放區](https://docs.microsoft.com/sql/database-engine/install-windows/change-the-database-compatibility-mode-and-use-the-query-store?view=sql-server-ver15)

## <a name="upgrade-sql-version"></a>升級 SQL 版本

> [!WARNING]
> 升級 SQL Server 的版本，除了任何相關聯的服務（例如 Analysis Services 和 R Services）之外，也會重新開機服務以 SQL Server。

若要升級 SQL Server 版本，請取得較新版本的 SQL Server 安裝媒體，以 [支援 SQL Server 的升級路徑](https://docs.microsoft.com/sql/database-engine/install-windows/supported-version-and-edition-upgrades-version-15?view=sql-server-ver15) ，然後執行下列步驟：

1. 在開始進行程式之前，請先備份資料庫，包括 tempdb) 和使用者資料庫以外的系統 (。 您也可以使用 Azure 備份服務來建立應用程式一致的 VM 層級備份。
1. 從 SQL Server 安裝媒體開始 Setup.exe。
1. 安裝精靈會啟動 SQL Server 的安裝中心。 若要升級現有的 SQL Server 實例，請選取流覽窗格上的 [ **安裝** ]，然後選取 [ **從舊版的 SQL Server 升級**]。

   :::image type="content" source="./media/change-sql-server-version/upgrade.png" alt-text="升級 SQL Server 版本的選取專案":::

1. 在 [ **產品金鑰** ] 頁面上，選取一個選項來指出您要升級為免費的 SQL Server 版本，還是您擁有產品之實際執行版本的 PID 金鑰。 如需詳細資訊，請參閱 [SQL Server 2019 (6.x) 的版本和支援的功能 ](https://docs.microsoft.com/sql/sql-server/editions-and-components-of-sql-server-version-15?view=sql-server-ver15) ，以及 [ (SQL Server 2016) 支援的版本與版本升級 ](https://docs.microsoft.com/sql/database-engine/install-windows/supported-version-and-edition-upgrades?view=sql-server-ver15)。
1. 選取 **[下一步]** ，直到到達 [ **準備升級** ] 頁面，然後選取 [ **升級**]。 當變更生效時，安裝程式視窗可能會停止回應數分鐘。 **完整**的頁面會確認您的升級已完成。 如需升級的逐步程式，請參閱 [完整的](https://docs.microsoft.com/sql/database-engine/install-windows/upgrade-sql-server-using-the-installation-wizard-setup?view=sql-server-ver15#procedure)程式。

   :::image type="content" source="./media/change-sql-server-version/complete-page.png" alt-text="升級 SQL Server 版本的選取專案":::

如果您除了變更版本之外變更了 SQL Server 版，也請更新版本，並參閱 [ **在入口網站中驗證版本與版本** ] 區段，以變更 SQL VM 實例。

   :::image type="content" source="./media/change-sql-server-version/change-portal.png" alt-text="升級 SQL Server 版本的選取專案":::

## <a name="downgrade-the-version-of-sql-server"></a>降級 SQL Server 版本

若要降級 SQL Server 的版本，您必須完全卸載 SQL Server，然後使用所需的版本重新安裝它。 這類似于全新安裝的 SQL Server，因為您無法將較新版本的舊版資料庫還原至新安裝的舊版本。 需要從頭重新建立資料庫。 如果您也在升級期間變更 SQL Server 的版本，請在 Azure 入口網站中將 SQL Server VM 的 [ **版本** ] 屬性變更為新的版本值。 這會更新與此 VM 相關聯的中繼資料和帳單。

> [!WARNING]
> 不支援 SQL Server 的就地降級。

您可以依照下列步驟來將 SQL Server 的版本降級：

1. 請確定您未使用 [較新版本中提供](https://social.technet.microsoft.com/wiki/contents/articles/24222.find-enterprise-only-features-in-your-database.aspx)的任何功能。
1. 備份所有資料庫，包括 tempdb) 和使用者資料庫以外的系統 (。
1. 將所有必要的伺服器層級物件匯出 (例如伺服器觸發程式、角色、登入、連結的伺服器、作業、認證和憑證) 。
1. 如果您沒有腳本可在舊版中重新建立使用者資料庫，則必須使用 BCP.exe、SSIS 或 DACPAC 編寫所有物件的腳本，並匯出所有資料。

   當您將這類專案的腳本做為目標版本、相依物件和 advanced 選項時，請務必選取正確的選項。

   :::image type="content" source="./media/change-sql-server-version/scripting-options.png" alt-text="升級 SQL Server 版本的選取專案":::

1. 完全解除安裝 SQL Server 和所有相關聯的服務。
1. 重新啟動 VM。
1. 使用所需的程式版本媒體來安裝 SQL Server。
1. 安裝最新的 Service Pack 和累積更新。
1. 匯入在步驟 3) 中匯出的所有必要伺服器層級物件 (。
1. 使用已建立的腳本或步驟 4) 的檔案，從頭開始 (重新建立所有必要的使用者資料庫。

## <a name="verify-the-version-and-edition-in-the-portal"></a>在入口網站中確認版本和版本

在您變更 SQL Server 的版本之後，請再次向 [SQL VM 資源提供者](sql-vm-resource-provider-register.md) 註冊 SQL Server VM，以便您可以使用 Azure 入口網站來查看 SQL Server 的版本。 列出的版本號碼現在應該會反映 SQL Server 安裝的新升級版本與版本。

:::image type="content" source="./media/change-sql-server-version/verify-portal.png" alt-text="升級 SQL Server 版本的選取專案":::

> [!NOTE]
> 如果您已向 SQL VM 資源提供者註冊，請 [從 RP 取消](sql-vm-resource-provider-register.md#unregister-from-rp) 註冊，然後再註冊一次 [SQL VM 資源](sql-vm-resource-provider-register.md#register-with-rp) ，讓它偵測到 VM 上已安裝 SQL Server 的正確版本與版本。 這會更新與此 VM 相關聯的中繼資料和帳單資訊。

## <a name="remarks"></a>備註

- 建議您在升級完成之後，起始備份/更新統計資料/重建索引/檢查一致性。 您也可以檢查個別的資料庫相容性層級，以確保它們會反映您所需的層級。
- 在 VM 上更新 SQL Server 之後，請確定 Azure 入口網站中 SQL Server 的 [ **版本** ] 屬性符合已安裝的版本號碼以進行計費。
- [變更版本](change-sql-server-edition.md#change-edition-in-portal)的功能是 SQL VM 資源提供者的功能。 透過 Azure 入口網站部署 Azure Marketplace 映像，會自動向資源提供者註冊 SQL Server VM。 不過，自行安裝 SQL Server 的客戶必須手動 [註冊其 SQL SERVER VM](sql-vm-resource-provider-register.md)。
- 如果您卸載 SQL Server VM 資源，則會還原映射的硬式編碼版本設定。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章：

- [Windows VM 上的 SQL Server 概觀](sql-server-on-azure-vm-iaas-what-is-overview.md)
- [Windows VM 上的 SQL Server 常見問題集](frequently-asked-questions-faq.md)
- [Windows VM 上的 SQL Server 定價指引](pricing-guidance.md)
- [Windows VM 上的 SQL Server 版本資訊](doc-changes-updates-release-notes.md)
