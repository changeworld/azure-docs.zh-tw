---
title: Azure Data Lake Storage Gen1 中的網路安全性 | Microsoft Docs
description: 了解虛擬網路在 Azure Data Lake Storage Gen1 中的整合方式
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/09/2018
ms.author: elsung
ms.openlocfilehash: 7d6c826df2a509ffb378809e3682073bd5ab1301
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "60612598"
---
# <a name="virtual-network-integration-for-azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1 的虛擬網路整合

本文介紹 Azure Data Lake Storage Gen1 的虛擬網路整合。 透過虛擬網路整合，您可以設定帳戶，使其只接受來自特定虛擬網路和子網路的流量。 

這項功能可協助 Data Lake Storage 帳戶免於抵禦外部威脅。

Data Lake Storage Gen1 的虛擬網路整合會在虛擬網路與 Azure Active Directory (Azure AD) 之間使用虛擬網路服務端點安全性，以在存取權杖中產生額外的安全性宣告。 這些宣告隨後會用來對 Data Lake Storage Gen1 帳戶驗證虛擬網路並允許存取。

> [!NOTE]
> 使用這些功能不會產生額外的相關費用。 帳戶會依照 Data Lake Storage Gen1 的標準費率來計費。 如需詳細資訊，請參閱[定價](https://azure.microsoft.com/pricing/details/data-lake-store/?cdn=disable)。 至於您所使用的所有其他 Azure 服務，則請參閱[定價](https://azure.microsoft.com/pricing/#product-picker)。

## <a name="scenarios-for-virtual-network-integration-for-data-lake-storage-gen1"></a>Data Lake Storage Gen1 的虛擬網路整合案例

透過 Data Lake Storage Gen1 虛擬網路整合，您可以限制特定虛擬網路和子網路對 Data Lake Storage Gen1 帳戶的存取動作。 將帳戶鎖定至指定虛擬網路子網路後，就無法存取 Azure 中的其他虛擬網路/VM。 在功能上，Data Lake Storage Gen1 虛擬網路整合可支援與[虛擬網路服務端點](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)相同的案例。 下列各節會詳述其間的一些主要差異。 

![Data Lake Storage Gen1 虛擬網路整合的案例圖](media/data-lake-store-network-security/scenario-diagram.png)

> [!NOTE]
> 在虛擬網路規則以外也可使用現有的 IP 防火牆規則，以同時允許來自內部部署網路的存取。 

## <a name="optimal-routing-with-data-lake-storage-gen1-virtual-network-integration"></a>Data Lake Storage Gen1 虛擬網路整合的最佳路由

虛擬網路服務端點的主要優點是您的虛擬網路具有[最佳路由](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview#key-benefits)。 您可以對 Data Lake Storage Gen1 帳戶執行相同的路由最佳化操作。 請使用下列[使用者定義的路由](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined) (從虛擬網路至 Data Lake Storage Gen1 帳戶)。

**Data Lake Storage 公用 IP 位址** – 使用目標 Data Lake Storage Gen1 帳戶的公用 IP 位址。 若要識別 Data Lake Storage Gen1 帳戶的 IP 位址，請[解析帳戶的 DNS 名稱](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-connectivity-from-vnets#enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity)。 請為每個位址建立個別的項目。

    ```azurecli
    # Create a route table for your resource group.
    az network route-table create --resource-group $RgName --name $RouteTableName
    
    # Create route table rules for Data Lake Storage public IP addresses.
    # There's one rule per Data Lake Storage public IP address. 
    az network route-table route create --name toADLSregion1 --resource-group $RgName --route-table-name $RouteTableName --address-prefix <ADLS Public IP Address> --next-hop-type Internet
    
    # Update the virtual network, and apply the newly created route table to it.
    az network vnet subnet update --vnet-name $VnetName --name $SubnetName --resource-group $RgName --route-table $RouteTableName
    ```

## <a name="data-exfiltration-from-the-customer-virtual-network"></a>客戶虛擬網路的資料外洩

除了針對來自虛擬網路的存取為 Data Lake Storage 帳戶提供保護外，您可能也會想要確定不會有任何資料外洩給未經授權的帳戶。

請在虛擬網路中使用防火牆解決方案，以根據目的地帳戶 URL 來篩選輸出流量。 僅允許存取已核准的 Data Lake Storage Gen1 帳戶。

可用的選項包括：
- [Azure 防火牆](https://docs.microsoft.com/azure/firewall/overview)：為虛擬網路[部署及設定 Azure 防火牆](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)。 保護輸出的 Data Lake Storage 流量，並將流量鎖定到已知且經過核准的帳戶 URL。
- [網路虛擬設備](https://azure.microsoft.com/solutions/network-appliances/)防火牆：系統管理員可以只允許使用某些商業防火牆廠商。 使用 Azure Marketplace 中所提供的網路虛擬設備防火牆解決方案，來執行相同的功能。

> [!NOTE]
> 在資料路徑中使用防火牆會導致資料路徑中出現額外的躍點。 在進行端對端資料交換時，這可能會影響網路效能。 輸送量可用性和連線延遲可能會受到影響。 

## <a name="limitations"></a>限制

- 在 Data Lake Storage Gen1 虛擬網路整合支援推出前建立的 HDInsight 叢集，必須重新建立才能支援這項新功能。
 
- 在已啟用虛擬網路整合的狀況下，當您建立新的 HDInsight 叢集並選取 Data Lake Storage Gen1 帳戶，程序將會失敗。 請先停用虛擬網路規則。 或在 Data Lake Storage 帳戶的 [防火牆與虛擬網路]**** 刀鋒視窗中，選取 [允許來自所有網路和服務的存取]****。 然後，在最後重新啟用虛擬網路規則或取消選取 [允許來自所有網路和服務的存取]**** 之前，請建立 HDInsight 叢集。 如需詳細資訊，請參閱[例外狀況](#exceptions)一節。

- Data Lake Storage Gen1 虛擬網路整合無法與 [Azure 資源的受控識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)搭配運作。
  
- 對於已啟用虛擬網路的 Data Lake Storage Gen1 帳戶，其中的檔案和資料夾資料並無法從入口網站存取。 此限制包括從虛擬網路內的 VM 存取，以及諸如使用資料總管的活動。 帳戶管理活動會繼續運作。 對於已啟用虛擬網路的 Data Lake Storage 帳戶，其中的檔案和資料夾資料則可透過非入口網站資源來存取。 這些資源包括 SDK 存取權、PowerShell 指令碼和其他 Azure 服務，但前提是這些資源不能來自入口網站。 

## <a name="configuration"></a>設定

### <a name="step-1-configure-your-virtual-network-to-use-an-azure-ad-service-endpoint"></a>步驟 1：將虛擬網路設定為使用 Azure AD 服務端點

1.  移至 Azure 入口網站，然後登入帳戶。
 
2.  在訂用帳戶中[建立新的虛擬網路](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)。 或者，也可以移至現有虛擬網路。 虛擬網路必須位於與 Data Lake Storage Gen1 帳戶相同的區域中。
 
3.  在 [虛擬網路]**** 刀鋒視窗上，選取 [服務端點]****。
 
4.  選取 [新增]**** 以新增服務端點。

    ![新增虛擬網路服務端點](media/data-lake-store-network-security/config-vnet-1.png)

5.  選取 **Microsoft.AzureActiveDirectory** 作為端點的服務。

     ![選取 Microsoft.AzureActiveDirectory 服務端點](media/data-lake-store-network-security/config-vnet-2.png)

6.  選取要允許連線的子網路。 選取 [新增]  。

    ![選取子網路](media/data-lake-store-network-security/config-vnet-3.png)

7.  新增服務端點可能需要 15 分鐘的時間。 新增完成後，便會顯示在清單中。 請確認端點已顯示，且所有詳細資料皆如預期設定。
 
    ![成功新增的服務端點](media/data-lake-store-network-security/config-vnet-4.png)

### <a name="step-2-set-up-the-allowed-virtual-network-or-subnet-for-your-data-lake-storage-gen1-account"></a>步驟 2：為 Data Lake Storage Gen1 帳戶設定允許的虛擬網路或子網路

1.  設定虛擬網路之後，請在訂用帳戶中[建立新的 Azure Data Lake Storage Gen1 帳戶](data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account)。 或者，您也可以移至現有的 Data Lake Storage Gen1 帳戶。 Data Lake Storage Gen1 帳戶必須位於與虛擬網路相同的區域中。
 
2.  選取 [防火牆與虛擬網路]****。

    > [!NOTE]
    > 如果您未在設定中看到 [防火牆與虛擬網路]****，則請登出入口網站。 關閉瀏覽器，並清除瀏覽器快取。 重新啟動電腦並重試。

       ![對 Data Lake Storage 帳戶新增虛擬網路規則](media/data-lake-store-network-security/config-adls-1.png)

3.  選取 [選取的網路]****。
 
4.  選取 [**新增現有的虛擬網路**]。

    ![新增現有的虛擬網路](media/data-lake-store-network-security/config-adls-2.png)

5.  選取要允許連線的虛擬網路和子網路。 選取 [新增]  。

    ![選擇虛擬網路和子網路](media/data-lake-store-network-security/config-adls-3.png)

6.  確定虛擬網路和子網路正確顯示於清單中。 選取 [儲存]  。

    ![儲存新規則](media/data-lake-store-network-security/config-adls-4.png)

    > [!NOTE]
    > 設定在進行儲存後最多可能需要 5 分鐘才會生效。

7.  [選擇性] 在 [防火牆與虛擬網路]**** 頁面的 [防火牆]**** 區段中，您可以允許來自特定 IP 位址的連線。 

## <a name="exceptions"></a>例外狀況
您可以允許從所選虛擬網路外的 Azure 服務和 VM 進行連線。 在 [防火牆與虛擬網路]**** 刀鋒視窗上的 [例外狀況]**** 區域中，選取兩個選項之一：
 
- **允許所有 Azure 服務存取此 Data Lake Storage Gen1 帳戶**。 此選項可讓 Azure 服務 (例如，Azure Data Factory、Azure 事件中樞和所有 Azure VM) 與 Data Lake Storage 帳戶進行通訊。

- **允許 Azure Data Lake Analytics 存取此 Data Lake Storage Gen1 帳戶**。 此選項可讓 Data Lake Analytics 連線至這個 Data Lake Storage 帳戶。 

  ![防火牆與虛擬網路例外狀況](media/data-lake-store-network-security/firewall-exceptions.png)

建議您讓這兩個例外狀況保持關閉。 只有在必須從虛擬網路外的這些其他服務連線時，才將其開啟。
