---
title: 在 Azure 入口網站中建立 Azure Arc 資料控制器
description: 在 Azure 入口網站中建立 Azure Arc 資料控制器
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 297efa83fb1563e3a360f652a6ac1bc2b1679998
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934638"
---
# <a name="create-an-azure-arc-data-controller-in-the-azure-portal"></a>在 Azure 入口網站中建立 Azure Arc 資料控制器

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="introduction"></a>簡介

您可以使用 Azure 入口網站建立 Azure Arc 資料控制器。

Azure Arc 在 Azure 入口網站中開始的許多建立體驗，即使要建立或管理的資源是在 Azure 基礎結構之外也是一樣。 在這些情況下，使用者體驗模式，特別是在 Azure 與您的環境之間沒有直接連線時，是使用 Azure 入口網站產生可在環境中下載並執行的腳本，以建立 Azure 的安全連線。 例如，Azure Arc 啟用的伺服器會遵循此模式來 [建立啟用 Arc 的伺服器](../servers/onboard-portal.md)。

目前，假設預覽僅支援 Azure Arc 啟用的資料服務的間接連接模式，您可以使用 Azure 入口網站為您產生筆記本，然後在 Azure Data Studio 中針對您的 Kubernetes 叢集進行下載和執行。 未來，當直接連線模式可用時，您就可以直接從 Azure 入口網站布建資料控制器。 您可以閱讀更多 [連接模式](connectivity.md)的相關資訊。

## <a name="use-the-azure-portal-to-create-an-azure-arc-data-controller"></a>使用 Azure 入口網站建立 Azure Arc 資料控制器

遵循下列步驟，使用 Azure 入口網站和 Azure Data Studio 建立 Azure Arc 資料控制器。

1. 首先，登入 [Azure 入口網站 marketplace](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/home/searchQuery/azure%20arc%20data%20controller)。  Marketplace 搜尋結果將會進行篩選，以顯示「Azure Arc 資料控制器」。
2. 如果第一個步驟未輸入搜尋準則。 請輸入搜尋結果，然後按一下 [Azure Arc 資料控制器]。
3. 從 marketplace 選取 [Azure 資料控制器] 磚。
4. 按一下 [建立]  按鈕。
5. 請參閱建立 Azure Arc 資料控制器的需求，並安裝任何遺漏的先決條件軟體，例如 Azure Data Studio 和 kubectl。
6. 按一下 [ **資料控制器詳細資料** ] 按鈕。
7. 選擇訂用帳戶、資源群組和 Azure 位置，就像您在 Azure 入口網站中建立的任何其他資源一樣。 在此情況下，您選取的 Azure 位置將會是將儲存資源相關中繼資料的位置。  資源本身將會建立在您選擇的任何基礎結構上。 它不需要在 Azure 基礎結構上。
8. 輸入資料控制器的名稱。
9. 目前在預覽中只支援間接連接模式。
10. 選取部署設定檔。
11. 按一下 [ **在 Azure Studio 中開啟** ] 按鈕。
12. 在下一個畫面中，您會看到所選項目的摘要以及產生的筆記本。  您可以按一下 [ **下載** 布建筆記本] 按鈕來下載筆記本。
13. 在 Azure Data Studio 中開啟筆記本，然後按一下頂端的 [ **全部執行** ] 按鈕。
14. 依照筆記本中的提示和指示完成建立資料控制器。

## <a name="monitoring-the-creation-status"></a>監視建立狀態

建立控制器需要幾分鐘的時間才能完成。 您可以使用下列命令來監視另一個終端機視窗中的進度：

> [!NOTE]
>  下列範例命令假設您已建立名為 ' arc ' 的資料控制器和 Kubernetes 命名空間。  如果您使用不同的命名空間/資料控制器名稱，您可以將 ' arc ' 取代為您的名稱。

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

您也可以執行如下所示的命令，檢查任何特定 pod 的建立狀態。  這特別適用于針對任何問題進行疑難排解。

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>針對建立問題進行疑難排解

如果您在建立麻煩很快找上門時遇到任何問題，請參閱 [疑難排解指南](troubleshoot-guide.md)。
