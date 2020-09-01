---
title: 在 Microsoft Azure Stack Edge 裝置上建立和管理 Kubernetes 叢集 |Microsoft Docs
description: 說明如何透過 Windows PowerShell 介面，在 Microsoft Azure Stack Edge 裝置上建立和管理 Kubernetes 叢集。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: ceab9b6b469da84221758d55fdb63aef8dbb1a8e
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89083601"
---
# <a name="connect-to-and-manage-a-kubernetes-cluster-via-kubectl-on-your-azure-stack-edge-device"></a>透過您 Azure Stack Edge 裝置上的 kubectl 來連線及管理 Kubernetes 叢集

在您的 Azure Stack Edge 裝置上，當您設定計算角色時，會建立 Kubernetes 叢集。 建立 Kubernetes 叢集之後，您就可以從用戶端電腦在本機連線到叢集，並透過 *kubectl*之類的原生工具來管理叢集。

本文說明如何連線到 Azure Stack Edge 裝置上的 Kubernetes 叢集，然後使用 *kubectl*進行管理。 


## <a name="prerequisites"></a>必要條件

在您開始前，請確定：

1. 您可以存取 Azure Stack Edge 裝置。

2. 您已啟用 Azure Stack Edge 裝置，如 [啟動 Azure Stack Edge](azure-stack-edge-gpu-deploy-activate.md)所述。

3. 您已在裝置上啟用計算角色。 當您根據在 [Azure Stack Edge 裝置上設定計算](azure-stack-edge-gpu-deploy-configure-compute.md)的指示，在裝置上設定計算時，也會在裝置上建立 Kubernetes 叢集。

4. 您可以存取執行 PowerShell 5.0 或更新版本的 Windows 用戶端系統來存取裝置。 您也可以讓任何其他用戶端使用 [支援的作業系統](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) 。 

5. 您的本機 web UI 的 [ **裝置** ] 頁面中有 Kubernetes API 端點。 如需詳細資訊，請參閱[Get KUBERNETES API 端點](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-api-endpoint)中的指示。


## <a name="connect-to-powershell-interface"></a>連接到 PowerShell 介面

建立 Kubernetes 叢集之後，您可以存取此叢集來建立命名空間和使用者，並將使用者指派給命名空間。 這會要求您連接到裝置的 PowerShell 介面。 請在執行 PowerShell 的 Windows 用戶端上執行下列步驟。

[!INCLUDE [Connect to admin runspace](../../includes/azure-stack-edge-gateway-connect-minishell.md)]


## <a name="configure-cluster-access-via-rbac"></a>透過 RBAC 設定叢集存取

建立 Kubernetes 叢集之後，您可以透過 cmdline 使用 *kubectl* 來存取叢集。 

在此方法中，您會建立命名空間和使用者。 然後，將使用者與命名空間建立關聯。 您也需要取得 *配置* 檔，讓您可以使用 Kubernetes 用戶端直接與您建立的 Kubernetes 叢集交談，而不需要連線到 Azure Stack Edge 裝置的 PowerShell 介面。

1. 建立命名空間。 輸入：

    `New-HcsKubernetesNamespace -Namespace <string>` 

    > [!NOTE]
    > 對於命名空間和使用者名稱，則適用 [DNS 子域命名慣例](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names) 。

    以下是範例輸出：

    `[10.128.46.54]: PS> New-HcsKubernetesNamespace -Namespace "myasetest1"`

