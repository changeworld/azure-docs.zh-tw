---
title: 教學課程 - 使用 Azure Pipelines 在 Azure VM 上進行 CI/CD
description: 在本教學課程中，您將了解如何使用 YAML 型 Azure 管線將 Node.js 應用程式的持續整合 (CI) 和持續部署 (CD) 安裝至 Azure VM。
author: ushan
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 1/3/2020
ms.author: ushan
ms.custom: devops
ms.openlocfilehash: 8aa53d4b08a4a0bdaa4e1f12169811ae88edbd2f
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86501869"
---
# <a name="tutorial-deploy-your-app-to-linux-virtual-machines-in-azure-using-azure-devops-services-and-azure-pipelines"></a>教學課程：使用 Azure DevOps Services 和 Azure Pipelines 在 Azure 中將應用程式部署至 Linux 虛擬機器

持續整合 (CI) 與持續部署 (CD) 可形成一個讓您在每次程式碼認可後建置、發行和部署程式碼的管線。 本文件包含使用 Azure Pipelines 來設定 CI/CD 管線以執行多電腦部署的相關步驟。

Azure Pipelines 提供了一組完整且功能齊全的 CI/CD 自動化工具，可用來部署至內部部署或任何雲端上的虛擬機器。

在本教學課程中，您將設定 YAML 型 CI/CD 管線，將您的應用程式部署至以 Linux 虛擬機器作為資源，且以各項資源作為 Web 伺服器以執行應用程式的 Azure Pipelines [環境](/azure/devops/pipelines/process/environments?view=azure-devops)。

您會了解如何：

> [!div class="checklist"]
> * 取得範例應用程式。
> * 建立 YAML 型 Azure Pipelines CI 管線以建置範例應用程式。
> * 建立 Azure 虛擬機器的 Azure Pipelines 環境
> * 建立 Azure Pipelines CD 管線。
> * 執行手動和 CI 觸發部署。

## <a name="before-you-begin"></a>開始之前

