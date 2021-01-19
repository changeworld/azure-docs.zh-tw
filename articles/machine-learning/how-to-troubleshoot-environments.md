---
title: 針對環境映射進行疑難排解
titleSuffix: Azure Machine Learning
description: 瞭解如何針對環境映射組建和套件安裝的問題進行疑難排解。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: saachigopal
ms.author: sagopal
ms.date: 12/3/2020
ms.topic: troubleshooting
ms.custom: devx-track-python
ms.openlocfilehash: 71061c056b499f79727f70fb855db7a81a65f3bd
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/19/2021
ms.locfileid: "98572165"
---
# <a name="troubleshoot-environment-image-builds"></a>針對環境映射組建進行疑難排解

瞭解如何針對 Docker 環境映射組建和套件安裝的問題進行疑難排解。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。
* [Azure Machine Learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)。
* [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)。
* [適用於 Azure Machine Learning 的 CLI 擴充功能](reference-azure-machine-learning-cli.md)。
* 若要在本機偵錯，您必須在本機系統上擁有正常運作的 Docker 安裝。

## <a name="docker-image-build-failures"></a>Docker 映射組建失敗
 
針對大部分的映射組建失敗，您會在映射組建記錄檔中找到根本原因。
從 Azure Machine Learning 入口網站 (20 個 \_ 映射 \_ 組建 \_log.txt) 或從您的 Azure Container Registry 工作執行記錄中，尋找映射組建記錄檔。
 
通常更容易在本機重現錯誤。 檢查錯誤類型，然後嘗試下列其中一項 `setuptools` ：

- 在本機安裝 conda 相依性： `conda install suspicious-dependency==X.Y.Z` 。
- 在本機安裝 pip 相依性： `pip install suspicious-dependency==X.Y.Z` 。
- 請嘗試具體化整個環境： `conda create -f conda-specification.yml` 。

> [!IMPORTANT]
> 請確定本機計算叢集上的平臺和解譯器符合遠端計算叢集上的平臺和解譯器。 

### <a name="timeout"></a>逾時 
 
下列網路問題可能會導致逾時錯誤：

- 低網際網路頻寬
- 伺服器問題
- 無法以指定的 conda 或 pip timeout 設定下載的大型相依性
 
類似下列範例的訊息會指出問題：
 
```
('Connection broken: OSError("(104, \'ECONNRESET\')")', OSError("(104, 'ECONNRESET')"))
```
```
ReadTimeoutError("HTTPSConnectionPool(host='****', port=443): Read timed out. (read timeout=15)",)
```

如果您收到錯誤訊息，請嘗試下列其中一個可能的解決方案：
 
- 針對相依性，嘗試不同的來源，例如鏡像、Azure Blob 儲存體或其他 Python 摘要。
- 更新 conda 或 pip。 如果您是使用自訂的 Docker 檔案，請更新 timeout 設定。
- 有些 pip 版本有已知問題。 請考慮將特定版本的 pip 新增至環境相依性。

### <a name="package-not-found"></a>找不到套件

以下是映射組建失敗最常見的錯誤：

- 找不到 Conda 套件：

   ```
   ResolvePackageNotFound: 
   - not-existing-conda-package
   ```

- 找不到指定的 pip 套件或版本：

   ```
   ERROR: Could not find a version that satisfies the requirement invalid-pip-package (from versions: none)
   ERROR: No matching distribution found for invalid-pip-package
   ```

- 錯誤的嵌套 pip 相依性：

   ```
   ERROR: No matching distribution found for bad-package==0.0 (from good-package==1.0)
   ```

