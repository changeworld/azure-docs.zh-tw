---
title: 疑難排解-Azure 事件方格 IoT Edge |Microsoft Docs
description: IoT Edge 上的事件方格中進行疑難排解。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 0196522618d4b61f615f7cc6faeacbe9a8c7c5b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171341"
---
# <a name="common-issues"></a>常見的問題

如果您在環境中的 IoT Edge 使用 Azure 事件方格時遇到問題，請使用本文作為疑難排解和解決方案的指南。

## <a name="view-event-grid-module-logs"></a>查看事件方格模組記錄

若要進行疑難排解，您可能需要存取事件方格模組記錄。 若要這樣做，請在部署模組的 VM 上執行下列命令：

在 Windows 上，

```sh
docker -H npipe:////./pipe/iotedge_moby_engine container logs eventgridmodule
```

在 Linux 上，

```sh
sudo docker logs eventgridmodule
```

## <a name="unable-to-make-https-requests"></a>無法提出 HTTPS 要求

* 首先，請確定事件方格模組的 **輸入： serverAuth： tlsPolicy** 設定為 **strict** 或 **enabled**。

* 如果其模組對模組通訊，請確定您是在埠 **4438** 上進行呼叫，且模組的名稱符合部署的名稱。 

  例如，如果事件方格模組是以名稱 **eventgridmodule** 部署，則您的 URL 應該是 **https://eventgridmodule:4438** 。 請確定大小寫和埠號碼正確。
    
* 如果是來自非 IoT 模組，請確定在部署期間將事件方格埠對應至主機電腦，例如

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

* 首先，請確定事件方格模組的 **輸入： serverAuth： tlsPolicy** 已設定為 [ **已啟用** ] 或 [ **已停用**]。

* 如果其模組對模組通訊，請確定您是在埠 **5888** 上進行呼叫，且模組的名稱符合部署的名稱。 

  例如，如果事件方格模組是以名稱 **eventgridmodule** 部署，則您的 URL 應該是 **http://eventgridmodule:5888** 。 請確定大小寫和埠號碼正確。
    
* 如果是來自非 IoT 模組，請確定在部署期間將事件方格埠對應至主機電腦，例如

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

## <a name="certificate-chain-was-issued-by-an-authority-thats-not-trusted"></a>憑證鏈是由不受信任的授權單位所發行

根據預設，事件方格模組會設定為使用 IoT Edge security daemon 發出的憑證來驗證用戶端。 請確定用戶端轉譯的憑證是此鏈的根。

中的**IoTSecurity**類別示範 [https://github.com/Azure/event-grid-iot-edge](https://github.com/Azure/event-grid-iot-edge) 如何從 IoT Edge 的安全 daemon 取得憑證，並使用該憑證來設定傳出的呼叫。

如果是非生產環境，您可以選擇關閉用戶端驗證。 如需如何進行的詳細資訊，請參閱 [安全性和驗證](security-authentication.md) 。

## <a name="debug-events-not-received-by-subscriber"></a>訂閱者未收到的 Debug 事件

常見的原因如下：

* 從未成功張貼事件。 將事件張貼至事件方格模組時，應會收到 200 (確定) 的 HTTP StatusCode。

* 檢查事件訂閱以確認：
    * 端點 URL 有效
    * 訂用帳戶中的任何篩選都不會造成事件被「捨棄」。

* 確認訂閱者模組是否正在執行

* 登入事件方格模組部署所在的 VM，並查看其記錄。

* 藉由設定 **broker： logDeliverySuccess = true** 並重新部署事件方格模組，然後重試要求，來開啟每個傳遞記錄。 開啟每個傳遞的記錄可能會影響輸送量和延遲，因此在完成偵錯工具之後，建議您將其重新開啟為 **broker： logDeliverySuccess = false**  ，並重新部署事件方格模組。

* 藉由設定計量來開啟計量 **： reportertype = 主控台** 和重新部署事件方格模組。 之後的任何作業都會導致在事件方格模組的主控台中記錄計量，而這可以用來進一步進行調試。 我們的建議是只針對偵錯工具開啟計量，並在完成之後，藉由設定計量來將其關閉 **： reportertype = none** 並重新部署事件方格模組。

## <a name="next-steps"></a>接下來的步驟

回報任何問題、在 IoT Edge 上使用事件方格的建議 [https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues) 。