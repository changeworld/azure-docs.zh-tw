---
title: 教學課程：使用 Azure 流量管理員設定加權循環配置流量路由
description: 本教學課程將說明如何在流量管理員中使用循環配置資源方法對流量進行負載平衡
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2020
ms.author: duau
ms.openlocfilehash: abcfce43b90c7371d5b38aa5b7a6d478e9d6a0dd
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207806"
---
# <a name="tutorial-configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>教學課程：在流量管理員中設定加權流量路由方法

常見的流量路由方法提供一組完全相同的端點 (包括雲端服務和網站)，並以均等的方式將流量傳送到每一個端點。 下列步驟概述如何設定這種類型的流量路由方法。

> [!NOTE]
> Azure Web 應用程式已為 Azure 區域 (可能包含多個資料中心) 內的網站提供循環配置資源負載平衡功能。 流量管理員可讓您將流量分散到位於不同資料中心的網站。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> - 建立具有加權路由的流量管理員設定檔。
> - 使用流量管理員設定檔。
> - 刪除流量管理員設定檔。

## <a name="prerequisites"></a>必要條件

* 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/)。

## <a name="configure-the-weighted-traffic-routing-method"></a>設定加權流量路由方法

1. 從瀏覽器登入 [Azure 入口網站](https://portal.azure.com)。

1. 在入口網站的搜尋列中，搜尋您在上一節建立的 **流量管理員設定檔** 名稱，然後在顯示的結果中選取流量管理員設定檔。

    :::image type="content" source="./media/traffic-manager-weighted-routing-method/search-traffic-manager-weighted-profile.png" alt-text="搜尋流量管理員設定檔&quot;:::

1. 選取 [組態] 並選取或輸入下列設定：

    | 設定         | 值                                              |
    | ---             | ---                                                |
    | 路由方法            | 選取 [加權]。 |    
    | DNS 存留時間 (TTL) | 此值會針對用戶端的本機快取名稱伺服器，控制其查詢流量管理員系統是否有更新 DNS 項目的頻率。 流量管理員的所有變更，例如流量路由方法變更或所新增端點的可用性變更，都要等到這段時間過後才會經由全球 DNS 伺服器的系統重新整理。 |
    | 通訊協定    | 選取用於端點監視取通訊協定。 *選項：HTTP、HTTPS 和 TCP* |
    | 連接埠 | 指定連接埠號碼。 |
    | Path | 若要監視端點，您必須指定路徑和檔案名稱。 正斜線 &quot;/" 是相對路徑的有效項目，表示檔案位於根目錄 (預設值)。 |
    | 自訂標題設定 | 以 host:contoso.com,newheader:newvalue 格式設定自訂標頭。 支援的最大配對為 8。 適用於 Http 和 Https 通訊協定。 適用於設定檔中的所有端點 |
    | 預期的狀態碼範圍 (預設值：200) | 以 200-299、301-301 格式設定狀態碼範圍。 支援的最大範圍為 8。 適用於 Http 和 Https 通訊協定。 適用於設定檔中的所有端點 |
    | 探查間隔 | 設定端點健全狀況探查之間的時間間隔。 您可以選擇 10 或 30 秒。 |
    | 容許失敗次數 | 設定在觸發端點失敗之前，容許的健全狀態探查失敗次數。 您可以輸入 0 到 9 之間的數字。 | 
    | 探查逾時 | 設定端點健全狀態探查逾時前所需的時間。此值必須至少為 5，且小於探查間隔值。 |

1. 選取 [儲存]  以完成設定。

    :::image type="content" source="./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-configuration.png" alt-text="搜尋流量管理員設定檔&quot;:::

1. 選取 [組態] 並選取或輸入下列設定：

    | 設定         | 值                                              |
    | ---             | ---                                                |
    | 路由方法            | 選取 [加權]。 |    
    | DNS 存留時間 (TTL) | 此值會針對用戶端的本機快取名稱伺服器，控制其查詢流量管理員系統是否有更新 DNS 項目的頻率。 流量管理員的所有變更，例如流量路由方法變更或所新增端點的可用性變更，都要等到這段時間過後才會經由全球 DNS 伺服器的系統重新整理。 |
    | 通訊協定    | 選取用於端點監視取通訊協定。 *選項：HTTP、HTTPS 和 TCP* |
    | 連接埠 | 指定連接埠號碼。 |
    | Path | 若要監視端點，您必須指定路徑和檔案名稱。 正斜線 &quot;/"::: 

1. 選取 [端點]並設定每個端點的權數。 權數可介於 1-1000 之間。 權數越高，優先順序也就越高。  

    :::image type="content" source="./media/traffic-manager-weighted-routing-method/traffic-manager-configure-endpoints-weighted.png" alt-text="搜尋流量管理員設定檔&quot;:::

1. 選取 [組態] 並選取或輸入下列設定：

    | 設定         | 值                                              |
    | ---             | ---                                                |
    | 路由方法            | 選取 [加權]。 |    
    | DNS 存留時間 (TTL) | 此值會針對用戶端的本機快取名稱伺服器，控制其查詢流量管理員系統是否有更新 DNS 項目的頻率。 流量管理員的所有變更，例如流量路由方法變更或所新增端點的可用性變更，都要等到這段時間過後才會經由全球 DNS 伺服器的系統重新整理。 |
    | 通訊協定    | 選取用於端點監視取通訊協定。 *選項：HTTP、HTTPS 和 TCP* |
    | 連接埠 | 指定連接埠號碼。 |
    | Path | 若要監視端點，您必須指定路徑和檔案名稱。 正斜線 &quot;/"::: 

## <a name="use-the-traffic-manager-profile"></a>使用流量管理員設定檔

[流量管理員設定檔]  會顯示新建立之流量管理員設定檔的 DNS 名稱。 此名稱可由任何用戶端使用 (例如使用網頁瀏覽器進行瀏覽)，以路由傳送至由路由類型所決定的正確端點。 在此情況下，所有要求都會以循環配置資源方式路由到每個端點。

:::image type="content" source="./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-overview.png" alt-text="搜尋流量管理員設定檔&quot;:::

1. 選取 [組態] 並選取或輸入下列設定：

    | 設定         | 值                                              |
    | ---             | ---                                                |
    | 路由方法            | 選取 [加權]。 |    
    | DNS 存留時間 (TTL) | 此值會針對用戶端的本機快取名稱伺服器，控制其查詢流量管理員系統是否有更新 DNS 項目的頻率。 流量管理員的所有變更，例如流量路由方法變更或所新增端點的可用性變更，都要等到這段時間過後才會經由全球 DNS 伺服器的系統重新整理。 |
    | 通訊協定    | 選取用於端點監視取通訊協定。 *選項：HTTP、HTTPS 和 TCP* |
    | 連接埠 | 指定連接埠號碼。 |
    | Path | 若要監視端點，您必須指定路徑和檔案名稱。 正斜線 &quot;/"::: 

## <a name="clean-up-resources"></a>清除資源

如果您不再需要流量管理員設定檔，請找出設定檔，然後選取 [刪除設定檔]。

:::image type="content" source="./media/traffic-manager-weighted-routing-method/delete-traffic-manager-weighted-profile.png" alt-text="搜尋流量管理員設定檔&quot;:::

1. 選取 [組態] 並選取或輸入下列設定：

    | 設定         | 值                                              |
    | ---             | ---                                                |
    | 路由方法            | 選取 [加權]。 |    
    | DNS 存留時間 (TTL) | 此值會針對用戶端的本機快取名稱伺服器，控制其查詢流量管理員系統是否有更新 DNS 項目的頻率。 流量管理員的所有變更，例如流量路由方法變更或所新增端點的可用性變更，都要等到這段時間過後才會經由全球 DNS 伺服器的系統重新整理。 |
    | 通訊協定    | 選取用於端點監視取通訊協定。 *選項：HTTP、HTTPS 和 TCP* |
    | 連接埠 | 指定連接埠號碼。 |
    | Path | 若要監視端點，您必須指定路徑和檔案名稱。 正斜線 &quot;/":::

## <a name="next-steps"></a>後續步驟

若要深入了解加權路由方法，請參閱：

> [!div class="nextstepaction"]
> [加權流量路由方法](traffic-manager-routing-methods.md#weighted)