---
title: 在 Azure 上的 Docker 容器中執行微焦點企業伺服器 5.0 |Microsoft Docs
description: 使用 Azure 虛擬機器（Vm）上的微焦點開發和測試環境，重新裝載您的 IBM z/OS 大型主機工作負載。
services: virtual-machines-linux
documentationcenter: ''
author: maggsl
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 06/29/2020
tags: ''
keywords: ''
ms.service: multiple
ms.openlocfilehash: 3cc561a7f7f6f58c439a70315eba857e63def09d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85561312"
---
# <a name="run-micro-focus-enterprise-server-50-in-a-docker-container-on-azure"></a>在 Azure 上的 Docker 容器中執行微焦點企業伺服器5。0

您可以在 Azure 上的 Docker 容器中執行微焦點企業伺服器5.0。 本教學課程將為您示範作法。 它會使用適用于企業伺服器的 Windows CICS （客戶資訊控制系統） acctdemo 示範。

Docker 為應用程式新增可攜性和隔離。 例如，您可以從一個 Windows 虛擬機器（VM）匯出 Docker 映射，以在另一部電腦上執行，或從存放庫匯出至具有 Docker 的 Windows server。 Docker 映射會在具有相同設定的新位置中執行，而不需要安裝企業伺服器。 它是映射的一部分。 但仍適用授權考慮。

本教學課程會從 Azure Marketplace 安裝**具有容器 VM 的 Windows 2016 Datacenter** 。 此 VM 包含**Docker 18.09.0**。 接下來的步驟會示範如何部署容器、加以執行，然後使用3270模擬器連接到它。

## <a name="prerequisites"></a>必要條件

在開始之前，請先參閱下列必要條件：

-   Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

-   微焦點軟體和有效的授權（或試用版授權）。 如果您是現有的微焦點客戶，請洽詢您的微焦點代表。 否則，請[要求試用版](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/)。

    > [!Note] 
    > Docker 示範檔案隨附于 Enterprise Server 5.0。 本教學課程使用 ent \_ server \_ dockerfile \_ 5.0 \_windows.zip。 從您存取企業伺服器安裝檔案的相同位置存取，或移至*微焦點*以開始使用。

