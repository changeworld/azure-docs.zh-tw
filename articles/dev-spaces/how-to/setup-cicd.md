---
title: 使用 CI/CD 搭配 Azure Dev Spaces
services: azure-dev-spaces
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: conceptual
manager: gwallace
description: 瞭解如何使用 Azure 開發人員空間使用 Azure DevOps 設定連續整合/連續部署
keywords: Docker、Kubernetes、Azure、AKS、Azure Container Service、容器
ms.openlocfilehash: f2eb9449518b32ab74f2dbbca6b5489aed325db7
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685622"
---
# <a name="use-cicd-with-azure-dev-spaces"></a>使用 CI/CD 搭配 Azure Dev Spaces

此文章將引導您在啟用 Dev Spaces 的情況下為 Azure Kubernetes Service (AKS) 設定持續整合/持續部署 (CI/CD)。 CI/CD 到 AKS 允許在將認可的程式碼推送至來源存放庫時，自動部署應用程式更新。 將 CI/CD 與啟用 Dev Spaces 的叢集搭配使用非常有用，因為它可以讓應用程式的基準保持最新，以便小組使用。

![CI/CD 圖表範例](../media/common/ci-cd-simple.png)

雖然此文章將引導您使用 Azure DevOps，但相同的概念適用於 Jenkins、TeamCity 等 CI/CD 系統。

