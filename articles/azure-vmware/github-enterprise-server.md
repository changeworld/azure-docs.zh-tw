---
title: 在您的 Azure VMware 解決方案私人雲端上設定 GitHub Enterprise Server
description: 瞭解如何在 Azure VMware 解決方案私人雲端上設定 GitHub Enterprise Server。
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 53e5264eed761909217c2e3a902c9fee9faaffaa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91341897"
---
# <a name="set-up-github-enterprise-server-on-your-azure-vmware-solution-private-cloud"></a>在您的 Azure VMware 解決方案私人雲端上設定 GitHub Enterprise Server

在本文中，我們將逐步解說在您的 Azure VMware 解決方案私人雲端上設定 GitHub Enterprise Server （ [GitHub.com](https://github.com/)的「內部部署」版本）的步驟。 本逐步解說中所涵蓋的案例適用于可提供高達3000開發人員的 GitHub Enterprise 伺服器實例，每分鐘最多可在 GitHub Actions 上執行25個作業。 它包括在撰寫) *預覽* 功能（例如 GitHub Actions）時的 (設定。 若要根據您的特定需求自訂安裝程式，請參閱在 [VMware 上安裝 GitHub Enterprise Server](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations)中所列的需求。

## <a name="before-you-begin"></a>開始之前

GitHub Enterprise Server 需要有效的授權金鑰。 您可以註冊 [試用版授權](https://enterprise.github.com/trial)。 如果您想要透過整合來延伸 GitHub Enterprise Server 的功能，您可能有資格獲得免費的五基座開發人員授權。 透過 [GitHub 的合作夥伴方案](https://partner.github.com/)申請此授權。

## <a name="installing-github-enterprise-server-on-vmware"></a>在 VMware 上安裝 GitHub Enterprise Server

下載 [GitHub Enterprise Server 的目前版本](https://enterprise.github.com/releases/2.19.0/download) ，以取得 VMware ESXi/VSPHERE (OVA) ，並部署您下載 [的 OVA 範本](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html) 。

:::image type="content" source="media/github-enterprise-server/github-options.png" alt-text="選擇在內部部署或雲端中執行 GitHub。":::  

:::image type="content" source="media/github-enterprise-server/deploy-ova-template.png" alt-text="選擇在內部部署或雲端中執行 GitHub。":::  

為您的新虛擬機器提供可辨識的名稱，例如 GitHubEnterpriseServer。 您不需要在 VM 名稱中包含發行詳細資料，因為當升級實例時，這些詳細資料會變成過時。 選取所有預設值，現在 (我們稍後會編輯這些詳細資料) 並等待匯入 OVA。

匯入之後，請根據您 [的需求調整硬體設定](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#creating-the-github-enterprise-server-instance) 。 在我們的範例案例中，我們需要下列設定。

| 資源 | 標準設定 | 標準設定 + 「Beta 功能」 (動作)  |
| --- | --- | --- |
| vCPU | 4 | 8 |
| Memory | 32 GB | 61 GB |
| 連接的儲存體 | 250 GB | 300 GB |
| 根儲存體 | 200 GB | 200 GB |

不過，您的需求可能會有所不同。 請參閱在 [VMware 上安裝 GitHub Enterprise Server](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations)的硬體考慮指引。 另請參閱 [新增 VMware 的 CPU 或記憶體資源](https://docs.github.com/en/enterprise/admin/enterprise-management/increasing-cpu-or-memory-resources#adding-cpu-or-memory-resources-for-vmware) ，以根據您的情況自訂硬體設定。

## <a name="configuring-the-github-enterprise-server-instance"></a>設定 GitHub Enterprise 伺服器實例

:::image type="content" source="media/github-enterprise-server/install-github-enterprise.png" alt-text="選擇在內部部署或雲端中執行 GitHub。":::  

在新布建的虛擬機器 (VM) 開啟電源之後，請透過 [您的瀏覽器進行設定](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#configuring-the-github-enterprise-server-instance)。 您必須上傳您的授權檔案並設定管理主控台密碼。 請務必將此密碼寫在安全的地方。

:::image type="content" source="media/github-enterprise-server/ssh-access.png" alt-text="選擇在內部部署或雲端中執行 GitHub。":::    

建議您至少採取下列步驟：

1. 將公開 SSH 金鑰上傳至管理主控台，讓您可以透過 [SSH 存取系統管理命令](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh)介面。 

2. [在您的實例上設定 TLS](https://docs.github.com/en/enterprise/admin/configuration/configuring-tls) ，讓您可以使用由受信任的憑證授權單位單位所簽署的憑證。

:::image type="content" source="media/github-enterprise-server/configuring-your-instance.png" alt-text="選擇在內部部署或雲端中執行 GitHub。":::

套用您的設定。  當實例重新開機時，您可以繼續進行下一個步驟， **為 GitHub Actions 設定 Blob 儲存體**。

:::image type="content" source="media/github-enterprise-server/create-admin-account.png" alt-text="選擇在內部部署或雲端中執行 GitHub。":::

實例重新開機之後，在實例上建立新的系統管理員帳戶。 也請務必記下此使用者的密碼。

### <a name="additional-configuration-steps"></a>其他設定步驟

若要強化您的實例以供生產環境使用，建議使用下列選用的設定步驟：

1. 設定用於保護的 [高可用性](https://help.github.com/enterprise/admin/guides/installation/configuring-github-enterprise-for-high-availability/) ：

    - 軟體損毀 (OS 或應用層級) 
    - 硬體故障 (儲存體、CPU、RAM 等等) 
    - 虛擬化主機系統失敗
    - 邏輯或物理地切斷網路

2. [設定](https://docs.github.com/en/enterprise/admin/configuration/configuring-backups-on-your-appliance)[備份公用程式](https://github.com/github/backup-utils)，為損毀修復提供已建立版本的快照集，其裝載于與主要實例不同的可用性。
3. 使用有效的 TLS 憑證[設定子域隔離](https://docs.github.com/en/enterprise/admin/configuration/enabling-subdomain-isolation)，以降低跨網站腳本和其他相關弱點。

## <a name="configuring-blob-storage-for-github-actions"></a>針對 GitHub Actions 設定 blob 儲存體

> [!NOTE]
> GitHub Actions [目前以 GitHub Enterprise Server 2.22 版的有限 Beta 版提供](https://docs.github.com/en/enterprise/admin/github-actions)。

您必須要有外部 blob 儲存體，才能在 GitHub Enterprise Server (上啟用 GitHub Actions，目前提供為「搶鮮版（Beta）」功能) 。 此外部 blob 儲存體是由動作用來儲存成品和記錄。 GitHub Enterprise Server 上 [的動作支援將 Azure Blob 儲存體作為存放裝置提供者](https://docs.github.com/en/enterprise/admin/github-actions/enabling-github-actions-and-configuring-storage#about-external-storage-requirements) (以及其他) 。 因此，我們將布建具有 [儲存體帳戶類型](https://docs.microsoft.com/azure/storage/common/storage-account-overview?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#types-of-storage-accounts) BlobStorage 的新 Azure 儲存體帳戶：

:::image type="content" source="media/github-enterprise-server/storage-account.png" alt-text="選擇在內部部署或雲端中執行 GitHub。":::

當新的 BlobStorage 資源部署完成之後，請複製並記下連接字串， (可在 [存取金鑰]) 下取得。 我們很快就需要這個字串。

## <a name="setting-up-the-github-actions-runner"></a>設定 GitHub Actions 執行器

> [!NOTE]
> GitHub Actions [目前以 GitHub Enterprise Server 2.22 版的有限 Beta 版提供](https://docs.github.com/en/enterprise/admin/github-actions)。

此時，您應該會有一個執行 GitHub Enterprise Server 的實例，並已建立系統管理員帳戶。 您也應該有 GitHub Actions 將用於持續性的外部 blob 儲存體。

現在讓我們建立一個地方來執行 GitHub Actions;同樣地，我們會使用 Azure VMware 解決方案。

首先，讓我們在叢集中布建新的 VM。 我們的 VM 會 [以最新版本的 Ubuntu Server 為](http://releases.ubuntu.com/20.04.1/)基礎。

:::image type="content" source="media/github-enterprise-server/provision-new-vm.png" alt-text="選擇在內部部署或雲端中執行 GitHub。":::

:::image type="content" source="media/github-enterprise-server/provision-new-vm-2.png" alt-text="選擇在內部部署或雲端中執行 GitHub。":::

一旦建立 VM 之後，請將它開啟，並透過 SSH 連接到 VM。

接下來，安裝 [動作](https://github.com/actions/runner) 執行器應用程式，這會從 GitHub Actions 工作流程中執行工作。 從 [[發行] 頁面](https://github.com/actions/runner/releases) 或執行下列快速腳本，找出並下載最新的 Linux x64 版本的動作執行器。 此腳本需要有捲曲和 [jq](https://stedolan.github.io/jq/) 存在於您的 VM 上。

`LATEST\_RELEASE\_ASSET\_URL=$( curl https://api.github.com/repos/actions/runner/releases/latest | \`

`  jq -r '.assets | .[] | select(.name | match("actions-runner-linux-arm64")) | .url' )`

`DOWNLOAD\_URL=$( curl $LATEST\_RELEASE\_ASSET\_URL | \`

`  jq -r '.browser\_download\_url' )`

`curl -OL $DOWNLOAD\_URL`

您現在應該會在 VM 的本機上有一個檔案，即動作-執行程式-linux-arm64- \* gz。 在本機將此 tarball 解壓縮：

`tar xzf actions-runner-linux-arm64-\*.tar.gz`

這種解壓縮會在本機解壓縮幾個檔案，包括 `config.sh` 和 `run.sh` 腳本，我們將會在稍後回來。

## <a name="enabling-github-actions"></a>啟用 GitHub Actions

> [!NOTE]
> GitHub Actions [目前以 GitHub Enterprise Server 2.22 版的有限 Beta 版提供](https://docs.github.com/en/enterprise/admin/github-actions)。

即將出現！ 讓我們設定並啟用 GitHub Enterprise Server 實例上的 GitHub Actions。 我們必須透過 [SSH 存取 GitHub Enterprise Server 實例的系統管理命令](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh)介面，然後執行下列命令：

`# set an environment variable containing your Blob storage connection string`

`export CONNECTION\_STRING="<your connection string from the blob storage step>"`

`# configure actions storage`

`ghe-config secrets.actions.storage.blob-provider azure`

`ghe-config secrets.actions.storage.azure.connection-string "$CONNECTION\_STRING"`

`# apply these settings`

`ghe-config-apply`

`# execute a precheck, this install additional software required by Actions on GitHub Enterprise Server`

`ghe-actions-precheck -p azure -cs "$CONNECTION\_STRING"`

`# enable actions, and re-apply the config`

`ghe-config app.actions.enabled true`

`ghe-config-apply`

下次執行時間：

`ghe-actions-check -s blob`

您應該會看到輸出：「Blob 儲存體狀況良好」。

現在已設定 GitHub Actions，請為您的使用者啟用它。 以系統管理員身分登入您的 GitHub Enterprise 伺服器實例，然後選取 ![ Rocket 圖示。](media/github-enterprise-server/rocket-icon.png) 位於任何頁面的右上角。 在左側邊欄中，選取 [ **企業總覽**]、[ **原則**]、[ **動作**]，然後選取選項來 **啟用所有組織的動作**。

接下來，從 [自我裝載執行 **程式** ] 索引標籤設定您的執行器。從下拉式清單中選取 [ **加入新** 的]，然後選取 [ **新** 的執行器]。

在下一個頁面上，您會看到一組要執行的命令，我們只需要複製 **命令來設定** 執行器，例如：

`./config.sh --url https://10.1.1.26/enterprises/octo-org --token AAAAAA5RHF34QLYBDCHWLJC7L73MA`

複製 `config.sh` 命令，並將其貼入您先前) 建立的動作執行器 (的會話中。

:::image type="content" source="media/github-enterprise-server/actions-runner.png" alt-text="選擇在內部部署或雲端中執行 GitHub。":::

使用 run.sh 命令來 *執行* 執行器：

:::image type="content" source="media/github-enterprise-server/run-runner.png" alt-text="選擇在內部部署或雲端中執行 GitHub。":::

若要讓您企業中的組織可以使用此執行程式，請編輯其組織存取權：

:::image type="content" source="media/github-enterprise-server/edit-runner-access.png" alt-text="選擇在內部部署或雲端中執行 GitHub。":::

在這裡，我們會將它提供給所有組織使用，但您也可以限制組織子集的存取權，甚至是特定的存放庫。

## <a name="optional-configuring-github-connect"></a> (選用的) 設定 GitHub Connect

雖然這個步驟是選擇性的，但如果您打算使用 GitHub.com 上提供的開放原始碼動作，建議您這樣做。 這可讓您在工作流程中參考這些可重複使用的動作，以建立其他人的工作。

若要啟用 GitHub Connect，請遵循 [使用 GitHub Connect 啟用自動存取 GitHub.com 動作](https://docs.github.com/en/enterprise/admin/github-actions/enabling-automatic-access-to-githubcom-actions-using-github-connect)的步驟。

啟用 GitHub Connect 之後，請在 [ **工作流程執行] 選項中選取要使用 GitHub.com 動作的伺服器** 。

:::image type="content" source="media/github-enterprise-server/enable-using-actions.png" alt-text="選擇在內部部署或雲端中執行 GitHub。":::

## <a name="setting-up-and-running-your-first-workflow"></a>設定並執行您的第一個工作流程

現在已設定動作和 GitHub Connect，讓我們將所有的工作都設為良好使用。 以下是參考絕佳 [octokit/要求動作](https://github.com/octokit/request-action)的範例工作流程，可讓我們使用 github API 的互動來「編寫」 github 的腳本，由 GitHub Actions 提供技術支援。

在此基本工作流程中，我們將使用， `octokit/request-action` 只在使用 API 的 GitHub 上開啟問題。

:::image type="content" source="media/github-enterprise-server/workflow-example.png" alt-text="選擇在內部部署或雲端中執行 GitHub。":::

>[!NOTE]
>GitHub.com 會裝載動作，但是當它在 GitHub Enterprise 伺服器上執行時，它 *會自動* 使用 GITHUB ENTERPRISE Server API。

如果您選擇不啟用 GitHub Connect，您可以使用下列替代工作流程。

:::image type="content" source="media/github-enterprise-server/workflow-example-2.png" alt-text="選擇在內部部署或雲端中執行 GitHub。":::

流覽至實例上的存放庫，並將上述工作流程新增為： `.github/workflows/hello-world.yml`

:::image type="content" source="media/github-enterprise-server/workflow-example-3.png" alt-text="選擇在內部部署或雲端中執行 GitHub。":::

在存放庫的 [ **動作** ] 索引標籤中，等候工作流程執行。

:::image type="content" source="media/github-enterprise-server/executed-example-workflow.png" alt-text="選擇在內部部署或雲端中執行 GitHub。":::

您也可以觀看執行器所處理的專案。

:::image type="content" source="media/github-enterprise-server/workflow-processed-by-runner.png" alt-text="選擇在內部部署或雲端中執行 GitHub。":::

如果一切順利執行，您應該會在您的存放庫中看到新的問題，其標題為「Hello world」。

:::image type="content" source="media/github-enterprise-server/example-in-repo.png" alt-text="選擇在內部部署或雲端中執行 GitHub。":::

恭喜！ 您剛完成在 Azure VMware 解決方案私人雲端上執行的 GitHub Enterprise Server 上的第一個動作工作流程。

我們只是抓著您可以運用 GitHub Actions 的介面。 如需更多靈感，請簽出 [GitHub Marketplace](https://github.com/marketplace)上的動作清單，或 [建立您自己](https://docs.github.com/en/actions/creating-actions)的動作。

## <a name="next-steps"></a>後續步驟

在本文中，我們會在您的 Azure VMware Solution 私人雲端上，設定 GitHub Enterprise Server 的新實例，其為 GitHub.com 的自我裝載對等專案。 此實例包含 GitHub Actions 的支援，並使用 Azure Blob 儲存體來保存記錄和成品的持續性。 這是新式、共同作業和安全軟體發展經驗的絕佳組合。 它建置於 Azure VMware 解決方案的穩固基礎上，可讓您以熟悉的設定運用雲端資源。

如需詳細資訊，請參閱下列資源：

- [開始使用 GitHub Actions](https://docs.github.com/en/actions)
- [加入測試計劃](https://resources.github.com/beta-signup/)
- [深入瞭解 GitHub Enterprise Server 的管理](https://githubtraining.github.io/admin-training/#/00_getting_started)
