---
title: 針對于 postgresql 超大規模伺服器群組進行疑難排解
description: 使用 Jupyter Notebook 針對于 postgresql 超大規模伺服器群組進行疑難排解
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 9c0d3d9c74be8dabaec20ff5d4c7e7cfc74d8eef
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934326"
---
# <a name="troubleshooting-postgresql-hyperscale-server-groups"></a>針對于 postgresql 超大規模伺服器群組進行疑難排解

筆記本可以記錄程式，方法是包含 markdown 內容，以描述要做什麼/如何進行。 它也可以提供可執行程式碼來將程式自動化。  這種模式適用于從標準作業系統程式到疑難排解指南的所有專案。

例如，讓我們針對使用 Azure Data Studio 可能發生一些問題的于 postgresql 超大規模伺服器群組進行疑難排解。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="install-tools"></a>安裝工具

安裝 Azure Data Studio， `kubectl` 以及在 `azdata` Azure Data Studio 中用來執行筆記本的用戶端電腦上。 若要這樣做，請依照[安裝用戶端工具](install-client-tools.md)上的指示進行。

## <a name="update-the-path-environment-variable"></a>更新 PATH 環境變數

請確定可以從這部用戶端電腦上的任何位置叫用這些工具。 例如，在 Windows 用戶端電腦上，更新 PATH 系統內容變數，並新增您安裝 kubectl 的資料夾。

## <a name="sign-in-with-azdata"></a>登入 `azdata`

從這部用戶端電腦登入 Arc 資料控制器，然後再啟動 Azure Data Studio。 若要這樣做，請執行類似下列的命令：

```console
azdata login --endpoint https://<IP address>:<port>
```

將取代 `<IP address>` 為 Kubernetes 叢集的 IP 位址，以及 `<port>` Kubernetes 接聽的埠。 系統會提示您輸入使用者名稱和密碼。 若要查看更多詳細資料，請執行： _

```console
azdata login --help
```

## <a name="log-into-your-kubernetes-cluster-with-kubectl"></a>使用 kubectl 登入您的 Kubernetes 叢集

若要這樣做，您可能會想要使用 [此](https://blog.christianposta.com/kubernetes/logging-into-a-kubernetes-cluster-with-kubectl/) blog 文章中提供的範例命令。
您可以執行類似下列的命令：

```console
kubectl config view
kubectl config set-credentials kubeuser/my_kubeuser --username=<your Arc Data Controller Admin user name> --password=<password>
kubectl config set-cluster my_kubeuser --server=https://<IP address>:<port>
kubectl config set-context default/my_kubeuser/ArcDataControllerAdmin --user=ArcDataControllerAdmin/my_kubeuser --namespace=arc --cluster=my_kubeuser
kubectl config use-context default/my_kubeuser/ArcDataControllerAdmin
```

### <a name="the-troubleshooting-notebook"></a>疑難排解筆記本

啟動 Azure Data Studio，然後開啟疑難排解筆記本。 

執行  [033-manage-Postgres-with-AzureDataStudio.md](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md) 中所述的步驟，以：

1. 連接到您的 Arc 資料控制器
2. 以滑鼠右鍵選取您的 Postgres 實例，然後選擇 **[管理]**
3. 選取 **[診斷並解決問題] 儀表板**
4. 選取 **[疑難排解] 連結**

:::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook.jpg" alt-text="Azure Data Studio-開啟于 postgresql 疑難排解筆記本":::

**TSG100-已開啟 Azure Arc Enabled 于 postgresql 超大規模疑難排解工具筆記本**： :::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook2.jpg" alt-text="Azure Data Studio-使用於 postgresql 疑難排解筆記本":::

### <a name="run-the-scripts"></a>執行指令碼
選取頂端的 [全部執行] 按鈕以一次執行筆記本，或您可以逐一執行和執行每個程式碼資料格。

查看程式碼資料格執行的輸出是否有任何潛在問題。

我們會在一段時間內，將更多詳細資料新增至筆記本，以瞭解如何辨識常見問題以及如何解決這些問題。

## <a name="next-step"></a>後續步驟
- 瞭解如何 [取得 Azure Arc 啟用資料服務的記錄](troubleshooting-get-logs.md)
- 瞭解如何 [使用 Kibana 搜尋記錄](monitor-grafana-kibana.md)
- 瞭解如何 [使用 Grafana 進行監視](monitor-grafana-kibana.md)
- 建立您自己的筆記本
