---
title: Azure 中的 OpenShift 容器平臺3.11 部署後工作
description: 部署 OpenShift 容器平臺3.11 叢集之後的其他工作。
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.custom: devx-track-ansible
ms.openlocfilehash: ba6fab2c2112c0e2c32e07965ededea817c37cbd
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86510594"
---
# <a name="post-deployment-tasks"></a>部署後工作

部署 OpenShift 叢集之後，您可以設定其他項目。 本文將說明：

- 如何使用 Azure Active Directory (Azure AD) 設定單一登入
- 如何設定 Azure 監視器記錄來監視 OpenShift
- 如何設定計量與記錄
- 如何安裝 Open Service Broker for Azure (OSBA)

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>使用 Azure Active Directory 設定單一登入

若要用 Azure Active Directory 進行驗證，請先建立 Azure AD 的應用程式註冊。 此程序牽涉兩個步驟：建立應用程式註冊，以及設定權限。

### <a name="create-an-app-registration"></a>建立應用程式註冊

這些步驟使用 Azure CLI 建立應用程式註冊，以及使用 GUI (入口網站) 設定權限。 若要建立應用程式註冊，您需要下列五項資訊：

- 顯示名稱：應用程式註冊名稱 (例如 OCPAzureAD)
- 首頁： OpenShift 主控台 URL （例如， `https://masterdns343khhde.westus.cloudapp.azure.com/console` ）
- 識別碼 URI： OpenShift 主控台 URL （例如， `https://masterdns343khhde.westus.cloudapp.azure.com/console` ）
- 回復 URL：主要公用 URL 和應用程式註冊名稱（例如， `https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD` ）
- 密碼：安全密碼 (使用強式密碼)

下列範例將會使用上述資訊建立應用程式註冊：

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com/console --password {Strong Password}
```

如果命令成功，您會獲得類似以下的 JSON 輸出：

```json
{
  "appId": "12345678-ca3c-427b-9a04-ab12345cd678",
  "appPermissions": null,
  "availableToOtherTenants": false,
  "displayName": "OCPAzureAD",
  "homepage": "https://masterdns343khhde.westus.cloudapp.azure.com/console",
  "identifierUris": [
    "https://masterdns343khhde.westus.cloudapp.azure.com/console"
  ],
  "objectId": "62cd74c9-42bb-4b9f-b2b5-b6ee88991c80",
  "objectType": "Application",
  "replyUrls": [
    "https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD"
  ]
}
```

記下命令在之後的步驟傳回的 appId 屬性。

在 Azure 入口網站中：

1. 選取 [ **Azure Active Directory**  >  **應用程式註冊**]。
2. 搜尋您的應用程式註冊 (例如 OCPAzureAD)。
3. 在結果中，按一下 [應用程式註冊]。
4. 在 [設定]**** 下，選取 [必要權限]****。
5. 在 [必要權限]**** 下，選取 [新增]****。

   ![App 註冊](media/openshift-post-deployment/app-registration.png)

6. 按一下「步驟 1：選取 API」，然後按一下 [Windows Azure Active Directory (Microsoft.Azure.ActiveDirectory)]****。 按一下底部的 [選取]****。

   ![應用程式註冊選取 API](media/openshift-post-deployment/app-registration-select-api.png)

7. 在「步驟 2：選取權限」，選取 [委派的權限]**** 底下的 [登入及讀取使用者個人檔案]****，然後按一下 [選取]****。

   ![應用程式註冊存取](media/openshift-post-deployment/app-registration-access.png)

8. 選取 [完成]。

### <a name="configure-openshift-for-azure-ad-authentication"></a>設定 OpenShift 進行 Azure AD 驗證

若要將 OpenShift 設定為使用 Azure AD 作為驗證提供者，必須在所有主要節點上編輯 /etc/origin/master/master-config.yaml 檔案。

使用下列 CLI 命令找到租用戶識別碼：

```azurecli
az account show
```

在 yaml 檔案中，尋找下列幾行：

```yaml
oauthConfig:
  assetPublicURL: https://masterdns343khhde.westus.cloudapp.azure.com/console/
  grantConfig:
    method: auto
  identityProviders:
  - challenge: true
    login: true
    mappingMethod: claim
    name: htpasswd_auth
    provider:
      apiVersion: v1
      file: /etc/origin/master/htpasswd
      kind: HTPasswdPasswordIdentityProvider
