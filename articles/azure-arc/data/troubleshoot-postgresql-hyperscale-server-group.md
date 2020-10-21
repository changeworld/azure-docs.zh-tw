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
ms.openlocfilehash: caaab07200a8631935a2b5d5368a0c16ea9a60c5
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92320223"
---
# <a name="troubleshooting-postgresql-hyperscale-server-groups"></a>針對于 postgresql 超大規模伺服器群組進行疑難排解
本文描述您可以用來疑難排解伺服器群組的一些技巧。 除了本文之外，您還需要閱讀如何使用 [Kibana](monitor-grafana-kibana.md) 來搜尋記錄，或使用 [Grafana](monitor-grafana-kibana.md) 來視覺化伺服器群組的相關度量。 

## <a name="getting-more-details-about-the-execution-of-an-azdata-command"></a>取得 azdata 命令執行的更多詳細資料
您可以將參數 **--debug** 新增至您執行的任何 azdata 命令。 這麼做會對主控台顯示該命令執行的其他相關資訊。 您應該會發現有助於取得詳細資料，以協助您瞭解該命令的行為。
例如，您可以執行
```console
azdata arc postgres server create -n postgres01 -w 2 --debug
```

或
```console
azdata arc postgres server edit -n postgres01 --extension SomeExtensionName --debug
```

此外，您可以在任何 azdata 命令上使用參數--help 來顯示特定命令的一些說明和參數清單。 例如：
```console
azdata arc postgres server create --help
```


## <a name="collecting-logs-of-the-data-controller-and-your-server-groups"></a>收集資料控制器和您伺服器群組的記錄
閱讀有關[取得 Azure Arc 啟用的資料服務之記錄](troubleshooting-get-logs.md)檔的文章



## <a name="interactive-troubleshooting-with-jupyter-notebooks-in-azure-data-studio"></a>在 Azure Data Studio 中使用 Jupyter 筆記本進行互動式疑難排解
筆記本可以藉由包含 Markdown 內容來記載程序，以描述要執行的動作/執行的方法。 其也可以提供可執行程式碼來自動化程序。  此模式的適用範圍包含從標準操作程序到疑難排解指南的所有項目。

例如，讓我們針對使用 Azure Data Studio 可能發生一些問題的于 postgresql 超大規模伺服器群組進行疑難排解。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

### <a name="install-tools"></a>安裝工具

安裝 Azure Data Studio， `kubectl` 以及在 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] Azure Data Studio 中用來執行筆記本的用戶端電腦上。 若要這樣做，請依照[安裝用戶端工具](install-client-tools.md)上的指示進行。

### <a name="update-the-path-environment-variable"></a>更新 PATH 環境變數

請確定可以從這部用戶端電腦上的任何位置叫用這些工具。 例如，在 Windows 用戶端電腦上，更新 PATH 系統內容變數，並新增您安裝 kubectl 的資料夾。

### <a name="sign-in-with-azure-data-cli-azdata"></a>登入 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]

從這部用戶端電腦登入 Arc 資料控制器，然後再啟動 Azure Data Studio。 若要這樣做，請執行類似下列的命令：

```console
azdata login --endpoint https://<IP address>:<port>
```

將取代 `<IP address>` 為 Kubernetes 叢集的 IP 位址，以及 `<port>` Kubernetes 接聽的埠。 系統會提示您輸入使用者名稱和密碼。 若要查看更多詳細資料，請執行： _

```console
azdata login --help
```

### <a name="log-into-your-kubernetes-cluster-with-kubectl"></a>使用 kubectl 登入您的 Kubernetes 叢集

若要這樣做，您可能會想要使用 [此](https://blog.christianposta.com/kubernetes/logging-into-a-kubernetes-cluster-with-kubectl/) blog 文章中提供的範例命令。
您可以執行類似下列的命令：

```console
kubectl config view
kubectl config set-credentials kubeuser/my_kubeuser --username=<your Arc Data Controller Admin user name> --password=<password>
kubectl config set-cluster my_kubeuser --server=https://<IP address>:<port>
kubectl config set-context default/my_kubeuser/ArcDataControllerAdmin --user=ArcDataControllerAdmin/my_kubeuser --namespace=arc --cluster=my_kubeuser
kubectl config use-context default/my_kubeuser/ArcDataControllerAdmin
```

#### <a name="the-troubleshooting-notebook"></a>疑難排解筆記本

啟動 Azure Data Studio，然後開啟疑難排解筆記本。 

執行  [033-manage-Postgres-with-AzureDataStudio.md](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md) 中所述的步驟，以：

1. 連接到您的 Arc 資料控制器
2. 以滑鼠右鍵選取您的 Postgres 實例，然後選擇 **[管理]**
3. 選取 **[診斷並解決問題] 儀表板**
4. 選取 **[疑難排解] 連結**

:::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook.jpg" alt-text="Azure Data Studio-開啟于 postgresql 疑難排解筆記本":::

**TSG100-已開啟 Azure Arc Enabled 于 postgresql 超大規模疑難排解工具筆記本**： :::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook2.jpg" alt-text="Azure Data Studio-開啟于 postgresql 疑難排解筆記本":::

#### <a name="run-the-scripts"></a>執行指令碼
選取頂端的 [全部執行] 按鈕以一次執行筆記本，或您可以逐一執行和執行每個程式碼資料格。

查看程式碼資料格執行的輸出是否有任何潛在問題。

我們會在一段時間內，將更多詳細資料新增至筆記本，以瞭解如何辨識常見問題以及如何解決這些問題。

## <a name="next-step"></a>後續步驟
- 瞭解如何 [取得 Azure Arc 啟用資料服務的記錄](troubleshooting-get-logs.md)
- 瞭解如何 [使用 Kibana 搜尋記錄](monitor-grafana-kibana.md)
- 瞭解如何 [使用 Grafana 進行監視](monitor-grafana-kibana.md)
- 建立您自己的筆記本
