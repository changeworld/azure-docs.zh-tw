---
title: 將LetsEncrypt.org證書與應用程式閘道一起使用
description: 本文提供有關如何從 LetsEncrypt.org 獲取證書並在 AKS 群集的應用程式閘道上使用它的資訊。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 92e9747865f1a0910c8bae4001cc597ae9ea3da6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73957976"
---
# <a name="use-certificates-with-letsencryptorg-on-application-gateway-for-aks-clusters"></a>在 AKS 群集的應用程式閘道上使用具有LetsEncrypt.org證書

本節將 AKS 配置為利用[LetsEncrypt.org](https://letsencrypt.org/)並自動為您的域獲取 TLS/SSL 憑證。 證書將安裝在應用程式閘道上，它將對 AKS 群集執行 SSL/TLS 終止。 此處描述的設置使用[證書管理器](https://github.com/jetstack/cert-manager)Kubernetes 載入項，它自動創建和管理證書。

按照以下步驟在現有的 AKS 群集上安裝[證書管理器](https://docs.cert-manager.io)。

1. 赫爾姆圖

    運行以下腳本以安裝`cert-manager`掌舵圖。 這將會：

    - 在 AKS 上創建新`cert-manager`的命名空間
    - 創建以下 CRD：證書、挑戰、群集頒發者、頒發者、訂單
    - 安裝證書管理器圖表（從[docs.cert-manager.io）](https://docs.cert-manager.io/en/latest/getting-started/install/kubernetes.html#steps)

    ```bash
    #!/bin/bash

    # Install the CustomResourceDefinition resources separately
    kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.8/deploy/manifests/00-crds.yaml

    # Create the namespace for cert-manager
    kubectl create namespace cert-manager

    # Label the cert-manager namespace to disable resource validation
    kubectl label namespace cert-manager certmanager.k8s.io/disable-validation=true

    # Add the Jetstack Helm repository
    helm repo add jetstack https://charts.jetstack.io

    # Update your local Helm chart repository cache
    helm repo update

    # Install the cert-manager Helm chart
    helm install \
      --name cert-manager \
      --namespace cert-manager \
      --version v0.8.0 \
      jetstack/cert-manager
    ```

2. 群集頒發者資源

    創建`ClusterIssuer`資源。 `cert-manager`它需要表示將獲取已簽章憑證`Lets Encrypt`的憑證授權單位。

    通過使用非命名速度`ClusterIssuer`資源，證書管理器將頒發可以從多個命名空間使用的證書。 `Let’s Encrypt`使用 ACME 協定驗證您控制給定的功能變數名稱並頒發證書。 有關`ClusterIssuer`[在此處](https://docs.cert-manager.io/en/latest/tasks/issuers/index.html)配置屬性的更多詳細資訊。 `ClusterIssuer`將指示`cert-manager`使用用於測試的`Lets Encrypt`暫存環境（瀏覽器/用戶端信任存儲中不存在的根憑證）頒發證書。

    下面的 YAML 中的預設質詢類型是`http01`。 其他挑戰記錄[在letsencrypt.org - 挑戰類型](https://letsencrypt.org/docs/challenge-types/)

    > [!IMPORTANT] 
    > 下面的`<YOUR.EMAIL@ADDRESS>`YAML 更新

    ```bash
    #!/bin/bash
    kubectl apply -f - <<EOF
    apiVersion: certmanager.k8s.io/v1alpha1
    kind: ClusterIssuer
    metadata:
    name: letsencrypt-staging
    spec:
    acme:
        # You must replace this email address with your own.
        # Let's Encrypt will use this to contact you about expiring
        # certificates, and issues related to your account.
        email: <YOUR.EMAIL@ADDRESS>
        # ACME server URL for Let’s Encrypt’s staging environment.
        # The staging environment will not issue trusted certificates but is
        # used to ensure that the verification process is working properly
        # before moving to production
        server: https://acme-staging-v02.api.letsencrypt.org/directory
        privateKeySecretRef:
        # Secret resource used to store the account's private key.
        name: example-issuer-account-key
        # Enable the HTTP-01 challenge provider
        # you prove ownership of a domain by ensuring that a particular
        # file is present at the domain
        http01: {}
    EOF
    ```

3. 部署應用程式

    創建入口資源以使用具有"允許`guestbook`加密證書"的應用程式閘道公開應用程式。

    確保應用程式閘道具有具有 DNS 名稱的公共前端 IP 配置（使用預設`azure.com`域或預配`Azure DNS Zone`服務，並分配自己的自訂域）。
    請注意注釋`certmanager.k8s.io/cluster-issuer: letsencrypt-staging`，它告訴證書管理器處理標記的入口資源。

    > [!IMPORTANT] 
    > 使用`<PLACEHOLDERS.COM>`您自己的域（或應用程式閘道之一，例如"kh-aks-ingress.westeurope.cloudapp.azure.com"）在下面的 YAML 中進行更新

    ```bash
    kubectl apply -f - <<EOF
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
    name: guestbook-letsencrypt-staging
    annotations:
        kubernetes.io/ingress.class: azure/application-gateway
        certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    spec:
    tls:
    - hosts:
        - <PLACEHOLDERS.COM>
        secretName: guestbook-secret-name
    rules:
    - host: <PLACEHOLDERS.COM>
        http:
        paths:
        - backend:
            serviceName: frontend
            servicePort: 80
    EOF
    ```

    幾秒鐘後，您可以使用自動頒發的**暫存**`Lets Encrypt`證書`guestbook`通過應用程式閘道 HTTPS URL 訪問服務。
    您的瀏覽器可能會警告您證書授權無效。 暫存證書由 頒發`CN=Fake LE Intermediate X1`。 這表明系統按預期工作，您已準備好購買生產證書。

4. 生產證書

    成功設置暫存證書後，可以切換到生產 ACME 伺服器：
    1. 將入口資源上的暫存注釋替換為：`certmanager.k8s.io/cluster-issuer: letsencrypt-prod`
    1. 刪除您在上一`ClusterIssuer`步中創建的現有暫存，並通過從上述群集頒發機構 YAML 替換 ACME 伺服器來創建新的暫存`https://acme-v02.api.letsencrypt.org/directory`

5. 證書過期和續訂

    在`Lets Encrypt`證書過期之前，`cert-manager`將自動更新 Kubernetes 金鑰存儲中的證書。 此時，應用程式閘道入口控制器將應用其用於配置應用程式閘道的入口資源中引用的更新金鑰。
