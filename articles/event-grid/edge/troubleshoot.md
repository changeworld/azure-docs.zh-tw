---
title: 故障排除 - Azure 事件網格 IoT 邊緣 |微軟文檔
description: 在 IoT 邊緣的事件網格中進行故障排除。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/24/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 95181d0eb23d5956b2c6af52c77f85714b107345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73100147"
---
# <a name="common-issues"></a>常見的問題

如果在環境中使用 IoT Edge 上的 Azure 事件網格遇到問題，請使用本文作為故障排除和解決指南。

## <a name="view-event-grid-module-logs"></a>查看事件網格模組日誌

要進行故障排除，您可能需要訪問事件網格模組日誌。 為此，在部署模組的 VM 上運行以下命令：

在 Windows 上，

```sh
docker -H npipe:////./pipe/iotedge_moby_engine container logs eventgridmodule
```

在 Linux 上，

```sh
sudo docker logs eventgridmodule
```

## <a name="unable-to-make-https-requests"></a>無法發出 HTTPS 請求

* 首先確保事件網格模組已**入站：伺服器Auth：tls策略**設置為**嚴格**或**已啟用**。

* 如果模組到模組通信，請確保在埠**4438**上進行調用，模組的名稱與部署的內容匹配。 

  例如，如果事件網格模組使用名稱**事件網格模組**部署，則 URL 應為**https://eventgridmodule:4438**。 確保套管和埠號正確。
    
* 如果是來自非 IoT 模組，請確保事件網格埠在部署期間映射到主機，例如，

    ```json
    "HostConfig": {
                "PortBindings": {
                  "4438/tcp": [
                    {
                        "HostPort": "4438"
                    }
                  ]
                }
     }
    ```

## <a name="unable-to-make-http-requests"></a>無法發出 HTTP 要求

* 首先確保事件網格模組已**入站：伺服器Auth：tls策略**設置為**已啟用**或**禁用**。

* 如果模組到模組通信，請確保對埠**5888**進行調用，模組的名稱與部署的內容匹配。 

  例如，如果事件網格模組使用名稱**事件網格模組**部署，則 URL 應為**http://eventgridmodule:5888**。 確保套管和埠號正確。
    
* 如果是來自非 IoT 模組，請確保事件網格埠在部署期間映射到主機，例如，

    ```json
    "HostConfig": {
                "PortBindings": {
                  "5888/tcp": [
                    {
                        "HostPort": "5888"
                    }
                  ]
                }
    }
    ```

## <a name="certificate-chain-was-issued-by-an-authority-thats-not-trusted"></a>憑證連結由不信任的頒發機構頒發

預設情況下，事件網格模組配置為使用 IoT Edge 安全守護進程頒發的證書對用戶端進行身份驗證。 確保用戶端正在顯示根植于此鏈的證書。

中的[https://github.com/Azure/event-grid-iot-edge](https://github.com/Azure/event-grid-iot-edge) **IoTSecurity**類演示如何從 IoT 邊緣安全守護進程檢索證書，並用它來配置傳出調用。

如果是非生產環境，則可以選擇關閉用戶端身份驗證。 有關如何執行此操作的詳細資訊，請參閱[安全和身份驗證](security-authentication.md)。

## <a name="debug-events-not-received-by-subscriber"></a>訂閱者未接收的調試事件

造成這種情況的典型原因是：

* 事件從未成功發佈。 在將事件發佈到事件網格模組時，應收到 200（OK） 的 HTTP 狀態碼。

* 檢查事件訂閱以驗證：
    * 終結點 URL 有效
    * 訂閱中的任何篩選器不會導致事件"刪除"。

* 驗證訂戶模組是否正在運行

* 登錄到部署事件網格模組的 VM 並查看其日誌。

* 通過設置**代理：logDelivery成功=true**並重新部署事件網格模組並重試請求，打開每個傳遞日誌記錄。 打開每次交付的日誌記錄可能會影響輸送量和延遲，因此一旦調試完成，我們建議將其轉回**代理：logDelivery成功—false**並重新部署事件網格模組。

* 通過設置指標打開指標 **：報告器類型=主控台**和重新部署事件網格模組。 之後的任何操作都將導致指標記錄在事件網格模組的主控台上，可用於進一步調試。 我們建議僅打開用於調試的指標，並在完成一次，通過設置**指標：報告類型_none**和重新部署事件網格模組來將其關閉。

## <a name="next-steps"></a>後續步驟

報告任何問題，建議在 IoT 邊緣使用事件網格。 [https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues)