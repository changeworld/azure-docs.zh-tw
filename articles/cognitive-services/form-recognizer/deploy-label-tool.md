---
title: 如何部署表單辨識器範例圖章工具
titleSuffix: Azure Cognitive Services
description: 瞭解您可以部署表單辨識器範例圖章工具的不同方式，以協助進行監督式學習。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 3bb8f0e809ae1acbec1479c20e24c90fd81905d4
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2020
ms.locfileid: "85212440"
---
# <a name="deploy-the-sample-labeling-tool"></a>部署範例標籤工具

表單辨識器範例圖章工具是一種應用程式，提供簡單的使用者介面（UI），可讓您用來手動標記表單（檔）以供監督學習之用。 在本文中，我們將提供連結和指示，告訴您如何：

* [在本機執行範例標籤工具](#run-the-sample-labeling-tool-locally)
* [將範例標籤工具部署至 Azure 容器實例（ACI）](#deploy-with-azure-container-instances-aci)
* [使用並參與開放原始碼 OCR 表單標籤工具](#open-source-on-github)

## <a name="run-the-sample-labeling-tool-locally"></a>在本機執行範例標籤工具

開始標記資料最快的方式是在本機執行範例標籤工具。 下列快速入門會使用表單辨識器 REST API 和範例圖章工具，以手動標示的資料來定型自訂模型。 

* [快速入門：使用範例圖章工具來標記表單、定型模型，以及分析表單](./quickstarts/label-tool.md)。

## <a name="deploy-with-azure-container-instances-aci"></a>使用 Azure 容器實例（ACI）進行部署

開始之前，請務必注意，有兩種方式可將範例標籤工具部署至 Azure 容器實例（ACI）。 這兩個選項都是用來搭配 ACI 執行範例圖章工具： 

* [使用 Azure 入口網站](#azure-portal)
* [使用 Azure CLI](#azure-cli)

### <a name="azure-portal"></a>Azure 入口網站

請遵循下列步驟，使用 Azure 入口網站建立新的資源： 

1. 登入 [Azure 入口網站](https://portal.azure.com/signin/index/)。
2. 選取 [建立資源]。 
3. 接下來，選取 [ **Web 應用程式**]。 

   > [!div class="mx-imgBorder"]
   > ![選取 web 應用程式](./media/quickstarts/formre-create-web-app.png)
   
4. 首先，請確定已選取 [**基本**] 索引標籤。 現在，您需要提供一些資訊： 

   > [!div class="mx-imgBorder"]
   > ![選取基本概念](./media/quickstarts/formre-select-basics.png)
   * 訂用帳戶-選取現有的 Azure 訂用帳戶
   * 資源群組-您可以重複使用現有的資源群組，或為此專案建立一個新的。 建議您建立新的資源群組。
   * 名稱-為您的 web 應用程式提供名稱。 
   * 發佈-選取**Docker 容器**
   * 作業系統-選取 [ **Linux** ]
   * 區域-選擇對您有意義的區域。
   * Linux 方案-為您的 app service 選取定價層/方案。 

   > [!div class="mx-imgBorder"]
   > ![設定您的 web 應用程式](./media/quickstarts/formre-select-docker-linux.png)

5. 接下來，選取 [ **Docker** ] 索引標籤。 

   > [!div class="mx-imgBorder"]
   > ![選取 Docker](./media/quickstarts/formre-select-docker.png)

6. 現在讓我們來設定您的 Docker 容器。 除非另有注明，否則所有欄位都是必要的：

   * 選項-選取**單一容器**
   * 映射來源-選取 [**私人**登錄] 
   * 伺服器 URL-將此設定為`https://mcr.microsoft.com`
   * 使用者名稱（選用）-建立使用者名稱。 
   * 密碼（選擇性）-建立您要記住的安全密碼。
   * 影像和標記-將此設定為`mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest`
   * 持續部署-如果您想要在開發小組對範例標籤工具進行變更時接收自動更新，請將此設為 [**開啟**]。
   * 啟動命令-將此設定為`./run.sh eula=accept`

   > [!div class="mx-imgBorder"]
   > ![設定 Docker](./media/quickstarts/formre-configure-docker.png)

7. 這樣就大功告成了！ 接下來，選取 [**審查 + 建立**]，然後選取 [**建立**] 以部署您的 web 應用程式。 完成時，您可以在資源的**總覽**中提供的 URL 存取您的 web 應用程式。

> [!NOTE]
> 建立 web 應用程式時，您也可以設定授權/驗證。 這不是開始使用的必要條件。 

### <a name="azure-cli"></a>Azure CLI

除了使用 Azure 入口網站，您也可以使用 Azure CLI 來建立資源。 在繼續之前，您必須安裝[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 如果您已經在使用 Azure CLI，可以略過此步驟。 

關於此命令，您需要瞭解幾件事：

* `DNS_NAME_LABEL=aci-demo-$RANDOM`產生隨機 DNS 名稱。 
* 此範例假設您有可用來建立資源的資源群組。 取代為 `<resource_group_name>` 與您的訂用帳戶相關聯的有效資源群組。 
* 您必須指定要建立資源的位置。 將取代為 `<region name>` 您想要的 web 應用程式區域。 
* 此命令會自動接受 EULA。

從 Azure CLI 中，執行此命令以建立範例標籤工具的 web 應用程式資源： 

```azurecli
DNS_NAME_LABEL=aci-demo-$RANDOM

az container create \
  --resource-group <resource_group_name> \
  --name <name> \
  --image mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool \
  --ports 3000 \
  --dns-name-label $DNS_NAME_LABEL \
  --location <region name> \
  --cpu 2 \
  --memory 8 \
  --command-line "./run.sh eula=accept"
```

### <a name="connect-to-azure-ad-for-authorization"></a>連接到 Azure AD 以進行授權

建議您將 web 應用程式連線至 Azure Active Directory。 這可確保只有具備有效認證的使用者可以登入並使用您的 web 應用程式。 請遵循[設定您的 App Service 應用程式](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad)以連接到 Azure Active Directory 中的指示。

## <a name="open-source-on-github"></a>GitHub 上的開放原始碼

OCR 表單標籤工具也可作為 GitHub 上的開放原始碼專案。 此工具是使用 React + Redux 建置的 Web 應用程式，並以 TypeScript 撰寫。 若要深入了解或參與，請參閱 [OCR 表單標籤工具](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md)。

## <a name="next-steps"></a>後續步驟

使用 [[使用標籤定型](./quickstarts/label-tool.md)] 快速入門，以瞭解如何使用此工具來手動標示定型資料，並執行監督式學習。
