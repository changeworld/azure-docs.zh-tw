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
ms.openlocfilehash: 99c03ae4430d1a4caf575bdb9900200af0217bf1
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109739"
---
# <a name="troubleshoot-azure-data-factory-security-and-access-control-issues"></a>針對 Azure Data Factory 安全性和存取控制問題進行疑難排解

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文探討 Azure Data Factory 中安全性和存取控制的常見疑難排解方法。

## <a name="common-errors-and-messages"></a>常見錯誤和訊息


### <a name="connectivity-issue-of-copy-activity-in-cloud-data-store"></a>雲端資料存放區中複製活動的連線能力問題

#### <a name="symptoms"></a>徵兆

當來源/接收資料存放區發生連線問題時，可能會傳回各種類型的錯誤訊息。

#### <a name="cause"></a>原因 

問題大多是由下列因素所造成：

1. 如果您使用自我裝載 IR，則自我裝載 IR 的節點 (中的 Proxy 設定) 

1. 如果您使用自我裝載 IR， (自我裝載 IR 的節點中的防火牆設定) 

1. 雲端資料存放區中的防火牆設定

#### <a name="resolution"></a>解決方案

1. 請先檢查下列幾點，以確定問題是連線問題所造成：

   - 來源/接收連接器會擲回此錯誤。

   - 活動在複製開始時失敗

   - 這是具有一個節點的 Azure IR 或自我裝載 IR 的一致失敗，因為如果只有部分節點發生問題，則可能是多節點自我裝載 IR 的隨機失敗。

1. 如果您使用 **自我裝載 IR** ，則請檢查您的 proxy、防火牆和網路設定，因為 Azure IR 中的相同資料存放區執行可能會成功。 請參閱下列連結以進行疑難排解：

   [自我裝載的 IR 埠和防火牆](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#ports-and-firewalls) 
   [ADLS 連接器](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-store)
  
1. 如果您使用 **Azure IR**，請嘗試停用資料存放區的防火牆設定。 透過這種方式，可以修正下列兩種情況的問題：
  
   * [Azure IR 的 IP 位址](https://docs.microsoft.com/azure/data-factory/azure-integration-runtime-ip-addresses) 不在允許清單中。

   * [*允許信任的 Microsoft 服務存取此儲存體帳戶*] 功能已針對 [Azure Blob 儲存體](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#supported-capabilities)和 [ADLS Gen 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#supported-capabilities)關閉。

   * 未啟用 ADLS Gen1 的「*允許存取 Azure 服務*」。

1. 如果上述方法無法運作，請洽詢 Microsoft 尋求協助。


### <a name="invalid-or-empty-authentication-key-issue-after-public-network-access-is-disabled"></a>公用網路存取停用後的驗證金鑰問題無效或空白

#### <a name="symptoms"></a>徵兆

停用 Data Factory 的公用網路存取之後，因為自我裝載整合執行時間會擲回下列錯誤：「驗證金鑰無效或空白」。

#### <a name="cause"></a>原因

問題很可能是因為 DNS 解析問題所導致，因為停用公用連線和建立私人端點並不會協助重新連接。

您可以遵循下列步驟來確認 Data Factory FQDN 是否解析為公用 IP 位址：

1. 確認您已在與 Data Factory 私人端點相同的 VNET 中建立 Azure VM。

2. 從 Azure VM 執行 PsPing 和 Ping，以 Data Factory FQDN：

   `ping <dataFactoryName>.<region>.datafactory.azure.net`

   `psping.exe <dataFactoryName>.<region>.datafactory.azure.net:443`

   > [!Note]
   > 需要為 PsPing 命令指定埠，而443埠不是必須。

3. 檢查是否已將兩個命令解析為以指定區域為基礎的 ADF 公用 IP (格式 xxx. xxx. 0) 。

#### <a name="resolution"></a>解決方案

- 您可以參考 [Azure Data Factory Azure Private Link](https://docs.microsoft.com/azure/data-factory/data-factory-private-link#dns-changes-for-private-endpoints)中的文章。 此指示適用于設定私人 DNS 區域/伺服器，以將 Data Factory FQDN 解析為私人 IP 位址。

- 如果您目前不願意設定私人 DNS 區域/伺服器，請採取下列步驟做為暫時的解決方案。 不過，仍建議使用自訂 DNS 做為長期解決方案。

  1. 變更 windows 中的主機檔案，然後將私人 IP (ADF 私人端點) 對應至 ADF FQDN。
  
     流覽至 Azure VM 中的路徑 "C:\Windows\System32\drivers\etc"，然後使用 [記事本] 開啟 **主機** 檔案。 將對應私人 IP 的行新增至檔案結尾的 FQDN，並儲存變更。
     
     ![將對應新增至主機](media/self-hosted-integration-runtime-troubleshoot-guide/add-mapping-to-host.png)

  1. 在上述驗證步驟中重新執行相同的命令，以檢查應包含私人 IP 的回應。

  1. 重新註冊自我裝載整合執行時間，並解決問題。
 

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>因為 private link，所以無法在自我裝載的 Vm 上註冊 IR 驗證金鑰

#### <a name="symptoms"></a>徵兆

因為已啟用私人連結，所以無法在自我裝載的 VM 上註冊 IR 驗證金鑰。

錯誤資訊如下所示：

`
Failed to get service token from ADF service with key *************** and time cost is: 0.1250079 seconds, the error code is: InvalidGatewayKey, activityId is: XXXXXXX and detailed error message is Client IP address is not valid private ip Cause Data factory couldn’t access the public network thereby not able to reach out to the cloud to make the successful connection.
`

#### <a name="cause"></a>原因

問題可能是您正在嘗試安裝自我裝載 IR 的 VM 所造成。 若要連接到雲端，您應該確定已啟用公用網路存取。

#### <a name="resolution"></a>解決方案

 **解決方案1：** 您可以遵循下列步驟來解決此問題：

1. 移至 [ [工廠-更新](https://docs.microsoft.com/rest/api/datafactory/Factories/Update) ] 頁面。

1. 選取右上方的 [ **試試看** ] 按鈕。

1. 在 [ **參數**] 下，完成所需的資訊。 

1. 在 [ **主體**] 下，貼上下列屬性：
    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. 選取 [ **執行** ] 以執行函數。 

1. 確認顯示的是 [ **回應碼： 200** ]。 您貼上的屬性也應該顯示在 JSON 定義中。

1. 在整合執行時間中，再次新增 IR 驗證金鑰。


**解決方案2：** 您可以參閱下列文章以取得解決方案：

https://docs.microsoft.com/azure/data-factory/data-factory-private-link

嘗試在使用者介面上啟用公用網路存取，如下列螢幕擷取畫面所示：

![啟用公用網路存取](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

## <a name="next-steps"></a>後續步驟

如需疑難排解的詳細資訊，請嘗試下列資源：

*  [Data Factory 的 Private Link](data-factory-private-link.md)
*  [Data Factory 部落格](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 功能要求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 影片](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&頁面](/answers/topics/azure-data-factory.html)
*  [Data Factory 的堆疊溢位論壇](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [關於 Data Factory 的 Twitter 資訊](https://twitter.com/hashtag/DataFactory)
