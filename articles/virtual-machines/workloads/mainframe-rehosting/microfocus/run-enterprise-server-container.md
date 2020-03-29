---
title: 在 Azure 虛擬機器上的 Docker 容器中運行微焦點企業伺服器 4.0
description: 通過在 Azure 虛擬機器上的 Docker 容器中運行微焦點企業伺服器，重新託管 IBM z/OS 主機工作負載。
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 30d99c3f4767eb50361f7074c0d508fcf309faca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "61488342"
---
# <a name="run-micro-focus-enterprise-server-40-in-a-docker-container-on-azure"></a>在 Azure 上的 Docker 容器中運行微焦點企業伺服器 4.0

您可以在 Azure 上的 Docker 容器中運行微焦點企業伺服器 4.0。 本教程將介紹如何。 它使用企業伺服器的 Windows CICS（客戶資訊控制系統）acctdemo 演示。

Docker 為應用程式添加了可攜性和隔離性。 例如，可以從一個 Windows VM 匯出 Docker 映射以在另一個 Windows VM 上運行，或者從存儲庫匯出到使用 Docker 的 Windows 伺服器。 Docker 映射以相同的配置在新位置運行，而無需安裝企業伺服器。 它是圖像的一部分。 許可注意事項仍然適用。

本教程使用 Azure 應用商店中的容器虛擬機器 （VM） 安裝**Windows 2016 資料中心**。 此 VM 包括**Docker 18.09.0**。 以下步驟演示如何部署容器、運行容器，然後使用 3270 模擬器連接到該容器。

## <a name="prerequisites"></a>Prerequisites

在開始之前，請查看以下先決條件：

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 微焦點軟體和有效的許可證（或試用許可證）。 如果您是現有的微焦點客戶，請聯繫您的微焦點代表。 否則，[請請求試用](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/)。

     > [!NOTE]
     > Docker 演示檔包含在企業伺服器 4.0 中。 本教程使用 ent\_\_伺服器\_dockerfile\_4.0 windows.zip。 從訪問企業伺服器安裝檔的同一位置訪問它，或轉到*Micro Focus*以開始。