檢查封裝是否存在於指定的來源上。 使用 [pip 搜尋](https://pip.pypa.io/en/stable/reference/pip_search/) 來確認 pip 相依性：

- `pip search azureml-core`

針對 conda 相依性，請使用 [conda 搜尋](https://docs.conda.io/projects/conda/en/latest/commands/search.html)：

- `conda search conda-forge::numpy`

如需更多選項，請嘗試：
- `pip search -h`
- `conda search -h`

#### <a name="installer-notes"></a>安裝程式注意事項

請確定指定的平臺和 Python 解譯器版本有必要的散發。

針對 pip 相依性，請移至以 `https://pypi.org/project/[PROJECT NAME]/[VERSION]/#files` 查看是否有可用的必要版本。 移至 https://pypi.org/project/azureml-core/1.11.0/#files 以查看範例。

針對 conda 相依性，請檢查通道存放庫上的套件。
若為 Anaconda，Inc. 所維護的通道，請核取 [ [Anaconda 封裝] 頁面](https://repo.anaconda.com/pkgs/)。

### <a name="pip-package-update"></a>Pip 套件更新

在安裝或更新 pip 封裝期間，解析程式可能需要更新已安裝的套件，以符合新的需求。
卸載可能會因各種與 pip 版本相關的原因或安裝相依性的方式而失敗。
最常見的情況是，conda 所安裝的相依性無法由 pip 卸載。
針對此案例，請考慮使用卸載相依性 `conda remove mypackage` 。

```
  Attempting uninstall: mypackage
    Found existing installation: mypackage X.Y.Z
ERROR: Cannot uninstall 'mypackage'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.
``` 
### <a name="installer-issues"></a>安裝程式問題

某些安裝程式版本在封裝解析程式中有可能會導致組建失敗的問題。

如果您使用的是自訂基底映射或 Dockerfile，建議使用 conda 版本4.5.4 或更新版本。

安裝 pip 相依性需要 pip 套件。 如果未在環境中指定版本，則會使用最新版本。
我們建議使用已知的 pip 版本，以避免發生最新版工具可能造成的暫時性問題或重大變更。

如果您看到下列訊息，請考慮在您的環境中釘選 pip 版本：

   ```
   Warning: you have pip-installed dependencies in your environment file, but you do not list pip itself as one of your conda dependencies. Conda may not use the correct pip to install your packages, and they may end up in the wrong place. Please add an explicit pip dependency. I'm adding one for you, but still nagging you.
   ```

Pip 子流程錯誤：
   ```
   ERROR: THESE PACKAGES DO NOT MATCH THE HASHES FROM THE REQUIREMENTS FILE. If you have updated the package versions, update the hashes as well. Otherwise, examine the package contents carefully; someone may have tampered with them.
   ```

如果相依性中有無法解析的衝突，Pip 安裝可能會卡在無限迴圈中。 如果您是在本機工作，請將 pip 版本降級為 < 20.3。 在從 YAML 檔建立的 conda 環境中，只有在 conda-偽造是最高優先順序的通道時，才會看到此問題。 若要減輕此問題，請在 conda 規格檔中明確指定 pip < 20.3 (！ = 20.3 或 = 20.2.4 釘選至其他版本) 作為 conda 相依性。

## <a name="service-side-failures"></a>服務端失敗

請參閱下列案例，以針對可能的服務端失敗進行疑難排解。

### <a name="youre-unable-to-pull-an-image-from-a-container-registry-or-the-address-couldnt-be-resolved-for-a-container-registry"></a>您無法從容器登錄提取映射，或無法解析容器登錄的位址

可能的問題：
- 可能無法正確解析容器登錄的路徑名稱。 檢查映射名稱是否使用雙斜線，以及 Linux 與 Windows 主機上的斜線方向是否正確。
- 如果在虛擬網路後方的容器登錄使用 [不受支援區域](https://docs.microsoft.com/azure/private-link/private-link-overview#availability)中的私人端點，請從入口網站使用服務端點 (公用存取) 來設定容器登錄，然後再試一次。
- 將容器登錄放在虛擬網路後方之後，請執行 [Azure Resource Manager 範本](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network#azure-container-registry) ，讓工作區能夠與容器登錄實例進行通訊。

### <a name="you-get-a-401-error-from-a-workspace-container-registry"></a>您從工作區容器登錄收到401錯誤

使用 [ws.sync_keys ( # B1 ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#sync-keys--)重新同步處理儲存體金鑰。

### <a name="the-environment-keeps-throwing-a-waiting-for-other-conda-operations-to-finish-error"></a>環境持續擲回「正在等候其他 conda 作業完成 ...」錯誤

當映射組建正在進行時，SDK 用戶端會鎖定 conda。 如果進程損毀或使用者未正確地取消，conda 會保持鎖定狀態。 若要解決此問題，請手動刪除鎖定檔案。 

### <a name="your-custom-docker-image-isnt-in-the-registry"></a>您的自訂 Docker 映射不在登錄中

檢查是否使用 [正確的標記](https://docs.microsoft.com/azure/machine-learning/how-to-use-environments#create-an-environment) ，以及是否使用 `user_managed_dependencies = True` 。 `Environment.python.user_managed_dependencies = True` 停用 conda 並使用使用者的已安裝套件。

### <a name="you-get-one-of-the-following-common-virtual-network-issues"></a>您會看到下列其中一個常見的虛擬網路問題

- 請確認儲存體帳戶、計算叢集和 container registry 都位於虛擬網路的相同子網中。
- 當您的容器登錄位於虛擬網路後方時，無法直接用來建立映射。 您必須使用計算叢集來建立映射。
- 如果您有下列情況，儲存體可能需要放在虛擬網路後方：
    - 使用推斷或私用滾輪。
    - 請參閱 403 (未授權) 服務錯誤。
    - 無法從 Azure Container Registry 取得映射詳細資料。

### <a name="the-image-build-fails-when-youre-trying-to-access-network-protected-storage"></a>當您嘗試存取受網路保護的儲存體時，映射組建會失敗

- Azure Container Registry 工作無法在虛擬網路後方運作。 如果使用者在虛擬網路後方有容器登錄，則必須使用計算叢集來建立映射。
- 儲存體應位於虛擬網路後方，才能從中提取相依性。

### <a name="you-cant-run-experiments-when-storage-has-network-security-enabled"></a>當存放裝置啟用網路安全性時，無法執行實驗

如果您使用預設的 Docker 映射，並啟用使用者管理的相依性，請使用 MicrosoftContainerRegistry 和 AzureFrontDoor FirstParty [服務](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network) 標籤來允許清單 Azure Container Registry 及其相依性。

 如需詳細資訊，請參閱 [啟用虛擬網路](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network#azure-container-registry)。

### <a name="you-need-to-create-an-icm"></a>您必須建立 ICM

當您建立/指派 ICM 給中繼存放區時，請包含 CSS 支援票證，讓我們可以更清楚地瞭解問題。

## <a name="next-steps"></a>後續步驟

- [將機器學習模型定型以將花卉分類](how-to-train-scikit-learn.md)
- [使用自訂 Docker 映射來定型機器學習模型](how-to-train-with-custom-image.md)