2. 建立使用者並取得設定檔。 輸入：

    `New-HcsKubernetesUser -UserName <string>`

    > [!NOTE]
    > 您無法使用 *aseuser* 作為使用者名稱，因為它會保留給與 Azure Stack Edge 的 IoT 命名空間相關聯的預設使用者。

    以下是設定檔的範例輸出：
   
    ```powershell
    [10.128.46.54]: PS> New-HcsKubernetesUser -UserName "aseuser1"
        apiVersion: v1
        clusters:
        - cluster:
            certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01ERXlPVEUyTlRFeE4xb1hEVE13TURFeU5qRTJOVEV4TjFvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBTXNpCkdZUHB0U1VWcDhKZEdVcHE1MVBURWhsZm8wWkk3YXFBdUlrOHZWdUFCZHJQK1FBSzFxcEN1di93NjIwbUtpZ0QKak1aT3Q4QkREREppWHF6UDZRZm5Oc0U2VXBHMnh0YnYrcTZHV2R5K0t6WkxMbXlwWGY3VjlzZEJnejVKVDNvYQpIdzFja2NTUklHSlV3UWxTbklNaHJUS3JUNDZFUUp3d282TmlNUzZMZDZieVk3WkUrTGg3OS9aNEhLanhTRmhMClc5ZG8veThZR3FXUDZmZTFmMmVmSkhUeGtwR05HZE1UVjNuOFlCZ0pSRzdrNjh0N2MrZ1NhbUlVWVJpTUNSNFAKYlFxcFpscWYvV2REZEJHOFh6aDJ0M1l4SkVIMm00T0Z1cSsvUitMYm95aHdKbmNMdVJ5OEpNZWlwTEQ3UlN0QwpZTDNNR0EzN2JieTRyYm4zVzg4Q0F3RUFBYU1qTUNFd0RnWURWUjBQQVFIL0JBUURBZ0trTUE4R0ExVWRFd0VCCi93UUZNQU1CQWY4d0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFNbzFwWlBtQzV1cmRPZUJhSWQ4eEQzRkxCMG8KTlErbXBXMWpDd0ZtY3h6dUtlWmRsNXc2N0tuS2JTcDR0TXo1cXg3bUtSc0UxcnBoWkh2VHlKUXg1ZFk2ZE1Kdgp5d2FQZjBpT05TNlU2cC9INE12U1dJaEtJZ1FuTnE1dDh4TjJCNnZpQW41RmZoRkx6WEQrUlZGSm42cnovWkZnCmV6MHpxTkNKYmcvelFucFROcmQ2cnFFRHpoSVFZOVdYVWQycFh3ZXRqUXJpMkpZamh4NmtEcTVoRkZTM0FLUnIKOWlQTVQxaWNkR1NUMFVvM1hIZ1k2ck45WGp3MHFrY2I0Sy83UlVVWlRvS3dKamROR3lNTnpad000L2puR0p5SwpQTE9ycU5Ddlkvb0lkVEM5eVZVY3VRbXVlR0VqT20xUnN1RDFHYVE0RTZwakppVWJpMVdrajJ1bFhOWT0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
            server: https://10.128.47.90:6443
            name: kubernetes
        contexts:
        - context:
            cluster: kubernetes
            user: aseuser1
            name: aseuser1@kubernetes
        current-context: aseuser1@kubernetes
        kind: Config
        preferences: {}
        users:
        - name: aseuser1
            user:
            client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwVENDQWJtZ0F3SUJBZ0lJWlFXcjY2cGFWSm93RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURBeE1qa3hOalV4TVRkYUZ3MHlNVEF4TWpneU1qVTJNVGRhTUJNeApFVEFQQmdOVkJBTVRDR0Z6WlhWelpYSXhNSUlCSWpBTkJna3Foa2lHOXcwQkFRRUZBQU9DQVE4QU1JSUJDZ0tDCkFRRUF4R3RDaXJ2cVhGYm5wVmtaYlVPRWQ3cXg2UzNVZ092MlhHRHNKM2VYWXN0bUxQVjMrQnlBcWwyN211L1AKaWdmaWt6MG9QSW1iYmtvcVJkamlYckxFZnk0N3dHcEhzdUhUOHNLY2tHTnJleFE2bXhaZ29xaU1nL2FuMUpMdwpiOFEvVnlQaWdVdUt6eVBseEhUZmlmSVM5MzR1VnZVZUc0dzlMRjAyZ2s2Nitpc0ZtanhsVmhseWRMNlc2UmZTCjl0OGpNMEFkdEpJL0xNbE13RHJJRVdFKzM4WDVNelJhQkJYNnlzNDFWSkZxekcwdW14dHdxN2pGOXp1UTE4ekIKalRZaDl3OWVKcDJwS2Fvak5tNE9SSDh4SzVSaUhocjJ2anFJWXkxRDd2WDh0b0U1K05HNmxHZjh5L1NvQnNRbQpmOG9vL1k3SEZmQXVGdlN6WUc1RUlQTFM4UUlEQVFBQm95Y3dKVEFPQmdOVkhROEJBZjhFQkFNQ0JhQXdFd1lEClZSMGxCQXd3Q2dZSUt3WUJCUVVIQXdJd0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFNR1BxY0YzS1BCbHZ0K24KN1NOcGE3anhWYkhZVGxyNTgwVWxzek93WEwwVnVPUUlCYmN2djIzZk9HNkhDZlQ0bWxBU0JRWVNZcmpLMjJTTwpTWld4cjNQUDlhVzNHajkxc0ttSnc1ZUF1WFhQbUJpK1RWQzBvY0ZLaEQvZ0o1aC93YnBaVndpVjVyRWE5Kzc2CnhNcFAzRld6dG5tT1hPaEl6UFNlR3B4YWpwQXd3ZXd4QU0yb0xGRFZFcy9XTFFMODJZM3NFcE93NVNaSVJJNXMKUHhMUTVnV1ZPM2x2SXcwZ3IrdkJlanZSOUZKaWVuTWFRdGdjSVgyRmpDaDBRMHVYRkdsTVNXWEljbjRLRTR0TApQSFFMalRSVUwyVnRXcW1YZ1RBM3RzN01DcGNRTFdPZFJUYkpSejZCbkc1aXVwcDdOSlFvYW9YcWpNVk5DVDZCCllYMEd0Skk9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
            client-key-data: LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQpNSUlFb2dJQkFBS0NBUUVBeEd0Q2lydnFYRmJucFZrWmJVT0VkN3F4NlMzVWdPdjJYR0RzSjNlWFlzdG1MUFYzCitCeUFxbDI3bXUvUGlnZmlrejBvUEltYmJrb3FSZGppWHJMRWZ5NDd3R3BIc3VIVDhzS2NrR05yZXhRNm14WmcKb3FpTWcvYW4xSkx3YjhRL1Z5UGlnVXVLenlQbHhIVGZpZklTOTM0dVZ2VWVHNHc5TEYwMmdrNjYraXNGbWp4bApWaGx5ZEw2VzZSZlM5dDhqTTBBZHRKSS9MTWxNd0RySUVXRSszOFg1TXpSYUJCWDZ5czQxVkpGcXpHMHVteHR3CnE3akY5enVRMTh6QmpUWWg5dzllSnAycEthb2pObTRPUkg4eEs1UmlIaHIydmpxSVl5MUQ3dlg4dG9FNStORzYKbEdmOHkvU29Cc1FtZjhvby9ZN0hGZkF1RnZTellHNUVJUExTOFFJREFRQUJBb0lCQUVVSUVXM2kxMTQycU5raQo5RjNEWWZZV1pscTJZYjRoc0FjTmhWSGxwUTN5d0dsQ3FEUktDQ3BZSVF3MkJqSFR6WnpEM0xWU0E0K0NmMUxuCkE4QVdnaHJVcStsWE1QVzhpcG9DTGJaTlNzUUord0x3bld2dFl0MHFQaGZtd0p2M1UrK1RUQkwyOHNVVUw3ZVkKLzh0aWlhbno3ZU5mNklIMENyZmgxcnQ3WWhsemtRd1hBVHNScVJja0dMaTgrdGN5WnVzdGFhbENUSzBGRTdCaQpBUGE5a2w1SG56eCs4TTcvNWladHkwTUIxYWpWMnlGblBkUmlKSFVCb1AxVVV0QUthYjVZU0RvNllkZ2pIUTRHCjNWN1l1YWZobnVFMXA0VVIvUkloVVdjRlVVaTFBOFpZMFdnd1BDTmhnMWpQZU5vb2Y1UHpRbEY1OTRBREVwUUYKOFR2bG92RUNnWUVBOWZZbUxyY0tlQ0JiMTFoQVhoTi91Z1RTbU5xNnpFL1pPSWl6M0xwckdjRDhvWDdCVW9GcgplelkxbktSS2tkczE5OERnVjlQZUhuNzllQTRoMjM5RkIwNFFhMUJBdUVMRzRsdHJ3VlNxaFBENUR6YkcrSEhSCnJtYThVMEpUSmVVS0tJVjRUUGxlTzFtK2tjbkRJVXY1ckpwZDVXU3RvcUhXdk9RZkEvRUF0VlVDZ1lFQXpHOTcKTitCZVVvbFNiREttVUNGdTdPZGhYSXJYR3RnSEorZ2JOMDlnSHRURG5PY0IxZ1NzNkpZa1FPQU9qbWFxK05lRAp5SUF1NytheWlFRmpyT2tzTGhkSTREUXNkWFZveFFGVko1V1JwWlk3UTVRaFZpYUR2enR4NDlzSDlKSkplM2U0Cnl3NWdpNGkxKy90MnY2eWRKcWdNQ0xxOHlEdFRrcE9PSitkbkp5MENnWUJwZ3lpcURaZU9KTU9CUTdpSkl2QSsKQ21lVmJ1K0hTaEd6TU9HSHBPamc2V3IybEh1Mk94S3lqblM5TjdWTmtLNDhGQitwVFpnUm1RUi9CZ0Q4T2tLUQplYXFOZnFYazViQ1AxZ3dKcVpwazRVTFdoZmNoQ1NLY0lESlZ2VFFTSTRrU0RQK29kYWs0Nkt6WnVhWGRtTXdJCmdVZ2FhZkFhdmpaeVhhSDRmT0NDNlFLQmdHVXJCaDh3dVh5KzJEc1RGWnF4OE9McjNoS2Q0clUyRXRSODJIc1cKbk1xbEgraVZxU0x3VFdFTWJBUnUzTVU3cVlCYnBxdWlRNWdVNG1UcmR4Z3FpK0tEUTEwd2RJL3IrbDBEdTlCTApCRGlkajlaeGg4M0tZWWhSTXBzLzJULys1TDVsRU4zcnozczl2RkZtcisxS3pycENqeklDdDBtZmtrd0hHV0pGCjhaWkJBb0dBVXB3aUIrcWlHbkpxU1FtZHNSZFVabGFBaTRpbGhaa01RYTRHem95ZFQ3OTVHTm44ZThBRjd3WHMKTGpyYjdEV1FwakdCMnZpUlkySUZBVmIyKzZsdDlwOVJRMTZnSmxpNU5ZRXVvQWRoWXBsVWdBZGFHWHNGNHdabwo3SHFHTHBGdmUxVU5Gb0dQdkxpWUNrUFVYdGduQ3dNb0R2SEpKNzVYMXl6ckh6cmxUS1k9Ci0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg==  
        
    [10.128.46.54]: PS>
    ```
    

