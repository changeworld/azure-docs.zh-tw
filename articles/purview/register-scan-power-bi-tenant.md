---
title: '註冊並掃描 Power BI 租使用者 (preview) '
description: 瞭解如何使用 Azure 範疇入口網站來註冊和掃描 Power BI 租使用者。
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/19/2020
ms.openlocfilehash: 3d8107e980b9cad9bc55cb51cc78b63985986ba5
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/19/2020
ms.locfileid: "97696239"
---
# <a name="register-and-scan-a-power-bi-tenant-preview"></a>註冊並掃描 Power BI 租使用者 (preview) 

本文說明如何使用 Azure 範疇入口網站來註冊和掃描 Power BI 租使用者。

> [!Note]
> 如果範疇實例和 Power BI 租使用者位於相同的 Azure 租使用者中，您只能使用受控識別 (MSI) 驗證來設定 Power BI 租使用者的掃描。 

## <a name="create-a-security-group-for-permissions"></a>建立許可權的安全性群組

若要設定驗證，請建立安全性群組，並將目錄的受控識別新增至該群組。

1. 在 [Azure 入口網站](https://portal.azure.com)中，搜尋 **Azure Active Directory**。
1. 在您的 Azure Active Directory 中建立新的安全性群組，方法如下： [建立基本的群組，並使用 Azure Active Directory 來新增成員](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)。

    > [!Tip]
    > 如果您已經有想要使用的安全性群組，可以略過此步驟。

1. 選取 [ **安全性** ] 作為 **群組類型**。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="安全性群組類型":::

1. 將目錄的受控識別新增至此安全性群組。 選取 [ **成員**]，然後選取 [ **+ 新增成員**]。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="將目錄的受控實例新增至群組。":::

1. 搜尋您的目錄，然後選取它。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="藉由搜尋來新增類別目錄":::

    您應該會看到成功通知，告訴您已新增它。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="新增類別目錄 MSI 成功":::

## <a name="associate-the-security-group-with-the-tenant"></a>將安全性群組與租使用者建立關聯

1. 登入 [Power BI 系統管理員入口網站](https://app.powerbi.com/admin-portal/tenantSettings)。
1. 選取 [ **租使用者設定** ] 頁面。

    > [!Important]
    > 您必須是 Power BI 系統管理員，才能看到 [租使用者設定] 頁面。

1. 選取 [**開發人員設定**]  >  **可讓服務主體使用唯讀 Power BI 管理員 api (Preview)**。
1. 選取 **特定的安全性群組**。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="顯示如何允許服務主體取得唯讀 Power BI 管理員 API 許可權的影像":::

    > [!Caution]
    > 當您允許您建立的安全性群組 (以您的資料目錄受控識別作為成員) 以使用唯讀 Power BI 系統管理員 Api 時，您也會允許它存取此租使用者中所有 ) 成品的中繼資料 (例如儀表板和報表名稱、擁有者、描述等 Power BI。 一旦將中繼資料提取到 Azure 範疇中，範疇的許可權，不 Power BI 許可權，就可以決定誰可以看到該中繼資料。

    > [!Note]
    > 您可以從開發人員設定中移除安全性群組，但先前解壓縮的中繼資料不會從範疇帳戶中移除。 如果您想要的話，可以另外刪除它。

## <a name="register-your-power-bi-and-set-up-a-scan"></a>註冊您的 Power BI 並設定掃描

既然您已將目錄許可權提供給 Power BI 租使用者的系統管理員 API，您可以從目錄入口網站設定您的掃描。

首先，將特殊功能旗標新增至您的範疇 URL 

1. 選取 [ **管理中心** ] 圖示。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/management-center.png" alt-text="管理中心圖示。":::

1. 然後選取 [**資料來源**] 上的 [**新增**]。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/data-sources.png" alt-text="[新增資料來源] 按鈕的影像":::

    選取 **Power BI** 作為資料來源。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/select-power-bi-data-source.png" alt-text="此圖顯示可供選擇的資料來源清單":::

1. 為您的 Power BI 實例提供易記的名稱。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-friendly-name.png" alt-text="顯示 Power BI 資料來源易記名稱的影像":::

    名稱長度必須介於3-63 個字元之間，且只能包含字母、數位、底線和連字號。  不允許使用空格。

    根據預設，系統會在相同的 Azure 訂用帳戶中找到存在的 Power BI 租使用者。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-datasource-registered.png" alt-text="Power BI 已註冊的資料來源":::

1. 為您的掃描提供名稱。 請注意，唯一支援的驗證方法是 **受控識別**。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-scan-setup.png" alt-text="顯示 Power BI 掃描設定的影像":::

    掃描名稱的長度必須介於3-63 個字元之間，且只能包含字母、數位、底線和連字號。  不允許使用空格。

1. 設定掃描觸發程式。 您的選項是 **每7天****一次**，以及 **每隔30天**。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/scan-trigger.png" alt-text="掃描觸發程式影像":::

1. 在 [ **查看新的掃描**] 上，選取 [ **儲存並執行** ] 以啟動您的掃描。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/save-run-power-bi-scan.png" alt-text="儲存並執行 Power BI 螢幕影像":::

## <a name="next-steps"></a>後續步驟

- [瀏覽 Azure Purview 資料目錄](how-to-browse-catalog.md)
- [搜尋 Azure Purview 資料目錄](how-to-search-catalog.md)