-   [企業伺服器和企業開發人員](https://www.microfocus.com/documentation/enterprise-developer/#%22)的檔。

## <a name="create-a-vm"></a>建立 VM

1.  從 ent \_ server \_ dockerfile \_ 5.0windows.zip 檔案保護媒體 \_ 。 保護 ES-Docker-mflic 授權檔案（建立 Docker 映射所需）。

2.  建立 VM。 若要這麼做，請開啟 Azure 入口網站，從左上方的功能表中選取 [**建立資源**]，然後依*windows server 作業系統*進行篩選。 在結果中，選取 [ **Windows Server]。** 在下一個畫面中，選取 [ **Windows Server 2016 Datacenter –含容器**]。

    ![Azure 入口網站搜尋結果的螢幕擷取畫面](./media/run-image-1.png)

3.  若要設定 VM 的屬性，請選擇 [實例詳細資料]：

    1.  選擇 VM 大小。 在本教學課程中，請考慮使用具有2個 vcpu 和 16 GB 記憶體的**標準 DS2 \_ v3** VM。

    2.  選取您想要部署的**區域**和**資源群組**。

    3.  針對 [**可用性選項**]，使用預設設定。

    4.  針對 [使用者**名稱**]，輸入您想要使用的系統管理員帳戶和密碼。

    5.  請確定**埠 3389 RDP**已開啟。 只有此埠需要公開公開，讓您可以登入 VM。 然後，接受所有預設值，然後按一下 [**審核 + 建立**]。

    ![[建立虛擬機器] 窗格的螢幕擷取畫面](./media/run-image-2.png)

4.  等待部署完成（幾分鐘）。 一則訊息指出您的 VM 已建立。

5.  選取 [**前往資源**]，移至您 VM 的 [**總覽**] 分頁。

6.  在右側，選取 **[連線]**。 [連線**至虛擬機器]** 選項會出現在右側。

7.  選取 [**下載 RDP**檔案] 按鈕，下載可讓您連結至 VM 的遠端桌面通訊協定（RDP）檔案。

8.  檔案完成下載之後，請開啟該檔案，然後輸入您為 VM 建立的使用者名稱和密碼。

    > [!Note]    
    > 請勿使用您的公司認證來登入。 （RDP 用戶端會假設您可能想要使用這些。 您不能這麼做）。

9.  選取 [**更多**選擇]，然後選取您的 VM 認證。

此時，VM 正在執行，並透過 RDP 連接。 您已登入並準備好進行下一個步驟。

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>建立沙箱目錄並上傳 zip 檔案

1.  在 VM 上建立目錄，您可以在其中上傳示範和授權檔案。 例如， **C： \\ 沙箱**。

2.  將**ent \_ 伺服器 \_ dockerfile \_ 5.0 \_windows.zip**和**ES-Docker-mflic**檔案上傳到您所建立的目錄。

3.  將 zip 檔案的內容解壓縮至解壓縮程式所建立的**ent \_ 伺服器 \_ dockerfile \_ 5.0 \_ windows**目錄。 此目錄包含讀我檔案（.html 和 .txt 檔案）和兩個子目錄（ **EnterpriseServer**和**範例**）。

4.  將**ES-Docker-mflic**至 C： \\ 沙箱 \\ ent \_ 伺服器 \_ dockerfile \_ 5.0 \_ windows \\ EnterpriseServer 和 C： \\ 沙箱 \\ ent \_ 伺服器 \_ dockerfile \_ 5.0 \_ windows \\ 範例 \\ CICS 目錄。  
      
    > [!Note]
    > 請務必將授權檔案複製到這兩個目錄。 這是 Docker build 步驟的必要元件，以確保映射具有適當的授權。

## <a name="check-docker-version-and-create-base-image"></a>檢查 Docker 版本並建立基底映射

> [!Important]  
> 建立適當的 Docker 映射是一個兩個步驟的程式。 首先，建立 Enterprise Server 5.0 基底映射。 然後為 x64 平臺建立另一個映射。 雖然您可以建立 x86 （32位）映射，但請使用64位映射。

1.  開啟命令提示字元。

2.  檢查是否已安裝 Docker。 在命令提示字元中，輸入： **docker 版本**  
    例如，此版本是在寫入時18.09.0 的。

3.  若要變更目錄，請輸入：  
    **cd \\沙箱 \\ ent \_ 伺服器 \_ dockerfile \_ 5.0 \_ windows \\ EnterpriseServer**。

4.  輸入**bld.bat IacceptEULA** ，以開始初始基底映射的組建進程。 等候幾分鐘讓此進程執行。 在結果中，請注意已建立的兩個映射：一個用於 x64，另一個用於 x86。。

    ![顯示影像命令視窗](./media/run-image-3.png)

5.  若要建立 CICS 示範的最終影像，請輸入**cd \\ Sandbox \\ ent \_ server \_ dockerfile \_ 5.0 \_ WINDOWS \\ 範例 \\ cics**，以切換至 cics 目錄。

6.  若要建立映射，請輸入**bld.bat x64**。 請等候幾分鐘讓進程執行，並將訊息指出映射已建立。

7.  輸入**docker images** ，以顯示 VM 上所安裝的所有 docker 映射清單。 請確定**microfocus/es acctdemo**是其中之一。

    ![命令視窗顯示 es acctdemo 影像](./media/run-image-4.png)

## <a name="run-the-image"></a>執行映像

1.  若要啟動企業伺服器5.0 和 acctdemo 應用程式，請在命令提示字元中輸入：

    ~~~
    **docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win\_5.0\_x64
    ~~~

1.  安裝3270終端機模擬器（例如[x3270](http://x3270.bgp.nu/) ），並使用它透過埠9040附加至執行中的映射。

2.  取得 acctdemo 容器的 IP 位址，讓 Docker 可以做為它所管理容器的動態主機設定通訊協定（DHCP）伺服器：

    1.  取得執行中容器的識別碼。 在命令提示字元中輸入**Docker ps** ，並記下識別碼（在此範例中為**22a0fe3159d0** ）。 儲存以進行下一個步驟。

    2.  若要取得 acctdemo 容器的 IP 位址，請使用上一個步驟中的容器識別碼，如下所示：

    ~~~
    docker inspect \<containerID\> --format="{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ~~~

    例如：

    ~~~
    docker inspect 22a0fe3159d0 --format="{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ~~~

4. 請記下 acctdemo 映射的 IP 位址。 例如，下列輸出中的位址是172.19.202.52。

    ![顯示 IP 位址之命令視窗的螢幕擷取畫面](./media/run-image-5.png)

5. 使用模擬器掛接映射。 將模擬器設定為使用 acctdemo 映射的位址和埠9040。 在這裡，它是**172.19.202.52： 9040**。 您的情況類似。 [登入**CICS** ] 畫面隨即開啟。

    ![登錄至 CICS 的螢幕擷取畫面](./media/run-image-6.png)

6. 輸入**USERID**的**SYSAD** ，並**SYSAD**作為**密碼**，以登入 CICS 區域。

7. 使用模擬器的快速鍵映射清除畫面。 針對 [x3270]，選取 [**快速鍵**對應] 功能表選項。

8. 若要啟動 acctdemo 應用程式，請輸入「**帳戶**」。 隨即顯示應用程式的初始畫面。

     ![帳戶示範的螢幕擷取畫面](./media/run-image-7.png)

9. 使用顯示帳戶類型進行實驗。 例如，針對要求輸入**D** ，並針對**帳戶**鍵入**11111** 。 其他要嘗試的帳戶號碼為22222、33333等等。

    ![帳戶示範的螢幕擷取畫面](./media/run-image-8.png)

10. 若要顯示 [企業伺服器管理] 主控台，請移至命令提示字元，然後輸入**start HTTP：172.19.202.52： 86**。

    ![企業伺服器管理主控台](media/run-image-9.png)

就這麼簡單！ 現在您正在執行並管理 Docker 容器中的 CICS 應用程式。

## <a name="next-steps"></a>後續步驟

-   [在 Azure 上安裝微焦點企業伺服器5.0 和企業開發人員5。0](https://docs.microsoft.com/azure/virtual-machines/workloads/mainframe-rehosting/microfocus/set-up-micro-focus-azure)

-   [大型主機應用程式移轉](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