* 登入您的 Azure DevOps Services 組織 ( **https://dev.azure.com/** )。 
  您可以取得[免費的 Azure DevOps Services 組織](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308)。

  > [!NOTE]
  > 如需詳細資訊，請參閱[連線至 Azure DevOps Services](/azure/devops/organizations/projects/connect-to-projects?view=vsts)。

*  若是部署目標，您需要 Linux 虛擬機器。  如需詳細資訊，請參閱[使用 Azure CLI 來建立和管理 Linux VM](./tutorial-manage-vm.md)。

*  開啟虛擬機器的輸入連接埠 80。 如需詳細資訊，請參閱[使用 Azure 入口網站建立網路安全性群組](../../virtual-network/tutorial-filter-network-traffic.md)。

## <a name="get-your-sample-app-code"></a>取得您的範例應用程式程式碼

如果您在 GitHub 中已有想要部署的應用程式，您可以嘗試為該程式碼建立管線。

不過，如果您是新的使用者，您可以利用我們的範例程式碼順利展開作業。 在此情況下，請在 GitHub 中派生此存放庫：

#### <a name="java"></a>[Java](#tab/java)

```
https://github.com/spring-projects/spring-petclinic
```

> [!NOTE]
> Petclinic 是使用 [Maven](https://spring.io/guides/gs/maven/) 建立的 [Java Spring Boot](https://spring.io/guides/gs/spring-boot) 應用程式。

#### <a name="javascript"></a>[JavaScript](#tab/java-script)

```
https://github.com/azure-devops/fabrikam-node
```

> [!NOTE]
> 此 Node.js 應用程式是透過 [Yeoman](https://yeoman.io/learning/index.html) 建置的。 它會使用 Express、Bower 和 Grunt。 同時其具有某些 npm 套件作為相依項目。
> 此範例也包含設定 Nginx 和部署應用程式的指令碼。 其執行於虛擬機器上。 具體來說，指令碼可以：
> 1. 安裝 Node、Nginx 和 PM2。
> 2. 設定 Nginx 和 PM2。
> 3. 啟動 Node 應用程式。

* * * 

## <a name="prerequisites-for-the-linux-vm"></a>Linux VM 的必要條件

前述的範例應用程式已在 Ubuntu 16.04 上測試過，建議您在本快速入門中使用相同版本的 Linux VM。
請根據用於應用程式的執行階段堆疊，依照以下說明的其他步驟操作。

#### <a name="java"></a>[Java](#tab/java)

- 若要部署以 Java Spring Boot 和 Spring Cloud 為基礎的應用程式，請使用[這個](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-ubuntu-1804)範本在 Azure 中建立 Linux VM，以提供完整支援的 OpenJDK 型執行階段。
- 若要在 Tomcat 伺服器上部署 Java Servlet，請使用[這個](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-ubuntu-1804) Azure 範本建立具有 Java 8 的 Linux VM，並[將 Tomcat 9.x 設定為服務](https://tomcat.apache.org/tomcat-9.0-doc/setup.html)。
- 若要部署 Java EE 型應用程式，請使用 Azure 範本建立 [Linux VM + Java + WebSphere 9.x](https://azuremarketplace.microsoft.com/marketplace/apps/midvision.websphere-application-server-nde-90) 或 [Linux VM + Java + WebLogic 12.x](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.20191009-arm-oraclelinux-wls-admin) 或 [Linux VM +Java](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-ubuntu-1804) + WildFly/JBoss 14 

#### <a name="javascript"></a>[JavaScript](#tab/java-script)

若要安裝 Javascript 應用程式或 Node.js 應用程式，您將需要具有 Nginx Web 伺服器的 Linux VM，才能部署應用程式。
如果您還沒有具有 Nginx 的 Linux VM，請使用[此範例](./quick-create-cli.md)中的步驟，在 Azure 中立即建立一個。

* * * 

## <a name="create-an-azure-pipelines-environment-with-azure-virtual-machines"></a>建立具有 Azure 虛擬機器的 Azure Pipelines 環境

虛擬機器可以新增為[環境中的資源](/azure/devops/pipelines/process/environments)，而且可作為多電腦部署的目標。 環境內的部署歷程記錄檢視可提供從 VM 到管線、再到認可的追蹤。

您可以在「管線」  區段內的「環境」  中樞建立環境。
1.  登入您的 Azure DevOps 組織，然後瀏覽至您的專案。
2.  在您的專案中，瀏覽至 [管線]  頁面。 然後，選擇 [環境]  並按一下 [建立環境]  。 指定環境的 [名稱]  (必要) 和 [描述]  。
3.  選擇 [虛擬機器]  作為要新增至環境的 [資源]  ，然後按 [下一步]  。
4.  選擇 [作業系統] (Windows/Linux) 和 [複製 PS 註冊指令碼]  。 
5.  現在，在每個要註冊至此環境的目標 VM 上，從系統管理員 PowerShell 命令提示字元執行複製的指令碼。
    > [!NOTE]
    > - 已登入使用者的個人存取權杖會在指令碼中預先插入，並且在同一天到期而使複製的指令碼後續無法再使用。
    > - 如果您的 VM 已執行任何代理程式，請提供「代理程式」的唯一名稱，以向環境註冊。
6.  VM 在註冊後，就會開始顯示為環境的 [資源] 索引標籤下的環境資源。

    ![VMcreation](media/tutorial-deploy-vms-azure-pipelines/vm-creation.png)

7.  若要新增更多 VM，您可以按一下 [新增資源] 並選擇 [虛擬機器] 作為資源，以再次檢視及複製指令碼。 對於所有要新增至此環境的 VM 而言，此指令碼均維持不變。 
8.  每個機器都會與 Azure Pipelines 互動，以協調應用程式的部署。

    ![VMresource_view](media/tutorial-deploy-vms-azure-pipelines/vm-resourceview.png)

9. 您可以將標籤新增至 VM 作為互動式 PS 註冊指令碼的一部分，或者，您也可以在資源檢視中，按一下每個 VM 資源結尾處的三個點，以新增/移除相同的標籤。

   您指派的標籤可讓您在部署作業中使用環境時，將部署限定於特定的虛擬機器。 每個標籤的上限為 256 個字元，但您可以使用的標籤數目並沒有限制。

   ![VMtags](media/tutorial-deploy-vms-azure-pipelines/vm-tags.png)

* * * 

## <a name="define-your-ci-build-pipeline"></a>定義您的 CI 組建管線

您將需要用來發佈 Web 應用程式的持續整合 (CI) 組建管線，以及可以在 Ubuntu 伺服器本機執行的部署指令碼。 請根據您想要使用的執行階段來設定 CI 組建管線。 

1. 登入您的 Azure DevOps 組織，然後瀏覽至您的專案。

1. 在您的專案中，瀏覽至 [管線]  頁面。 然後，選擇用來建立新管線的動作。

1. 先選取 **GitHub** 作為原始程式碼的位置，以逐步完成精靈的步驟。

1. 系統可能會將您重新導向至 GitHub 以進行登入。 若是如此，請輸入您的 GitHub 認證。

1. 在存放庫清單出現時，選取您所需的範例應用程式存放庫。

1. Azure Pipelines 會分析您的存放庫，並建議適用的管線範本。

#### <a name="java"></a>[Java](#tab/java)

選取**入門**範本，並複製下列 YAML 程式碼片段，以使用 Apache Maven 建置您的 Java 專案並執行測試：

```YAML
- job: Build
  displayName: Build Maven Project
  steps:
  - task: Maven@3
    displayName: 'Maven Package'
    inputs:
      mavenPomFile: 'pom.xml'
  - task: CopyFiles@2
    displayName: 'Copy Files to artifact staging directory'
    inputs:
      SourceFolder: '$(System.DefaultWorkingDirectory)'
      Contents: '**/target/*.?(war|jar)'
      TargetFolder: $(Build.ArtifactStagingDirectory)
  - upload: $(Build.ArtifactStagingDirectory)
    artifact: drop
```

如需詳細指引，請遵循[使用 Maven 建置 Java 應用程式](/azure/devops/pipelines/ecosystems/java)中所述的步驟。

#### <a name="javascript"></a>[JavaScript](#tab/java-script)

選取**入門**範本，並複製下列 YAML 程式碼片段，以使用 npm 建置一般 Node.js 專案。

```YAML
- stage: Build
  displayName: Build stage
  jobs:  
  - job: Build
    displayName: Build
    - task: NodeTool@0
      inputs:
        versionSpec: '10.x'
      displayName: 'Install Node.js'
    - script: |
        npm install
        npm run build --if-present
        npm run test --if-present
      displayName: 'npm install, build and test'
    - task: ArchiveFiles@2
      displayName: 'Archive files'
      inputs:
        rootFolderOrFile: '$(System.DefaultWorkingDirectory)'
        includeRootFolder: false
        archiveType: zip
        archiveFile: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
        replaceExistingArchive: true
    - upload: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
      artifact: drop
```

如需詳細指引，請遵循[使用 gulp 建置 Node.js 應用程式](/azure/devops/pipelines/ecosystems/javascript)中的步驟。

- 查看管線以了解其作用。 請確定所有預設輸入均適用於您的程式碼。

- 選取 [儲存並執行]  ，並選取 [直接認可至主要分支]  ，然後再次選擇 [儲存並執行]  。

- 新的執行隨即啟動。 等待執行完成。

* * * 

## <a name="define-cd-steps-to-deploy-to-the-linux-vm"></a>定義要部署至 Linux VM 的 CD 步驟

1. 藉由參考您先前使用下列 YAML 語法取得的環境和 VM 資源，編輯上述管線並納入[部署作業](/azure/devops/pipelines/process/deployment-jobs)：

   ```YAML
   jobs:  
   - deployment: VMDeploy
     displayName: web
     environment:
       name:  <environment name>
       resourceType: VirtualMachine
       tags: web1
     strategy:
   ```
2. 您可以藉由指定您為環境中的每個虛擬機器定義的**標籤**，從環境中選取特定幾組虛擬機器來接收部署。
[這裡](/azure/devops/pipelines/yaml-schema?view=azure-devops&tabs=schema#deployment-job)提供部署作業的完整 YAML 結構描述。

3. 您可以指定 `runOnce` 或 `rolling` 作為部署策略。 

   `runOnce` 是最簡單的部署策略，其中，所有生命週期勾點 (也就是 `preDeploy`、`deploy`、`routeTraffic` 和 `postRouteTraffic`) 都會執行一次。 然後會執行 `on:` `success` 或 `on:` `failure`。

   以下是 `runOnce` 的範例 YAML 程式碼片段：
   ```YAML
   jobs:
   - deployment: VMDeploy
     displayName: web
     pool:
       vmImage: 'Ubuntu-16.04'
     environment:
       name: <environment name>
       resourceType: VirtualMachine
     strategy:
         runOnce:
           deploy:
             steps:
             - script: echo my first deployment
   ```

4. 以下是 YAML 程式碼片段的範例，可供您在每個反覆項目中用來定義虛擬機器更新的輪流策略，最多 5 個目標。 `maxParallel` 將決定可以平行部署到的目標數目。 選取範圍可說明在要部署到的目標以外，有多少數量或百分比的目標必須隨時保持可用狀態。 這也可用來判斷部署期間的成功和失敗狀況。

   ```YAML
   jobs: 
   - deployment: VMDeploy
     displayName: web
     environment:
       name: <environment name>
       resourceType: VirtualMachine
     strategy:
         rolling:
           maxParallel: 5  #for percentages, mention as x%
           preDeploy:
             steps:
             - download: current
               artifact: drop
             - script: echo initialize, cleanup, backup, install certs
           deploy:
             steps:
             - task: Bash@3
               inputs:
                 targetType: 'inline'
                 script: |
                   # Modify deployment script based on the app type
                   echo "Starting deployment script run"
                   sudo java -jar '$(Pipeline.Workspace)/drop/**/target/*.jar'
           routeTraffic:
             steps:
             - script: echo routing traffic
           postRouteTraffic:
             steps:
             - script: echo health check post-route traffic
           on:
             failure:
               steps:
               - script: echo Restore from backup! This is on failure
             success:
               steps:
               - script: echo Notify! This is on success
   ```

   每次執行此作業時，都會針對您已建立並註冊 VM 的 `<environment name>` 環境記錄部署歷程記錄。

## <a name="run-your-pipeline-and-get-traceability-views-in-environment"></a>在環境中執行您的管線並取得可追蹤性檢視
環境的部署檢視會提供認可和工作項目的完整追蹤性，以及每個環境/資源的跨管線部署歷程記錄。

![VMDeployments_view](media/tutorial-deploy-vms-azure-pipelines/vm-deployments.png)
  
![VMjobs_view](media/tutorial-deploy-vms-azure-pipelines/vm-jobsview.png)

## <a name="next-steps"></a>後續步驟
- 您可以繼續進行[自訂您剛建立的管線](/azure/devops/pipelines/customize-pipeline)。
- 若要了解您可以在 YAML 管線中執行的其他動作，請參閱 [YAML 結構描述參考](/azure/devops/pipelines/yaml-schema)。
- 若要了解如何部署 LAMP (Linux、Apache、MySQL 和 PHP) 堆疊，請前進到下一個教學課程。

> [!div class="nextstepaction"]
> [部署 LAMP 堆疊](tutorial-lamp-stack.md)
