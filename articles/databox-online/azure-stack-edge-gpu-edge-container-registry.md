---
title: 在 Azure Stack Edge Pro GPU 裝置上啟用 Edge container registry
description: 說明如何在 Azure Stack Edge Pro GPU 裝置上啟用本機 Edge container registry。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 11/11/2020
ms.author: alkohli
ms.openlocfilehash: bccb6fa33007082737997c7282fb286c38e3bbd7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466196"
---
# <a name="enable-edge-container-registry-on-your-azure-stack-edge-pro-gpu-device"></a>在 Azure Stack Edge Pro GPU 裝置上啟用 Edge container registry

本文說明如何啟用 Edge container registry，並在 Azure Stack Edge Pro 裝置的 Kubernetes 叢集中使用它。 本文中使用的範例會詳細說明如何從來源登錄（在此案例中為 Microsoft Container registry）將映射推送至 Azure Stack Edge 裝置上的登錄，也就是 Edge 容器登錄。

### <a name="about-edge-container-registry"></a>關於 Edge container registry

容器化計算應用程式會在容器映射上執行，而這些映射會儲存在登錄中。 登錄可以是公用的，例如 Docker Hub、私用或雲端提供者，例如 Azure Container Registry。 如需詳細資訊，請參閱 [關於登錄、存放庫和映射](../container-registry/container-registry-concepts.md)。

Edge container registry 可在您 Azure Stack Edge Pro 裝置上的邊緣提供存放庫。 您可以使用此登錄來儲存及管理您的私人容器映射。

在多節點環境中，您可以下載容器映射，並將其推送至 Edge 容器登錄一次。 所有 Edge 應用程式都可以使用 Edge container registry 進行後續部署。


## <a name="prerequisites"></a>必要條件

在您開始前，請確定：

1. 您可以存取 Azure Stack Edge Pro 裝置。

1. 您已依照[啟動 Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md) 中的說明來啟動 Azure Stack Edge Pro 裝置。

1. 您已在裝置上啟用計算角色。 當您在裝置上設定計算時，也會根據在 [您 Azure Stack Edge Pro 裝置上設定計算](azure-stack-edge-gpu-deploy-configure-compute.md)的指示，在裝置上建立 Kubernetes 叢集。

1. 您的本機 web UI 的 [ **裝置** ] 頁面中有 Kubernetes API 端點。 如需詳細資訊，請參閱 [Get KUBERNETES API 端點](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints)中的指示。