3. 設定檔會以純文字顯示。 複製此檔案，並將它儲存為 *配置* 檔。 

    > [!IMPORTANT]
    > 請勿將設定檔儲存為 *.txt* 檔案，請儲存不含任何副檔名的檔案。

4. 設定檔應該存留在 `.kube` 本機電腦上的使用者設定檔資料夾中。 將檔案複製到使用者設定檔中的該資料夾。

    ![用戶端上的設定檔位置](media/azure-stack-edge-j-series-create-kubernetes-cluster/location-config-file.png)

5. 將命名空間與您建立的使用者產生關聯。 輸入：

    `Grant-HcsKubernetesNamespaceAccess -Namespace <string> -UserName <string>`

    以下是範例輸出：

    `[10.128.46.54]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace "myasetest1" -UserName "aseuser1"`

    一旦有了設定檔，您就不需要叢集的實體存取。 如果您的用戶端可以 ping Azure Stack Edge 的裝置 IP，您應該能夠使用 *kubectl* 命令來導向叢集。

6. 在您的用戶端上啟動新的 PowerShell 會話。 您不需要連線到裝置介面。 您現在可以 `kubectl` 使用下列命令在用戶端上安裝：

    ```powershell
    PS C:\windows\system32> curl https://storage.googleapis.com/kubernetes-release/release/v1.15.2/bin/windows/amd64/kubectl.exe -O kubectl.exe
    
    PS C:\windows\system32>
    ```
    例如，如果 Kubernetes 主要節點正在執行 v 1.15.2，請在用戶端上安裝 v 1.15.2。

    > [!IMPORTANT]
    > 下載用戶端，而該用戶端不會從主伺服器進行超過一個次要版本的扭曲。 用戶端版本，但可能會導致主伺服器最多一個次要版本。 例如，v1.0 主機應該適用于 v1.1、v1.0 和 v1.0 節點，而且應該適用于 v1.0、1.3 和1.4 版用戶端。 如需 Kubernetes 用戶端版本的詳細資訊，請參閱 [Kubernetes 版本和版本扭曲支援原則](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-version-skew)。 如需 Azure Stack Edge 上 Kubernetes server 版本的詳細資訊，請移至取得 Kubernetes 伺服器版本。<!-- insert link-->
    > 有時候， `kubectl` 如果您正在執行適用於 Windows 的 Docker 或其他工具，則會預先安裝在您的系統上。 請務必下載的特定版本，如本節 `kubectl` 所示，以使用此 kubernetes 叢集。 

    安裝需要幾分鐘的時間。

