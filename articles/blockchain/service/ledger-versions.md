---
title: Azure 區塊鏈服務總帳版本、修補 & 升級
description: 瞭解 Azure 區塊鏈 Service 中支援的總帳版本。 包括系統修補和升級的原則。
ms.date: 06/30/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: ea7c54e229178320329204a3199ab2b4c44058fe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85807735"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>支援的 Azure 區塊鏈 Service 總帳版本

Azure 區塊鏈 Service 會使用以乙太坊為基礎的[仲裁](https://www.goquorum.com/developers)總帳，其設計目的是要處理一組已知參與者（在 Azure 區塊鏈 Service 中識別為聯盟）內的私人交易。

目前，Azure 區塊鏈 Service 支援[仲裁版本 2.6.0](https://github.com/jpmorganchase/quorum/releases/tag/v2.6.0)和[Tessera 交易管理員](https://github.com/jpmorganchase/tessera)。

## <a name="managing-updates-and-upgrades"></a>管理更新和升級

仲裁中的版本控制是透過主要、次要和修補發行來完成。 例如，如果仲裁版本是2.0.1，則版本類型會分類如下：

|主要 | Minor  | Patch  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Azure 區塊鏈 Service 會在從仲裁提供的30天內，自動將仲裁的修補版本更新為現有執行中的成員。

## <a name="availability-of-new-ledger-versions"></a>新總帳版本的可用性

Azure 區塊鏈 Service 提供仲裁廠商所提供60天內的仲裁總帳最新主要和次要版本。 在布建新的成員和聯盟時，最多會提供四個次要版本供聯盟選擇。 目前不支援從升級至主要或次要版本。 例如，如果您執行的是2.x 版，則目前不支援升級至版本3.x。 同樣地，如果您執行的是2.2 版，則目前不支援升級至2.3 版。

## <a name="how-to-check-quorum-ledger-version"></a>如何檢查仲裁總帳版本

您可以使用 geth 或查看診斷記錄來附加至您的節點，以檢查 Azure 區塊鏈服務成員的仲裁版本。

### <a name="using-geth"></a>使用 geth

使用 geth 附加至您的 Azure 區塊鏈 Service 節點。 例如： `geth attach https://myblockchainmember.blockchain.azure.com:3200/<Access key>` 。

節點連線之後，geth 會報告類似下列輸出的仲裁版本：

``` text
instance: Geth/v1.9.7-stable-9339be03(quorum-v2.6.0)/linux-amd64/go1.13.12
```

如需使用 geth 的詳細資訊，請參閱[快速入門：使用 geth 附加至 Azure 區塊鏈 Service 交易節點](connect-geth.md)。

### <a name="using-diagnostic-logs"></a>使用診斷記錄

如果您啟用診斷記錄，就會針對交易節點回報仲裁版本。 例如，下列節點資訊記錄訊息包含仲裁版本。

``` text 
{"NodeName":"transaction-node","Message":"INFO [06-22|05:31:45.156] Starting peer-to-peer node instance=Geth/v1.9.7-stable-9339be03(quorum-v2.6.0)/linux-amd64/go1.13.12\n"}
{"NodeName":"transaction-node","Message":"[*] Starting Quorum node with QUORUM_VERSION=2.6.0, TESSERA_VERSION=0.10.5 and PRIVATE_CONFIG=/working-dir/c/tm.ipc\n"}
111
```

如需診斷記錄的詳細資訊，請參閱[透過 Azure 監視器來監視 Azure 區塊鏈 Service](monitor-azure-blockchain-service.md#diagnostic-settings)。

## <a name="how-to-check-genesis-file-content"></a>如何檢查創世檔案內容

若要檢查區塊鏈節點的創世檔案內容，您可以使用下列乙太坊 JavaScript API：

``` bash
admin.nodeInfo.protocols
```
您可以使用 geth 主控台或 web3 程式庫來呼叫 API。 如需使用 geth 的詳細資訊，請參閱[快速入門：使用 geth 附加至 Azure 區塊鏈 Service 交易節點](connect-geth.md)。

## <a name="next-steps"></a>後續步驟

[Azure 區塊鏈 Service 中的限制](limits.md)
