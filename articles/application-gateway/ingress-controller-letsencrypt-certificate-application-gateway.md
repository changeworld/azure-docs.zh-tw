---
title: 搭配應用程式閘道使用 LetsEncrypt.org 憑證
description: 本文提供有關如何從 LetsEncrypt.org 取得憑證，並在您的應用程式閘道上用於 AKS 叢集的資訊。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 92e9747865f1a0910c8bae4001cc597ae9ea3da6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "73957976"
---
# <a name="use-certificates-with-letsencryptorg-on-application-gateway-for-aks-clusters"></a>在 AKS 叢集的應用程式閘道上搭配 LetsEncrypt.org 使用憑證

本節會將您的 AKS 設定為利用[LetsEncrypt.org](https://letsencrypt.org/) ，並自動為您的網域取得 TLS/SSL 憑證。 憑證會安裝在應用程式閘道上，這會針對您的 AKS 叢集執行 SSL/TLS 終止。 此處所述的安裝程式使用[cert-manager](https://github.com/jetstack/cert-manager) Kubernetes 附加元件，可將憑證的建立和管理自動化。

請遵循下列步驟，在現有的 AKS 叢集上安裝[cert manager](https://docs.cert-manager.io) 。

1. Helm 圖表

    執行下列腳本來安裝`cert-manager` helm 圖表。 這將會：

    - 在您的`cert-manager` AKS 上建立新的命名空間
    - 建立下列 CRDs：憑證、挑戰、ClusterIssuer、簽發者、訂單
    - 安裝 cert-manager 圖表（來自[docs.cert-manager.io）](https://docs.cert-manager.io/en/latest/getting-started/install/kubernetes.html#steps)

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

2. ClusterIssuer 資源

    建立`ClusterIssuer`資源。 需要此項`cert-manager`才能代表將取得`Lets Encrypt`已簽署憑證的憑證授權單位單位。

    藉由使用非命名空間`ClusterIssuer`資源，cert manager 會發行可從多個命名空間取用的憑證。 `Let’s Encrypt`會使用 ACME 通訊協定來驗證您是否控制了指定的功能變數名稱，併發出憑證給您。 這裡有更多`ClusterIssuer`關於[here](https://docs.cert-manager.io/en/latest/tasks/issuers/index.html)設定屬性的詳細資料。 `ClusterIssuer`會指示`cert-manager`使用用於測試的`Lets Encrypt`預備環境（不存在於瀏覽器/用戶端信任存放區中的根憑證）來頒發證書。

    下列 YAML 中的預設挑戰類型為`http01`。 其他挑戰記載于[letsencrypt.org-挑戰類型](https://letsencrypt.org/docs/challenge-types/)

    > [!IMPORTANT] 
    > 下列`<YOUR.EMAIL@ADDRESS>` YAML 中的更新

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

    建立輸入資源，以使用應用程式閘道`guestbook`搭配「讓加密憑證」來公開應用程式。

    請確定您應用程式閘道具有具有 DNS 名稱（使用預設`azure.com`網域或布建`Azure DNS Zone`服務，並指派您自己的自訂網域）的公用前端 IP 設定。
    請注意批註`certmanager.k8s.io/cluster-issuer: letsencrypt-staging`，它會告知 cert 管理員處理標記的輸入資源。

    > [!IMPORTANT] 
    > 在`<PLACEHOLDERS.COM>`下列 YAML 中，使用您自己的網域（或應用程式閘道一個，例如 ' kh-aks-ingress.westeurope.cloudapp.azure.com '）更新

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

    幾秒鐘之後，您就可以使用自動`guestbook`發行的**暫存** `Lets Encrypt`憑證，透過應用程式閘道 HTTPS url 來存取服務。
    您的瀏覽器可能會警告您有不正確憑證授權單位單位。 暫存憑證是由`CN=Fake LE Intermediate X1`所發行。 這表示系統已如預期般運作，而且您已準備好使用您的生產環境憑證。

4. 生產憑證

    一旦成功設定您的暫存憑證，您就可以切換到生產的 ACME 伺服器：
    1. 將輸入資源上的預備註釋取代為：`certmanager.k8s.io/cluster-issuer: letsencrypt-prod`
    1. 刪除您在上`ClusterIssuer`一個步驟中建立的現有暫存，並藉由將 ClusterIssuer YAML 中的 ACME 伺服器取代為，以建立新的預備環境`https://acme-v02.api.letsencrypt.org/directory`

5. 憑證到期和更新

    `Lets Encrypt`憑證過期之前， `cert-manager`會自動更新 Kubernetes 秘密存放區中的憑證。 此時，應用程式閘道輸入控制器會套用用來設定應用程式閘道的輸入資源中所參考的更新密碼。