7. 確認已安裝的版本是您下載的版本。 您應該指定在 `kubectl.exe` 您的系統上安裝的絕對路徑。
    
    ```powershell
    PS C:\Users\myuser> C:\windows\system32\kubectl.exe version
    Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.2", GitCommit:"f6278300bebbb750328ac16ee6dd3aa7d3549568", GitTreeState:"clean", BuildDate:"2019-08-05T09:23:26Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"windows/amd64"}
    Server Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.1", GitCommit:"4485c6f18cee9a5d3c3b4e523bd27972b1b53892", GitTreeState:"clean", BuildDate:"2019-07-18T09:09:21Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"linux/amd64"}
    PS C:\Users\myuser>
    ``` 
    
    如需有關 `kubectl` 用來管理 Kubernetes 叢集之命令的詳細資訊，請移至 [Kubectl 的總覽](https://kubernetes.io/docs/reference/kubectl/overview/)。

8. 將 DNS 專案新增至系統上的 hosts 檔案。 

    1. 以系統管理員身分執行 [記事本]，然後開啟位於的檔案 `hosts` `C:\windows\system32\drivers\etc\hosts` 。 
    2. 使用您在先前步驟的本機 UI 中，從 [ **裝置** ] 頁面儲存的資訊，在 hosts 檔案中建立專案。 

        例如，複製此端點 `https://compute.asedevice.microsoftdatabox.com/[10.100.10.10]` 以建立具有裝置 IP 位址和 DNS 網域的下列專案： 

        `10.100.10.10 compute.asedevice.microsoftdatabox.com`

9. 若要確認您可以連接到 Kubernetes pod，請輸入：
    
    ```powershell
    PS C:\Users\myuser> kubectl get pods -n "myasetest1"
    No resources found.
    PS C:\Users\myuser>
    ```
您現在可以在命名空間中部署應用程式，然後查看這些應用程式及其記錄。

> [!IMPORTANT]   
> 您將無法執行許多命令，例如需要您擁有系統管理員存取權的命令。 您只能執行命名空間所允許的作業。


## <a name="remove-kubernetes-cluster"></a>移除 Kubernetes 叢集

若要移除 Kubernetes 叢集，您將需要移除計算設定。

如需詳細指示，請移至 [移除計算](azure-stack-edge-j-series-manage-compute.md#remove-compute-configuration)設定。
   

## <a name="next-steps"></a>後續步驟

- [在您的 Azure Stack Edge 上部署無狀態應用程式](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md)。