1. 您可以使用 [支援的作業系統](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)來存取用戶端系統。 如果使用 Windows 用戶端，系統應該執行 PowerShell 5.0 或更新版本以存取裝置。

    1. 如果您想要提取和推送您自己的容器映射，請確定系統已安裝 Docker 用戶端。 如果使用 Windows 用戶端，請 [在 windows 上安裝 Docker Desktop](https://docs.docker.com/docker-for-windows/install/)。  


## <a name="enable-container-registry-as-add-on"></a>啟用 container registry 作為附加元件

第一個步驟是啟用 Edge container registry 作為附加元件。

1. [連接到裝置的 PowerShell 介面](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)。 

1. 若要啟用 container registry 作為附加元件，請輸入： 

    `Set-HcsKubernetesContainerRegistry`
    
    此作業可能需要幾分鐘的時間才能完成。

    以下是此命令的範例輸出：  
            
    ```powershell
    [10.128.44.40]: PS>Set-HcsKubernetesContainerRegistry
    Operation completed successfully. Use Get-HcsKubernetesContainerRegistryInfo for credentials    
    ```
            
1. 若要取得 container registry 詳細資料，請輸入：

    `Get-HcsKubernetesContainerRegistryInfo`

    以下是此命令的範例：  
    
    ```powershell
    [10.128.44.40]: PS> Get-HcsKubernetesContainerRegistryInfo
                
    Endpoint                                   IPAddress    Username     Password
    --------                                   ---------    --------     --------
    ecr.dbe-hw6h1t2.microsoftdatabox.com:31001 10.128.44.41 ase-ecr-user i3eTsU4zGYyIgxV
    ``` 

1. 從的輸出中記下使用者名稱和密碼 `Get-HcsKubernetesContainerRegistryInfo` 。 在推送映射時，會使用這些認證來登入 Edge 容器登錄。         


## <a name="manage-container-registry-images"></a>管理 container registry 映射

您可能會想要從 Azure Stack Edge 裝置以外的地方存取 container registry。 您也可能想要在登錄中推送或提取映射。

請遵循下列步驟來存取 Edge container registry：

1. 取得 Edge container registry 的端點詳細資料。
    1. 在裝置的本機 UI 中，移至 [ **裝置**]。
    1. 找出 **Edge container registry 端點**。
        ![裝置頁面上的 Edge container registry 端點](media/azure-stack-edge-gpu-edge-container-registry/get-edge-container-registry-endpoint-1.png) 
    1. 複製此端點，並在您的用戶端檔案中建立對應的 DNS 專案 `C:\Windows\System32\Drivers\etc\hosts` ，以連線至 Edge container registry 端點。 

        <IP address of the Kubernetes main node>    <Edge container registry endpoint> 
        
        ![新增 Edge container registry 端點的 DNS 專案](media/azure-stack-edge-gpu-edge-container-registry/add-domain-name-service-entry-hosts-1.png)    

1. 從本機 UI 下載 Edge container registry 憑證。 
    1. 在裝置的本機 UI 中，移至 [ **憑證**]。
    1. 找出 **Edge container registry 憑證** 的專案。 在此專案的右邊，選取 **下載** 以下載您將用來存取裝置的用戶端系統上的 Edge container registry 憑證。 

        ![下載 Edge container registry 端點憑證](media/azure-stack-edge-gpu-edge-container-registry/download-edge-container-registry-endpoint-certificate-1.png)  

1. 在用戶端上安裝下載的憑證。 如果使用 Windows 用戶端，請遵循下列步驟： 
    1. 選取憑證，然後在 [ **憑證匯入] 嚮導** 中選取 [存放區位置] 作為 [ **本機電腦**]。 

        ![安裝憑證1](media/azure-stack-edge-gpu-edge-container-registry/install-certificate-1.png) 
    
    1. 將憑證安裝在本機電腦上的受根信任存放區中。 

        ![安裝憑證2](media/azure-stack-edge-gpu-edge-container-registry/install-certificate-2.png) 

1. 安裝憑證之後，請在您的系統上重新開機 Docker 用戶端。

1. 登入 Edge 容器登錄。 輸入：

    `docker login <Edge container registry endpoint> -u <username> -p <password>`

    從 [ **裝置** ] 頁面提供 Edge container registry 端點，以及您從輸出取得的使用者名稱和密碼 `Get-HcsKubernetesContainerRegistryInfo` 。 

1. 使用 docker push 或 pull 命令，從 container registry 推送或提取容器映射。
 
    1. 從 Microsoft Container Registry 映射提取映射。 輸入：
        
        `docker pull <Full path to the container image in the Microsoft Container Registry>`
       
    1. 使用登錄的完整路徑來建立您所提取映射的別名。

        `docker tag <Path to the image in the Microsoft container registry> <Path to the image in the Edge container registry/Image name with tag>`

    1. 將映像推送至您的登錄。
    
        `docker push <Path to the image in the Edge container registry/Image name with tag>`

    1. 執行您推送至登錄的映射。
    
        `docker run -it --rm -p 8080:80 <Path to the image in the Edge container registry/Image name with tag>`

        以下是 pull 和 push 命令的範例輸出：

        ```powershell
        PS C:\WINDOWS\system32> docker login ecr.dbe-hw6h1t2.microsoftdatabox.com:31001 -u ase-ecr-user -p 3bbo2sOtDe8FouD
        WARNING! Using --password via the CLI is insecure. Use --password-stdin.
        Login Succeeded
        PS C:\WINDOWS\system32> docker pull mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        1.17.5-alpine: Pulling from oss/nginx/nginx
        Digest: sha256:5466bbc0a989bd1cd283c0ba86d9c2fc133491ccfaea63160089f47b32ae973b
        Status: Image is up to date for mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        PS C:\WINDOWS\system32> docker tag mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        PS C:\WINDOWS\system32> docker push ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        The push refers to repository [ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx]
        bba7d2385bc1: Pushed
        77cae8ab23bf: Pushed
        2.0: digest: sha256:b4c0378c841cd76f0b75bc63454bfc6fe194a5220d4eab0d75963bccdbc327ff size: 739
        PS C:\WINDOWS\system32> docker run -it --rm -p 8080:80 ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        2020/11/10 00:00:49 [error] 6#6: *1 open() "/usr/share/nginx/html/favicon.ico" failed (2: No such file or directory), client: 172.17.0.1, server: localhost, request: "GET /favicon.ico HTTP/1.1", host: "localhost:8080", referrer: "http://localhost:8080/"
        172.17.0.1 - - [10/Nov/2020:00:00:49 +0000] "GET /favicon.ico HTTP/1.1" 404 555 "http://localhost:8080/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.183 Safari/537.36" "-"
        ^C
        PS C:\WINDOWS\system32>    
        ```
    
1. 瀏覽至 `http://localhost:8080` 以檢視執行中的容器。 在此情況下，您會看到 nginx web 伺服器正在執行。

    ![查看正在執行的容器](media/azure-stack-edge-gpu-edge-container-registry/view-running-container-1.png)

    若要停止並移除該容器，請按 `Control+C`。

 

## <a name="use-edge-container-registry-images-via-kubernetes-pods"></a>透過 Kubernetes pod 使用 Edge container registry 映射

您現在可以從 Kubernetes pod 中，部署您在 Edge container registry 中推送的映射。

1. 若要部署映射，您必須透過 *kubectl* 設定叢集存取。 建立命名空間、使用者、將命名空間的存取權授與使用者，以及取得 *配置* 檔案。 請確定您可以連線到 Kubernetes pod。 
    
    遵循 [您 Azure Stack Edge PRO GPU 裝置上的 kubectl，以透過連線和管理 Kubernetes](azure-stack-edge-gpu-create-kubernetes-cluster.md)叢集的所有步驟。 

    以下是您裝置上的命名空間範例輸出，使用者可從中存取 Kubernetes 叢集。

    ```powershell
    [10.128.44.40]: PS>New-HcsKubernetesNamespace -Namespace myecr
    [10.128.44.40]: PS>New-HcsKubernetesUser -UserName ecruser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01URXdOVEF6TkRJek1Gb1hEVE13TVRFd016QXpOREl6TUZvd0ZURVRNQkVnNjOVRLWndCQ042cm1XQms2eXFwcXI1MUx6bApTaXMyTy91UEJ2YXNSSUUzdzgrbmEwdG1aTERZZ2F6MkQwMm42Q29mUmtyUTR2d1lLTnR1MlpzR3pUdz0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
        server: https://compute.dbe-hw6h1t2.microsoftdatabox.com:6443
        name: kubernetes
        ===================CUT=========================================CUT==============
        client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwRENDQWJpZ0F3SUJBZ0lJYmVWRGJSTzZ3ell3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURFeE1EVXdNelF5TXpCYUZ3MHlNVEV4TURreU16UTRNal
        ===================CUT=========================================CUT==============
        DMVUvN3lFOG5UU3k3b2VPWitUeHdzCjF1UDByMjhDZ1lCdHdRY0ZpcFh1blN5ak16dTNIYjhveFI2V3VWWmZldFFKNElKWEFXOStVWGhKTFhyQ2x4bUcKWHRtbCt4UU5UTzFjQVNKRVZWVDd6Tjg2ay9kSU43S3JIVkdUdUxlUDd4eGVjV2VRcWJrZEVScUsxN0liTXpiVApmbnNxc0dobEdmLzdmM21kTGtyOENrcWs5TU5aM3MvUVIwRlFCdk94ZVpuUlpTeDVGbUR5S1E9PQotLS0tLUVORCBSU0EgUFJJVkFURSBLRVktLS0tLQo=

    [10.128.44.40]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace myecr -UserName ecruser
    [10.128.44.40]: PS>kubectl get pods -n "myecr"
    No resources found.
    PS C:\WINDOWS\system32>
    ```  

2. 映射提取秘密已在裝置上的所有 Kubernetes 命名空間中設定。 您可以使用命令來取得秘密 `get secrets` 。 以下是範例輸出：

    ```powershell
    PS C:\WINDOWS\system32> .\kubectl.exe get secrets -n myecr
    NAME                  TYPE                                  DATA   AGE
    ase-ecr-credentials   kubernetes.io/dockerconfigjson        1      99m
    default-token-c7kww   kubernetes.io/service-account-token   3      107m
    sec-smbcredentials    microsoft.com/smb                     2      99m
    PS C:\WINDOWS\system32>   
    ```    

3. 使用 kubectl 將 pod 部署到您的命名空間。 使用下列各項 `yaml` 。 

    更換映射： `<image-name>` 將映射推送至容器登錄。 使用 imagePullSecrets 的名稱來參考命名空間中的密碼： `ase-ecr-credentials` 。
    
    ```yml
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx
    spec:
      containers:
      - name: nginx
        image: ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        imagePullPolicy: Always
      imagePullSecrets:
      - name: ase-ecr-credentials
    ```

4. 使用 [套用] 命令，將部署套用至您建立的命名空間。 確認容器正在執行。 以下是範例輸出：
   
    ```yml
    PS C:\Windows\System32> .\kubectl.exe apply -f .\deployment.yml -n myecr
    pod/nginx configured
    PS C:\Windows\System32> .\kubectl.exe get pods -n myecr
    NAME    READY   STATUS    RESTARTS   AGE
    nginx   1/1     Running   0          27m
    PS C:\Windows\System32>
    ```

## <a name="delete-container-registry-images"></a>刪除 container registry 映射

Edge 容器登錄儲存體裝載于 Azure Stack Edge Pro 裝置內的本機共用，受限於裝置上的可用儲存空間。 您必須負責使用 Docker HTTP v2 API (從 container registry 中刪除未使用的 docker 映射 https://docs.docker.com/registry/spec/api/) 。  

若要移除一或多個容器映射，請遵循下列步驟：

1. 將映射名稱設定為您想要刪除的映射。

    ```powershell
    PS C:\WINDOWS\system32> $imageName="nginx"    
    ```

1. 將 container registry 的使用者名稱和密碼設定為 PS 認證

    ```powershell
    PS C:\WINDOWS\system32> $username="ase-ecr-user"
    PS C:\WINDOWS\system32> $password="3bbo2sOtDe8FouD"
    PS C:\WINDOWS\system32> $securePassword = ConvertTo-SecureString $password -AsPlainText -Force
    PS C:\WINDOWS\system32> $credential = New-Object System.Management.Automation.PSCredential ($username, $securePassword)    
    ```

1. 列出與影像相關聯的標記

    ```powershell
    PS C:\WINDOWS\system32> $tags = Invoke-RestMethod -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/nginx/tags/list" | Select-Object -ExpandProperty tags
    PS C:\WINDOWS\system32> $tags
    2.0
    PS C:\WINDOWS\system32> $tags = Invoke-RestMethod -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/tags/list" | Select-Object -ExpandProperty tags
    PS C:\WINDOWS\system32> $tags
    2.0
    PS C:\WINDOWS\system32>    
    ```
  
1. 列出與您想要刪除之標記相關聯的摘要。 這會使用上述命令的輸出中的 $tags。 如果您有多個標記，請選取其中一個，並在下一個命令中使用。

    ```powershell
    PS C:\WINDOWS\system32> $response = Invoke-WebRequest -Method Head -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/manifests/$tags" -Headers @{ 'Accept' = 'application/vnd.docker.distribution.manifest.v2+json' }
    PS C:\WINDOWS\system32> $digest = $response.Headers['Docker-Content-Digest']
    PS C:\WINDOWS\system32> $digest
    sha256:b4c0378c841cd76f0b75bc63454bfc6fe194a5220d4eab0d75963bccdbc327ff
    PS C:\WINDOWS\system32>    
    ```
1. 使用映射的摘要來刪除映射：標記

    ```powershell
    PS C:\WINDOWS\system32> Invoke-WebRequest -Method Delete -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/manifests/$digest" | Select-Object -ExpandProperty StatusDescription    
    ```

當您刪除未使用的影像之後，與未參考影像相關聯的空間會由每晚執行的進程自動回收。 

## <a name="next-steps"></a>後續步驟

- [在您的 Azure Stack Edge Pro 上部署無狀態應用程式](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md)。
