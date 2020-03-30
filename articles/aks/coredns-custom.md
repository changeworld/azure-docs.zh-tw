---
title: 為 Azure 庫伯奈斯服務 （AKS） 自訂核心 DNS
description: 瞭解如何自訂 CoreDNS 以使用 Azure 庫伯奈斯服務 （AKS） 添加子域或擴展自訂 DNS 終結點
services: container-service
author: jnoller
ms.topic: article
ms.date: 03/15/2019
ms.author: jenoller
ms.openlocfilehash: 78132a53313f4a8ee5c10af340c8dab08c3e42c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595819"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>使用 Azure Kubernetes Service 自訂 CoreDNS

Azure 庫伯奈斯服務 （AKS） 使用[CoreDNS][coredns]專案對所有*1.12.x*和更高的群集進行群集 DNS 管理和解析。 以前，使用 kube-dns 專案。 此 kube-dns 專案現已棄用。 有關 CoreDNS 自訂和庫伯奈斯的詳細資訊，請參閱[官方上游文檔][corednsk8s]。

由於 AKS 是託管服務，因此無法修改*CoreDNS（CoreFile）* 的主配置。 相反，您可以使用庫伯內斯*配置映射*來覆蓋預設設置。 要查看預設 AKS CoreDNS 配置映射，請使用`kubectl get configmaps --namespace=kube-system coredns -o yaml`命令。

本文介紹如何在 AKS 中使用 ConfigMap 進行 CoreDNS 的基本自訂選項。 此方法不同于在其他上下文中（如使用 CoreFile）配置 CoreDNS。 驗證正在運行的 CoreDNS 版本，因為配置值可能會在版本之間更改。

> [!NOTE]
> `kube-dns`通過庫伯內斯配置圖提供了不同的[自訂選項][kubednsblog]。 CoreDNS**不**向後相容 kube-dns。 必須更新以前使用的任何自訂項，以便與 CoreDNS 一起使用。

## <a name="before-you-begin"></a>開始之前

此文章假設您目前具有 AKS 叢集。 如果您需要 AKS 叢集，請參閱[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 入口網站][aks-quickstart-portal]的 AKS 快速入門。

## <a name="what-is-supportedunsupported"></a>支援/不支援的內容

支援所有內置的 CoreDNS 外掛程式。 不支援附加元件/協力廠商外掛程式。

## <a name="rewrite-dns"></a>重寫 DNS

您有的一個方案是執行動態 DNS 名稱重寫。 在下面的示例中，替換為`<domain to be written>`您自己的完全限定的功能變數名稱。 創建名為`corednsms.yaml`的檔並粘貼以下示例配置：

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: |
    <domain to be rewritten>.com:53 {
        errors
        cache 30
        rewrite name substring <domain to be rewritten>.com default.svc.cluster.local
        forward .  /etc/resolv.conf # you can redirect this to a specific DNS server such as 10.0.0.10
    }
```

使用[kubectl 應用配置映射][kubectl-apply]命令創建 ConfigMap 並指定 YAML 清單的名稱：

```console
kubectl apply -f corednsms.yaml
```

要驗證已應用的自訂項，請使用[kubectl 獲取配置圖][kubectl-get]並指定*核心dns自訂*配置映射：

```
kubectl get configmaps --namespace=kube-system coredns-custom -o yaml
```

現在強制 CoreDNS 重新載入配置映射。 [庫布ectl刪除 pod][kubectl delete]命令不會具有破壞性，也不會導致停機時間。 這些`kube-dns`窗格將被刪除，庫伯內斯計畫程式然後重新創建它們。 這些新窗格包含 TTL 值中的更改。

```console
kubectl delete pod --namespace kube-system -l k8s-app=kube-dns
```

> [!Note]
> 上面的命令是正確的。 更改時`coredns`，部署位於**kube-dns**名稱下。

## <a name="custom-forward-server"></a>自訂轉發伺服器

如果需要為網路流量指定轉發伺服器，可以創建 ConfigMap 來自訂 DNS。 在下面的示例中，`forward`使用您自己的環境的值更新名稱和位址。 創建名為`corednsms.yaml`的檔並粘貼以下示例配置：

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        forward foo.com 1.1.1.1
    }
```

