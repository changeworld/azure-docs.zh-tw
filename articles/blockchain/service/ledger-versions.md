---
title: Azure 區塊鏈服務總帳版本、修補 & 升級
description: Azure 區塊鏈服務中支援的總帳版本總覽。 包括系統修補和升級的原則。
ms.date: 06/30/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: ea7c54e229178320329204a3199ab2b4c44058fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85807735"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>支援的 Azure 區塊鏈服務總帳版本

Azure 區塊鏈服務使用以乙太坊為基礎的 [仲裁](https://www.goquorum.com/developers) 總帳，其設計目的是要在一組已知參與者（識別為 Azure 區塊鏈服務的聯盟）內處理私用交易。

目前，Azure 區塊鏈服務支援 [仲裁版本 2.6.0](https://github.com/jpmorganchase/quorum/releases/tag/v2.6.0) 和 [Tessera 交易管理員](https://github.com/jpmorganchase/tessera)。

## <a name="managing-updates-and-upgrades"></a>管理更新和升級

仲裁中的版本控制是透過主要、次要和修補程式版本來完成。 例如，如果仲裁版本是2.0.1，則發行類型會分類如下：

|主要 | Minor  | Patch  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Azure 區塊鏈服務會在從仲裁提供的30天內，自動將仲裁的修補程式版本更新為現有執行中的成員。

## <a name="availability-of-new-ledger-versions"></a>新總帳版本的可用性

Azure 區塊鏈服務在可從仲裁製造商取得的60天內，提供仲裁總帳的最新主要和次要版本。 提供最多四個次要版本，可供聯盟在布建新的成員和聯盟時進行選擇。 目前不支援從升級至主要或次要版本。 例如，如果您執行的是2.x 版，則目前不支援升級至3.x 版。 同樣地，如果您正在執行2.2 版，則目前不支援升級至版本2.3。

## <a name="how-to-check-quorum-ledger-version"></a>如何檢查仲裁總帳版本

您可以使用 geth 附加至節點，或查看診斷記錄，以檢查 Azure 區塊鏈服務成員上的仲裁版本。

### <a name="using-geth"></a>使用 geth

使用 geth 連接到您的 Azure 區塊鏈服務節點。 例如： `geth attach https://myblockchainmember.blockchain.azure.com:3200/<Access key>` 。

當您的節點連線之後，geth 會報告類似下列輸出的仲裁版本：

``` text
instance: Geth/v1.9.7-stable-9339be03(quorum-v2.6.0)/linux-amd64/go1.13.12
```

如需使用 geth 的詳細資訊，請參閱 [快速入門：使用 geth 附加至 Azure 區塊鏈服務的交易節點](connect-geth.md)。

### <a name="using-diagnostic-logs"></a>使用診斷記錄

如果您啟用診斷記錄，則會報告交易節點的仲裁版本。 例如，下列節點資訊記錄訊息包含仲裁版本。

``` text 
{"NodeName":"transaction-node","Message":"INFO [06-22|05:31:45.156] Starting peer-to-peer node instance=Geth/v1.9.7-stable-9339be03(quorum-v2.6.0)/linux-amd64/go1.13.12\n"}
{"NodeName":"transaction-node","Message":"[*] Starting Quorum node with QUORUM_VERSION=2.6.0, TESSERA_VERSION=0.10.5 and PRIVATE_CONFIG=/working-dir/c/tm.ipc\n"}
111
```

如需診斷記錄的詳細資訊，請參閱 [透過 Azure 監視器監視 Azure 區塊鏈服務](monitor-azure-blockchain-service.md#diagnostic-settings)。

## <a name="how-to-check-genesis-file-content"></a>如何檢查創世檔案內容

若要檢查區塊鏈節點的創世檔案內容，您可以使用下列乙太坊 JavaScript API：

``` bash
admin.nodeInfo.protocols
```
您可以使用 geth 主控台或 web3 程式庫來呼叫 API。 如需使用 geth 的詳細資訊，請參閱 [快速入門：使用 geth 附加至 Azure 區塊鏈服務的交易節點](connect-geth.md)。

## <a name="next-steps"></a>接下來的步驟

[Azure 區塊鏈服務中的限制](limits.md)
