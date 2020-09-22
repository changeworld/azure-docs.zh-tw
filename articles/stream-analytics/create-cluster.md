---
title: 建立 Azure 串流分析叢集快速入門
description: 了解如何建立 Azure 串流分析叢集。
author: sidramadoss
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: a7be204c30d242be991fb9a57d239b69342ace97
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943747"
---
# <a name="quickstart-create-a-dedicated-azure-stream-analytics-cluster-using-azure-portal"></a>快速入門：使用 Azure 入口網站建立專用的 Azure 串流分析叢集

使用 Azure 入口網站建立 Azure 串流分析叢集。 [串流分析叢集](cluster-overview.md)是單一租用戶部署，可用於複雜且高需求的串流使用案例。 您可以在串流分析叢集上執行多個串流分析作業。

## <a name="prerequisites"></a>Prerequisites

* 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 完成[快速入門：使用 Azure 入口網站建立串流分析作業](stream-analytics-quick-create-portal.md)。

## <a name="create-a-stream-analytics-cluster"></a>建立串流分析叢集

在這一節中，您會建立串流分析叢集資源。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 選取 [建立資源]。 在「搜尋 Marketplace」方塊中，輸入並選取 [串流分析叢集]。 然後選取 [新增]。

   :::image type="content" source="./media/create-cluster/search-result.png" alt-text="串流分析叢集搜尋結果。":::

1. 在**建立串流分析叢集**頁面上，輸入新叢集的基本設定。

   |設定|值|描述 |
   |---|---|---|
   |訂用帳戶|訂閱名稱|選取要用於此串流分析叢集的 Azure 訂用帳戶。 |
   |資源群組|資源群組名稱|選取資源群組，或選取 [新建]，然後輸入新資源群組的唯一名稱。 |
   |叢集名稱|唯一的名稱|輸入用來識別您串流分析叢集的名稱。|
   |位置|最接近資料來源和接收的區域|選取您可以裝載串流分析叢集的地理位置。 使用最接近您資料來源和接收的位置，以進行低延遲分析。|
   |串流單位容量|36 到 216 個 |藉由評估希望執行的串流分析作業數目以及作業將需要的 SU 總數，判斷叢集的大小。 您可以從 36 個 SU 開始，稍後視需要擴大或縮減。|

   ![建立叢集](./media/create-cluster/create-cluster.png)

1. 選取 [檢閱 + 建立]。 您可以略過**標籤**區段。

1. 檢閱叢集設定，然後選取 [建立]。 叢集建立作業是長時間執行的作業，需要大約 60 分鐘才能完成。 等候入口網站頁面顯示 [您的部署已完成] 訊息。 在此同時，您也可以建立和開發要在此叢集上執行的 [串流分析作業](stream-analytics-quick-create-portal.md#create-a-stream-analytics-job) (如果尚未這麼做)。

1. 選取 [前往資源]，移至串流分析叢集頁面。

## <a name="delete-your-cluster"></a>刪除叢集

如果您不打算在串流分析叢集上執行任何串流分析作業，可以將其刪除。 遵循 Azure 入口網站的步驟，刪除您的叢集：

1. 移至**設定**下的**串流分析作業**並停止所有執行中的作業。

1. 移至您叢集的**概觀**。 選取 [刪除]，然後遵循指示刪除您的叢集。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何建立 Azure 串流分析叢集。 進入下一篇文章，了解如何在您的叢集上執行串流分析作業：

> [!div class="nextstepaction"]
> [管理串流分析叢集中的串流分析作業](manage-jobs-cluster.md)
