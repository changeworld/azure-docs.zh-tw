---
title: 疑難排解-Azure Event Grid IoT Edge |Microsoft Docs
description: IoT Edge 上的事件方格中進行疑難排解。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 0196522618d4b61f615f7cc6faeacbe9a8c7c5b4
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171341"
---
# <a name="common-issues"></a>常見的問題

如果您在環境中的 IoT Edge 上使用 Azure 事件方格時遇到問題，請使用這篇文章做為疑難排解和解決方案的指南。

## <a name="view-event-grid-module-logs"></a>查看事件方格模組記錄

若要進行疑難排解，您可能需要存取事件方格模組記錄。 若要這麼做，請在部署模組的 VM 上執行下列命令：

在 Windows 上，

```sh
docker -H npipe:////./pipe/iotedge_moby_engine container logs eventgridmodule
```

在 Linux 上，

```sh
sudo docker logs eventgridmodule
```

## <a name="unable-to-make-https-requests"></a>無法發出 HTTPS 要求

* 首先，請確定事件方格模組的**輸入： serverAuth： tlsPolicy**設為**strict**或**enabled**。

* 如果它的模組對模組通訊，請確定您是在埠**4438**上進行呼叫，而模組的名稱符合部署的專案。 

  例如，如果事件方格模組是以名稱**eventgridmodule**部署，則您的 URL 應該是 **https://eventgridmodule:4438** 。 請確定大小寫與埠號碼正確。
    
* 如果是來自非 IoT 模組，請確定在部署期間，事件格線埠會對應到主機電腦，例如

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

## <a name="unable-to-make-http-requests"></a>無法提出 HTTP 要求

* 首先，請確定事件方格模組的**輸入： serverAuth： tlsPolicy**設為**enabled**或**disabled**。

* 如果它的模組對模組通訊，請確定您是在埠**5888**上進行呼叫，而模組的名稱符合部署的專案。 

  例如，如果事件方格模組是以名稱**eventgridmodule**部署，則您的 URL 應該是 **http://eventgridmodule:5888** 。 請確定大小寫與埠號碼正確。
    
* 如果是來自非 IoT 模組，請確定在部署期間，事件格線埠會對應到主機電腦，例如

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

## <a name="certificate-chain-was-issued-by-an-authority-thats-not-trusted"></a>憑證鏈是由不受信任的授權單位所簽發

根據預設，事件方格模組已設定為使用 IoT Edge 安全性守護程式發行的憑證來驗證用戶端。 請確定用戶端轉譯的憑證是此鏈的根項目。

中的**IoTSecurity**類別示範 [https://github.com/Azure/event-grid-iot-edge](https://github.com/Azure/event-grid-iot-edge) 如何從 IoT Edge 安全性守護程式抓取憑證，並使用它來設定傳出的呼叫。

如果是非生產環境，您可以選擇關閉用戶端驗證。 如需如何執行此動作的詳細資訊，請參閱[安全性和驗證](security-authentication.md)。

## <a name="debug-events-not-received-by-subscriber"></a>訂閱者未收到的 Debug 事件

常見的原因如下：

* 事件從未成功張貼。 將事件張貼至事件方格模組時，應該會收到 200 (確定) 的 HTTP StatusCode。

* 檢查事件訂用帳戶以確認：
    * 端點 URL 有效
    * 訂用帳戶中的任何篩選準則都不會導致事件「捨棄」。

* 確認訂閱者模組是否正在執行

* 登入部署事件方格模組的 VM，並查看其記錄。

* 藉由設定**broker： logDeliverySuccess = true**並重新部署事件方格模組並重試要求，來開啟每個傳遞記錄。 開啟每個傳遞的記錄可能會影響輸送量和延遲，因此，一旦完成偵錯工具，我們的建議是將其重新設回**broker： logDeliverySuccess = false** ，並重新部署事件方格模組。

* 藉由設定計量來開啟計量 **： reportertype = 主控台**，並重新部署事件方格模組。 之後的任何作業都會導致計量記錄在事件方格模組的主控台上，可用來進一步進行進一步的調試。 我們的建議是只開啟用於偵錯工具的計量，並在完成後設定計量以將其關閉 **： reportertype = none**和重新部署事件方格模組。

## <a name="next-steps"></a>後續步驟

在 IoT Edge 的上使用事件方格回報任何問題、建議，網址為 [https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues) 。