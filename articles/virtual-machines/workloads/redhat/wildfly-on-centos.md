---
title: 快速入門 - CentOS 上的 WildFly
description: 將 Java 應用程式部署至 CentOS VM 上的 WildFly
author: Theresa-Nguyen
ms.author: bicnguy
ms.topic: quickstart
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.assetid: 7aa21ef8-9cfb-43e0-bfda-3f10a2a2f3ef
ms.date: 10/23/2020
ms.openlocfilehash: ddd6f277bc71467060aa2279d93f9410a1327dde
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96486601"
---
# <a name="quickstart-wildfly-on-centos-8"></a>快速入門：CentOS 8 上的 WildFly

本快速入門說明如何部署 CentOS 8 VM 的 WildFly 獨立節點。 這適用於在 Azure 上開發和測試企業 Java 應用程式。 部署本快速入門不需要應用程式伺服器訂用帳戶。

## <a name="prerequisites"></a>Prerequisites

* 具有有效訂用帳戶的 Azure 帳戶。 如果您還沒有 Azure 訂用帳戶，則可以啟用 [MSDN 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)或[建立免費帳戶](https://azure.microsoft.com/pricing/free-trial)。

## <a name="use-case"></a>使用案例

WildFly 適用於在 Azure 上開發和測試企業 Java 應用程式。 WildFly 18 伺服器組態設定檔中提供的技術清單可在 [WildFly 使用者入門指南](https://docs.wildfly.org/18/Getting_Started_Guide.html#getting-started-with-wildfly)中取得。

您可以根據您的使用案例，在獨立或叢集模式中使用 WildFly。 在節點叢集上執行 WildFly 可確保重要 Jakarta EE 應用程式的高可用性。 進行少量的應用程式設定變更，然後在叢集中部署應用程式。 若要深入了解這方面的資訊，請參閱 [WildFly 高可用性指南](https://docs.wildfly.org/18/High_Availability_Guide.html)。

## <a name="configuration-choice"></a>組態選擇

WildFly 可以在 **獨立伺服器** 模式下開機 - 獨立伺服器執行個體是獨立的程序，非常類似 JBoss 應用程式伺服器 (AS) 3、4、5 或 6 執行個體。 您可以透過 standalone.sh 或 standalone.bat 啟動指令碼來啟動獨立執行個體。 對於一個以上的獨立執行個體，使用者必須負責協調跨伺服器的多伺服器管理。

您也可以使用設定資料夾中可用的設定檔，以替代組態來啟動 WildFly 執行個體。

以下是獨立伺服器組態檔：

- standalone.xml (預設) - 此設定是用來啟動 WildFly 執行個體的預設檔案。 其中包含具有所需技術的 Jakarta Web 設定檔認證組態。
   
- standalone-ha.xml - 高可用性的 Jakarta EE Web Profile 8認證組態 (以 Web 應用程式為目標)。
   
- standalone-full.xml - Jakarta EE Platform 8 認證組態，包括裝載 Jakarta EE 應用程式的所有必要技術。

- standalone-full.xml - Jakarta EE Platform 8 認證組態，包括裝載 Jakarta EE 應用程式的高可用性。

若要使用另一個提供的組態來啟動獨立 WildFly 伺服器，請使用 --server-config 引數搭配伺服器設定檔。

例如，若要使用具有叢集功能的 Jakarta EE Platform 8，請使用下列命令：

```
./standalone.sh --server-config=standalone-full-ha.xml
```

若要深入了解這些設定，請參閱 [WildFly 使用者入門指南](https://docs.wildfly.org/18/Getting_Started_Guide.html#wildfly-10-configurations)。

## <a name="licensing-support-and-subscription-notes"></a>授權、支援和訂用帳戶注意事項

Azure CentOS 8 映像是隨用隨付 (PAYG) VM 映射，使用者無需取得授權。 第一次啟動 VM 時，會自動啟用 VM 的作業系統授權，並以每小時費率計費。 除了 Microsoft Linux 每小時的 VM 費率以外，您需要額外付費。 如需詳細資訊，請按一下 [Linux VM 定價](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#linux)。 WildFly 可免費下載及使用，而且不需要 Red Hat 訂用帳戶或授權。

## <a name="how-to-consume"></a>如何取用

您可以透過下列三種方式來部署範本：

- 使用 PowerShell - 執行下列命令部署範本：(如需有關安裝和設定 Azure PowerShell 的資訊，請參閱[ Azure PowerShell](/powershell/azure/))。

    ```
    New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new Resource Group for your deployment
    ```

    ```
    New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/wildfly-standalone-centos8/azuredeploy.json
    ```
    
- 使用 Azure CLI - 執行下列命令部署範本：(如需有關安裝和設定 Azure 跨平台命令列介面的詳細資訊，請參閱 [Azure 跨平台命令列](/cli/azure/install-azure-cli))。

    ```
    az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new Resource Group for your deployment
    ```

    ```
    az group deployment create --resource-group <my-resource-group> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/wildfly-standalone-centos8/azuredeploy.json
    ```

- 使用 Azure 入口網站 - 按一下<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fwildfly-standalone-centos8%2Fazuredeploy.json" target="_blank">此處</a>並登入 Azure 入口網站以部署範本。

## <a name="arm-template"></a>ARM 範本

<a href="https://github.com/Azure/azure-quickstart-templates/tree/master/wildfly-standalone-centos8" target="_blank">CentOS 8 上的 WildFly 18 (獨立 VM)</a> - 這是快速入門範本，會在資源群組 (RG) 中的 CentOS 8 VM 上建立 WildFly 18.0.1 的獨立節點，其中包括 VM、虛擬網路和診斷儲存體帳戶的私人 IP。 其也會在 Azyre 的 WildFly 上部署名為 JBoss-EAP 的範例 Java 應用程式。

## <a name="resource-links"></a>資源連結

* 深入了解 [WildFly 18](https://docs.wildfly.org/18/)
* 深入了解[Azure 上的 Linux 散發套件](../../linux/endorsed-distros.md)
* [適用於 Java 開發人員的 Azure 文件](https://github.com/JasonFreeberg/jboss-on-app-service)

## <a name="next-steps"></a>後續步驟

針對生產環境，請查看 Red Hat JBoss EAP Azure 快速入門 ARM 範本：

具有範例應用程式的獨立 RHEL 虛擬機器：

*  <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-standalone-rhel" target="_blank"> RHEL 上的 JBoss EAP (獨立 VM)</a>

具有範例應用程式的叢集 RHEL 虛擬機器：

* <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-clustered-multivm-rhel" target="_blank">RHEL 上的 JBoss EAP (叢集 VM)</a>

具有範例應用程式的叢集 RHEL 虛擬機器擴展集：

* <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-clustered-vmss-rhel" target="_blank"> RHEL 上的 JBoss EAP (叢集虛擬機器擴展集)</a>