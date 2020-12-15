---
title: 針對安全性和存取控制問題進行疑難排解
description: 瞭解如何對 Azure Data Factory 中的安全性和存取控制問題進行疑難排解。
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/19/2020
ms.author: lle
ms.reviewer: craigg
ms.openlocfilehash: 51cb1a1a8151748fc9c6cd4c81da967424b52868
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505149"
---
# <a name="troubleshoot-azure-data-factory-security-and-access-control-issues"></a>針對 Azure Data Factory 安全性和存取控制問題進行疑難排解

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文探討 Azure Data Factory 中安全性和存取控制的常見疑難排解方法。

## <a name="common-errors-and-messages"></a>常見錯誤和訊息

### <a name="connectivity-issue-in-the-copy-activity-of-the-cloud-datastore"></a>雲端資料存放區複製活動中的連線能力問題

#### <a name="symptoms"></a>徵兆

當來源或接收資料存放區發生連接問題時，可能會傳回各種錯誤訊息。

#### <a name="cause"></a>原因 

問題通常是由下列其中一個因素所造成：

* 如果您使用自我裝載的 IR，自我裝載整合執行時間 (IR) 節點的 proxy 設定。

* 如果您是使用自我裝載 IR，則為自我裝載 IR 節點中的防火牆設定。

* 雲端資料存放區中的防火牆設定。

#### <a name="resolution"></a>解決方案

* 為確保這是連線問題，請檢查下列各點：

   - 從來源或接收連接器擲回錯誤。
   - 失敗是在複製活動開始時。
   - 針對 Azure IR 或自我裝載 IR 與一個節點的失敗是一致的，因為如果只有部分節點發生問題，它可能是多節點自我裝載 IR 中的隨機失敗。

