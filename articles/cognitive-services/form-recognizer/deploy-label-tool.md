---
title: 如何部署表單識別器示例標籤工具
titleSuffix: Azure Cognitive Services
description: 瞭解部署表單識別器示例標籤工具以説明進行監督學習的不同方法。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 03/20/2020
ms.author: pafarley
ms.openlocfilehash: 795d21e05ade652b52c06d597ca4c5fef85e7245
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80152797"
---
# <a name="deploy-the-sample-labeling-tool"></a>部署示例標籤工具

表單識別器示例標籤工具是一個應用程式，它提供了一個簡單的使用者介面 （UI），可用於手動標記表單（文檔），以便進行監督學習。 在本文中，我們將提供連結和說明，教您如何：

* [在本地運行示例圖章工具](#run-the-sample-labeling-tool-locally)
* [將示例圖章工具部署到 Azure 容器實例 （ACI）](#deploy-with-azure-container-instances-aci)
* [使用和貢獻開源 OCR 表單標籤工具](#open-source-on-github)

## <a name="run-the-sample-labeling-tool-locally"></a>在本地運行示例圖章工具

開始標記資料的最快方法是在本地運行示例圖章工具。 以下快速入門使用表單識別器 REST API 和示例圖章工具使用手動標記的資料訓練自訂模型。 

* [快速啟動：使用示例圖章工具標記表單、訓練模型和分析表單](./quickstarts/label-tool.md)。

## <a name="deploy-with-azure-container-instances-aci"></a>使用 Azure 容器實例 （ACI） 部署

在開始之前，請務必注意，有兩種方法可以將示例圖章工具部署到 Azure 容器實例 （ACI）。 這兩個選項都用於使用 ACI 運行示例圖章工具： 

* [使用 Azure 入口網站](#azure-portal)
* [使用 Azure CLI](#azure-cli)

### <a name="azure-portal"></a>Azure 入口網站

按照以下步驟使用 Azure 門戶創建新資源： 

1. 登錄到 Azure[門戶](https://portal.azure.com/signin/index/)。
2. 選擇 **"創建資源**"。 
3. 接下來，選擇**Web 應用程式**。 

   > [!div class="mx-imgBorder"]
   > ![選取 Web 應用程式](./media/quickstarts/formre-create-web-app.png)
   
4. 首先，請確保選擇了 **"基礎知識"** 選項卡。 現在，您需要提供一些資訊： 

   > [!div class="mx-imgBorder"]
   > ![選擇基礎知識](./media/quickstarts/formre-select-basics.png)
   * 訂閱 - 選擇現有的 Azure 訂閱
   * 資源組 - 您可以重用現有資源組或為此專案創建新資源組。 建議創建新的資源組。
   * 名稱 - 為 Web 應用指定名稱。 
   * 發佈 - 選擇**Docker 容器**
   * 作業系統 - 選擇**Linux**
   * 區域 - 選擇適合的區域。
   * Linux 計畫 - 為您的應用服務選擇定價層/計畫。 

   > [!div class="mx-imgBorder"]
   > ![配置 Web 應用](./media/quickstarts/formre-select-docker-linux.png)

5. 接下來，選擇**Docker**選項卡。 

   > [!div class="mx-imgBorder"]
   > ![選擇 Docker](./media/quickstarts/formre-select-docker.png)

6. 現在，讓我們配置 Docker 容器。 除非另有說明，否則所有欄位都是必需的：

   * 選項 - 選擇**單個容器**
   * 圖像源 - 選擇**專用註冊表** 
   * 伺服器 URL - 將此設置為`https://mcr.microsoft.com`
   * 使用者名（可選） - 創建使用者名。 
   * 密碼（可選） - 創建您記得的安全密碼。
   * 圖像和標記 - 將此設置為`mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest`
   * 啟動命令 - 將此設置為`./run.sh eula=accept`

   > [!div class="mx-imgBorder"]
   > ![設定 Docker](./media/quickstarts/formre-configure-docker.png)

7. 就這麼簡單。 接下來，選擇 **"查看 + 創建**"，然後**創建**以部署 Web 應用。 完成後，您可以在資源**概述**中提供的 URL 訪問 Web 應用。

> [!NOTE]
> 創建 Web 應用時，還可以配置授權/身份驗證。 這是沒有必要的開始。 

### <a name="azure-cli"></a>Azure CLI

作為使用 Azure 門戶的替代方法，可以使用 Azure CLI 創建資源。 在繼續之前，您需要安裝[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。 如果您已經在使用 Azure CLI，則可以跳過此步驟。 

關於此命令，您需要瞭解一些事項：

* `DNS_NAME_LABEL=aci-demo-$RANDOM`生成隨機 DNS 名稱。 
* 此示例假定您有一個可用於創建資源的資源組。 替換為`<resource_group_name>`與您的訂閱關聯的有效資源組。 
* 您需要指定要創建資源的位置。 替換為`<region name>`Web 應用所需的區域。 
* 此命令自動接受 EULA。

在 Azure CLI 中，運行此命令以創建示例標籤工具的 Web 應用資源： 

```azurecli
DNS_NAME_LABEL=aci-demo-$RANDOM

az container create \
  --resource-group <resorunce_group_name> \
  --name <name> \
  --image mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool \
  --ports 3000 \
  --dns-name-label $DNS_NAME_LABEL \
  --location <region name> \
  --cpu 2 \
  --memory 8
  --command-line "./run.sh eula=accept"
```

### <a name="connect-to-azure-ad-for-authorization"></a>連接到 Azure AD 進行授權

建議您將 Web 應用連接到 Azure 活動目錄。 這可確保只有具有有效憑據的使用者才能登錄並使用 Web 應用。 按照["配置應用服務"應用](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad)中的說明進行連接到 Azure 活動目錄。

## <a name="open-source-on-github"></a>GitHub 上的開源

OCR 表單標籤工具也可作為 GitHub 上的開源專案提供。 該工具是使用 React + Redux 構建的 Web 應用程式，在 TypeScript 中編寫。 要瞭解更多資訊或貢獻，請參閱[OCR 表單標籤工具](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md)。

## <a name="next-steps"></a>後續步驟

使用[帶有標籤的"訓練"](./quickstarts/label-tool.md)快速入門學習如何使用該工具手動標記訓練資料並執行監督學習。
