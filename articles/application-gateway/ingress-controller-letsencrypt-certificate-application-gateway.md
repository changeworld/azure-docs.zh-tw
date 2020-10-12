---
title: 搭配應用程式閘道使用 LetsEncrypt.org 憑證
description: 本文提供有關如何從 LetsEncrypt.org 取得憑證，並在適用于 AKS 叢集的應用程式閘道上使用憑證的資訊。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: df8722e8160538daa1535711092790dbb2405097
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84807024"
---
# <a name="use-certificates-with-letsencryptorg-on-application-gateway-for-aks-clusters"></a>在 AKS 叢集的應用程式閘道上搭配 LetsEncrypt.org 使用憑證

此區段會將您的 AKS 設定為利用 [LetsEncrypt.org](https://letsencrypt.org/) ，並自動取得您網域的 TLS/SSL 憑證。 憑證將會安裝在應用程式閘道上，這會對您的 AKS 叢集執行 SSL/TLS 終止。 此處所述的設定使用 [cert 管理員](https://github.com/jetstack/cert-manager) Kubernetes 附加元件，可自動建立及管理憑證。

請遵循下列步驟，在您現有的 AKS 叢集上安裝 [cert manager](https://docs.cert-manager.io) 。

1. Helm 圖

    執行下列腳本來安裝 `cert-manager` helm 圖。 這將會：

    - `cert-manager`在 AKS 上建立新的命名空間
    - 建立下列 CRDs：憑證、挑戰、ClusterIssuer、簽發者、訂單
    - 從[docs.cert-manager.io) ](https://docs.cert-manager.io/en/latest/getting-started/install/kubernetes.html#steps)安裝 cert manager 圖表 (

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

    建立 `ClusterIssuer` 資源。 這是必要的， `cert-manager` 代表 `Lets Encrypt` 將取得已簽署憑證的憑證授權單位單位。

    Cert 管理員會使用非命名空間的 `ClusterIssuer` 資源，發出可從多個命名空間取用的憑證。 `Let’s Encrypt` 使用 ACME 通訊協定來驗證您是否控制指定的功能變數名稱，併發出憑證給您。 在這裡設定屬性的詳細資料 `ClusterIssuer` 。 [here](https://docs.cert-manager.io/en/latest/tasks/issuers/index.html) `ClusterIssuer` 會指示 `cert-manager` 使用 `Lets Encrypt` 用於測試的預備環境來發出憑證 () 的瀏覽器/用戶端信任存放區中不會有根憑證。

    下列 YAML 中的預設挑戰類型為 `http01` 。 其他挑戰記載于 [letsencrypt.org-挑戰類型](https://letsencrypt.org/docs/challenge-types/)

    > [!IMPORTANT] 
    > `<YOUR.EMAIL@ADDRESS>`下列 YAML 中的更新

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

    `guestbook`使用「讓加密憑證」的應用程式閘道，建立輸入資源來公開應用程式。

    確定您的應用程式閘道具有具有 DNS 名稱的公用前端 IP 設定 (使用預設 `azure.com` 網域或布建 `Azure DNS Zone` 服務，並將您自己的自訂網域) 。
    請注意注釋 `certmanager.k8s.io/cluster-issuer: letsencrypt-staging` ，這會告知 cert 管理員處理已標記的輸入資源。

    > [!IMPORTANT] 
    > `<PLACEHOLDERS.COM>`使用您自己的網域 (或應用程式閘道（例如 ' kh-aks-ingress.westeurope.cloudapp.azure.com '），在下列 YAML 中進行更新 ) 

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

    幾秒鐘之後，您就可以 `guestbook` 使用自動發行的 **臨時**憑證，透過應用程式閘道 HTTPS url 來存取服務 `Lets Encrypt` 。
    您的瀏覽器可能會警告您不正確憑證授權單位單位。 暫存憑證是由發出 `CN=Fake LE Intermediate X1` 。 這表示系統會如預期般運作，而且您已準備好使用您的生產環境憑證。

4. 生產憑證

    一旦您的預備憑證設定成功，您就可以切換到生產的 ACME 伺服器：
    1. 以下列內容取代輸入資源上的預備註釋： `certmanager.k8s.io/cluster-issuer: letsencrypt-prod`
    1. 將 `ClusterIssuer` 您在上一個步驟中建立的現有暫存刪除，並從上述的 CLUSTERISSUER YAML 取代 ACME 伺服器，以建立新的暫存。 `https://acme-v02.api.letsencrypt.org/directory`

5. 憑證到期和更新

    `Lets Encrypt`憑證到期之前， `cert-manager` 會自動更新 Kubernetes 秘密存放區中的憑證。 在該時間點，應用程式閘道輸入控制器會套用其用來設定應用程式閘道之輸入資源中所參考的更新秘密。
