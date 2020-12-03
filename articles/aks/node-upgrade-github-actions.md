---
title: 使用 GitHub Actions 處理 AKS 節點升級
titleSuffix: Azure Kubernetes Service
description: 瞭解如何使用 GitHub Actions 來更新 AKS 節點
services: container-service
ms.topic: article
ms.date: 11/27/2020
ms.openlocfilehash: 7a24911fd771663c7edbbdf0c8d2d763a74fc586
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96535187"
---
# <a name="apply-security-updates-to-azure-kubernetes-service-aks-nodes-automatically-using-github-actions"></a>使用 GitHub Actions 自動將安全性更新套用至 Azure Kubernetes Service (AKS) 節點

安全性更新是維護 AKS 叢集安全性的重要部分，以及最新的基礎作業系統修正程式的合規性。 這些更新包括 OS 安全性修正或核心更新。 某些更新需要重新開機節點，才能完成此程式。

`az aks upgrade`執行可讓您以零停機的方式套用更新。 此命令會處理將最新的更新套用至所有叢集節點、干擾和清空節點的流量，以及重新開機節點，然後允許流向更新節點的流量。 如果您使用不同的方法來更新節點，AKS 將不會自動重新開機您的節點。

> [!NOTE]
> 搭配旗標使用時的主要差異在於， `az aks upgrade` `--node-image-only` 使用時，只會升級節點映射。 如果省略，則會升級節點映射和 Kubernetes 控制平面版本。 您可以在 [節點上檢查受管理的升級][managed-node-upgrades-article] 檔以及叢集 [升級][cluster-upgrades-article] 的檔，以取得更深入的資訊。

所有 Kubernetes 的節點都會在 (VM) 的標準 Azure 虛擬機器中執行。 這些 Vm 可以是 Windows 或以 Linux 為基礎。 以 Linux 為基礎的 Vm 會使用 Ubuntu 映射，並將作業系統設定為每晚自動檢查更新。

當您使用 `az aks upgrade` 命令時，Azure CLI 會以最新的安全性和核心更新建立新節點的激增，這些節點一開始會隔離，以避免在更新完成之前，將任何應用程式排程至這些節點。 完成之後，Azure 隔離 (會讓節點無法用於排程新的工作負載) 並清空 (將現有的工作負載移至其他節點) 較舊的節點，並 uncordon 新的節點，並將所有已排程的應用程式有效傳輸到新的節點。

此程式優於手動更新以 Linux 為基礎的核心，因為 Linux 需要在安裝新的核心更新時重新開機。 如果您手動更新 OS，也需要重新開機 VM、手動干擾和清空所有應用程式。

本文說明如何將 AKS 節點的更新程式自動化。 您將使用 GitHub Actions 和 Azure CLI，根據自動執行的來建立更新工作 `cron` 。

## <a name="before-you-begin"></a>開始之前

此文章假設您目前具有 AKS 叢集。 如果您需要 AKS 叢集，請參閱[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 入口網站][aks-quickstart-portal]的 AKS 快速入門。

您也必須安裝並設定 Azure CLI 2.0.59 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][install-azure-cli]。

本文也假設您有 [GitHub][github] 帳戶，可以在中建立您的動作。

## <a name="create-a-timed-github-action"></a>建立計時的 GitHub 動作

`cron` 是一個公用程式，可讓您以自動化排程執行一組命令或作業。 若要建立作業以自動排程更新您的 AKS 節點，您需要一個存放庫來裝載您的動作。 GitHub 動作通常會在與您應用程式相同的存放庫中設定，但您可以使用任何存放庫。 在本文中，我們將使用您的 [設定檔存放庫][profile-repository]。 如果您沒有帳戶，請使用與您的 GitHub 使用者名稱相同的名稱建立新的存放庫。

