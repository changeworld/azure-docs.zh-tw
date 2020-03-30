---
title: 使用 SSH 通道存取 Azure HDInsight
description: 了解如何使用 SSH 通道，安全地瀏覽以 Linux 為基礎的 HDInsight 節點上裝載的 Web 資源。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/28/2019
ms.openlocfilehash: 6f4efd9a316b92f17f89cea66a7c81e84ac3cf06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72991357"
---
# <a name="use-ssh-tunneling-to-access-apache-ambari-web-ui-jobhistory-namenode-apache-oozie-and-other-uis"></a>使用 SSH 隧道訪問 Apache Ambari Web UI、作業歷史記錄、NameNode、Apache Oozie 和其他 UI

HDInsight 叢集可讓您透過網際網路存取 Apache Ambari Web UI，但某些功能則須有 SSH 通道。 例如，沒有 SSh 隧道，無法通過互聯網訪問 Apache Oozie 服務的 Web UI。

## <a name="why-use-an-ssh-tunnel"></a>為何要使用 SSH 通道

Ambari 中的數個功能表只有透過 SSH 通道才能運作。 這些功能表依賴其他節點類型上執行的網站和服務，例如背景工作節點。

下列 Web UI 需要 SSH 通道：

* JobHistory
* NameNode
* 執行緒堆疊
* Oozie Web UI
* HBase Master 和記錄 UI

如果您使用指令碼動作來自訂叢集，則您安裝的任何服務或公用程式，都會需要 SSH 通道才能公開 Web 服務。 例如，如果您使用指令碼動作安裝 Hue，就必須使用 SSH 通道來存取 Hue Web UI。

> [!IMPORTANT]  
> 如果您透過虛擬網路直接存取 HDInsight，便不需要使用 SSH 通道。 如需透過虛擬網路直接存取 HDInsight 的範例，請參閱[將 HDInsight 連線至內部部署網](connect-on-premises-network.md)文件。

## <a name="what-is-an-ssh-tunnel"></a>什麼是 SSH 通道

[Secure Shell (SSH) 通道](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling)能連接本機電腦上的連接埠與 HDInsight 上的前端節點。 傳送到本機連接埠的流量會透過 SSH 連線路由傳送到前端節點。 解析要求的方式就像它是源自前端節點一樣。 接著，透過工作站的通道，將回應路由傳送回去。

## <a name="prerequisites"></a>Prerequisites

* SSH 用戶端。 如需詳細資訊，請參閱[使用 SSH 連線至 HDInsight (Apache Hadoop)](hdinsight-hadoop-linux-use-ssh-unix.md)。

