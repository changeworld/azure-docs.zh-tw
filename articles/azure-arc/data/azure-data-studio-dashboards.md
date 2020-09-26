---
title: Azure Data Studio 儀表板
description: Azure Data Studio 儀表板
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 608f984b3e3a3020863e0c28b0aa2ad504531e3b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91273218"
---
# <a name="azure-data-studio-dashboards"></a>Azure Data Studio 儀表板

[Azure Data Studio](https://aka.ms/azuredatastudio) 可提供類似于 Azure 入口網站的體驗，以查看 Azure Arc 資源的相關資訊。  這些視圖稱為 **儀表板** ，其配置和選項類似于您在 Azure 入口網站中指定資源的內容，但可讓您彈性地在您的環境中，于沒有 Azure 可用連線的情況下查看該資訊。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connecting-to-a-data-controller"></a>連接至資料控制器

### <a name="prerequisites"></a>Prerequisites

- 下載 [Azure Data Studio](https://aka.ms/getazuredatastudio)
- 已安裝 Azure Arc 擴充功能

### <a name="determine-the-data-controller-server-api-endpoint-url"></a>判斷資料控制器伺服器 API 端點 URL

首先，您必須將 Azure Data Studio 連接到您的資料控制器服務 API 端點 URL。

若要取得此端點，您可以執行下列命令：

```console
kubectl get svc/controller-svc-external -n <namespace name>

#Example:
kubectl get svc/controller-svc-external -n arc
```

您會看到如下所示的輸出：

```console
NAME                      TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                       AGE
controller-svc-external   LoadBalancer   10.0.175.137   52.154.152.24    30080:32192/TCP                               22h
```

如果您使用的是 LoadBalancer 類型，您會想要複製外部 IP 位址和埠號碼。 如果您使用的是 NodePort，您會想要使用 Kubernetes API 伺服器的 IP 位址，以及埠 (S) 資料行底下所列的埠號碼。

現在，您會想要藉由合併此資訊來建立端點的 URL，如下所示：

```console
https://<ip address>:<port>

Example:
https://52.;154.152.24:30080
```

記下您的 IP 位址，因為您將在下一個步驟中使用它。

### <a name="connect"></a>連線

1. 開啟 Azure Data Studio

1. 選取左側的 [ **連接** ] 索引標籤

在底部展開稱為 **Azure Arc 控制器**的面板。

按一下 + 圖示以新增資料控制器連線。

在 [命令選擇區] 畫面的頂端，輸入您在步驟1中所建立的 URL，然後按一下 [輸入]。
輸入資料控制器的使用者名稱。  這是您在資料控制站部署期間傳遞的使用者名稱值。  按一下 [輸入]。
輸入資料控制器的密碼。  這是您在資料控制站部署期間傳遞的密碼值。 按一下 [輸入]。

現在您已連線到資料控制器，您可以查看資料控制器的儀表板，以及您擁有的任何 SQL 受控實例或于 postgresql 超大規模伺服器群組資源。

## <a name="view-the-data-controller-dashboard"></a>查看資料控制器儀表板

以滑鼠右鍵按一下 [ **Arc 控制器** 可展開面板] 中 [連線] 面板中的資料控制器，然後選擇 [ **管理**]。

您可以在這裡查看資料控制器資源的詳細資料，例如名稱、區域、連線模式、資源群組、訂用帳戶、控制器端點以及命名空間。  您也可以查看資料控制器管理的所有受控資料庫資源的清單。

您將會注意到，版面配置與您可能會在 Azure 入口網站中看到的內容相似。

您可以輕鬆地按一下 [+ 新增實例] 按鈕，以啟動 SQL 受控實例或于 postgresql 超大規模伺服器群組的建立。

您也可以按一下 [在 Azure 入口網站中開啟] 按鈕，在內容中開啟此資料控制器的 Azure 入口網站。

## <a name="view-the-sql-managed-instance-dashboards"></a>查看 SQL 受控實例儀表板

如果您已建立一些 SQL 受控實例，您可以在管理它們的資料控制器底下的 [連線] 面板中看到它們列在 [Azure 資料控制器可展開面板] 下的 [連線] 面板中。

若要查看指定實例的 [SQL 受控實例] 儀表板，請在實例上按一下滑鼠右鍵，然後選擇 [管理]。

[連線] 面板會顯示在右側，並提示您輸入登入/密碼以連接到該 SQL 實例。 如果您知道連線資訊，可以輸入連接資訊，然後按一下 [連接]。  如果您不知道，您可以按一下 [取消]。  無論何種方式，當連接面板關閉時，您都會進入儀表板。

在 [總覽] 索引標籤上，您可以查看 SQL 受控實例的詳細資料，例如資源群組、資料控制器、訂用帳戶識別碼、狀態、區域等等。  您也可以在 [Grafana] 或 [Kibana 儀表板] 中，按一下以移至該 SQL 受控實例的內容。

如果您可以連接到 SQL 管理實例，您可以在這裡看到其他資訊。

您可以從這裡刪除 SQL 受控實例，或開啟 Azure 入口網站來查看 Azure 入口網站中的 SQL 受控實例。

如果您按一下左側的 [連接字串] 索引標籤，您可以看到該 SQL 受控實例的預先建立的連接字串清單，讓您輕鬆地複製/貼到其他各種應用程式或程式碼中。

## <a name="view-the-postgresql-hyperscale-server-group-dashboards"></a>查看于 postgresql 超大規模伺服器群組儀表板

如果您已建立一些于 postgresql 超大規模伺服器群組，您可以在管理它們的資料控制器底下的 [連線] 面板中看到它們列在 [連線] 面板中。

若要查看指定伺服器群組的于 postgresql 超大規模伺服器群組儀表板，請以滑鼠右鍵按一下伺服器群組，然後選擇 [管理]。

在 [總覽] 索引標籤上，您可以查看伺服器群組的詳細資料，例如資源群組、資料控制器、訂用帳戶識別碼、狀態、區域等等。  您也可以看到連結，讓您可以按一下以移至 Grafana 或 Kibana 儀表板中的內容到該伺服器群組。

您可以從這裡刪除伺服器群組，或開啟 Azure 入口網站來查看 Azure 入口網站中的伺服器群組。

如果您按一下左側的 [連接字串] 索引標籤，就可以看到該伺服器群組的預先建立的連接字串清單，讓您輕鬆地複製/貼到其他各種應用程式或程式碼中。

如果您按一下左側的 [內容] 索引標籤，就可以看到其他詳細資料。

如果您按一下左側的 [資源健康情況] 索引標籤，就會看到該伺服器群組目前的高階健全狀況。

如果您按一下左側的 [診斷並解決問題] 索引標籤，您可以啟動于 postgresql 疑難排解筆記本。

如果您按一下左側的 [新增支援要求] 索引標籤，您可以在伺服器群組的內容中啟動 Azure 入口網站，並從該處建立 Azure 支援要求。
