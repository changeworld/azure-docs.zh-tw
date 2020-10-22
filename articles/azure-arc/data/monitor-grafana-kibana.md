---
title: 使用 Kibana 和 Grafana 來查看記錄和計量
description: 使用 Kibana 和 Grafana 來查看記錄和計量
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 3693c30a34601512770f5d9071f5d786410fb00e
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92360372"
---
# <a name="view-logs-and-metrics-using-kibana-and-grafana"></a>使用 Kibana 和 Grafana 來查看記錄和計量

在資料服務已啟用 Azure Arc 的情況下，Kibana 和 Grafana Web 儀表板可針對資料服務所使用的 Kubernetes 命名空間提供深入解析和清楚說明。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="retrieve-the-ip-address-of-your-cluster"></a>取出叢集的 IP 位址

若要存取儀表板，您將需要取出叢集的 IP 位址。 用來取得正確 IP 位址的方法會根據您選擇部署 Kubernetes 的方式而有所不同。 逐步執行下列選項以找出正確的選項。

### <a name="azure-virtual-machine"></a>Azure 虛擬機器

若要擷取公用 IP 位址，請使用下列命令：

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

### <a name="kubeadm-cluster"></a>Kubeadm 叢集

若要取得叢集 ip 位址，請使用下列命令：

```console
kubectl cluster-info
```


### <a name="aks-or-other-load-balanced-cluster"></a>AKS 或其他負載平衡叢集

若要在 AKS 或其他負載平衡叢集中監視您的環境，您需要取得管理 proxy 服務的 ip 位址。 您可以使用此命令來取出 **外部 ip** 位址：

```console
kubectl get svc mgmtproxy-svc-external -n <namespace>

#Example:
#kubectl get svc mgmtproxy-svc-external -n arc
NAME                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)           AGE
mgmtproxy-svc-external   LoadBalancer   10.0.186.28   52.152.148.25   30777:30849/TCP   19h
```

## <a name="additional-firewall-configuration"></a>其他防火牆設定

您可能會發現您需要在防火牆上開啟埠，才能存取 Kibana 和 Grafana 端點。

以下是如何針對 Azure VM 進行這項作業的範例。 如果您已使用腳本部署 Kubernetes，您將需要這麼做。

下列步驟強調如何建立 Kibana 和 Grafana 端點的 NSG 規則：

### <a name="find-the-name-of-the-nsg"></a>尋找 NSG 的名稱

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

### <a name="add-the-nsg-rule"></a>新增 NSG 規則

擁有 NSG 的名稱之後，您可以使用下列命令來新增規則：

```azurecli
az network nsg rule create -n ports_30777 --nsg-name azurearcvmNSG --priority 600 -g azurearcvm-rg --access Allow --description 'Allow Kibana and Grafana ports' --destination-address-prefixes '*' --destination-port-ranges 30777 --direction Inbound --protocol Tcp --source-address-prefixes '*' --source-port-ranges '*'
```

## <a name="monitor-azure-sql-managed-instances-on-azure-arc"></a>監視 Azure Arc 上的 Azure SQL 受控實例

現在您已經有 IP 位址並公開端口，您應該能夠存取 Grafana 和 Kibana。

> [!NOTE]
>  當系統提示您輸入使用者名稱和密碼時，請輸入您在建立 Azure Arc 資料控制器時所提供的使用者名稱和密碼。

> [!NOTE]
>  系統會提示您提供憑證警告，因為預覽中使用的憑證是自我簽署的憑證。

使用下列 URL 模式來存取 Azure SQL 受控實例的記錄和監視儀表板：

```html
https://<external-ip-from-above>:30777/grafana
https://<external-ip-from-above>:30777/kibana
```

相關儀表板如下：

* 「Azure SQL 受控實例計量」
* 「主機節點計量」
* 「主機 pod 計量」

## <a name="monitor-azure-database-for-postgresql-hyperscale---azure-arc"></a>監視適用於 PostgreSQL 的 Azure 資料庫超大規模-Azure Arc

使用下列 URL 模式來存取于 postgresql 超大規模的記錄和監視儀表板：

```html
https://<external-ip-from-above>:30777/grafana
https://<external-ip-from-above>:30777/kibana
```

相關儀表板如下：

* 「Postgres 計量」
* 「Postgres 資料表度量」
* 「主機節點計量」
* 「主機 pod 計量」

## <a name="next-steps"></a>後續步驟
- 請嘗試 [將計量和記錄上傳至 Azure 監視器](upload-metrics-and-logs-to-azure-monitor.md)
- 閱讀 Grafana：
   - [快速入門](https://grafana.com/docs/grafana/latest/getting-started/getting-started)
   - [Grafana 基本概念](https://grafana.com/tutorials/grafana-fundamentals/#1)
   - [Grafana 教學課程](https://grafana.com/tutorials/grafana-fundamentals/#1)
- 深入瞭解 Kibana
   - [簡介](https://www.elastic.co/webinars/getting-started-kibana?baymax=default&elektra=docs&storm=top-video)
   - [Kibana 指南](https://www.elastic.co/guide/en/kibana/current/index.html)
   - [在 Kibana 中使用資料視覺效果的儀表板向下鑽研簡介](https://www.elastic.co/webinars/dashboard-drilldowns-with-data-visualizations-in-kibana/)
   - [如何建立 Kibana 儀表板](https://www.elastic.co/webinars/how-to-build-kibana-dashboards/)