```

緊接在上述幾行之後，插入下列這幾行：

```yaml
  - name: <App Registration Name>
    challenge: false
    login: true
    mappingMethod: claim
    provider:
      apiVersion: v1
      kind: OpenIDIdentityProvider
      clientID: <appId>
      clientSecret: <Strong Password>
      claims:
        id:
        - sub
        preferredUsername:
        - unique_name
        name:
        - name
        email:
        - email
      urls:
        authorize: https://login.microsoftonline.com/<tenant Id>/oauth2/authorize
        token: https://login.microsoftonline.com/<tenant Id>/oauth2/token
```

請確定 identityProviders 下方的文字已正確對齊。 使用下列 CLI 命令找到租用戶識別碼：```az account show```

重新啟動所有主要節點上的 OpenShift 主要服務：

```bash
sudo /usr/local/bin/master-restart api
sudo /usr/local/bin/master-restart controllers
```

在 OpenShift 主控台中，您現在會看到兩個用於驗證的選項：htpasswd_auth 和 [應用程式註冊]。

## <a name="monitor-openshift-with-azure-monitor-logs"></a>使用 Azure 監視器記錄監視 OpenShift

有三種方式可將 Log Analytics 代理程式新增至 OpenShift。
- 直接在每個 OpenShift 節點上安裝適用於 Linux 的 Log Analytics 代理程式
- 在每個 OpenShift 節點上啟用 Azure 監視器 VM 擴充功能
- 將 Log Analytics 代理程式安裝為 OpenShift daemon-set

如需詳細資訊，請參閱完整[指示](../../azure-monitor/insights/containers.md#configure-a-log-analytics-agent-for-red-hat-openshift)。

## <a name="configure-metrics-and-logging"></a>設定計量與記錄

根據分支，適用於 OpenShift 容器平台和 OKD 的 Azure Resource Manager 範本可提供輸入參數，用以在安裝程序中啟用計量與記錄。

OpenShift 容器平台服務 Marketplace 供應項目也會提供在叢集安裝期間啟用計量和記錄的選項。

如果未在叢集安裝期間啟用計量/記錄功能，後續仍可輕鬆地加以啟用。

### <a name="azure-cloud-provider-in-use"></a>Azure 雲端提供者使用中

使用部署期間提供的認證，透過 SSH 連線至第一個主要節點 (根據使用中的範本和分支)。 發出下列命令：

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True \
-e openshift_logging_es_pvc_dynamic=true
```

### <a name="azure-cloud-provider-not-in-use"></a>Azure 雲端提供者未使用中

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True
```

## <a name="install-open-service-broker-for-azure-osba"></a>安裝 Open Service Broker for Azure (OSBA)

開啟 Service Broker for Azure (或稱 OSBA)，可讓您直接從 OpenShift 佈建 Azure 雲端服務。 Azure 的 Open Service Broker API 實作中的 OSBA。 Open Service Broker API 是一項規格，可為雲端提供者定義可供雲端原生應用程式直接用來管理雲端服務而不需要鎖定的通用語言。

若要在 OpenShift 上安裝 OSBA，請遵循此處的指示：https://github.com/Azure/open-service-broker-azure#openshift-project-template。 
> [!NOTE]
> 僅完成 OpenShift 專案範本一節中的步驟，而不是整個安裝區段。

## <a name="next-steps"></a>後續步驟

- [開始使用 OpenShift 容器平臺](https://docs.openshift.com)
