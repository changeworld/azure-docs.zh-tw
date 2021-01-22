---
title: 在 Azure 上建立 Pivotal Cloud Foundry 叢集
description: 深入了解如何設定在 Azure 上佈建 Pivotal Cloud Foundry (PCF) 叢集所需的參數
services: Cloud Foundry
documentationcenter: CloudFoundry
author: ruyakubu
manager: brunoborges
editor: ruyakubu
ms.assetid: ''
ms.author: ruyakubu
ms.date: 09/13/2018
ms.devlang: ''
ms.service: azure
ms.tgt_pltfrm: multiple
ms.topic: tutorial
ms.workload: web
ms.openlocfilehash: 735c0955a25a3995c94c73bd6471643ce2783df3
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98682609"
---
# <a name="create-a-pivotal-cloud-foundry-cluster-on-azure"></a>在 Azure 上建立 Pivotal Cloud Foundry 叢集

本教學課程提供快速步驟，以便建立及產生在 Azure 上佈建 Pivotal Cloud Foundry (PCF) 叢集所需的參數。 若要尋找 Pivotal Cloud Foundry 解決方案，請在 Azure [MarketPlace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry) 中執行搜尋。

![在 Azure 中搜尋 Pivotal Cloud Foundry](media/deploy/pcf-marketplace.png)


## <a name="generate-an-ssh-public-key"></a>產生 SSH 公開金鑰

使用 Windows、Mac 或 Linux 來產生公開安全殼層 (SSH) 金鑰的方式有很多種。

```Bash
ssh-keygen -t rsa -b 2048
```

如需詳細資訊，請參閱[在 Azure 上對 Windows 使用 SSH 金鑰](../virtual-machines/linux/ssh-from-windows.md)。

## <a name="create-a-service-principal"></a>建立服務主體

> [!NOTE]
>
> 若要建立服務主體，您需要擁有者帳戶權限。 您也可以撰寫指令碼來自動建立服務主體。 例如，您可以使用 Azure CLI [az ad sp create-for-rbac](/cli/azure/ad/sp)。

1. 登入您的 Azure 帳戶。

    ```azurecli
    az login
    ```

    ![Azure CLI 登入](media/deploy/az-login-output.png )
 
    複製 "id" 值作為您的 **訂用帳戶識別碼**，並複製 "tenantId" 值以供稍後使用。

2. 設定此組態的預設訂用帳戶。

    ```azurecli
    az account set -s {id}
    ```

3. 為您的 PCF 建立 Azure Active Directory 應用程式。 指定唯一的英數字元密碼。 儲存密碼作為 **clientSecret**，以供稍後使用。

    ```azurecli
    az ad app create --display-name "Svc Principal for OpsManager" --password {enter-your-password} --homepage "{enter-your-homepage}" --identifier-uris {enter-your-homepage}
    ```

    複製輸出中的 "appId" 值作為 **ClientID**，以供稍後使用。

    > [!NOTE]
    >
    > 選擇您自己的應用程式首頁和識別碼 URI，例如 http\://www\.contoso.com。

4. 使用新的應用程式識別碼建立服務主體。

    ```azurecli
    az ad sp create --id {appId}
    ```

5. 將您服務主體的權限角色設為 [參與者]。

    ```azurecli
    az role assignment create --assignee "{enter-your-homepage}" --role "Contributor"
    ```

    或者，您也可以使用

    ```azurecli
    az role assignment create --assignee {service-principal-name} --role "Contributor"
    ```

    ![服務主體角色指派](media/deploy/svc-princ.png )

6. 確認您可以使用應用程式識別碼、密碼和租用戶識別碼來成功登入服務主體。

    ```azurecli
    az login --service-principal -u {appId} -p {your-password}  --tenant {tenantId}
    ```

7. 建立下列格式的 .json 檔案。 使用您先前複製的 **訂用帳戶識別碼**、**tenantID**、**clientID** 及 **clientSecret** 值。 儲存檔案。

    ```json
    {
        "subscriptionID": "{enter-your-subscription-Id-here}",
        "tenantID": "{enter-your-tenant-Id-here}",
        "clientID": "{enter-your-app-Id-here}",
        "clientSecret": "{enter-your-key-here}"
    }
    ```

## <a name="get-the-pivotal-network-token"></a>取得 Pivotal Network 權杖

1. 註冊或登入您的 [Pivotal Network](https://network.pivotal.io) 帳戶。
2. 選取頁面右上角的設定檔名稱。 選取 [編輯設定檔]  。
3. 捲動至頁面底部，然後複製 [舊版 API 權杖]  值。 此值是您稍後會用到的 [Pivotal Network 權杖]  值。

## <a name="provision-your-cloud-foundry-cluster-on-azure"></a>在 Azure 上佈建 Cloud Foundry 叢集

現在您已經有了[在 Azure 上佈建 Pivotal Cloud Foundry 叢集](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry)所需的所有參數。
輸入參數，並建立您的 PCF 叢集。

## <a name="verify-the-deployment-and-sign-in-to-the-pivotal-ops-manager"></a>驗證部署並登入 Pivotal Ops Manager

1. 您的 PCF 叢集會顯示部署狀態。

    ![Azure 部署狀態](media/deploy/deployment.png )

2. 選取左側導覽中的 [部署]  連結，以取得您的 PCF Ops Manager 認證。 在下一頁選取 [部署名稱]  。
3. 在左側導覽中選取 [輸出]  連結，以顯示 PCF Ops Manager 的 URL、使用者名稱和密碼。 "OPSMAN-FQDN" 值是 URL。
 
    ![Cloud Foundry 部署輸出](media/deploy/deploy-outputs.png )
 
4. 在網頁瀏覽器中啟動 URL。 輸入上一個步驟中的認證進行登入。

    ![Pivotal 登入頁面](media/deploy/pivotal-login.png )
         
    > [!NOTE]
    >
    > 如果 Internet Explorer 瀏覽器由於「網站不安全」警告訊息而發生失敗，請選取 [詳細資訊]  並前往網頁。 若為 Firefox，選取 [進階]  並且新增憑證以繼續。

5. 您的 PCF Ops Manager 會顯示已部署的 Azure 執行個體。 您現在可以在此部署和管理應用程式。
               
    ![Pivotal 中已部署的 Azure 執行個體](media/deploy/ops-mgr.png )