- [企業伺服器和企業開發人員](https://www.microfocus.com/documentation/enterprise-developer/#")的文檔。

## <a name="create-a-vm"></a>建立 VM

1. \_從 ent\_伺服器 dockerfile\_4.0\_windows.ZIP 檔案保護媒體。 保護 ES-Docker-Prod-XXXXXXXX.mflic 許可檔（構建 Docker 映射所需的）。

2. 建立 VM。 為此，請打開 Azure 門戶，選擇從左上角**創建資源**，然後按*視窗伺服器*進行篩選。 在結果中，選擇**Windows 伺服器 2016 資料中心 + 帶容器**。

     ![Azure 門戶搜尋結果](media/01-portal.png)

3. 要配置 VM 的屬性，請選擇實例詳細資訊：

    1. 選擇 VM 大小。 在本教程中，請考慮使用具有 2 個 vCPU 和 7 GB 記憶體**的標準\_DS2 v2** VM。

    2. 選擇要部署到**的區域****和資源組**。

    3. 對於**可用性選項**，請使用預設設置。

    4. 對於**使用者名**，鍵入要使用的管理員帳戶和密碼。

    5. 確保**埠 3389 RDP**已打開。 只有此埠需要公開公開，因此您可以登錄到 VM。 然後接受所有預設值，然後按一下 **"審閱+創建**"。

     ![創建虛擬機器窗格](media/container-02.png)

4. 等待部署完成（幾分鐘）。 一條消息指出您的 VM 已創建。

5. 按一下"**轉到資源**"轉到 VM 的 **"概述"** 邊欄選項卡。

6. 在右側，按一下"**連接**"按鈕。 右側顯示 **"連接到虛擬機器**"選項。

7. 按一下 **"下載 RDP 檔"** 按鈕下載 RDP 檔，該檔允許您附加到 VM。

8. 檔下載完成後，打開該檔並鍵入您為 VM 創建的使用者名和密碼。

     > [!NOTE]
     > 請勿使用公司憑據登錄。 （RDP 用戶端假定您可能需要使用這些。 您沒有。

9.  選擇**更多選項**，然後選擇 VM 憑據。

此時，VM 正在通過 RDP 運行和連接。 您已登錄，並準備好執行下一步。

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>創建沙箱目錄並上傳 ZIP 檔案

1.  在 VM 上創建一個目錄，您可以在其中上載演示和授權檔案。 例如 **，C：\\沙箱**。

2.  將**\_ent\_伺服器\_dockerfile\_4.0 windows.zip**和**ES-Docker-Prod-XXXXXXXX.mflic**檔上載到您創建的目錄。

3.  將 ZIP 檔案的內容提取到由提取過程創建的**ent\_伺服器\_\_dockerfile 4.0\_視窗**目錄。 此目錄包括一個 readme 檔（作為 .html 和 .txt 檔）和兩個子目錄，**企業伺服器**和**示例**。

4.  將**ES-Docker-Prod-XXXXXXXX.mflic 複製到**\\C：沙\\盒\_\_ent\_伺服器 Dockerfile 4.0\_視窗\\企業伺服器和\_\_C：\_\\沙箱\\ent\\伺服器 Dockerfile 4.0\_視窗\\示例 CICS 目錄。

> [!NOTE]
> 請確保將許可檔案複製到兩個目錄。 它們是必需的 Docker 生成步驟，以確保映射已正確獲得許可。

## <a name="check-docker-version-and-create-base-image"></a>檢查 Docker 版本並創建基本映射

> [!IMPORTANT]
> 創建適當的 Docker 映射是一個兩步過程。 首先，創建企業伺服器 4.0 基本映射。然後為 x64 平臺創建另一個映射。 儘管您可以創建 x86（32 位）圖像，但請使用 64 點陣圖像。

1. 開啟命令提示字元。

2. 檢查是否安裝了 Docker。 在命令提示字元中，輸入：

    ```
        docker version
    ```

     例如，編寫此版本時的版本為 18.09.0。

3. 要更改目錄，請鍵入**cd [沙箱]ent_server_dockerfiles_4.0_windows\企業伺服器**。

4. 鍵入**bld.bat IacceptEULA**以開始初始基本映射的生成過程。 等待幾分鐘，此過程運行。 在結果中，請注意已創建的兩個圖像 - 一個用於 x64，一個用於 x86：

     ![顯示圖像的命令視窗](media/container-04.png)

5. 要為 CICS 演示創建最終映射，請通過鍵入**\\cd 沙\\盒 ent\_伺服器\_dockerfile\_4.0\_視窗\\示例\\CICS**切換到 CICS 目錄。

6. 要創建圖像，請鍵入**bld.bat x64**。 等待幾分鐘，等待進程運行，並顯示已創建映射的消息。

7. 鍵入**Docker 映射**以顯示安裝在 VM 上的所有 Docker 映射的清單。 確保**微聚焦/es-acctdemo**是其中之一。

     ![顯示 es-acctdemo 圖像的命令視窗](media/container-05.png)

## <a name="run-the-image"></a>執行映像 

1.  要啟動企業伺服器 4.0 和 acctdemo 應用程式，請執行命令提示符類型：

    ```
         docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win_4.0_x64
    ```

2.  安裝 3270 終端模擬器（如[x3270），](http://x3270.bgp.nu/)並用它來通過埠 9040 連接到正在運行的映射。

3.  獲取 acctdemo 容器的 IP 位址，因此 Docker 可以充當其管理的容器的 DHCP 伺服器：

    1.  獲取正在運行的容器的 ID。 在命令提示符處鍵入**Docker ps**並記下 ID（在此示例中為**22a0fe3159d0）。** 將其保存為下一步。

    2.  要獲取 acctdemo 容器的 IP 位址，請使用上一步的容器 ID，如下所示：

    ```
       docker inspect <containerID> --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

       例如：

    ```   
        docker inspect 22a0fe3159d0 --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```
4. 請注意 acctdemo 映射的 IP 位址。 例如，以下輸出中的位址為 172.19.202.52。

     ![顯示 IP 位址的命令視窗](media/container-06.png)

5. 使用模擬器安裝圖像。 將模擬器配置為使用 acctdemo 映射和埠 9040 的位址。 這裡是**172.19.202.52：9040**。 你的是相似的。 將打開 **"登錄到 CICS"** 螢幕。

    ![登錄到 CICS 螢幕](media/container-07.png)

6. 登錄CICS區域，輸入**SYSAD**的**USERID**和**SYSAD****的密碼**。

7. 使用模擬器的鍵映射清除螢幕。 對於 x3270，選擇 **"鍵映射"** 功能表選項。

8. 要啟動 acctdemo 應用程式，請鍵入**ACCT**。 將顯示應用程式的初始螢幕。

     ![帳戶演示螢幕](media/container-08.png)

9. 試驗顯示帳戶類型。 例如，為請求鍵入**D，** 為**帳戶**鍵入**11111。** 其他要嘗試的帳號是 22222、33333 等。

     ![帳戶演示螢幕](media/container-09.png)

10. 要顯示企業伺服器管理主控台，請轉到命令提示符並鍵入 **"開始"HTTP：172.19.202.52：86**

     ![企業伺服器管理主控台](media/container-010.png)

就這麼簡單！ 現在，您正在 Docker 容器中運行和管理 CICS 應用程式。

## <a name="next-steps"></a>後續步驟

- [在 Azure 上安裝微焦點企業伺服器 4.0 和企業開發人員 4.0](./set-up-micro-focus-azure.md)
- [大型主機應用程式移轉](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
