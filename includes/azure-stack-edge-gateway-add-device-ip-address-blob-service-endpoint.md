---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 11/21/2019
ms.author: alkohli
ms.openlocfilehash: 0c6845f081ccbe42e70964eaa939d58597e3b69b
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185522"
---
1. 移至您裝置的本機 Web UI，並登入您的裝置。 確定裝置已解除鎖定。

2. 移至 **網路設定** 頁面。 記下用來連線至用戶端之網路介面的裝置 IP 位址。

3. 如果使用遠端 Windows 用戶端，請以管理員的身分開啟 **記事本**，然後開啟位於 `C:\Windows\System32\Drivers\etc` 的主控檔案。

4. 將下列項目新增至 hosts 檔案：`<Device IP address> <Blob service endpoint>`

    您會從 Azure 入口網站中建立的 Edge 儲存體帳戶，取得 Blob 服務端點。 您只會使用 Blob 服務端點的尾碼。

    使用下圖以供參考。 儲存 `hosts` 檔案。

    ![修改 Windows 用戶端上的主控檔案](media/azure-stack-edge-gateway-add-device-ip-address-blob-service-endpoint/hosts-file-1.png)