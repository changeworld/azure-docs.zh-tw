---
title: 啟用 Synapse 工作區功能
description: 本檔會說明使用者如何在現有的專用 SQL 集區上啟用 Synapse 工作區功能， (先前的 SQL DW) 。
services: synapse-analytics
author: antvgski
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/25/2020
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 769ca4cf0ecbdba5fd80d977eb69c8a4f58df55f
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98071182"
---
# <a name="enabling-synapse-workspace-features-for-a-dedicated-sql-pool-formerly-sql-dw"></a>針對 (先前為 SQL DW) 的專用 SQL 集區啟用 Synapse 工作區功能

所有 SQL 資料倉儲使用者現在都可以透過 Synapse Studio 和工作區，存取和使用現有的專用 SQL 集區 (先前的 SQL DW) 實例。 使用者可以使用 Synapse Studio 和工作區，而不會影響自動化、連接或工具。 本文說明現有的 Azure Synapse Analytics 使用者如何為現有的專用 SQL 集區 (先前的 SQL DW) 啟用 Synapse 工作區功能。 使用者可以利用現在可透過 Synapse 工作區和 Studio 取得的新功能豐富功能，來擴充其現有的分析解決方案。   

## <a name="prerequisites"></a>必要條件
啟用資料倉儲上的 Synapse 工作區功能之前，您必須確定您具有下列各項：
- 建立及管理 sql 邏輯伺服器上所裝載之 SQL 資源的許可權。
- 建立 Azure Synapse 資源的許可權。
- 邏輯伺服器上識別的 Azure Active Directory 系統管理員

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="enabling-synapse-workspace-features-for-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>針對現有的專用 SQL 集區啟用 Synapse 工作區功能 (先前為 SQL DW) 

您可以在任何現有的專用 SQL 集區上啟用 Synapse 工作區功能， (先前的 SQL DW) 在支援的區域中。 這項功能僅可透過 Azure 入口網站取得。

請遵循下列步驟，為您現有的資料倉儲建立 Synapse 工作區。
1. 選取現有的專用 SQL 集區 (先前的 SQL DW) ，然後開啟 [總覽] 頁面。
2. 在頂端功能表列中選取 [ **新增 Synapse 工作區** ]，或在下方選取訊息。
3. 在透過 [ **建立新的 Azure Synapse 工作區** ] 頁面上的 [新的 Synapse 工作區] 查看可供使用的資料倉儲清單之後。 選取 [ **繼續** ] 繼續進行。
4. 在 [基本] 頁面上， (**專案** 詳細資料] 區段的現有專用 SQL 集區應預先填入邏輯伺服器下部署的相同 **訂** 用帳戶和 **資源群組** 。 在 **工作區詳細資料** 下， **工作區** 名稱會預先填入 SQL 邏輯伺服器的相同名稱和區域，以確保您可以在布建程式期間建立新 Synapse 工作區和邏輯伺服器之間的連接。 布建此連線必須保留，以確保可透過工作區和 Studio (先前的 SQL DW) 實例，繼續存取專用的 SQL 集區。
5. 瀏覽至 [選取 Data Lake Storage Gen 2]。
6. 選取 [ **建立新** 的] 或選取現有的 **Data Lake Storage Gen2** ，然後選取 **[下一步：網路]**。
7. 選擇 **無伺服器實例** 的 **SQL 系統管理員密碼**。 變更此密碼並不會更新或變更邏輯伺服器的 SQL 認證。 如果您偏好系統定義的密碼，請將這些欄位保留空白。 您可以隨時在新的工作區中變更此密碼。 系統管理員名稱必須與 SQL Server 上使用的名稱相同。
8. 選取您慣用的 **網路設定** ，然後選取 [ **審核 + 建立** ] 以開始布建工作區。
9. 選取 [移至 **資源** ] 以開啟新的工作區。

    > [!NOTE]
    > 所有專用的 SQL 集區 (先前裝載于邏輯伺服器上的 SQL DW) 實例可透過新的工作區使用。

## <a name="post-provisioning-steps"></a>布建後步驟
必須完成下列步驟，以確保您現有的專用 SQL 集區 (先前的 SQL DW) 實例可透過 Synapse Studio 進行存取。
1. 在 [Synapse 工作區總覽] 頁面中，選取 [ **已連結的服務器**]。 **連線的伺服器** 會帶您前往裝載資料倉儲的連線 SQL 邏輯伺服器。 在 [基本] 功能表中，選取 [ **已連線的伺服器**]。
2. 開啟 **防火牆和虛擬網路** ，並確定您的用戶端 ip 或預先決定的 ip 範圍具有邏輯伺服器的存取權。
3. 開啟 **Active Directory 系統管理員** ，並確定已在邏輯伺服器上設定 AAD 系統管理員。
4. 選取其中一個專用的 SQL 集區 (先前裝載于邏輯伺服器上的 SQL DW) 實例。 在 [總覽] 頁面中，選取 [ **啟動 Synapse studio** ]，或移至 [Synapse studio](https://web.azuresynapse.net) 並登入您的工作區。

5. 開啟 **資料中樞** ，並在 [物件瀏覽器] 中展開專用的 SQL 集區，以確定您已存取並可查詢您的資料倉儲。

    > [!NOTE] 
    > 您可以隨時刪除已連線的工作區。 刪除工作區並不會刪除已連線的專用 SQL 集區 (先前的 SQL DW) 。 當刪除作業完成時，可以在專用的 SQL 集區上重新啟用工作區功能 (先前的 SQL DW) 。

## <a name="next-steps"></a>後續步驟
開始使用 [Synapse Workspace 和 Studio](../get-started.md)。
