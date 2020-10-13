---
title: 針對 Azure 映射產生器服務進行疑難排解
description: 針對使用 Azure VM Image Builder 服務時的常見問題和錯誤進行疑難排解
author: cynthn
ms.author: danis
ms.date: 10/02/2020
ms.topic: troubleshooting
ms.service: virtual-machines
ms.subservice: imaging
ms.openlocfilehash: 7c937353c645ee5d977a52ec0f8e935eba19a940
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91969971"
---
# <a name="troubleshoot-azure-image-builder-service"></a>針對 Azure 映射產生器服務進行疑難排解

本文可協助您針對使用 Azure 映射建立器服務時可能遇到的常見問題進行疑難排解並解決問題。

AIB 失敗可能會發生在2個區域中：
- 提交影像範本
- 映射組建

## <a name="troubleshoot-image-template-submission-errors"></a>針對影像範本提交錯誤進行疑難排解

映射範本提交錯誤只會在提交時傳回。 未提供映射範本提交錯誤的錯誤記錄檔。 如果提交期間發生錯誤，您可以藉由檢查範本的狀態來傳回錯誤，特別是查看 `ProvisioningState` 和 `ProvisioningErrorMessage` / `provisioningError` 。

```azurecli
az image builder show --name $imageTemplateName  --resource-group $imageResourceGroup
```

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName  <imageTemplateName> -ResourceGroupName <imageTemplateResourceGroup> | Select-Object ProvisioningState, ProvisioningErrorMessage
```
> [!NOTE]
> 針對 PowerShell，您將需要安裝 [Azure 映射產生器 PowerShell 模組](../windows/image-builder-powershell.md#prerequisites)。

下列各節包含常見影像範本提交錯誤的問題解決指引。

### <a name="updateupgrade-of-image-templates-is-currently-not-supported"></a>目前不支援更新/升級映射範本

#### <a name="error"></a>錯誤

```text
'Conflict'. Details: Update/Upgrade of image templates is currently not supported
```

#### <a name="cause"></a>原因

範本已經存在。

#### <a name="solution"></a>解決方法

如果您提交映射設定範本，但提交失敗，則範本成品仍然存在。 刪除失敗的範本。

### <a name="the-resource-operation-completed-with-terminal-provisioning-state-failed"></a>資源作業已完成，終端布建狀態為「失敗」

#### <a name="error"></a>錯誤

```text
Microsoft.VirtualMachineImages/imageTemplates 'helloImageTemplateforSIG01' failed with message '{
  "status": "Failed",
  "error": {
    "code": "ResourceDeploymentFailure",
    "message": "The resource operation completed with terminal provisioning state 'Failed'.",
    "details": [
      {
        "code": "InternalOperationError",
        "message": "Internal error occurred."
```
#### <a name="cause"></a>原因

在大部分情況下，因為缺少許可權，所以會發生資源部署失敗錯誤。

#### <a name="solution"></a>解決方法

根據您的案例，Azure 映射產生器可能需要下列許可權：
- 來源映射或共用映射庫資源群組
- 發佈映射或共用映射庫資源
- 檔案自訂員正在存取的儲存體帳戶、容器或 blob。 

如需設定許可權的詳細資訊，請參閱 [使用 Azure CLI 設定 Azure 映射產生器服務許可權](image-builder-permissions-cli.md) 或 [使用 PowerShell 設定 azure 映射產生器服務許可權](image-builder-permissions-powershell.md)

### <a name="error-getting-managed-image"></a>取得受控映射時發生錯誤

#### <a name="error"></a>錯誤

```text
Build (Managed Image) step failed: Error getting Managed Image '/subscriptions/.../providers/Microsoft.Compute/images/mymanagedmg1': Error getting managed image (...): compute.
ImagesClient#Get: Failure responding to request: StatusCode=403 -- Original Error: autorest/azure: Service returned an error.
Status=403 Code="AuthorizationFailed" Message="The client '......' with object id '......' does not have authorization to perform action 'Microsoft.Compute/images/read' over scope 
```
#### <a name="cause"></a>原因

缺少許可權。

#### <a name="solution"></a>解決方法

根據您的案例，Azure 映射產生器可能需要下列許可權：
* 來源映射或共用映射庫資源群組
* 發佈映射或共用映射庫資源
* 檔案自訂員正在存取的儲存體帳戶、容器或 blob。 

如需設定許可權的詳細資訊，請參閱 [使用 Azure CLI 設定 Azure 映射產生器服務許可權](image-builder-permissions-cli.md) 或 [使用 PowerShell 設定 azure 映射產生器服務許可權](image-builder-permissions-powershell.md)

### <a name="build--step-failed-for-image-version"></a>映射版本的組建步驟失敗

#### <a name="error"></a>錯誤
```text
Build (Shared Image Version) step failed for Image Version '/subscriptions/.../providers/Microsoft.Compute/galleries/.../images/... /versions/0.23768.4001': Error getting Image Version '/subscriptions/.../resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/.../images/.../versions/0.23768.4001': Error getting image version '... :0.23768.4001': compute.GalleryImageVersionsClient#Get: Failure responding to request: StatusCode=404 -- Original Error: autorest/azure: Service returned an error. 
Status=404 Code="ResourceNotFound" Message="The Resource 'Microsoft.Compute/galleries/.../images/.../versions/0.23768.4001' under resource group '<rgName>' was not found."
```
#### <a name="cause"></a>原因

Azure 映射產生器找不到來源映射。

#### <a name="solution"></a>解決方法

請確定來源映射正確，且存在於 Azure 映射產生器服務的位置。

### <a name="downloading-external-file-to-local-file"></a>正在將外部檔案下載到本機檔案

#### <a name="error"></a>錯誤

```text
Downloading external file (<myFile>) to local file (xxxxx.0.customizer.fp) [attempt 1 of 10] failed: Error downloading '<myFile>' to 'xxxxx.0.customizer.fp'..
```

#### <a name="cause"></a>原因

檔案名或位置不正確，或無法連線到該位置。

#### <a name="solution"></a>解決方法

請確定該檔案可供存取。 請確認名稱和位置是否正確。

## <a name="troubleshoot-build-failures"></a>針對組建失敗進行疑難排解

若為映射組建失敗，您可以從取得錯誤 `lastrunstatus` ，然後檢查自訂記錄檔中的詳細資料。


```azurecli
az image builder show --name $imageTemplateName  --resource-group $imageResourceGroup
```

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName  <imageTemplateName> -ResourceGroupName <imageTemplateResourceGroup> | Select-Object LastRunStatus, LastRunStatusMessage
```

### <a name="customization-log"></a>自訂記錄檔

當映射組建正在執行時，會建立記錄並儲存在儲存體帳戶中。 當您建立映射範本成品時，Azure 映射產生器會在暫存資源群組中建立儲存體帳戶。

儲存體帳戶名稱會使用下列模式： **IT_ \<ImageResourceGroupName\> _\<TemplateName\>_ \<GUID\> **

例如， *IT_aibmdi_helloImageTemplateLinux01*。

您可以藉由選取 [**儲存體帳戶**  >  **blob**] 來查看自訂。請在資源群組中，選取儲存體帳戶來登  >  入。 `packerlogs`  然後選取 [ **目錄] > 自訂 .log**。


### <a name="understanding-the-customization-log"></a>瞭解自訂記錄檔

記錄檔是詳細資訊。 其中涵蓋映射組建，包括映射發佈的任何問題，例如共用映射庫複寫。 這些錯誤會出現在影像範本狀態的錯誤訊息中。

自訂記錄包含下列階段：

1. 使用 ARM 範本將組建 VM 和相依性部署到 IT_ 預備資源群組階段。 這個階段包含多個 Azure 映射產生器資源提供者的貼文：
    ```text
    Azure request method="POST" request="https://management.azure.com/subscriptions/<subID>/resourceGroups/IT_aibImageRG200_window2019VnetTemplate01_dec33089-1cc3-cccc-cccc-ccccccc/providers/Microsoft.Storage/storageAccounts
    ..
    PACKER OUT ==> azure-arm: Deploying deployment template ...
    ..
    ```

2. 部署階段的狀態。 此階段包含每個資源部署的狀態：
    ```text
    PACKER ERR 2020/04/30 23:28:50 packer: 2020/04/30 23:28:50 Azure request method="GET" request="https://management.azure.com/subscriptions/<subID>/resourcegroups/IT_aibImageRG200_window2019VnetTemplate01_dec33089-1cc3-4505-ae28-6661e43fac48/providers/Microsoft.Resources/deployments/pkrdp51lc0339jg/operationStatuses/08586133176207523519?[REDACTED]" body=""
    ```

3. 連接到組建 VM 階段。

    若為 Windows，Azure 映射建立器服務會使用 WinRM 進行連線：
    ```text
    PACKER ERR 2020/04/30 23:30:50 packer: 2020/04/30 23:30:50 Waiting for WinRM, up to timeout: 10m0s
    ..
    PACKER OUT     azure-arm: WinRM connected.
    ```

    如果是 Linux，Azure 映射產生器服務將會使用 SSH 來連接：
    ```text
    PACKER OUT ==> azure-arm: Waiting for SSH to become available...
    PACKER ERR 2019/12/10 17:20:51 packer: 2020/04/10 17:20:51 [INFO] Waiting for SSH, up to timeout: 20m0s
    PACKER OUT ==> azure-arm: Connected to SSH!
    ```

4. 執行自訂階段。 當自訂執行時，您可以藉由檢查自訂 .log 來識別這些自訂。 搜尋 * (遙測) *。
    ```text
    (telemetry) Starting provisioner windows-update
    (telemetry) ending windows-update
    (telemetry) Starting provisioner powershell
    (telemetry) ending powershell
    (telemetry) Starting provisioner file
    (telemetry) ending file
    (telemetry) Starting provisioner windows-restart
    (telemetry) ending windows-restart
    
    (telemetry) Finalizing. - This means the build hasfinished
    ```
5. 解除布建階段。 Azure 映射產生器會新增隱藏的自訂員。 此取消布建步驟負責準備 VM 以進行解除布建。 它會使用 c:\DeprovisioningScript.ps1) 來執行 Windows Sysprep (，或使用/tmp/DeprovisioningScript.sh) 在 Linux waagent 取消布建 (。 

    例如：
    ```text
    PACKER ERR 2020/03/04 23:05:04 [INFO] (telemetry) Starting provisioner powershell
    PACKER ERR 2020/03/04 23:05:04 packer: 2020/03/04 23:05:04 Found command: if( TEST-PATH c:\DeprovisioningScript.ps1 ){cat c:\DeprovisioningScript.ps1} else {echo "Deprovisioning script [c:\DeprovisioningScript.ps1] could not be found. Image build may fail or the VM created from the Image may not boot. Please make sure the deprovisioning script is not accidentally deleted by a Customizer in the Template."}
    ```

6. 清除階段。 組建完成後，就會刪除 Azure 映射產生器資源。
    ```text
    PACKER ERR 2020/02/04 02:04:23 packer: 2020/02/04 02:04:23 Azure request method="DELETE" request="https://management.azure.com/subscriptions/<subId>/resourceGroups/IT_aibDevOpsImg_t_vvvvvvv_yyyyyy-de5f-4f7c-92f2-xxxxxxxx/providers/Microsoft.Network/networkInterfaces/pkrnijamvpo08eo?[REDACTED]" body=""
    ```
## <a name="tips-for-troubleshooting-scriptinline-customization"></a>腳本/內嵌自訂疑難排解的秘訣
- 先測試程式碼，再將它提供給 Image Builder
- 確定 Azure 原則和防火牆允許連線至遠端資源。
- 將批註輸出到主控台，例如使用 `Write-Host` 或 `echo` ，這可讓您搜尋自訂 .log。

## <a name="troubleshoot-common-build-errors"></a>針對常見的組建錯誤進行疑難排解

### <a name="packer-build-command-failure"></a>Packer 組建命令失敗

#### <a name="error"></a>錯誤

```text
  "provisioningState": "Succeeded",
  "lastRunStatus": {
   "startTime": "2020-04-30T23:24:06.756985789Z",
   "endTime": "2020-04-30T23:39:14.268729811Z",
   "runState": "Failed",
   "message": "Failed while waiting for packerizer: Microservice has failed: Failed while processing request: Error when executing packerizer: Packer build command has failed: exit status 1. During the image build, a failure has occurred, please review the build log to identify which build/customization step failed. For more troubleshooting steps go to https://aka.ms/azvmimagebuilderts. Image Build log location: https://xxxxxxxxxx.blob.core.windows.net/packerlogs/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/customization.log. OperationId: xxxxxx-5a8c-4379-xxxx-8d85493bc791. Use this operationId to search packer logs."
```

#### <a name="cause"></a>原因

自訂失敗。

#### <a name="solution"></a>解決方法

查看記錄以找出自訂失敗。 搜尋 * (遙測) *。 

例如：
```text
(telemetry) Starting provisioner windows-update
(telemetry) ending windows-update
(telemetry) Starting provisioner powershell
(telemetry) ending powershell
(telemetry) Starting provisioner file
(telemetry) ending file
(telemetry) Starting provisioner windows-restart
(telemetry) ending windows-restart

(telemetry) Finalizing. - This means the build has finished
```

### <a name="timeout-exceeded"></a>超過 Timeout

#### <a name="error"></a>錯誤

```text
Deployment failed. Correlation ID: xxxxx-xxxx-xxxx-xxxx-xxxxxxxxx. Failed in building/customizing image: Failed while waiting for packerizer: Timeout waiting for microservice to complete: 'context deadline exceeded'
```

#### <a name="cause"></a>原因

組建超過組建的超時時間。 此錯誤會出現在 ' lastrunstatus ' 中。

#### <a name="solution"></a>解決方法

1. 檢查自訂 .log。 識別最後一個要執行的自訂器。 `(telemetry)`從記錄檔底部開始搜尋。 

2. 檢查腳本自訂專案。 自訂可能不會隱藏命令的使用者互動，例如 `quiet` 選項。 例如，會 `apt-get install -y` 導致腳本執行等候使用者互動。

3. 如果您使用自訂者 `File` 來下載大於 20 MB 的成品，請參閱因應措施區段。

4. 檢查腳本中可能會導致腳本等待的錯誤和相依性。

5. 如果您預期自訂需要更多時間，請增加 [buildTimeoutInMinutes](image-builder-json.md)。 預設值為4小時。

6. 如果您有需要大量資源的動作，例如下載 gb 的檔案，請考慮基礎組建 VM 大小。 服務會使用 Standard_D1_v2 VM。 VM 具有 1 vCPU 和 3.5 GB 的記憶體。 如果您要下載 50 GB，這可能會耗盡 VM 資源，並導致 Azure 映射產生器服務與組建 VM 之間的通訊失敗。 藉由設定 [VM_Size](image-builder-json.md#vmprofile)，以較大的記憶體 VM 重試組建。

### <a name="long-file-download-time"></a>完整檔案下載時間

#### <a name="error"></a>錯誤
```text
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
myBigFile.zip 826 B / 826000 B  1.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
myBigFile.zip 1652 B / 826000 B  2.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
..
hours later...
..
myBigFile.zip 826000 B / 826000 B  100.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
```
#### <a name="cause"></a>原因 

檔案自訂員正在下載大型檔案。

#### <a name="solution"></a>解決方法

檔案自訂項僅適用于小於 20 MB 的小型檔案下載。 針對較大的檔案下載，請使用腳本或內嵌命令。 例如，在 Linux 上，您可以使用 `wget` 或 `curl` 。 在 Windows 上，您可以使用 `Invoke-WebRequest` 。

### <a name="error-waiting-on-shared-image-gallery"></a>等候共用映射庫時發生錯誤

#### <a name="error"></a>錯誤

```text
Deployment failed. Correlation ID: XXXXXX-XXXX-XXXXXX-XXXX-XXXXXX. Failed in distributing 1 images out of total 1: {[Error 0] [Distribute 0] Error publishing MDI to shared image gallery:/subscriptions/<subId>/resourceGroups/xxxxxx/providers/Microsoft.Compute/galleries/xxxxx/images/xxxxxx, Location:eastus. Error: Error returned from SIG client while publishing MDI to shared image gallery for dstImageLocation: eastus, dstSubscription: <subId>, dstResourceGroupName: XXXXXX, dstGalleryName: XXXXXX, dstGalleryImageName: XXXXXX. Error: Error waiting on shared image gallery future for resource group: XXXXXX, gallery name: XXXXXX, gallery image name: XXXXXX.Error: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded}
```

#### <a name="cause"></a>原因

影像產生器超時，等候將映射新增並複寫到共用映射庫 (SIG) 。 如果映射要插入至 SIG，則可以假設映射組建成功。 但是，整體進程失敗，因為映射產生器正在等待共用映射庫完成複寫。 即使組建失敗，還是會繼續複寫。 您可以藉由檢查散發 *runOutput*來取得映射版本的屬性。

```bash
$runOutputName=<distributionRunOutput>
az resource show \
    --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/$imageTemplateName/runOutputs/$runOutputName"  \
    --api-version=2019-05-01-preview
```

#### <a name="solution"></a>解決方法

增加 **buildTimeoutInMinutes**。
 
### <a name="low-windows-resource-information-events"></a>低 Windows 資源資訊事件

#### <a name="error"></a>錯誤

```text
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 1% cpu; 37% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 51% cpu; 35% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 21% cpu; 37% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 21% cpu; 36% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 90% cpu; 32% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for the Windows Modules Installer to exit...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] command 'PowerShell -ExecutionPolicy Bypass -OutputFormat Text -File C:/Windows/Temp/packer-windows-update-elevated.ps1' exited with code: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: Restarting the machine...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 1672 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: Waiting for machine to become available...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 1672 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] starting remote command: shutdown.exe -f -r -t 0 -c "packer restart"
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] command 'shutdown.exe -f -r -t 0 -c "packer restart"' exited with code: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: A system shutdown is in progress.(1115)
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] starting remote command: shutdown.exe -f -r -t 60 -c "packer restart test"
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] command 'shutdown.exe -f -r -t 60 -c "packer restart test"' exited with code: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] RPC endpoint: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] 40 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] RPC client: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] RPC endpoint: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] 40 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] RPC client: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 Retryable error: Machine not yet available (exit status 1115)
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT Build 'azure-arm' errored: unexpected EOF
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT 
```
#### <a name="cause"></a>原因

資源耗盡。 使用預設組建 VM 大小 D1_V2 Windows Update 執行時，通常會看到此問題。

#### <a name="solution"></a>解決方法

增加組建 VM 大小。

### <a name="builds-finished-but-no-artifacts-were-created"></a>組建已完成，但未建立任何構件

#### <a name="error"></a>錯誤

```text
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT Build 'azure-arm' errored: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR ==> Some builds didn't complete successfully and had errors:
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT 
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 machine readable: azure-arm,error []string{"Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded"}
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT ==> Some builds didn't complete successfully and had errors:
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR ==> Builds finished but no artifacts were created.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT --> azure-arm: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 Cancelling builder after context cancellation context canceled
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT 
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 [INFO] (telemetry) Finalizing.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT ==> Builds finished but no artifacts were created.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:24 waiting for all plugin processes to complete...
Done exporting Packer logs to Azure for Packer prefix: [a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT
```
#### <a name="cause"></a>原因

等候建立所需的 Azure 資源所造成的超時時間。

#### <a name="solution"></a>解決方法

請重新執行組建，然後再試一次。

### <a name="resource-not-found"></a>找不到資源

#### <a name="error"></a>錯誤

```text
  "provisioningState": "Succeeded",
  "lastRunStatus": {
   "startTime": "2020-05-01T00:13:52.599326198Z",
   "endTime": "2020-05-01T00:15:13.62366898Z",
   "runState": "Failed",
   "message": "network.InterfacesClient#UpdateTags: Failure responding to request: StatusCode=404 -- Original Error: autorest/azure: Service returned an error. Status=404 Code=\"ResourceNotFound\" Message=\"The Resource 'Microsoft.Network/networkInterfaces/aibpls7lz2e.nic.4609d697-be0a-4cb0-86af-49b6fe877fe1' under resource group 'IT_aibImageRG200_window2019VnetTemplate01_9988723b-af56-413a-9006-84130af0e9df' was not found.\""
  },
```
#### <a name="cause"></a>原因

缺少許可權。

#### <a name="solution"></a>解決方法

重新檢查 Azure 映射產生器具有其所需的擁有權限。 

如需設定許可權的詳細資訊，請參閱 [使用 Azure CLI 設定 Azure 映射產生器服務許可權](image-builder-permissions-cli.md) 或 [使用 PowerShell 設定 azure 映射產生器服務許可權](image-builder-permissions-powershell.md)

### <a name="sysprep-timing"></a>Sysprep 時間

#### <a name="error"></a>錯誤

```text
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service RdAgent) -and ((Get-Service RdAgent).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service WindowsAzureGuestAgent) -and ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Sysprepping VM ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: & $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while($true) {
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Write-Output $imageState
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Start-Sleep -s 5
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Sysprep complete ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (RdAgent) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Sysprepping VM ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_COMPLETE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: Get-Service : Cannot find any service with service name 'WindowsAzureGuestAgent'.
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: At C:\DeprovisioningScript.ps1:6 char:9
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: + while ((Get-Service WindowsAzureGuestAgent) -and ((Get-Service Window ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: +         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:     + CategoryInfo          : ObjectNotFound: (WindowsAzureGuestAgent:String) [Get-Service], ServiceCommandException
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:     + FullyQualifiedErrorId : NoServiceFoundForGivenName,Microsoft.PowerShell.Commands.GetServiceCommand
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 Cancelling builder after context cancellation context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT Cancelling build after receiving terminated
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 packer: 2020/05/05 22:26:17 Cancelling provisioning due to context cancellation: context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: 
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 packer: 2020/05/05 22:26:17 Cancelling hook after context cancellation context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: The resource group was not created by Packer, deleting individual resources ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR ==> azure-arm: The resource group was not created by Packer, deleting individual resources ...
```
#### <a name="cause"></a>原因

原因可能是因為 D1_V2 VM 大小的時間問題。 如果自訂受限且在三秒內執行，則 Azure 映射產生器會執行 sysprep 命令以取消布建。 當 Azure 映射建立器解除布建時，sysprep 命令會檢查是否有 *WindowsAzureGuestAgent*，而這可能不會完整安裝，而導致計時問題。 

#### <a name="solution"></a>解決方法

增加 VM 大小。 或者，您可以新增60秒的 PowerShell 睡眠自訂，以避免計時問題。

### <a name="cancelling-builder-after-context-cancellation-context-canceled"></a>取消 builder 內容取消內容之後取消產生器

#### <a name="error"></a>錯誤
```text
PACKER ERR 2020/03/26 22:11:23 Cancelling builder after context cancellation context canceled
PACKER OUT Cancelling build after receiving terminated
PACKER ERR 2020/03/26 22:11:23 packer-builder-azure-arm plugin: Cancelling hook after context cancellation context canceled
..
PACKER ERR 2020/03/26 22:11:23 packer-builder-azure-arm plugin: Cancelling provisioning due to context cancellation: context canceled
PACKER ERR 2020/03/26 22:11:25 packer-builder-azure-arm plugin: [ERROR] Remote command exited without exit status or exit signal.
PACKER ERR 2020/03/26 22:11:25 packer-builder-azure-arm plugin: [INFO] RPC endpoint: Communicator ended with: 2300218
PACKER ERR 2020/03/26 22:11:25 [INFO] 148974 bytes written for 'stdout'
PACKER ERR 2020/03/26 22:11:25 [INFO] 0 bytes written for 'stderr'
PACKER ERR 2020/03/26 22:11:25 [INFO] RPC client: Communicator ended with: 2300218
PACKER ERR 2020/03/26 22:11:25 [INFO] RPC endpoint: Communicator ended with: 2300218
```
#### <a name="cause"></a>原因
映射產生器服務會使用埠 22 (Linux) ，或 5986 (Windows) 連接到組建 VM，這會在映射組建期間服務與組建 VM 中斷連線時發生。 中斷連接的原因可能會有所不同，但在腳本中啟用或設定防火牆可能會封鎖上述埠。

#### <a name="solution"></a>解決方法
請參閱您的腳本，以進行防火牆變更/啟用，或對 SSH 或 WinRM 的變更，並確保在上述埠上的服務與組建 VM 之間有固定的連線能力。 如需有關 Image Builder 網路功能的詳細資訊，請參閱 [需求](./image-builder-networking.md)。

## <a name="devops-task"></a>DevOps 工作 

### <a name="troubleshooting-the-task"></a>疑難排解工作
只有在自訂期間發生錯誤時，工作才會失敗。當這種情況發生時，工作將會回報失敗，並保留暫存資源群組和記錄檔，讓您可以找出問題。 

若要找出記錄檔，您必須知道範本名稱、進入管線 > 失敗的組建 > 深入探索 AIB DevOps 工作，然後您會看到記錄檔和範本名稱：
```text
start reading task parameters...
found build at:  /home/vsts/work/r1/a/_ImageBuilding/webapp
end reading parameters
getting storage account details for aibstordot1556933914
created archive /home/vsts/work/_temp/temp_web_package_21475337782320203.zip
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
template name:  t_1556938436xxx
``` 

移至入口網站，在資源群組中搜尋範本名稱，然後尋找具有 IT_ * 的資源群組。
移至儲存體帳戶 > blob > 容器 > 記錄。

### <a name="troubleshooting-successful-builds"></a>針對成功的組建進行疑難排解
在某些情況下，您可能需要調查成功的組建，而且想要檢查記錄檔。 如前所述，如果映射組建成功，則包含記錄檔的預備資源群組會在清除過程中遭到刪除。 不過，您可以做的是在內嵌命令之後引入睡眠，然後在組建暫停時取得記錄。 若要這樣做，請遵循下列步驟：
 
1. 更新內嵌命令，並新增： Write-Host/Echo "Sleep" –這樣可讓您在記錄檔中搜尋
2. 新增至少10mins 的睡眠，您可以使用 [ [開始-睡眠](/powershell/module/microsoft.powershell.utility/start-sleep)] 或 [ `Sleep` Linux] 命令。
3. 使用上述方法來識別記錄檔位置，然後繼續下載/檢查記錄直到進入睡眠狀態為止。


### <a name="operation-was-canceled"></a>作業已取消

#### <a name="error"></a>錯誤

```text
2020-05-05T18:28:24.9280196Z ##[section]Starting: Azure VM Image Builder Task
2020-05-05T18:28:24.9609966Z ==============================================================================
2020-05-05T18:28:24.9610739Z Task         : Azure VM Image Builder Test(Preview)
2020-05-05T18:28:24.9611277Z Description  : Build images using Azure Image Builder resource provider.
2020-05-05T18:28:24.9611608Z Version      : 1.0.18
2020-05-05T18:28:24.9612003Z Author       : Microsoft Corporation
2020-05-05T18:28:24.9612718Z Help         : For documentation, and end to end example, please visit: http://aka.ms/azvmimagebuilderdevops
2020-05-05T18:28:24.9613390Z ==============================================================================
2020-05-05T18:28:26.0651512Z start reading task parameters...
2020-05-05T18:28:26.0673377Z found build at:  d:\a\r1\a\_AppsAndImageBuilder\webApp
2020-05-05T18:28:26.0708785Z end reading parameters
2020-05-05T18:28:26.0745447Z getting storage account details for aibstagstor1565047758
2020-05-05T18:28:29.8812270Z created archive d:\a\_temp\temp_web_package_09737279437949953.zip
2020-05-05T18:28:33.1568013Z Source for image:  { type: 'PlatformImage',
2020-05-05T18:28:33.1584131Z   publisher: 'MicrosoftWindowsServer',
2020-05-05T18:28:33.1585965Z   offer: 'WindowsServer',
2020-05-05T18:28:33.1592768Z   sku: '2016-Datacenter',
2020-05-05T18:28:33.1594191Z   version: '14393.3630.2004101604' }
2020-05-05T18:28:33.1595387Z template name:  t_1588703313152
2020-05-05T18:28:33.1597453Z starting put template...
2020-05-05T18:28:52.9278603Z put template:  Succeeded
2020-05-05T18:28:52.9281282Z starting run template...
2020-05-05T19:33:14.3923479Z ##[error]The operation was canceled.
2020-05-05T19:33:14.3939721Z ##[section]Finishing: Azure VM Image Builder Task
```
#### <a name="cause"></a>原因

如果使用者未取消組建，Azure DevOps 的使用者代理程式會將其取消。 由於 Azure DevOps 的功能，最有可能發生1小時的超時時間。 如果您使用的是私用專案和代理程式，則會收到60分鐘的組建時間。 如果組建超過此時間，DevOps 會取消正在執行的工作。

如需 Azure DevOps 功能和限制的詳細資訊，請參閱 [Microsoft 裝載的代理程式](/azure/devops/pipelines/agents/hosted?view=azure-devops#capabilities-and-limitations)
 
#### <a name="solution"></a>解決方法

您可以裝載自己的 DevOps 代理程式，或查看以縮短組建的時間。 例如，如果您要散發至共用映射庫，請複製到一個區域。 如果您想要以非同步方式複寫。 

### <a name="slow-windows-logon-please-wait-for-the-windows-modules-installer"></a>Windows 登入緩慢：「請稍候 Windows 模組安裝程式」

#### <a name="error"></a>錯誤
使用 Image Builder 建立 Windows 10 映射之後，請從映射建立 VM、使用 RDP，並且在第一次登入時必須等待幾分鐘的時間，才會看到含有下列訊息的藍色畫面：
```text
Please wait for the Windows Modules Installer
```

#### <a name="solution"></a>解決方法
首先，在映射組建中，將 Windows 重新開機自訂程式新增為最後一次自訂，並完成所有的軟體安裝，以確認沒有任何未完成的重新開機。 最後，將 [/mode： vm](/windows-hardware/manufacture/desktop/sysprep-command-line-options) 選項新增至 AIB 使用的預設 sysprep，如下所示：「從 AIB 映射建立的 vm 未成功建立」 > ' 覆寫命令」  

 
## <a name="vms-created-from-aib-images-do-not-create-successfully"></a>無法成功建立從 AIB 映射建立的 Vm

根據預設，Azure 映射建立器會在每個映射自訂階段結束時執行 *取消布建程式* 代碼，以將映射 *一般化* 。 一般化是將映射設定為重複使用以建立多個 Vm 的程式，您可以傳入 VM 設定，例如主機名稱、使用者名稱等。針對 Windows，Azure 映射建立器會執行 *Sysprep*，並針對 Linux Azure 映射產生器執行 `waagent -deprovision` 。 

針對 Windows，Azure 映射產生器會使用一般 Sysprep 命令。 不過，這可能不適用於每個成功的 Windows 一般化。 Azure Image Builder 可讓您自訂 Sysprep 命令。 注意： Azure 映射產生器是影像自動化工具。 它負責成功執行 Sysprep 命令。 但您可能需要不同的 Sysprep 命令，才能讓您的映射可供使用。 針對 Linux，Azure 映射產生器會使用一般 `waagent -deprovision+user` 命令。 如需詳細資訊，請參閱 [Microsoft Azure Linux 代理程式檔](https://github.com/Azure/WALinuxAgent#command-line-options)。

如果您要遷移現有的自訂，而且您使用的是不同的 Sysprep/waagent 命令，您可以嘗試映射產生器的一般命令。 如果 VM 建立失敗，請使用您先前的 Sysprep/waagent 命令。

> [!NOTE]
> 如果 AIB 成功建立 Windows 自訂映射，而且您從該映射建立 VM，則會發現 VM 將無法成功建立 (例如，VM 建立命令未完成/超時) ，您將需要檢查 Windows Server Sysprep 檔。 或者，您可以向 Windows Server Sysprep 客戶服務支援小組提出支援要求，他們可以針對正確的 Sysprep 命令進行疑難排解並提出建議。

### <a name="command-locations-and-filenames"></a>命令位置和檔案名

Windows：

```
c:\DeprovisioningScript.ps1
```

Linux：
```bash
/tmp/DeprovisioningScript.sh
```

### <a name="sysprep-command-windows"></a>Sysprep 命令： Windows

```PowerShell
Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
  Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
}
& $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
while($true) {
  $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
  Write-Output $imageState
  if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
  Start-Sleep -s 5
}
Write-Output '>>> Sysprep complete ...'
```

### <a name="deprovision-command-linux"></a>解除布建命令： Linux

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

### <a name="overriding-the-commands"></a>覆寫命令

若要覆寫命令，請使用 PowerShell 或 shell 腳本 provisioners 來建立具有確切檔案名的命令檔，並將它們放在先前所列的目錄中。 Azure 映射產生器會讀取這些命令，並將輸出寫入 *自訂 .log*。

## <a name="getting-support"></a>取得支援
如果您已參考指導方針，但仍然無法針對您的問題進行疑難排解，您可以開啟支援案例。 當您這樣做時，請選取正確的產品和支援主題，這樣做會與 Azure VM 映射產生器支援小組接洽。

選取案例產品：
```bash
Product Family: Azure
Product: Virtual Machine Running (Window\Linux)
Support Topic: Azure Features
Support Subtopic: Azure Image Builder
```

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱 [Azure 映射](image-builder-overview.md)建立器總覽。