* 如果您使用自我裝載的 **IR**，請檢查您的 proxy、防火牆和網路設定，因為如果您使用 Azure IR，連線到相同的資料存放區可能會成功。 若要疑難排解此案例，請參閱：

   * [自我裝載的 IR 埠和防火牆](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#ports-and-firewalls)
   * [Azure Data Lake Storage 連接器](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-store)
  
* 如果您是使用 **Azure IR**，請嘗試停用資料存放區的防火牆設定。 這種方法可以解決下列兩種情況中的問題：
  
   * [Azure IR 的 IP 位址](https://docs.microsoft.com/azure/data-factory/azure-integration-runtime-ip-addresses) 不在允許清單中。
   * [ *允許信任的 Microsoft 服務存取此儲存體帳戶* ] 功能已針對 [Azure Blob 儲存體](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#supported-capabilities) 和 [Azure Data Lake Storage Gen 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#supported-capabilities)關閉。
   * 未針對 Azure Data Lake Storage Gen1 啟用 [ *允許存取 Azure 服務* ] 設定。

如果上述任何方法都無法運作，請洽詢 Microsoft 尋求協助。


### <a name="invalid-or-empty-authentication-key-issue-after-public-network-access-is-disabled"></a>公用網路存取停用後的驗證金鑰問題無效或空白

#### <a name="symptoms"></a>徵兆

停用 Data Factory 的公用網路存取之後，自我裝載整合執行時間會擲回下列錯誤：「驗證金鑰無效或空白」。

#### <a name="cause"></a>原因

此問題很可能是因為網域名稱系統 (DNS) 解析問題所造成，因為停用公用連線和建立私人端點可防止重新連接。

若要確認 Data Factory 的完整功能變數名稱 (FQDN) 是否解析為公用 IP 位址，請執行下列動作：

1. 確認您已在與 Data Factory 私人端點相同的虛擬網路中建立 Azure 虛擬機器 (VM) 。

2. 從 Azure VM 執行 PsPing 和 Ping 至 Data Factory FQDN：

   `psping.exe <dataFactoryName>.<region>.datafactory.azure.net:443`
   `ping <dataFactoryName>.<region>.datafactory.azure.net`

   > [!Note]
   > 您必須為 PsPing 命令指定埠。 埠443顯示在此處，但並非必要。

3. 查看兩個命令是否解析為以指定區域為基礎的 Azure Data Factory 公用 IP。 IP 應採用下列格式： `xxx.xxx.xxx.0`

#### <a name="resolution"></a>解決方案

若要解決這個問題，請執行下列動作：
- 請參閱 Azure Data Factory 文章的 [Azure Private Link](https://docs.microsoft.com/azure/data-factory/data-factory-private-link#dns-changes-for-private-endpoints) 。 此指示適用于設定私人 DNS 區域或伺服器，以將 Data Factory FQDN 解析為私人 IP 位址。

- 建議您使用自訂的 DNS 做為長期解決方案。 但是，如果您不想要設定私人 DNS 區域或伺服器，請嘗試下列暫時性解決方案：

  1. 在 Windows 中變更主機檔案，然後將私人 IP (Azure Data Factory 私人端點) 對應至 Azure Data Factory FQDN。
  
     在 Azure VM 中，移至 `C:\Windows\System32\drivers\etc` ，然後在 [記事本] 中開啟 *主機* 檔案。 新增將私人 IP 對應至檔案結尾 FQDN 的行，並儲存變更。
     
     ![將私人 IP 對應至主機的螢幕擷取畫面。](media/self-hosted-integration-runtime-troubleshoot-guide/add-mapping-to-host.png)

  1. 重新執行上述驗證步驟中的相同命令，以檢查應包含私人 IP 的回應。

  1. 重新註冊自我裝載整合執行時間，並解決問題。

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>因為 private link，所以無法在自我裝載的 Vm 上註冊 IR 驗證金鑰

#### <a name="symptoms"></a>徵兆

因為已啟用私人連結，所以您無法在自我裝載的 VM 上註冊 IR 驗證金鑰。 您收到下列錯誤訊息：

「無法從 ADF 服務取得具有金鑰 * * * * * * * * * * * * * * * * * * * * * * * * * 的服務權杖，時間成本為：0.1250079 秒，錯誤碼為： InvalidGatewayKey，activityId 為： >XXXXXXX，且詳細的錯誤訊息為：用戶端 IP 位址不是有效的私人 ip，原因是 Data factory 無法存取公用網路，因此無法連線到雲端以進行成功的連線。」

#### <a name="cause"></a>原因

問題可能是您嘗試安裝自我裝載 IR 的 VM 所造成。 若要連接到雲端，請確定已啟用公用網路存取。

#### <a name="resolution"></a>解決方案

**解決方案 1**
 
若要解決這個問題，請執行下列動作：

1. 移至 [ [工廠-更新](https://docs.microsoft.com/rest/api/datafactory/Factories/Update) ] 頁面。

1. 選取右上方的 [ **試試看** ] 按鈕。
1. 在 [ **參數**] 下，完成所需的資訊。 
1. 在 [ **主體**] 下，貼上下列屬性：

    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ```
1. 選取 [ **執行** ] 以執行函數。 

1. 在 [ **參數**] 下，完成所需的資訊。 

1. 在 [ **主體**] 下，貼上下列屬性：
    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. 選取 [ **執行** ] 以執行函數。 
1. 確認顯示的是 [ **回應碼： 200** ]。 您貼上的屬性也應該顯示在 JSON 定義中。

1. 在整合執行時間中，再次新增 IR 驗證金鑰。


**解決方案 2**

若要解決此問題，請移至 [Azure Data Factory 的 Azure Private Link](https://docs.microsoft.com/azure/data-factory/data-factory-private-link)。

嘗試在使用者介面上啟用公用網路存取，如下列螢幕擷取畫面所示：

![[網路] 窗格上 [允許公用網路存取] 的 [已啟用] 控制項的螢幕擷取畫面。](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

## <a name="next-steps"></a>後續步驟

如需疑難排解的詳細資訊，請嘗試下列資源：

*  [Data Factory 的 Private Link](data-factory-private-link.md)
*  [Data Factory 部落格](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 功能要求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 影片](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&頁面](/answers/topics/azure-data-factory.html)
*  [Data Factory 的堆疊溢位論壇](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [關於 Data Factory 的 Twitter 資訊](https://twitter.com/hashtag/DataFactory)
