---
title: 快速入門 - 在 Red Hat Enterprise Linux (RHEL) 上將 JBoss 企業應用程式平台 (EAP) 部署至 Azure VM 和虛擬機器擴展集
description: 如何使用 Red Hat JBoss EAP 在 Azure RHEL VM 和虛擬機器擴展集上部署企業 Java 應用程式。
author: theresa-nguyen
ms.author: bicnguy
ms.topic: quickstart
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.assetid: 8a4df7bf-be49-4198-800e-db381cda98f5
ms.date: 10/30/2020
ms.openlocfilehash: ce07a0667b1fd4b439f061966e4ee0b1112578c4
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94413202"
---
# <a name="deploy-enterprise-java-applications-to-azure-with-jboss-eap-on-red-hat-enterprise-linux"></a>使用 Red Hat Enterprise Linux 上的 JBoss EAP 將企業 Java 應用程式部署至 Azure

本文中的 Azure 快速入門範本會說明如何使用 [Red Hat Enterprise Linux (RHEL)](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux)，將 [JBoss 企業應用程式平台 (EAP)](https://www.redhat.com/en/technologies/jboss-middleware/application-platform) 部署至 Azure 虛擬機器 (VM) 和虛擬機器擴展集。 您將使用範例 Java 應用程式來驗證部署。 

JBoss EAP 是一個開放原始碼的應用程式伺服器平台。 其可為您的 Java 應用程式提供企業級的安全性、擴充性和效能。 RHEL 是一個開放原始碼作業系統 (OS) 平台。 其可讓您調整現有的應用程式，並在所有環境中推出新興技術。 

JBoss EAP 和 RHEL 包含在任何環境中建置、執行、部署及管理企業 Java 應用程式所需的一切。 此組合是開放原始碼解決方案，適用於內部部署、虛擬環境，以及私人、公用或混合式雲端。

## <a name="prerequisites"></a>必要條件 

* 具有有效訂用帳戶的 Azure 帳戶。 若要取得 Azure 訂用帳戶，請啟用 [Visual Studio 訂閱者的 Azure 點數](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)或[建立免費的帳戶](https://azure.microsoft.com/pricing/free-trial)。

* JBoss EAP 安裝。 您需要有一個 Red Hat 帳戶，且具有 JBoss EAP 的 Red Hat 訂用帳戶管理 (RHSM) 權利。 此權利可讓您下載經過 Red Hat 測試和認證的 JBoss EAP 版本。  

  如果您沒有 EAP 權利，請在開始使用之前，先取得 [JBoss EAP 評估訂用帳戶](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation)。 若要建立新的 Red Hat 訂用帳戶，請移至 [Red Hat 客戶入口網站](https://access.redhat.com/)並設定帳戶。

* [Azure CLI](https://docs.microsoft.com/cli/azure/overview)。

* RHEL 選項。 選擇 [隨用隨付 (PAYG)] 或 [自備訂用帳戶 (BYOS)]。 使用 BYOS 時，您在部署快速入門範本之前，必須先啟用 [Red Hat Cloud Access](https://access.redhat.com/) RHEL Gold 映像。

## <a name="java-ee-and-jakarta-ee-application-migration"></a>Java EE 和 Jakarta EE 應用程式移轉

### <a name="migrate-to-jboss-eap"></a>遷移至 JBoss EAP
JBoss EAP 7.2 和7.3 是 Java Enterprise Edition (Java EE) 8 和 Jakarta EE 8 規格的認證實作。 JBoss EAP 會針對高可用性 (HA) 叢集、傳訊和分散式快取等功能，提供預先設定的選項。 其也可讓使用者使用 JBoss EAP 提供的各種 API 和服務來撰寫、部署和執行應用程式。  

如需 JBoss EAP 的詳細資訊，請參閱 [JBoss EAP 7.2 簡介](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html-single/introduction_to_jboss_eap/index)或 [JBoss EAP 7.3 簡介](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/introduction_to_jboss_eap/index)。

 #### <a name="applications-on-jboss-eap"></a>JBoss EAP 上的應用程式

* **Web 服務應用程式**。 Web 服務提供在軟體應用程式間交互操作的標準方式。 每個應用程式都可以在不同的平台和架構上執行。 這些 Web 服務可促進內部與異質子系統通訊。 

  若要深入了解，請參閱[在 EAP 7.2 上開發 Web 服務應用程式](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_web_services_applications/index)或[在 EAP 7.3 上開發 Web 服務應用程式](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_web_services_applications/index)。

* **Enterprise Java Bean (EJB) 應用程式**。 EJB 3.2 是用來開發分散式、交易式、安全、可攜式 Java EE 和 Jakarta EE 應用程式的 API。 EJB 使用稱為 Enterprise Beans 的伺服器端元件，以低耦合方式來實作應用程式的商務邏輯，以鼓勵重複使用。 

  若要深入了解，請參閱[在 EAP 7.2 上開發 EJB 應用程式](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_ejb_applications/index)或[在 EAP 7.3 上開發 EJB 應用程式](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_ejb_applications/index)。

* **Hibernate 應用程式**。 開發人員和系統管理員可以使用 JBoss EAP 來開發及部署 Java Persistence API (JPA) 和 Hibernate 應用程式。 Hibernate Core 是適用於 Java 語言的物件關聯式對應架構。 其提供將物件導向網域模型對應至關聯式資料庫的架構，讓應用程式可避免直接與資料庫互動。 

  Hibernate Entity Manager 會依照 [JPA 2.1 規格](https://www.jcp.org/en/jsr/overview)所定義，實作程式設計介面和生命週期規則。 搭配 Hibernate Annotations，此包裝函式會在成熟的 Hibernate Core 之上，實作完整 (和獨立) 的 JPA 解決方案。 
  
  若要深入了解 Hibernate，請瀏覽 [EAP 7.2 上的 JPA](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/development_guide/java_persistence_api) 或 [EAP 7.3 上的 Jakarta Persistence](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/development_guide/java_persistence_api)。

#### <a name="red-hat-migration-toolkit-for-applications"></a>適用於應用程式的 Red Hat 移轉工具組
[適用於應用程式的 Red Hat 移轉工具組 (MTA)](https://developers.redhat.com/products/mta/overview) 是 Java 應用程式伺服器的移轉工具。 使用此工具可從另一個應用程式伺服器遷移至 JBoss EAP。 其可搭配用於 [Eclipse IDE](https://www.eclipse.org/ide/)、[Red Hat CodeReady Workspaces](https://developers.redhat.com/products/codeready-workspaces/overview) 和適用於 Java 的 [Visual Studio Code](https://code.visualstudio.com/docs/languages/java) 的 IDE 外掛程式。 

MTA 是免費的開放原始碼工具：
* 自動進行應用程式分析。
* 支援投入量估計。
* 加速程式碼移轉。
* 支援容器化。
* 與 Azure Workload Builder 整合。

### <a name="migrate-jboss-eap-from-on-premises-to-azure"></a>將 JBoss EAP 從內部部署遷移至 Azure
RHEL 上 JBoss EAP 的 Azure Marketplace 供應項目將在 20 分鐘內安裝和佈建於 Azure VM 上。 您可以從 [Azure Marketplace](https://azuremarketplace.microsoft.com/) 存取這些供應項目。

此 Azure Marketplace 供應項目包含 EAP 和 RHEL 版本的各種組合，以支援您的需求。 JBoss EAP 一律為 BYOS，但在 RHEL OS 中，您可以選擇 BYOS 或 PAYG。 Azure Marketplace 供應項目包括將 RHEL 上的 EAP JBoss 規劃為獨立或叢集式 VM 的選項：

* RHEL 7.7 VM 上的 JBoss EAP 7.2 (PAYG)
* RHEL 8.0 VM 上的 JBoss EAP 7.2 (PAYG)
* RHEL 8.0 VM 上的 JBoss EAP 7.3 (PAYG)
* RHEL 7.7 VM 上的 JBoss EAP 7.2 (BYOS)
* RHEL 8.0 VM 上的 JBoss EAP 7.2 (BYOS)
* RHEL 8.0 VM 上的 JBoss EAP 7.3 (BYOS)

除了 Azure Marketplace 供應項目，您還可以使用快速入門範本開始進行 Azure 移轉旅程。 這些快速入門包含預先建立的 Azure Resource Manager (ARM) 範本，以及在各種設定和版本組合中用以部署 RHEL 上的 JBoss EAP 的指令碼。 您會具有：

* 負載平衡器。
* 用於負載平衡的私人 IP 和 VM。
* 具有單一子網路的虛擬網路。
* VM 設定 (叢集或獨立)。
* 範例 Java 應用程式。

這些範本的解決方案架構包括：

* 獨立 RHEL VM 上的 JBoss EAP。
* 跨多個 RHEL VM 叢集化的 JBoss EAP。
* 透過 Azure 虛擬機器擴展集叢集化的 JBoss EAP。

#### <a name="linux-workload-migration-for-jboss-eap"></a>適用於 JBoss EAP 的 Linux 工作負載移轉
Azure Workload Builder 會簡化內部部署 Java 應用程式至 Azure 的概念證明、評估和移轉程序。 Workload Builder 會與 Azure Migrate 探索工具整合，以識別 JBoss EAP 伺服器。 接著會以動態方式產生 JBoss EAP 伺服器部署的 Ansible 劇本。 它會使用 Red Hat MTA 工具，將伺服器從其他應用程式伺服器遷移至 JBoss EAP。 

簡化移轉的步驟包括：
1. **評估**。 使用 Azure VM 或虛擬機器擴展集來評估 JBoss EAP 叢集。
1. **評量**。 掃描應用程式與基礎結構。
1. **基礎結構設定**。 建立工作負載設定檔。
1. **部署和測試**。 部署、遷移和測試工作負載。
1. **部署後設定**。 與資料、監視、安全性、備份等功能整合。

## <a name="server-configuration-choice"></a>伺服器組態選擇

若要部署 RHEL VM，您可以選擇 PAYG 或 BYOS。 來自 [Azure Marketplace](https://azuremarketplace.microsoft.com) 的映像預設為 PAYG。 如果您有自己的 RHEL OS 映像，請部署 BYOS 類型 RHEL VM。 在您部署 VM 或虛擬機器擴展集之前，請先透過 Cloud Access 確定您的 RHSM 帳戶具有 BYOS 權利。

JBoss EAP 具有強大的管理功能，並提供其應用程式的功能和 API。 這些管理功能會根據您用來啟動 JBoss EAP 的作業模式而有所不同。 RHEL 和 Windows Server 上均提供其支援。 JBoss EAP 提供獨立伺服器作業模式來管理離散執行個體。 其也提供受控網域作業模式，用於從單一控制點管理執行個體群組。 

> [!NOTE]
> Microsoft Azure 不支援 JBoss EAP 管理的網域，因為 Azure 基礎結構服務會管理 HA 功能。 

環境變數 `EAP_HOME` 表示 JBoss EAP 安裝的路徑。 請使用下列命令，以獨立模式啟動 JBoss EAP 服務：

```
$EAP_HOME/bin/standalone.sh
```
    
此啟動指令碼會使用 EAP_HOME/bin/standalone.conf 檔案來設定一些預設喜好設定，例如 JVM 選項。 您可以自訂此檔案中的設定。 JBoss EAP 依預設會使用 standalone.xml 組態檔以獨立模式啟動，但您可以使用不同的模式加以啟動。 

如需可用獨立組態檔及其使用方式的詳細資訊，請參閱[適用於 EAP 7.2 的獨立伺服器組態檔](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files)或[適用於 EAP 7.3 的獨立伺服器組態檔](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files)。 

若要使用不同的設定來啟動 JBoss EAP，請使用 `--server-config` 引數。 例如︰
    
 ```
 $EAP_HOME/bin/standalone.sh --server-config=standalone-full.xml
 ```
    
如需所有可用的啟動指令碼引數及其用途的完整清單，請使用 `--help` 引數。 如需詳細資訊，請參閱 [EAP 7.2 上伺服器執行階段引數](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime)或 [EAP 7.3 上的伺服器執行階段引數](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime)。
    
JBoss EAP 也可以在叢集模式中運作。 JBoss EAP 叢集傳訊可讓 JBoss EAP 傳訊伺服器群組分擔訊息處理負載。 叢集中的每個作用中節點都是作用中 JBoss EAP 傳訊伺服器，其會管理自己的訊息並處理自己的連線。 若要深入了解，請參閱 [EAP 7.2 上的叢集概觀](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuring_messaging/clusters_overview)或 [EAP 7.3 上的叢集概觀](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuring_messaging/clusters_overview)。 

## <a name="support-and-subscription-notes"></a>援和訂用帳戶注意事項
這些快速入門範本會以下列形式提供： 

- RHEL OS 會透過 Red Hat Gold 映像模型，以 PAYG 或 BYOS 形式提供。
- JBoss EAP 僅以 BYOS 形式提供。

#### <a name="using-rhel-os-with-the-payg-model"></a>使用 RHEL OS 搭配 PAYG 模型

根據預設，這些快速入門範本會使用 Azure Marketplace 中的隨選 RHEL 7.7 或 8.0 PAYG 映像。 除了一般計算、網路和儲存體成本以外，PAYG 映像會有額外的每小時 RHEL 訂用帳戶費用。 同時，執行個體會註冊到您的 Red Hat 訂用帳戶。 這表示您將將使用您的其中一個權利。 

此 PAYG 映像會導致「雙重計費」。 您可藉由建立自己的 RHEL 映像來避免此問題。 若要深入了解，請閱讀有關[如何為 Microsoft Azure 佈建 RHEL VM](https://access.redhat.com/articles/uploading-rhel-image-to-azure) 的 Red Hat 知識庫文章。 或啟用您的 [Red Hat Cloud Access](https://access.redhat.com/) RHEL Gold 映像。

如需 PAYG VM 定價的詳細資訊，請參閱 [Red Hat Enterprise Linux 定價](https://azure.microsoft.com/pricing/details/virtual-machines/red-hat/)。 若要在 PAYG 模型中使用 RHEL，您將需要具有 [Azure Marketplace 上的 RHEL 7.7](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux77-ARM) 或 [Azure Marketplace 上的 RHEL 8.0](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux80-ARM) 指定付款方式的 Azure 訂用帳戶。 這些供應項目需使用在 Azure 訂用帳戶中指定的付款方式。

#### <a name="using-rhel-os-with-the-byos-model"></a>使用 RHEL OS 搭配 BYOS 模型

若要使用適用於 RHEL OS 的 BYOS，您必須有具備權利的有效 Red Hat 訂用帳戶，才能在 Azure 中使用 RHEL OS。 在使用 BYOS 模型部署 RHEL OS 之前，請先完成下列必要條件：

1. 確定您已將 RHEL OS 和 JBoss EAP 權利連結至您的 Red Hat 訂用帳戶。
2. 授權您的 Azure 訂用帳戶識別碼使用 RHEL BYOS 映像。 請依照 [Red Hat 訂用帳戶管理文件](https://access.redhat.com/documentation/en/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/con-enable-subs)的指示完成此程序，其中包括下列步驟：

   1. 在 Red Hat Cloud Access 儀表板中啟用 Microsoft Azure 作為提供者。

   1. 新增 Azure 訂用帳戶識別碼。

   1. 在 Microsoft Azure 上為 Cloud Access 啟用新產品。
    
   1. 為您的 Azure 訂用帳戶啟用 Red Hat Gold 映像。 如需詳細資訊，請參閱 [Microsoft Azure 上的 Red Hat Gold 映像](https://access.redhat.com/documentation/en/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure)。

   1. 等候 Red Hat Gold 映像在您的 Azure 訂用帳戶中可供使用。 這些映像通常會在提交後的三小時內可供使用。
    
3. 接受 RHEL BYOS 映像的 Azure Marketplace 條款及條件。 您可以執行下列 Azure CLI 命令，以完成此程序。 如需詳細資訊，請參閱 [Azure 中的 RHEL BYOS Gold 映像](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/byos)文件。 請務必執行最新的 Azure CLI 版本。

   1. 開啟 Azure CLI 工作階段，並使用您的 Azure 帳戶進行驗證。 如需協助，請參閱[使用 Azure CLI 登入](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)。

   1. 執行下列 CLI 命令，以確認您的訂用帳戶中有 RHEL BYOS 映像可供使用。 如果您未在此取得任何結果，請確定您的 Azure 訂用帳戶已啟用 RHEL BYOS 映像。
   
      ```
      az vm image list --offer rhel-byos --all
      ```

   1. 執行下列命令，以分別接受 RHEL 7.7 BYOS 和 RHEL 8.0 BYOS 的 Azure Marketplace 條款：
      ```
      az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm77
      ``` 

      ```
      az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm8
      ``` 
   
您的訂用帳戶現在已準備好在 Azure 虛擬機器上部署 RHEL 7.7 或 8.0 BYOS。

#### <a name="using-jboss-eap-with-the-byos-model"></a>使用 JBoss EAP 搭配 BYOS 模型

JBoss EAP 僅透過 BYOS 模型在 Azure 上提供。 部署此範本時，您必須提供 RHSM 認證以及具有有效 EAP 權利的 RHSM 集區識別碼。 如果您沒有 EAP 權利，請在開始使用之前先取得 [JBoss EAP 評估訂用帳戶](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation)。

## <a name="deployment-options"></a>部署選項

您可以透過下列方式來部署範本：

- **PowerShell**。 執行下列命令以部署範本： 

  ```
  New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri <raw link to the template which can be obtained from github>
  ```
 
  如需關於安裝和設定 Azure PowerShell 的資訊，請參閱 [PowerShell 文件](https://docs.microsoft.com/powershell/azure/)。  

- **Azure CLI**。 執行下列命令以部署範本：

  ```
  az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  az group deployment create --resource-group <my-resource-group> --template-uri <raw link to the template which can be obtained from github>
  ```

  如需關於安裝和設定 Azure CLI 的詳細資訊，請參閱[安裝 CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

- **Azure 入口網站**。 您可以移至 Azure 快速入門範本以部署至 Azure 入口網站，如下一節所說明。 在快速入門中，選取 [部署至 Azure] 或 [在 GitHub 上瀏覽] 按鈕。

## <a name="azure-quickstart-templates"></a>Azure 快速入門範本

首先，您可以根據您的部署目標，使用「RHEL 上的 JBoss EAP」的下列其中一個快速入門範本：

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-standalone-rhel/"> RHEL 上的 JBoss EAP (獨立 VM)</a>。 這會將 Azure 上名為 JBoss-EAP 的 Web 應用程式部署至在 RHEL 7.7 或 8.0 VM 上執行的 JBoss EAP 7.2 或 7.3。

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-clustered-multivm-rhel/">RHEL 上的 JBoss EAP (多個叢集式 VM)</a>。 這會在執行於 *n* 個 RHEL 7.7 或 8.0 VM 的 JBoss EAP 7.2 或 7.3 叢集上，部署名為 eap-session-replication 的 Web 應用程式。 *n* 值由使用者決定。 所有 VM 都會新增至負載平衡器的後端集區。

* <a href="https://azure.microsoft.com/en-us/resources/templates/jboss-eap-clustered-vmss-rhel/"> RHEL 上的 JBoss EAP (叢集式虛擬機器擴展集)</a>。 這會在執行於 RHEL 7.7 或 8.0 虛擬機器擴展集的 JBoss EAP 7.2 或 7.3 叢集上，部署名為 eap-session-replication 的 Web 應用程式。

## <a name="resource-links"></a>資源連結

* [Azure Hybrid Benefit](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing)
* [為 Azure App Service 設定 Java 應用程式](https://docs.microsoft.com/azure/app-service/configure-language-java)
* [Azure Red Hat OpenShift 上的 JBoss EAP](https://azure.microsoft.com/services/openshift/)
* [Azure App Service Linux 上的 JBoss EAP](https://docs.microsoft.com/azure/app-service/quickstart-java)
* [在 Azure App Service 上部署 JBoss EAP](https://github.com/JasonFreeberg/jboss-on-app-service)

## <a name="next-steps"></a>後續步驟

* 深入了解 [JBoss EAP 7.2](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.2/)。
* 深入了解 [JBoss EAP 7.3](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.3/)。
* 深入了解 [Red Hat 訂用帳戶管理](https://access.redhat.com/products/red-hat-subscription-management)。
* 深入了解 [Azure 上的 Red Hat 工作負載](https://aka.ms/rhel-docs)。
* [從 Azure Marketplace 在 RHEL VM 或虛擬機器擴展集上部署 JBoss EAP](https://aka.ms/AMP-JBoss-EAP)。
* [從 Azure 快速入門範本在 RHEL VM 或虛擬機器擴展集上部署 JBoss EAP](https://aka.ms/Quickstart-JBoss-EAP)。