1. 在 GitHub 上流覽至您的存放庫
1. 按一下頁面頂端的 [ **動作** ] 索引標籤。
1. 如果您已經在此儲存機制中設定工作流程，系統會將您導向至已完成的執行清單，在此案例中，按一下 [ **新增工作流程** ] 按鈕。 如果這是您在存放庫中的第一個工作流程，GitHub 會向您顯示一些專案範本，然後按一下描述文字下方的 [ **設定工作流程** ] 連結。
1. 變更工作流程 `name` 和 `on` 標記，如下所示。 GitHub Actions 使用與任何以 Linux 為基礎的系統相同的 [POSIX cron 語法][cron-syntax] 。 在此排程中，我們會告知工作流程每隔15天執行3am。

    ```yml
    name: Upgrade cluster node images
    on:
      schedule:
        - cron: '0 3 */15 * *'
    ```

1. 使用下列步驟建立新的作業。 這項作業的名稱是 `upgrade-node` 在 Ubuntu 代理程式上執行，並會連接到您的 Azure CLI 帳戶，以執行升級節點所需的步驟。

    ```yml
    name: Upgrade cluster node images

    on:
      schedule:
        - cron: '0 3 */15 * *'

    jobs:
      upgrade-node:
        runs-on: ubuntu-latest
    ```

## <a name="set-up-the-azure-cli-in-the-workflow"></a>在工作流程中設定 Azure CLI

在機 `steps` 碼中，您將定義工作流程將執行以升級節點的所有工作。

下載並登入 Azure CLI。

1. 在 GitHub Actions 畫面的右側，尋找 *marketplace 搜尋* 列，然後輸入「 **Azure 登** 入」。
1. 您將會得到一個結果，也就是 **azure** 所發佈的 **Azure 登** 入動作：

      :::image type="content" source="media/node-upgrade-github-actions/azure-login-search.png" alt-text="搜尋結果顯示兩行，第一個動作稱為「Azure Login ' 和第二個」 Azure Container Registry Login '":::

1. 按一下 **[Azure 登** 入]。 在下一個畫面上，按一下程式碼範例右上方的 **複製圖示** 。

    :::image type="content" source="media/node-upgrade-github-actions/azure-login.png" alt-text="具有以下程式碼範例的 Azure 登入動作結果窗格，複製圖示周圍的紅色方塊會反白顯示按一下點":::

1. 在機碼下貼上下列 `steps` 內容：

      ```yml
      name: Upgrade cluster node images

      on:
        schedule:
          - cron: '0 3 */15 * *'

      jobs:
        upgrade-node:
          runs-on: ubuntu-latest

          steps:
            - name: Azure Login
              uses: Azure/login@v1.1
              with:
                creds: ${{ secrets.AZURE_CREDENTIALS }}
      ```