* 可以設定為使用 SOCKS5 Proxy 的網頁瀏覽器。

    > [!WARNING]  
    > Windows 網際網路設定中的內建 SOCKS Proxy 支援不支援 SOCKS5，並且不適用此文件中的步驟。 下列瀏覽器會仰賴 Windows Proxy 設定，而且目前不適用本文件中的步驟︰
    >
    > * Microsoft Edge
    > * Microsoft Internet Explorer
    >
    > Google Chrome 也會依賴 Windows Proxy 設定。 不過，您可以安裝支援 SOCKS5 的延伸模組。 我們建議使用 [FoxyProxy Standard](https://chrome.google.com/webstore/detail/foxyproxy-standard/gcknhkkoolaabfmlnjonogaaifnjlfnp)。

## <a name="create-a-tunnel-using-the-ssh-command"></a><a name="usessh"></a>使用 SSH 命令建立通道

使用下列命令，利用 `ssh` 命令建立 SSH 通道。 替換為`sshuser`HDInsight 群集的 SSH 使用者，然後`CLUSTERNAME`替換為 HDInsight 群集的名稱：

```cmd
ssh -C2qTnNf -D 9876 sshuser@CLUSTERNAME-ssh.azurehdinsight.net
```

此命令會建立透過 SSH 將流量由本機連接埠 9876 路由傳送至叢集的連線。 可用選項包括：

* **D 9876** - 會透過通道路由傳送流量的本機連接埠。
* **C** - 壓縮所有資料，因為網路流量大多是文字。
* **2** - 強制 SSH 僅嘗試通訊協定第 2 版。
* **q** - 無訊息模式。
* **T** - 禁用偽 tty 分配，因為您只是轉發埠。
* **n** - 防止讀取 STDIN，因為您只是轉發埠。
* **N** - 不要執行遠端命令，因為您只是轉發埠。
* **f** - 在背景中執行。

在命令完成後，會將傳送至本機電腦上連接埠 9876 的流量路由傳送至叢集前端節點。

## <a name="create-a-tunnel-using-putty"></a><a name="useputty"></a>使用 PuTTY 建立通道

[PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty) 是適用於 Windows 的圖形化 SSH 用戶端。 如果您不熟悉 PuTTY，請參閱[PuTTY 文檔](https://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html)。 使用下列步驟，利用 PuTTY 建立 SSH 通道：

### <a name="create-or-load-a-session"></a>建立或載入工作階段

1. 開啟 PuTTY，並確認已左側功能表上選取**工作階段**。 如果已保存會話，請從 **"已保存會話"** 清單中選擇會話名稱，然後選擇 **"載入**"。

1. 如果您尚未儲存工作階段，請輸入您的連線資訊：

    |屬性 |值 |
    |---|---|
    |主機名稱 (或 IP 位址)|HDInsight 群集的 SSH 位址。 例如 **，mycluster-ssh.azurehdinsight.net**。|
    |連接埠|22|
    |連接類型|SSH|

1. 選擇 **"保存"**

    ![HDInsight 創建膩分會話](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-create-putty-session.png)

1. 在對話方塊左側的 [類別]**** 區段中，依序展開 [連線]**** 和 [SSH]****，然後選取 [通道]****。

1. 在 [ **控制 SSH 連接埠轉送的選項** ] 表單中提供下列資訊：

    |屬性 |值 |
    |---|---|
    |來源連接埠|要轉發的用戶端上的埠。 例如， **9876**。|
    |Destination|HDInsight 群集的 SSH 位址。 例如 **，mycluster-ssh.azurehdinsight.net**。|
    |動態|啟用動態 SOCKS 代理路由。|

    ![普蒂配置隧道選項](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-putty-tunnel.png)

1. 選擇 **"添加**"以添加設置，然後選擇 **"打開"** 以打開 SSH 連接。

1. 出現提示後，登錄到伺服器。

## <a name="use-the-tunnel-from-your-browser"></a>從瀏覽器使用通道

> [!IMPORTANT]  
> 本節中的步驟使用 Mozilla FireFox 瀏覽器，因為它在所有平台上提供相同的 Proxy 設定。 其他現代瀏覽器 (例如 Google Chrome) 可能需要延伸模組 (例如 FoxyProxy) 才能使用通道。

1. 將瀏覽器設定為使用建立通道時所使用的 **localhost** 和連接埠做為 **SOCKS v5** Proxy。 Firefox 的設定如下所示。 如果您使用與 9876 不同的連接埠，請將連接埠變更為您所用的連接埠：

    ![firefox 瀏覽器代理設置](./media/hdinsight-linux-ambari-ssh-tunnel/firefox-proxy-settings.png)

   > [!NOTE]  
   > 選取 [遠端 DNS]**** 會使用 HDInsight 叢集解析網域名稱系統 (DNS) 要求。 這項設定會使用叢集的前端節點來解析 DNS。

2. 通過訪問網站（如[https://www.whatismyip.com/](https://www.whatismyip.com/)） 驗證隧道是否有效。 傳回的 IP 應該是 Microsoft Azure 資料中心使用的 IP。

## <a name="verify-with-ambari-web-ui"></a>驗證 Ambari Web UI

建立叢集後，請使用下列步驟來確認您可以從 Ambari Web 存取服務 Web UI：

1. 在瀏覽器中，前往 `http://headnodehost:8080`。 `headnodehost` 位址會透過通道傳送到叢集，並解析為執行 Ambari 的前端節點。 出現提示時，請輸入您叢集的管理員使用者名稱 (admin) 和密碼。 Ambari Web UI 可能會出現第二次的提示。 若是如此，請重新輸入資訊。

   > [!NOTE]  
   > 使用 `http://headnodehost:8080` 位址連線至叢集時，表示您是透過通道進行連線。 通訊是使用 SSH 通道進行保護，而非 HTTPS。 要使用 HTTPS 在互聯網上連接，請使用`https://clustername.azurehdinsight.net`，`clustername`群集的名稱在哪裡。

2. 在 Ambari Web UI 中，選取頁面左邊清單中的 HDFS。

    ![阿帕奇安巴里高清服務選擇](./media/hdinsight-linux-ambari-ssh-tunnel/hdfs-service-selected.png)

3. 顯示 HDFS 服務資訊時，請選取 [快速連結] ****。 叢集前端節點的清單隨即出現。 選取其中一個前端節點，然後選取 [NameNode UI] ****。

    ![展開 [快速連結] 功能表的影像](./media/hdinsight-linux-ambari-ssh-tunnel/namenode-drop-down-menu.png)

    > [!NOTE]  
    > 當您選取 [快速連結]____ 時，可能會出現等候指示器。 如果您的網際網路連線速度較慢，可能會發生這種情況。 請等候一兩分鐘，讓系統從伺服器接收資料，然後再次嘗試列出作業。
    >
    > 螢幕右側可能會切掉 [快速連結]**** 功能表中的部分項目。 若是如此，請使用滑鼠展開功能表，然後使用向右鍵將畫面捲動到右邊，以查看功能表的其餘部分。

4. 將會顯示與下圖類似的頁面：

    ![Hadoop 名稱節點 UI 的圖像](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-namenode-ui.png)

    > [!NOTE]  
    > 請注意此頁面的 URL；它應該類似於 `http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster`。 此 URI 使用節點的內部完整網域名稱 (FQDN)，而且必須使用 SSH 通道才能存取。

## <a name="next-steps"></a>後續步驟

現在，您已經瞭解如何創建和使用 SSH 隧道，請參閱以下文檔，瞭解使用 Ambari 的其他方法：

* [使用 Apache Ambari 管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari.md)
