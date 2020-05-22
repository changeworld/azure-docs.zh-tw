---
title: 在生產前環境中檢閱 App 靜態 Web Apps 中的提取要求
description: 了解如何使用生產前環境來檢閱 Azure 靜態 Web Apps 中的提取要求變更。
services: static-web-apps
author: sinedied
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: yolasors
ms.openlocfilehash: 61c5917c1e4cb9dbf96e90af9a30777ea7c2e66c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594107"
---
# <a name="review-pull-requests-in-pre-production-environments-in-azure-static-web-apps-preview"></a>在生產前環境中檢閱 App 靜態 Web Apps 預覽版中的提取要求

本文示範如何使用生產前環境來檢閱以 [Azure 靜態 Web Apps](overview.md) 部署的應用程式有何變更。

生產前 (預備) 環境是具備完整功能的預備版應用程式，其中包含生產環境中所沒有的變更。

Azure 靜態 Web Apps 會在存放庫中產生 GitHub 動作工作流程。 對工作流程監看的分支建立提取要求時，就會建置生產前環境。 生產前環境會暫存應用程式，讓您能夠在推送至生產環境之前執行檢閱。

使用 Azure 靜態 Web Apps 時，多個生產前環境可同時並存。 每當您對受到監看的分支建立提取要求時，就會將含有變更的預備版本部署至不同的生產前環境。

使用生產前環境有許多優點。 例如，您可以：

- 檢閱生產和預備環境之間的視覺效果變更。 例如，檢視內容和配置的更新。
- 示範小組的變更。
- 比較應用程式的不同版本。
- 使用接受度測試來驗證變更。
- 在部署至生產環境之前，先執行例行性檢查。

> [!NOTE]
> 在預覽期間，一次[最多僅允許一個預備環境](quotas.md)。

## <a name="prerequisites"></a>Prerequisites

- 使用 Azure 靜態 Web Apps 設定的現有 GitHub 存放庫。 請參閱[建置您的第一個靜態應用程式](getting-started.md) (如果您沒有的話)。

## <a name="make-a-change"></a>進行變更

首先，請在您的存放庫中進行變更。 您可以直接在 GitHub 上執行，如下列步驟所示。

1. 瀏覽至 GitHub 上的專案存放庫，然後按一下 [分支] 按鈕以建立新的分支。

    :::image type="content" source="./media/review-publish-pull-requests/create-branch.png" alt-text="使用 GitHub 介面建立新的分支":::

    輸入分支名稱，然後按一下 [建立分支]。

1. 移至您的 _app_ 資料夾，並變更一些文字內容。 例如，您可以變更標題或段落。 找到您要編輯的檔案後，按一下 [編輯] 以進行變更。

    :::image type="content" source="./media/review-publish-pull-requests/edit-file.png" alt-text="GitHub 介面中的編輯檔案按鈕":::

1. 進行變更後，請按一下 [認可變更]，將變更認可到分支。

    :::image type="content" source="./media/review-publish-pull-requests/commit-changes.png" alt-text="GitHub 介面中的認可變更按鈕":::

## <a name="create-a-pull-request"></a>建立提取要求

接著，建立此變更的提取要求。

1. 在 GitHub 上開啟專案的 [提取要求] 索引標籤：

    :::image type="content" source="./media/review-publish-pull-requests/tab.png" alt-text="GitHub 存放庫中的 [提取要求] 索引標籤":::

1. 按一下分支的 [比較和提取要求] 按鈕。

1. 您可以選擇性地填入一些關於變更的詳細資料，然後按一下 [建立提取要求]。

    :::image type="content" source="./media/review-publish-pull-requests/open.png" alt-text="在 GitHub 中建立提取要求":::

如有需要，您可以指派檢閱者，並新增註解以討論您的變更。

> [!NOTE]
> 您可以將新認可推送至分支，以進行多項變更。 提取要求隨後將會自動更新，以反映所有變更。

## <a name="review-changes"></a>檢閱變更

建立提取要求後，[GitHub 動作](https://github.com/features/actions)部署工作流程即會執行，並將您的變更部署至生產前環境。

在工作流程完成應用程式的建置和部署後，GitHub Bot 會在您的提取要求中新增註解，其中包含生產前環境的 URL。 您可以按一下此連結，以查看您的暫存變更。

:::image type="content" source="./media/review-publish-pull-requests/bot-comment.png" alt-text="使用生產前 URL 的提取要求註解":::

按一下產生的 URL 以查看變更。

如果您仔細查看 URL，您會發現其組成方式如下：`https://<SUBDOMAIN-PULL_REQUEST_ID>.<AZURE_REGION>.azurestaticapps.net`。

針對指定的提取要求，即使您推送新的更新，URL 仍會保持不變。 除了 URL 保持不變之外，在提取要求的生命週期中也會重複使用相同的生產前環境。

## <a name="publish-changes"></a>發佈變更

變更一經核准後，您即可合併提取要求，藉以將變更發佈至生產環境。

按一下 [合併提取要求]：

:::image type="content" source="./media/review-publish-pull-requests/merge.png" alt-text="GitHub 介面中的合併提取要求按鈕":::

合併時會將您所做的變更複製到受追蹤的分支 (「生產」分支)。 然後，受追蹤的分支會啟動部署工作流程，而變更則會在您的應用程式重建之後生效。

若要驗證生產環境中的變更，請開啟您的生產 URL 以載入網站的最新版本。

## <a name="limitations"></a>限制

應用程式的預備版本目前可透過其 URL 公開存取，即使您的 GitHub 存放庫是私人的，仍是如此。

> [!WARNING]
> 將敏感性內容發佈至預備版本時請多加留意，因為對生產前環境的存取不受限制。

使用靜態 Web Apps 部署的每個應用程式可使用的生產前環境數目，取決於您所使用的 SKU 層。 例如，若您使用免費層，則除了生產環境以外，還可以有 1 個生產前環境。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [設定自訂網域](custom-domain.md)