1. 從 Azure CLI 執行下列命令，以產生新的使用者名稱和密碼。

    ```azurecli-interactive
    az ad sp create-for-rbac -o json
    ```

    輸出應該類似下列 json：

    ```output
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-xxxx-xx-xx-xx-xx-xx",
      "name": "http://azure-cli-xxxx-xx-xx-xx-xx-xx",
      "password": "xXxXxXxXx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. **在新的瀏覽器視窗中** ，流覽至您的 GitHub 存放庫，然後開啟存放庫的 [ **設定** ] 索引標籤。 按一下 [ **秘密** ]，然後按一下 [ **新增存放庫秘密**]。
1. 針對 [名稱] 使用 `AZURE_CREDENTIALS`。
1.  針對 [ *值*]，在您建立新的使用者名稱和密碼的上一個步驟輸出中加入完整的內容。

    :::image type="content" source="media/node-upgrade-github-actions/azure-credential-secret.png" alt-text="顯示 AZURE_CREDENTIALS 為秘密標題的表單，以及已執行之命令的輸出，已貼入 JSON":::

1. 按一下 [ **新增秘密**]。

您的動作使用的 CLI 將會記錄到您的 Azure 帳戶，並準備好執行命令。

建立執行 Azure CLI 命令的步驟。

1. 在畫面右側流覽至 *GitHub marketplace* 上的 **搜尋頁面**，並搜尋 *Azure CLI 動作*。 選擇 *Azure 的 Azure CLI 動作*。

    :::image type="content" source="media/node-upgrade-github-actions/azure-cli-action.png" alt-text="搜尋 ' Azure CLI Action ' 的結果，並顯示 Azure 所顯示的第一個結果":::

1. 按一下 *GitHub marketplace 結果* 上的 [複製] 按鈕，然後在主要編輯器的 [ *Azure 登* 入] 步驟下方，貼上動作的內容，如下所示：

    ```yml
    name: Upgrade cluster node images

    on:
      schedule:
        - cron: '0 3 */15 * *'

    jobs:
      upgrade-node:
        runs-on: ubuntu-latest

        steps:
          - name: Azure Login
            uses: Azure/login@v1.1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}
          - name: Upgrade node images
            uses: Azure/cli@v1.0.0
            with:
              inlineScript: az aks upgrade -g {resourceGroupName} -n {aksClusterName} --node-image-only
    ```

    > [!TIP]
    > 您可以藉 `-g` `-n` 由將和參數新增至類似先前步驟的秘密，將這些參數從命令分離出來。 `{resourceGroupName}` `{aksClusterName}` 以其秘密對應專案取代和預留位置，例如 `${{secrets.RESOURCE_GROUP_NAME}}` 和`${{secrets.AKS_CLUSTER_NAME}}`

1. 將檔案重新命名為 `upgrade-node-images`。
1. 按一下 [ **開始認可**]，加入訊息標題，然後儲存工作流程。

當您建立認可之後，工作流程將會儲存，並可供執行。

> [!NOTE]
> 若要升級單一節點集區，而不是叢集中的所有節點集區，請將 `--name` 參數新增至 `az aks nodepool upgrade` 命令以指定節點集區名稱。 例如︰
> ```
> inlineScript: az aks nodepool upgrade -g {resourceGroupName} --cluster-name {aksClusterName} --name {{nodePoolName}} --node-image-only
> ```

## <a name="run-the-github-action-manually"></a>手動執行 GitHub 動作

您可以藉由新增名為的新觸發程式，以手動方式執行工作流程（除了排定的執行之外） `on` `workflow_dispatch` 。 完成的檔案看起來應該像下面的 YAML：

```yml
name: Upgrade cluster node images

on:
  schedule:
    - cron: '0 3 */15 * *'
  workflow_dispatch:

jobs:
  upgrade-node:
    runs-on: ubuntu-latest

    steps:
      - name: Azure Login
        uses: Azure/login@v1.1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      # Code for upgrading one or more node pools
```

## <a name="next-steps"></a>後續步驟

- 如需最新節點映射的詳細資訊，請參閱 [AKS 版本](https://github.com/Azure/AKS/releases) 資訊。
- 瞭解如何使用 [upgrade a AKS cluster][cluster-upgrades-article]升級 Kubernetes 版本。
- 深入瞭解多個節點集區，以及如何使用 [建立和管理多個節點][use-multiple-node-pools]集區來升級節點集區。
- 深入瞭解[系統節點][system-pools]集區
- 若要瞭解如何使用點實例來節省成本，請參閱 [將點節點集區新增至 AKS][spot-pools]

<!-- LINKS - external -->
[github]: https://github.com
[profile-repository]: https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-github-profile/about-your-profile
[cron-syntax]: https://pubs.opengroup.org/onlinepubs/9699919799/utilities/crontab.html#tag_20_25_07

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[managed-node-upgrades-article]: node-image-upgrade.md
[cluster-upgrades-article]: upgrade-cluster.md
[system-pools]: use-system-pools.md
[spot-pools]: spot-node-pool.md
[use-multiple-node-pools]: use-multiple-node-pools.md