## <a name="prerequisites"></a>Prerequisites
* [啟用 Azure Dev Spaces 的 Azure Kubernetes Service (AKS) 叢集](../get-started-netcore.md)
* [已安裝 Azure Dev Spaces CLI](upgrade-tools.md)
* [Azure DevOps 組織與專案](https://docs.microsoft.com/azure/devops/user-guide/sign-up-invite-teammates?view=vsts)
* [Azure Container Registry (ACR)](../../container-registry/container-registry-get-started-azure-cli.md)
    * 有 Azure Container Registry [系統管理員帳戶](../../container-registry/container-registry-authentication.md#admin-account)詳細資料可用
* [授權您的 AKS 叢集從 Azure Container Registry 提取](../../aks/cluster-container-registry-integration.md)

## <a name="download-sample-code"></a>下載範例程式碼
為了節省時間，我們將建立一個範例程式碼 GitHub 存放庫的分支。 移至 https://github.com/Azure/dev-spaces 並選取 [分支]****。 分支程序完成後，請在本機**複製**分支版本的存放庫。 根據預設，_主要_分支將會簽出，但我們在 _azds_updates_ 分支中包含了一些節省時間的變更，這些變更也應該在分支期間轉移。 _azds_updates_ 分支包含我們要求您在 Dev Spaces 教學課程小節中以手動方式進行的更新，以及一些預先製作的 YAML 和 JSON 檔案，以簡化 CI/CD 系統的部署。 您可以使用 `git checkout -b azds_updates origin/azds_updates` 之類的命令檢查本機存放庫中的 _azds_updates_ 分支。

## <a name="dev-spaces-setup"></a>Dev Spaces 設定
使用 `azds space select` 命令建立名為 _dev_ 的新空間。 您的 CI/CD 管線將使用 _dev_ 空間來推送您的程式碼變更。 它還將用於根據 _dev_ 建立 _子空間_。

```cmd
azds space select -n dev
```

當系統提示您選擇父開發空間時,_\<請\>選擇 「無_」。

創建開發空間後,需要確定主機後綴。 使用`azds show-context`命令顯示 Azure 開發空間入口控制器的主機後綴。

```cmd
$ azds show-context
Name   ResourceGroup    DevSpace  HostSuffix
-----  ---------------  --------  ----------------------
MyAKS  MyResourceGroup  dev       fedcba098.eus.azds.io
```

在上面的範例中,主機後綴_fedcba098.eus.azds.io_。 稍後將創建發佈定義時使用此值。

_dev_ 空間將始終包含存放庫的最新狀態 (即基線)，以便開發人員可以從 _dev_ 建立_子空間_，以測試較大應用程式之內容中的隔離變更。 此概念在 Dev Spaces 教學課程中會更詳細地討論。

## <a name="creating-the-build-definition"></a>建立組建定義
在網頁瀏覽器中開啟您的 Azure DevOps 小組專案，並瀏覽至 [管線]__ 區段。 首先，按一下 Azure DevOps 網站右上角的個人檔案相片、開啟 [預覽功能] 窗格，然後停用 [新的 YAML 管線建立體驗]__：

![正在開啟 [預覽功能] 窗格](../media/common/preview-feature-open.png)

要停用的選項：

![[新的 YAML 管線建立體驗] 選項](../media/common/yaml-pipeline-preview-feature.png)

> [!Note]
> 此時 Azure DevOps [新的 YAML 管線建立體驗]__ 預覽功能與建立預先定義的建置管線衝突。 您現在需要停用它才能部署我們預先定義的建置管線。

在 _azds_updates_ 分支中，我們包含了一個簡單的 [Azure 管線 YAML](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema?view=vsts&tabs=schema)，它定義了 *mywebapi* 和 *webfrontend* 所需的建置步驟。

根據您所選擇的語言，管線 YAML 已在類似於 `samples/dotnetcore/getting-started/azure-pipelines.dotnetcore.yml` 的路徑中簽入

若要從此檔案建立管線：
1. 在 DevOps 專案主頁上,導航到"生成>管道。
1. 選擇創建**新**管道的選項。
1. 選擇**GitHub**作為源,如有必要,使用 GitHub 帳戶進行授權,並從_開發空間_範例應用程式儲存庫的分叉版本中選擇_azds_updates_分支。
1. 選擇 **「設定」作為代碼**,或作為**YAML**作為範本。
1. 現在，您將看到建置管線的組態頁面。 如上所述,使用 **...** 按鈕導航到**YAML 檔路徑**的語言特定路徑。 例如： `samples/dotnetcore/getting-started/azure-pipelines.dotnet.yml` 。
1. 跳到 **"變數"** 選項卡。
1. 手動將 _dockerId_ 新增為變數，該變數是 [Azure Container Registry 系統管理員帳戶](../../container-registry/container-registry-authentication.md#admin-account)的使用者名稱。 (如文章先決條件中所述)
1. 手動將 _dockerPassword_ 新增為變數，該變數是 [Azure Container Registry 系統管理員帳戶](../../container-registry/container-registry-authentication.md#admin-account)的密碼。 基於安全性考量，請務必將 _dockerPassword_ 指定為祕密 (藉由選取鎖頭圖示)。
1. 選擇 **'儲存&佇列**。

您現在擁有一個 CI 解決方案，可以自動建置 *mywebapi* 和 *webfrontend*，以便將任何更新推送到 GitHub 分支的 _azds_updates_ 分支。 您可以通過瀏覽到 Azure 門戶、選擇 Azure 容器註冊表和流覽**儲存庫**選項卡來驗證 Docker 映射已被推送。映射可能需要幾分鐘才能生成並顯示在容器註冊表中。

![Azure Container Registry 存放庫](../media/common/ci-cd-images-verify.png)

## <a name="creating-the-release-definition"></a>建立發行定義

1. 在 DevOps 專案主頁面上，瀏覽至 [管線] > [發行]
1. 如果您正在使用尚未包含發行定義的全新 DevOps 專案，則必須先建立一個空的發行定義，然後再繼續。 除非您有現有發行定義，否則 [匯入] 選項不會顯示在 UI 中。
1. 在左側,按一下 **+ 新建**按鈕,然後按一下「**導入管道**」 。。
1. 點選 **「瀏覽」** 並從項目`samples/release.json`中選擇 。
1. 按一下 [確定]  。 請注意，會載入 [管線] 窗格並顯示 [發行定義編輯] 頁面。 另請注意，有一些紅色警告圖示表示仍然必須設定特定叢集的詳細資料。
1. 在 [管線] 窗格的左側，按一下 [新增成品]**** 泡泡。
1. 在 **「源**」下拉下清單中,選擇之前創建的生成管道。
1. 對於**預設版本**,**從具有標記的生成管道預設分支中選擇" 最新"**
1. 將**標記**留空。
1. 將 [來源別名]**** 設定為 `drop`。 **源別名**值由預定義的發佈任務使用,因此必須設置它。
1. 按一下 [新增]  。
1. 現在按一下新建立的 `drop` 成品來源上的閃電圖示，如下所示：

    ![發行成品持續部署設定](../media/common/release-artifact-cd-setup.png)
1. 開啟**連續部署觸發器**。
1. 將滑鼠懸停在**管道**旁邊的 **「工作」** 選項卡上,然後單擊 _「開發」_ 以編輯_開發_階段任務。
1. 在**連接類型**「下選擇」驗證**Azure 資源管理員**。 您會看到以紅色突顯的三個下拉控制器:![發佈定義設定](../media/common/release-setup-tasks.png)
1. 選擇使用 Azure 開發空間的 Azure 訂閱。 您可能還需要按下 **「授權**」。
1. 選擇使用 Azure 開發空間的資源組和群集。
1. 按下 **「代理作業**」。
1. 選擇**託管 Ubuntu 1604**下的**代理池**。
1. 將滑鼠懸停在頂部**的"任務"** 選擇器上,按一下_prod_以編輯_prod_階段任務。
1. 在**連接類型**「下選擇」驗證**Azure 資源管理員**。 選擇與 Azure 開發空間一起使用的 Azure 訂閱、資源組和叢集。
1. 按下 **「代理作業**」。
1. 選擇**託管 Ubuntu 1604**下的**代理池**。
1. 按下 **「變數」** 選項卡以更新版本的變數。
1. 將**DevSpacesHostSuffix**的值從**UPDATE_ME**更新到主機後綴。 在較早之前執行命令時,`azds show-context`將顯示主機後綴。
1. 按一下右上方的 [儲存]****，然後按一下 [確定]****。
1. 按一下 [+ 發行]**** ([儲存] 按鈕旁邊)，然後按一下 [建立發行]****。
1. 在 **「專案」** 下,從生成管道中驗證最新生成被選中。
1. 按一下頁面底部的 [新增]  。

自動化發行處理程序將立即開始，將 *mywebapi* 和 *webfrontend* 圖表部署至 _dev_ 最上層空間中的 Kubernetes 叢集。 您可以在 Azure DevOps Web 入口上監視發佈進度:

1. 導覽到導管下的 **「發布****」 部分**。
1. 按一下範例應用程式的發布管道。
1. 按一下最新版本的名稱。
1. 將滑鼠懸停在 **「階段」** 下的**開發**框上,然後按一下「**日誌**」。

發佈在所有任務完成後完成。

> [!TIP]
> 如果您的發行失敗，並顯示類似 *UPGRADE FAILED: timed out waiting for the condition* 的錯誤訊息，請嘗試[使用 Kubernetes 儀表板](../../aks/kubernetes-dashboard.md)檢查叢集中的 Pod。 如果您看到 pod 未能從錯誤訊息開始,如*無法提取影像「azdsexample.azurecr.io/mywebapi:122」:rpc 錯誤:代碼 = 未知 desc =\/來自守護程序的錯誤回應:獲取 HTW /azdsexample.azurecr.io/v2/mywebapi/manifests/122:未經授權的身份驗證:需要身份驗證*,可能是因為群集尚未授權從 Azure 容器註冊表中提取。 請確定您已完成[授權您的 AKS 叢集從 Azure Container Registry 提取](../../aks/cluster-container-registry-integration.md)的先決條件。

您現在擁有一個完全自動的 CI/CD 管線，用於 Dev Spaces 範例應用程式的 GitHub 分支。 每次認可並推送程式碼時，建置管線都會建置並將 *mywebapi* 和 *webfrontend* 映像推送至您的自訂 ACR 執行個體。 然後，發行管線會將每個應用程式的 Helm 圖表部署到啟用 Dev Spaces 之叢集上的 _dev_ 空間。

## <a name="accessing-your-_dev_-services"></a>存取您的 _dev_ 服務
部署之後，可以使用公用 URL 存取 _dev_ 版本的 *webfrontend*，例如：`http://dev.webfrontend.fedcba098.eus.azds.io`。 您可以透過`azds list-uri`執行 指令找到此網址: 

```cmd
$ azds list-uris

Uri                                           Status
--------------------------------------------  ---------
http://dev.webfrontend.fedcba098.eus.azds.io  Available
```

## <a name="deploying-to-production"></a>部署至生產環境

使用此教學課程中建立的 CI/CD 系統手動將特定發行升級到 _prod_：
1. 導覽到導管下的 **「發布****」 部分**。
1. 按一下範例應用程式的發布管道。
1. 按一下最新版本的名稱。
1. 將滑鼠懸停在 **「階段**」下的**prod**框上,然後按一下「**部署**」。
    ![升級到生產環境](../media/common/prod-promote.png)
1. 再次將滑鼠懸停在 **「階段」** 下的**prod**框上,然後按一下「**日誌**」。。

發佈在所有任務完成後完成。

CI/CD 管道的_prod_階段使用負載均衡器而不是開發人員空間入口控制器來提供對_prod_服務的訪問。 在_prod_階段部署的服務可作為 IP 位址而不是 DNS 名稱訪問。 在生產環境中,您可以選擇創建自己的入口控制器,以便根據自己的 DNS 配置託管服務。

要確定 Webfrontend 服務的 IP,請單擊 **「列印 Webfrontend 公共 IP」** 步驟以擴展日誌輸出。 使用日誌輸出中顯示的 IP 訪問**Webfrontend**應用程式。

```cmd
...
2019-02-25T22:53:02.3237187Z webfrontend can be accessed at http://52.170.231.44
2019-02-25T22:53:02.3320366Z ##[section]Finishing: Print webfrontend public IP
...
```

## <a name="dev-spaces-instrumentation-in-production"></a>生產環境中的 Dev Spaces 檢測
雖然 Dev Spaces 檢測設計為_不會_妨礙應用程式正常運作，但我們建議您在未啟用 Dev Spaces 的 Kubernetes 命名空間中執行生產工作負載。 使用這種類型的 Kubernetes 命名空間表示您應該使用 `kubectl` CLI 建立生產環境命名空間，或者允許您的 CI/CD 系統在第一次的 Helm 部署期間建立它。 _選取_或使用 Dev Spaces 工具建立空間，會將 Dev Spaces 檢測新增至該命名空間。

下面是一個範例命名空間結構，它支援功能開發、'dev'環境、_ 和 _ 生產環境，全都在單一的 Kubernetes 叢集中：

![範例命名空間的結構](../media/common/cicd-namespaces.png)

> [!Tip]
> 如果您已經建立 `prod` 空間，並且只想將其從 Dev Spaces 檢測中排除 (而不刪除它！)，則可以使用下列 Dev Spaces CLI 命令執行此操作：
>
> `azds space remove -n prod --no-delete`
>
> 執行此操作後，您可能需要刪除 `prod` 命名空間中的所有 Pod，以便可以在不使用 Dev Spaces 檢測的情況下重新建立它們。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解使用 Azure Dev Spaces 的小組開發](../team-development-netcore.md)