與前面的示例一樣，使用[kubectl 應用配置map][kubectl-apply]命令創建 ConfigMap 並指定 YAML 清單的名稱。 然後，強制 CoreDNS 使用庫[布ectl 刪除窗格][kubectl delete]重新載入庫貝內特斯計畫程式的 ConfigMap 以重新創建它們：

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="use-custom-domains"></a>使用自訂域

您可能希望配置只能在內部解析的自訂域。 例如，您可能希望解析自訂域*puglife.local*，這不是有效的頂層網域。 如果沒有自訂域 ConfigMap，AKS 群集無法解決該位址。

在下面的示例中，更新自訂域和 IP 位址，以使用您自己的環境的值將流量定向到。 創建名為`corednsms.yaml`的檔並粘貼以下示例配置：

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  puglife.server: |
    puglife.local:53 {
        errors
        cache 30
        forward . 192.11.0.1  # this is my test/dev DNS server
    }
```

與前面的示例一樣，使用[kubectl 應用配置map][kubectl-apply]命令創建 ConfigMap 並指定 YAML 清單的名稱。 然後，強制 CoreDNS 使用庫[布ectl 刪除窗格][kubectl delete]重新載入庫貝內特斯計畫程式的 ConfigMap 以重新創建它們：

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="stub-domains"></a>存根域

CoreDNS 還可用於配置存根域。 在下面的示例中，使用您自己的環境的值更新自訂域和 IP 位址。 創建名為`corednsms.yaml`的檔並粘貼以下示例配置：

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: |
    abc.com:53 {
        errors
        cache 30
        forward . 1.2.3.4
    }
    my.cluster.local:53 {
        errors
        cache 30
        forward . 2.3.4.5
    }

```

與前面的示例一樣，使用[kubectl 應用配置map][kubectl-apply]命令創建 ConfigMap 並指定 YAML 清單的名稱。 然後，強制 CoreDNS 使用庫[布ectl 刪除窗格][kubectl delete]重新載入庫貝內特斯計畫程式的 ConfigMap 以重新創建它們：

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="hosts-plugin"></a>主機外掛程式

由於支援所有內置外掛程式，這意味著 CoreDNS[主機][coredns hosts]外掛程式也可用於自訂：

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom # this is the name of the configmap you can overwrite with your changes
  namespace: kube-system
data:
    test.override: |
          hosts example.hosts example.org { # example.hosts must be a file
              10.0.0.1 example.org
              fallthrough
          }
```

## <a name="enable-logging-for-dns-query-debugging"></a>為 DNS 查詢調試啟用日誌記錄 

要啟用 DNS 查詢日誌記錄，請在核心 dns 自訂配置映射中應用以下配置：

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  log.override: |
        log
```

## <a name="next-steps"></a>後續步驟

本文展示了一些針對 CoreDNS 自訂的示例方案。 有關 CoreDNS 專案的資訊，請參閱[CoreDNS 上游專案頁面][coredns]。

要瞭解有關核心網路概念的更多資訊，請參閱[AKS 中應用程式的網路概念][concepts-network]。

<!-- LINKS - external -->
[kubednsblog]: https://www.danielstechblog.io/using-custom-dns-server-for-domain-specific-name-resolution-with-azure-kubernetes-service/
[coredns]: https://coredns.io/
[corednsk8s]: https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/#coredns
[dnscache]: https://coredns.io/plugins/cache/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[coredns hosts]: https://coredns.io/plugins/hosts/

<!-- LINKS - internal -->
[concepts-network]: concepts-network.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
