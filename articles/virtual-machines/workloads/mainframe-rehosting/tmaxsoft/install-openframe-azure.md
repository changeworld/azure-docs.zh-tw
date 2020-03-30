---
title: 在 Azure 虛擬機器上安裝 TmaxSoft 開放幀
description: 使用 Azure 虛擬機器 （VM） 上的 TmaxSoft OpenFrame 環境重新託管 IBM z/OS 主機工作負載。
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 1ad6e52c421d9cfec4640d3a330b5507d6ed3e9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72436058"
---
# <a name="install-tmaxsoft-openframe-on-azure"></a>在 Azure 上安裝 TmaxSoft 開放幀

瞭解如何在 Azure 上設置適合開發、演示、測試或生產工作負載的 OpenFrame 環境。 本教程將引導您完成每一步。

OpenFrame 包括多個元件，這些元件在 Azure 上創建大型機模擬環境。 例如，OpenFrame 線上服務將大型機中介軟體（如 IBM 客戶資訊控制系統 （CICS））和 OpenFrame Batch）與其 TJES 元件替換 IBM 主機的作業輸入子系統 （JES）。

OpenFrame 適用于任何關係資料庫，包括 Oracle 資料庫、微軟 SQL 伺服器、IBM Db2 和 MySQL。 OpenFrame 的安裝使用 TmaxSoft Tibero 關係資料庫。 OpenFrame 和 Tibero 都在 Linux 作業系統上運行。 本教程安裝 CentOS 7.3，儘管您可以使用其他支援的 Linux 發行版本。OpenFrame 應用程式伺服器和 Tibero 資料庫安裝在一台虛擬機器 （VM） 上。

本教程將引導您完成 OpenFrame 套件元件的安裝。 有些必須單獨安裝。

主要開放框架元件：

- 所需的安裝包。
- 蒂貝羅資料庫。
- OpenFrame 中的應用程式使用開放資料庫連接 （ODBC） 與 Tibero 資料庫進行通信。
- OpenFrame Base，管理整個系統的中介軟體。
- OpenFrame 批次處理，替換大型機的批次處理系統的解決方案。
- TACF，一個控制使用者訪問系統和資源的服務模組。
- ProSort，批量事務的排序工具。
- OFCOBOL，一個解釋大型機COBOL程式的編譯器。
- OFASM，一個解釋大型機的組合語言程式的編譯器。
- OpenFrame 伺服器類型 C （OSC ），取代大型機中介軟體和 IBM CICS 的解決方案。
- JAVA企業使用者解決方案 （JEUS），一種經過 JAVA 企業版 6 認證的 Web 應用程式伺服器。
- OFGW，提供 3270 攔截器的 OpenFrame 閘道元件。
- OFManager，一種在 Web 環境中提供 OpenFrame 操作和管理功能的解決方案。

其他必需的 OpenFrame 元件：

- OSI，取代大型機中介軟體和IMS DC的解決方案。
- TJES，提供大型機 JES 環境的解決方案。
- OFTSAM，使 （V） SAM 檔能夠在打開的系統中使用的解決方案。
- OFHiDB，取代大型機的IMS DB的解決方案。
- OFPLI，一個解釋大型機PL/I程式的編譯器。
- PROTRIEVE，一個執行大型機語言CA-Easytrieve的解決方案。
- OFMiner，一種分析大型機資產，然後將它們遷移到 Azure 的解決方案。

## <a name="architecture"></a>架構

下圖概述了本教程中安裝的 OpenFrame 7.0 體系結構元件：

![開放框架元件](media/openframe-02.png)

## <a name="azure-system-requirements"></a>Azure 系統要求

下表列出了在 Azure 上安裝的要求。
<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>需求</th><th>描述</th></tr>
</thead>
<tbody>
<tr><td>Azure 上支援 Linux 發行版本
</td>
<td>
Linux x86 2.6 （32 位， 64 位）<br/>
紅帽 7.x<br/>
CentOS 7.x<br/>
</td>
</tr>
<tr><td>硬體
</td>
<td>核心： 2 （最小）<br/>
記憶體：4 GB（最小）<br/>
交換空間：1 GB（最小）<br/>
硬碟：100 GB（最小）<br/>
</td>
</tr>
<tr><td>適用于 Windows 使用者的可選軟體
</td>
<td>PuTTY：本指南用於配置 VM 功能<br/>
WinSCP：一個流行的SFTP用戶端和FTP用戶端，您可以使用<br/>
用於 Windows 的 Eclipse：TmaxSoft 支援的開發平臺<br/>
（目前不支援微軟視覺工作室）
</td>
</tr>
</tbody>
</table>

<!-- markdownlint-enable MD033 -->

## <a name="prerequisites"></a>Prerequisites

計畫花幾天時間組裝所有必需的軟體並完成所有手動流程。

在開始之前，執行以下操作：

- 從 TmaxSoft 獲取 OpenFrame 安裝媒體。 如果您是現有 TmaxSoft 客戶，請聯繫您的 TmaxSoft 代表以獲取許可副本。 否則，請從[TmaxSoft](https://www.tmaxsoft.com/contact/)請求試用版。

- 通過向 發送電子郵件請求<support@tmaxsoft.com>OpenFrame 文檔。

- 如果尚未訂閱 Azure 訂閱，則獲取 Azure 訂閱。 您還可以在開始之前創建[一個免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 選擇性。 佈建網站到網站 VPN 隧道或跳轉框，將 Azure VM 的訪問限制給組織中允許的使用者。 此步驟不是必需的，但它是最佳做法。

## <a name="set-up-a-vm-on-azure-for-openframe-and-tibero"></a>在 Azure 上為 OpenFrame 和 Tibero 設置 VM

您可以使用各種部署模式設置 OpenFrame 環境，但以下過程演示如何在一個 VM 上部署 OpenFrame 應用程式伺服器和 Tibero 資料庫。 在較大的環境和大型工作負載中，最佳做法是在自己的 VM 上單獨部署資料庫，以獲得最佳性能。

**創建 VM**

1. 轉到 "在"處<https://portal.azure.com>轉到 Azure 門戶並登錄到您的帳戶。

2. 按一下 [虛擬機器] ****。

    ![Azure 門戶中的資源清單](media/vm-01.png)

3. 按一下 **[新增]**。

    ![在 Azure 門戶中添加選項](media/vm-02.png)

4. 在**作業系統**的右側，按一下 **"更多**"。

     ![Azure 門戶中的更多選項](media/vm-03.png)

5. 按一下**基於 CentOS 的 7.3**以準確遵循此演練，或者您可以選擇其他受支援的 Linux 發行版本。

     ![Azure 門戶中的作業系統選項](media/vm-04.png)

6. 在 **"基本"** 設置中，**輸入名稱**、**使用者名**、**身份驗證類型**、**訂閱**（即用即付即用即付即用即付）和 **"資源組**"（使用現有使用者名或創建 TmaxSoft 組）。

7. 完成後（包括**身份驗證類型的**公開金鑰/私密金鑰對），按一下"**提交**"。

> [!NOTE]
> 如果使用 SSH 公開金鑰進行**身份驗證類型**，請參閱下一節中的步驟以生成公開金鑰/私密金鑰對，然後在此處繼續這些步驟。

### <a name="generate-a-publicprivate-key-pair"></a>生成公開金鑰/私密金鑰對

如果使用 Windows 作業系統，則需要 PuTTYgen 來生成公開金鑰/私密金鑰對。

公開金鑰可以自由共用，但私密金鑰應完全保密，絕不應與另一方共用。 生成金鑰後，必須將**SSH 公開金鑰**粘貼到配置中 — 實際上，將其上載到 Linux VM。 它存儲在使用者帳戶主目錄\_\~的 /.ssh 目錄中的授權金鑰中。 然後在 SSH 用戶端（在我們的案例中為 PuTTY）中提供關聯的**SSH 私密金鑰**後，Linux VM 能夠識別和驗證連接。

讓新個人訪問 VM 時： 

- 每個新人使用 PuTTYgen 生成自己的公共/私密金鑰。
- 個人單獨存儲自己的私密金鑰，並將公開金鑰資訊發送給 VM 的管理員。
- 管理員將公開金鑰的內容粘貼到\~/.ssh/授權\_金鑰檔。
- 新的個人通過PuTTY連接。

**生成公開金鑰/私密金鑰對**

1.  從<https://www.putty.org/>下載 PuTTYgen 並使用所有預設設置安裝它。

2.  要打開 PuTTYgen，請在 C 中找到 PuTTY 安裝目錄：\\程式檔\\PuTTY。

    ![普蒂介面](media/puttygen-01.png)

3.  按一下 **[產生]**。

    ![PuTTY 金鑰產生器對話方塊](media/puttygen-02.png)

4.  生成後，保存公開金鑰和私密金鑰。 將公開金鑰的內容粘貼到 **"創建虛擬機器\>基礎知識"** 窗格的**SSH 公開金鑰**部分（如上一節的步驟 6 和 7 所示）。

    ![PuTTY 金鑰產生器對話方塊](media/puttygen-03.png)

### <a name="configure-vm-features"></a>配置 VM 功能

1. 在 Azure 門戶中，在 **"選擇大小**邊欄選項卡"中，選擇所需的 Linux 電腦硬體設置。 安裝 Tibero 和 OpenFrame*的最低*要求為 2 個 CPU 和 4 GB RAM，如以下示例安裝所示：

    ![創建虛擬機器 - 基礎知識](media/create-vm-01.png)

2. 按一下 **"3 設置"** 並使用預設設置配置可選功能。
3. 查看您的付款詳細資訊。

    ![創建虛擬機器 - 購買](media/create-vm-02.png)

4. 提交您的選擇。 Azure 開始部署 VM。 此過程通常需要幾分鐘時間。

5. 部署 VM 時，將顯示其儀表板，顯示配置期間選擇的所有設置。 記下公共 IP**位址**。

    ![Azure 儀表板上的 tmax](media/create-vm-03.png)

6. 開啟 PuTTY。

7. 對於**主機名稱**，鍵入您的使用者名和您複製的公共 IP 位址。 例如，**\@使用者名公開 ip**。

    ![PuTTY 配置對話方塊](media/putty-01.png)

8. 在 **"類別"** 框中，**按一下\>"連接\>SSH Auth**"。提供**私密金鑰**檔的路徑。

    ![PuTTY 配置對話方塊](media/putty-02.png)

9. 按一下 **"打開**"以啟動 PuTTY 視窗。 如果成功，您將連接到在 Azure 上運行的新 CentOS VM。

10. 要以根使用者身份登錄，請鍵入**sudo bash**。

    ![根使用者登錄命令視窗](media/putty-03.png)

## <a name="set-up-the-environment-and-packages"></a>設置環境和包

現在，VM 已創建並登錄，您必須執行一些設置步驟並安裝所需的預先安裝包。

1. 使用 vi**ofdemo**編輯主機檔 （）`vi /etc/hosts`將demo 的名稱映射到本地 IP 位址。 假設我們的 IP 為 192.168.96.148，則此更改之前：

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    <IP Address>    <your hostname>
    ```

     這是在更改之後：

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    192.168.96.148   ofdemo
    ```

2. 創建組和使用者：

    ```vi
    [root@ofdemo ~]# adduser -d /home/oframe7 oframe7 
    [root@ofdemo ~]# passwd oframe7
    ```

3. 更改使用者 oframe7 的密碼：

    ```vi
    New password: 
    Retype new password: 
    passwd: all authentication tokens updated successfully.
    ```

4. 更新 /etc/sysctl.conf 中的內核參數：

    ```vi
    [root@ofdemo ~]# vi /etc/sysctl.conf
    kernel.shmall = 7294967296 
    kernel.sem = 10000 32000 10000 10000
    ```

5. 動態刷新內核參數而不重新開機：

    ```vi
    [root@ofdemo ~]# /sbin/sysctl -p
    ```

6. 獲取所需的包：確保伺服器已連接到 Internet，下載以下套裝程式，然後安裝它們：

     - 多斯2unix
     - glibc
     - 格利bc.i686 glibc.x86\_64
     - 利巴約
     - 恩詛咒

          > [!NOTE]
          > 安裝 ncurses 包後，創建以下符號連結：
         ```
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so.2
         ```

     - gcc
     - gcc-c++
     - 利巴約-德維爾.x86\_64
     - 跟蹤
     - ltrace
     - gdb

7. 在 JAVA RPM 安裝的情況下，執行以下操作：

```
root@ofdemo ~]# rpm -ivh jdk-7u79-linux-x64.rpm
[root@ofdemo ~]# vi .bash_profile

# JAVA ENV
export JAVA_HOME=/usr/java/jdk1.7.0_79/
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=$CLASSPATH:$JAVA_HOME/jre/lib/ext:$JAVA_HOME/lib/tools.jar

[root@ofdemo ~]# source /etc/profile
[root@ofdemo ~]# java –version

java version "1.7.0_79"
Java(TM) SE Runtime Environment (build 1.7.0_79-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.79-b02, mixed mode)

[root@ofdemo ~]# echo $JAVA_HOME /usr/java/jdk1.7.0_79/
```

## <a name="install-the-tibero-database"></a>安裝蒂貝羅資料庫

Tibero 在 Azure 上的 OpenFrame 環境中提供了幾個關鍵功能：

- Tibero 用作 OpenFrame 內部資料存儲，用於各種系統功能。
- VSAM 檔（包括 KSDS、RRDS 和 ESDS）在內部使用 Tibero 資料庫進行資料存儲。
- TACF 資料存儲庫存儲在蒂貝羅。
- OpenFrame 目錄資訊存儲在 Tibero 中。
- Tibero 資料庫可用作 IBM Db2 的替換來存儲應用程式資料。

**安裝蒂貝羅**

1. 驗證 Tibero 二進位安裝程式檔是否存在並查看版本號。
2. 將 Tibero 軟體複製到 Tibero 使用者帳戶 （oframe）。 例如：

    ```
    [oframe7@ofdemo ~]$ tar -xzvf tibero6-bin-6_rel_FS04-linux64-121793-opt-tested.tar.gz 
    [oframe7@ofdemo ~]$ mv license.xml /opt/tmaxdb/tibero6/license/
    ```

3. 在 vi\_（`vi .bash_profile`） 中打開 .bash 設定檔，並在其中粘貼以下內容：

    ```
    # Tibero6 ENV
    export TB_HOME=/opt/tmaxdb/tibero6 
    export TB_SID=TVSAM export TB_PROF_DIR=$TB_HOME/bin/prof 
    export LD_LIBRARY_PATH=$TB_HOME/lib:$TB_HOME/client/lib:$LD_LIBRARY_PATH 
    export PATH=$TB_HOME/bin:$TB_HOME/client/bin:$PATH
    ```

4. 要執行 bash 設定檔，請按命令提示符類型執行：

    ```
    source .bash_profile
    ```

5. 生成提示檔（Tibero 的設定檔），然後在 vi 中打開它。 例如：

    ```
    [oframe7@ofdemo ~]$ sh $TB_HOME/config/gen_tip.sh
    [oframe7@ofdemo ~]$ vi $TB_HOME/config/$TB_SID.tip
    ```

6. 修改\$\_TB HOME/用戶端/配置/tbdsn.tbr 並放置 127.0.0.1 而不是本地主機，如下所示：

    ```
    TVSAM=( 
    (INSTANCE=(HOST=127.0.0.1)
        (PT=8629)
    (DB_NAME=TVSAM)
          )
     )
    ```

7. 創建資料庫。 即會出現下列輸出：

    ```
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NOMOUNT mode).
     /--------------------- newmount sql ------------------------/
    create database character set MSWIN949 national character set UTF16;
    /-----------------------------------------------------------/
    Database created.
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    /opt/tmaxdb/tibero6/bin/tbsvr
    ………………………..
    Creating agent table...
    Done.
    For details, check /opt/tmaxdb/tibero6/instance/TVSAM/log/system_init.log.
    ************************************************** 
    * Tibero Database TVSAM is created successfully on Fri Aug 12 19:10:43 UTC 2016.
    *     Tibero home directory ($TB_HOME) =
    *         /opt/tmaxdb/tibero6
    *     Tibero service ID ($TB_SID) = TVSAM
    *     Tibero binary path =
    *         /opt/tmaxdb/tibero6/bin:/opt/tmaxdb/tibero6/client/bin
    *     Initialization parameter file =
    *         /opt/tmaxdb/tibero6/config/TVSAM.tip
    * 
    * Make sure that you always set up environment variables $TB_HOME and
    * $TB_SID properly before you run Tibero.
     ******************************************************************************
    ```

8. 要回收 Tibero，首先使用 命令`tbdown`將其關閉。 例如：

    ```
    [oframe7@ofdemo ~]$$ tbdown 
    Tibero instance terminated (NORMAL mode).
    ```

9. 現在啟動蒂貝羅使用`tbboot`。 例如：

    ```
    [oframe7@ofdemo ~]$ tbboot
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof. Listener port = 8629

    Tibero 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    ```

10. 要創建表空間，請使用 SYS 使用者 （sys/tmax） 訪問資料庫，然後為預設卷和 TACF 創建必要的表空間：

    ```
    [oframe7@ofdemo ~]$ tbsql tibero/tmax
    tbSQL 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Connected to Tibero.
    ```

11. 現在鍵入以下 SQL 命令：

    ```
    SQL> create tablespace "DEFVOL" datafile 'DEFVOL.dbf' size 500M autoextend on; create tablespace "TACF00" datafile 'TACF00.dbf' size 500M autoextend on; create tablespace "OFM_REPOSITORY" datafile 'ofm_repository.dbf' size 300M autoextend on;
    SQL> Tablespace 'DEFVOL' created.
    SQL> Tablespace 'TACF00' created.
    SQL> Tablespace ' OFM_REPOSITORY ' created.
    SQL> SQL> Disconnected.
    ```

12. 啟動 Tibero 並驗證 Tibero 進程是否正在運行：

    ```
    [oframe7@ofdemo ~]$ tbboot 
    ps -ef | egrep tbsvr
    ```

輸出：

![蒂貝羅輸出](media/tibero-01.png)

## <a name="install-odbc"></a>安裝 ODBC

OpenFrame 中的應用程式使用開源 unixODBC 專案提供的 ODBC API 與 Tibero 資料庫通信。

要安裝 ODBC：

1. 驗證 unixODBC-2.3.4.tar.gz 安裝程式檔是否存在，或使用 該`wget unixODBC-2.3.4.tar.gz`命令。 例如：

     ```
     [oframe7@ofdemo ~]$ wget ftp://ftp.unixodbc.org/pub/unixODBC/unixODBC-2.3.4.tar.gz
     ```

2. 解壓縮二進位檔案。 例如：

     ```
     [oframe7@ofdemo ~]$ tar -zxvf unixODBC-2.3.4.tar.gz
     ```

3. 導航到 unixODBC-2.3.4 目錄，並使用檢查機資訊生成 Makefile。 例如：

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ ./configure --prefix=/opt/tmaxapp/unixODBC/ --sysconfdir=/opt/tmaxapp/unixODBC/etc
     ```

     預設情況下，unixODBC 安裝在 /usr /local 中`--prefix`，因此傳遞值以更改位置。 同樣，預設情況下，設定檔安裝在 /etc 中，因此`--sysconfdir`傳遞所需位置的值。

4. 執行 Makefile：`[oframe7@ofdemo unixODBC-2.3.4]$ make`

5. 編譯後，在程式目錄中複製可執行檔。 例如：

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ make install
     ```

6. 使用 vi 編輯 bash`vi ~/.bash_profile`設定檔 （ ） 並添加以下內容：

     ```
     # UNIX ODBC ENV 
     export ODBC_HOME=$HOME/unixODBC 
     export PATH=$ODBC_HOME/bin:$PATH 
     export LD_LIBRARY_PATH=$ODBC_HOME/lib:$LD_LIBRARY_PATH 
     export ODBCINI=$HOME/unixODBC/etc/odbc.ini 
     export ODBCSYSINI=$HOME
     ```

7. 應用 ODBC。 相應地編輯以下檔。 例如：

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ source ~/.bash_profile

     [oframe7@ofdemo ~]$ cd

     [oframe7@ofdemo ~]$ odbcinst -j unixODBC 2.3.4
     DRIVERS............: /home/oframe7/odbcinst.ini
     SYSTEM DATA SOURCES: /home/oframe7/odbc.ini
     FILE DATA SOURCES..: /home/oframe7/ODBCDataSources
     USER DATA SOURCES..: /home/oframe7/unixODBC/etc/odbc.ini
     SQLULEN Size.......: 8
     SQLLEN Size........: 8
     SQLSETPOSIROW Size.: 8

     [oframe7@ofdemo ~]$ vi odbcinst.ini

     [Tibero]
     Description = Tibero ODBC driver for Tibero6
     Driver = /opt/tmaxdb/tibero6/client/lib/libtbodbc.so
     Setup = 
     FileUsage = 
     CPTimeout = 
     CPReuse = 
     Driver Logging = 7

     [ODBC]
     Trace = NO 
     TraceFile = /home/oframe7/odbc.log 
     ForceTrace = Yes 
     Pooling = No 
     DEBUG = 1

     [oframe7@ofdemo ~]$ vi odbc.ini

     [TVSAM]
     Description = Tibero ODBC driver for Tibero6 
     Driver = Tibero 
     DSN = TVSAM 
     SID = TVSAM 
     User = tibero 
     password = tmax
     ```

8. 創建符號連結並驗證 Tibero 資料庫連接：

     ```
     [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbc.so $ODBC_HOME/lib/libodbc.so.1 [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbcinst.so 
     $ODBC_HOME/lib/libodbcinst.so.1

     [oframe7@ofdemo lib]$ isql TVSAM tibero tmax
     ```

下列輸出隨即顯示：

![顯示連接到 SQL 的 ODBC 輸出](media/odbc-01.png)

## <a name="install-openframe-base"></a>安裝開放框架底座

基本應用程式伺服器安裝在 OpenFrame 用於在 Azure 上管理系統的各個服務之前，包括交易處理伺服器進程。

**安裝 OpenFrame 底座**

1. 確保 Tibero 安裝成功，然後驗證以下\_OpenFrame Base7\_\_0\_Linux\_x86 64.bin 安裝程式檔和 base.屬性設定檔是否存在。

2. 使用以下特定于 Tibero 的資訊更新 bash 設定檔：

     ```bash
     alias ofhome='cd $OPENFRAME_HOME'
     alias ulog='cd $OPENFRAME_HOME/log/tmax/ulog'
     alias sysjcl='cd $OPENFRAME_HOME/volume_default/SYS1.JCLLIB'
     alias sysload='cd $OPENFRAME_HOME/volume_default/SYS1.LOADLIB'
     alias sysproc='cd $OPENFRAME_HOME/volume_default/SYS1.PROCLIB'
     alias oscsrc='cd $OPENFRAME_HOME/osc/oivp'
     alias osisrc='cd $OPENFRAME_HOME/osi/oivp'
     alias defvol='cd $OPENFRAME_HOME/volume_default'
     ```

3. 執行 bash 設定檔：`[oframe7@ofdemo ~]$ . .bash_profile`
4. 確保 Tibero 進程正在運行。 例如：

     ```linux
     [oframe7@ofdemo ~]$ ps -ef|grep tbsvr
     ```

    ![基本](media/base-01.png)

     > [!IMPORTANT]
     > 請確保在安裝之前啟動 Tibero。

5. 在[technet.tmaxsoft.com](https://technet.tmaxsoft.com/en/front/main/main.do)生成許可證，並將 OpenFrame 基礎、批次處理、TACF、OSC 許可證放入相應的資料夾中：

     ```
     [oframe7@ofdemo ~]$ cp license.dat /opt/tmaxapp/OpenFrame/core/license/
     [oframe7@ofdemo ~]$ cp lictjes.dat lictacf.dat licosc.dat $OPENFRAME_HOME/license/
     ```

6. 下載 OpenFrame 基礎二進位檔案和 base.屬性檔：

     ```
     [oframe7@ofdemo ~]$ vi base.properties
     OPENFRAME_HOME= <appropriate location for installation> ex. /opt/tmaxapp/OpenFrame TP_HOST_NAME=<your IP Hostname> ex. ofdemo 
     TP_HOST_IP=<your IP Address> ex. 192.168.96.148 
     TP_SHMKEY=63481 
     TP_TPORTNO=6623 
     TP_UNBLOCK_PORT=6291 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     MASCAT_NAME=SYS1.MASTER.ICFCAT 
     MASCAT_CREATE=YES 
     DEFAULT_VOLSER=DEFVOL 
     VOLADD_DEFINE=YES TSAM_USERNAME=tibero 
     TSAM_PASSWORD=tmax 
     TSAM_DATABASE=oframe 
     DATASET_SHMKEY=63211 
     DSLOCK_DATA=SYS1.DSLOCK.DATA 
     DSLOCK_LOG=SYS1.DSLOCK.LOG 
     DSLOCK_SEQ=dslock_seq.dat 
     DSLOCK_CREATE=YES 
     OPENFRAME_LICENSE_PATH=/opt/tmaxapp/license/OPENFRAME TMAX_LICENSE_PATH=/opt/tmaxapp/license/TMAX
     ```

7. 使用 base.屬性檔執行安裝程式。 例如：

    ```
    [oframe7@ofdemo ~]$ chmod a+x OpenFrame_Base7_0_Linux_x86_64.bin 
    [oframe7@ofdemo ~]$ ./OpenFrame_Base7_0_Linux_x86_64.bin -f base.properties
    ```

    完成後，將播放"安裝完成"消息。

8. 使用`ls -ltr`命令驗證 OpenFrame 基礎目錄結構。 例如：

     ```
     [oframe7@ofdemo OpenFrame]$ ls -ltr
     total 44

     drwxrwxr-x. 4 oframe7 oframe7 61 Nov 30 16:57 UninstallerData 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 bin 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 cpm drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 data 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 include 
     drwxrwxr-x. 2 oframe7 oframe7 8192 Nov 30 16:57 lib 
     drwxrwxr-x. 6 oframe7 oframe7 48 Nov 30 16:57 log 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 profile 
     drwxrwxr-x. 7 oframe7 oframe7 62 Nov 30 16:57 sample 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 schema 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 temp 
     drwxrwxr-x. 3 oframe7 oframe7 16 Nov 30 16:57 shared 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 license 
     drwxrwxr-x. 23 oframe7 oframe7 4096 Nov 30 16:58 core 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 config 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 scripts 
     drwxrwxr-x. 2 oframe7 oframe7 25 Nov 30 16:58 volume_default
     ```

9. 啟動開放框架庫：

     ```
     [oframe7@ofdemo ~]$ cp /usr/lib/libtermcap.so.2 $TMAXDIR/lib
     Startup Tmax Server
     [oframe7@ofdemo ~]$ tmboot
     ```

     ![tmboot 命令輸出](media/base-02.png)

10. 使用 si 中的 tmadmin 命令驗證進程狀態是否就緒。 RDY 顯示在每個進程**的狀態**列中：

     ![tmadmin 命令輸出](media/base-03.png)

11. 關閉打開框架底座：

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(vtammgr:43) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLH downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLL downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TLM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-openframe-batch"></a>安裝打開幀批次處理

OpenFrame Batch 由多個元件組成，這些元件類比大型機批次處理環境，並用於在 Azure 上運行批次處理作業。

**安裝批次處理**

1. 確保基本安裝成功，\_然後驗證 OpenFrame Batch7\_0\_Fix2\_MVS\_\_\_Linux x86 64.bin 安裝程式檔和 batch.屬性設定檔是否存在：

2. 在命令提示符下，`vi batch.properties`鍵入以使用 vi 編輯 batch.屬性檔。

3. 修改參數如下：

     ```
     OPENFRAME_HOME = /opt/tmaxapp/OpenFrame
     DEFAULT_VOLSER=DEFVOL 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     RESOURCE_SHMKEY=66991 
     #JOBQ_DATASET_CREATE=YES 
     #OUTPUTQ_DATASET_CREATE=YES 
     DEFAULT_JCLLIB_CREATE=YES 
     DEFAULT_PROCLIB_CREATE=YES 
     DEFAULT_USERLIB_CREATE=YES 
     TJES_USERNAME=tibero 
     TJES_PASSWORD=tmax 
     TJES_DATABASE=oframe 
     BATCH_TABLE_CREATE=YES
     ```

4. 要執行批次處理安裝程式，請執行命令提示符類型：

     ```
     ./OpenFrame_Batch7_0_Fix2_MVS_Linux_x86_64.bin -f batch.properties
     ```

5. 安裝完成後，通過在命令提示`tmboot`符處鍵入啟動已安裝的 OpenFrame 套件。

    ![tmboot 輸出](media/tmboot-01.png)

6. 在`tmadmin`命令提示符處鍵入以檢查 OpenFrame 過程。

    ![Tmax 管理螢幕](media/tmadmin-01.png)

7. 執行下列命令：

     ```
     $$2 NODE1 (tmadm): quit 
     ADM quit for node (NODE1)
     ```

8. 使用`tmdown`命令啟動和關閉批次處理：

     ```
     [oframe7@ofdemo ~]$tmdown
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:44) downed: Wed Sep  7 16:01:46 2016
     TMDOWN: SERVER(vtammgr: 43) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:45) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:46) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjinit:55) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:49) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:51) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:52) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:53) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLL downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TLM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLH downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-tacf"></a>安裝 TACF

TACF 管理器是一個 OpenFrame 服務模組，通過 RACF 安全性控制使用者對系統和資源的訪問。

**安裝 TACF**

1. 驗證\_OpenFrame Tacf7\_\_0\_Fix2\_Linux\_x86 64.bin 安裝程式檔和 tacf.屬性設定檔是否存在。
2. 確保批次處理安裝成功，然後使用 vi 打開 tacf.屬性檔 （）。`vi tacf.properties`
3. 修改 TACF 參數：

     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame 
     USE_OS_AUTH=NO 
     TACF_USERNAME=tibero 
     TACF_PASSWORD=tmax 
     TACF_DATABASE=oframe 
     TACF_TABLESPACE=TACF00 
     TACF_TABLE_CREATE=YES
     ```

4. 完成 TACF 安裝程式後，應用 TACF 環境變數。 在命令提示字元中，輸入：

     ```
     source \~/.bash\_profile
     ```

5. 執行 TACF 安裝程式。 在命令提示字元中，輸入：

     ```
     ./OpenFrame_Tacf7_0_Fix2_Linux_x86_64.bin -f tacf.properties
     ```

     輸出會與下列內容類似：

     ```
     Wed Dec 07 17:36:42 EDT 2016
     Free Memory: 18703 kB 
     Total Memory: 28800 kB

     4 Command Line Args: 
     0:  -f 1:  tacf.properties 
     2:  -m 
     3:  SILENT 
     java.class.path: 
     /tmp/install.dir.41422/InstallerData 
     /tmp/install.dir.41422/InstallerData/installer.zip 
     ZGUtil.CLASS_PATH: 
     /tmp/install.dir.41422/InstallerData 
     tmp/install.dir.41422/InstallerData/installer.zip 
     sun.boot.class.path: 
     /tmp/install.dir.41422/Linux/resource/jre/lib/resources.jar /tmp/install.dir.41422/Linux/resource/jre/lib/rt.jar /tmp/install.dir.41422/Linux/resource/jre/lib/sunrsasign.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jsse.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jce.jar /tmp/install.dir.41422/Linux/resource/jre/lib/charsets.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jfr.jar /tmp/install.dir.41422/Linux/resource/jre/classes
     ```

6. 在命令提示符下，`tmboot`鍵入以重新開機 OpenFrame。 輸出會與下列內容類似：

     ```
     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/7 
     TMBOOT: TMM is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLL is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLH is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: TLM(tlm) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsasvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrlhsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdsedt) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrcmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofruisvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsmlog) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(vtammgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjschd) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjinit) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjhist) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjspbk) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrpmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmtsmgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(tmsvr) is starting: Wed Sep  7 17:48:53 2016
     ```

7. 驗證在`tmadmin``si`命令中使用進程狀態是否就緒。 例如：

     ```
     [oframe7\@ofdemo \~]\$ tmadmin
     ```

     在**狀態**列中，將顯示 RDY：

    ![狀態列中的 RDY](media/tmboot-02.png)

8. 執行下列命令：

     ```
     $$2 NODE1 (tmadm): quit 
     DM quit for node (NODE1)

     [oframe7@ofdemo ~]$ tacfmgr 
     Input USERNAME  : ROOT 
     Input PASSWORD  : SYS1

     TACFMGR: TACF MANAGER START!!!
     QUIT TACFMGR: TACF MANAGER END!!!

     [oframe7@ofdemo ~]$ tmdow
     ```

9. 使用`tmdown`命令關閉伺服器。 輸出會與下列內容類似：

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(tmsvr:60) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLL downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLH downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TLM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-prosort"></a>安裝 ProSort

ProSort 是一種用於批次處理事務的實用程式，用於對資料進行排序。

**安裝 ProSort**

1. 確保批次處理安裝成功，然後驗證**\_prosort-bin-prosort 2sp3-linux64-2123-opt.tar.gz**安裝程式檔是否存在。

2. 使用屬性檔執行安裝程式。 在命令提示字元中，輸入：

     ```
     tar -zxvf prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz
     ```

3. 將 prosort 目錄移動到主位置。 在命令提示字元中，輸入：

     ```
     mv prosort /opt/tmaxapp/prosort
     ```

4. 創建許可證子目錄並在那裡複製授權檔案。 例如：

     ```
     cd /opt/tmaxapp/prosort 
     mkdir license 
     cp /opt/tmaxsw/oflicense/prosort/license.xml /opt/tmaxapp/prosort/license
     ```

5. 在 vi 中打開`vi .bash_profile`bash.profile ， 並更新如下：

     ```bash
     #       PROSORT

     PROSORT_HOME=/opt/tmaxapp/prosort 
     PROSORT_SID=gbg 
     PATH=$PATH:$PROSORT_HOME/bin LD_LIBRARY_PATH=$PROSORT_HOME/lib:$LD_LIBRARY_PATH LIBPATH$PROSORT_HOME/lib:$LIBPATH 
     export PROSORT_HOME PROSORT_SID 
     PATH LD_LIBRARY_PATH LIBPATH 
     PATH=$PATH:$OPENFRAME_HOME/shbin 
     export PATH
     ```

6. 要執行 bash 設定檔，在命令提示符處，請鍵入：`. .bash_profile`

7. 創建設定檔。 例如：

     ```
     oframe@oframe7: cd /opt/tmaxapp/prosort/config 
     oframe@oframe7: ./gen_tip.sh 
     Using PROSORT_SID "gbg"
      /home/oframe7/prosort/config/gbg.tip generated
     ```

8. 創建符號連結。 例如：

     ```
     oframe@oframe7: cd /opt/tmaxapp/OpenFrame/util/ 
     oframe@oframe7home/oframe7/OpenFrame/util :  ln -s DFSORT SORT
     ```

9. 通過執行`prosort -h`命令驗證 ProSort 安裝。 例如：

     ```
     oframe@oframe7: prosort -h

     Usage: prosort [options] [sort script files]
     options ------
     -h             Display this information 
     -v             Display version information 
     -s             Display state information 
     -j             Display profile information 
     -x             Use SyncSort compatible mode
     ```

## <a name="install-ofcobol"></a>安裝 OFCOBOL

OFCOBOL 是解釋大型機 COBOL 程式的 OpenFrame 編譯器。 

**安裝 OFCOBOL**

1. 確保批次處理/\_連線安裝成功，然後驗證 OpenFrame COBOL3\_0\_40\_Linux\_x86\_64.bin 安裝程式檔是否存在。

2. 要執行 OFCOBOL 安裝程式，在命令提示符下，鍵入：

     ```
      ./OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64.bin
     ```

3. 閱讀授權合約，然後按 Enter 繼續。

4. 接受授權合約。 安裝完成後，將顯示以下內容：

     ```
     Choose Install Folder 
     --------------------
     Where would you like to install?
     Default Install Folder: /home/oframe7/OFCOBOL

     ENTER AN ABSOLUTE PATH, OR PRESS <ENTER> TO ACCEPT THE DEFAULT : /opt/tmaxapp/OFCOBOL

     INSTALL FOLDER IS: /opt/tmaxapp/OFCOBOL 
     IS THIS CORRECT? (Y/N): Y[oframe7@ofdemo ~]$ vi .bash_profile

     ============================================================================ Installing... 
     ------------
     [==================|==================|==================|==================] 
     [------------------|------------------|------------------|------------------]

     =============================================================================== Installation Complete 
     --------------------
     Congratulations. OpenFrame_COBOL has been successfully installed
     PRESS <ENTER> TO EXIT THE INSTALLER
     ```

5. 打開 vi 中的 bash`vi .bash_profile`設定檔 ， 並驗證是否更新了 OFCOBOL 變數。
6. 執行 bash 設定檔。 在命令提示字元中，輸入：

     ```
      source ~/.bash_profile
     ```

7. 將 OFCOBOL 許可證複製到已安裝的資料夾。 例如：
     ```
     mv licofcob.dat $OFCOB_HOME/license
     ```
8. 轉到 OpenFrame tjclrun.conf 設定檔，並在 vi 中打開該檔。 例如：
     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

   以下是更改前的 SYSLIB 部分：
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib
     ```
   以下是更改後的 SYSLIB 部分：
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib :${ODBC_HOME}/lib 
     :${OFCOB_HOME}/lib
     ```
9. 查看 vi\_中的\_OpenFrame COBOL 安裝日誌檔，並驗證是否存在錯誤。 例如：
     ```
     [oframe7@ofdemo ~]$ vi $OFCOB_HOME/UninstallerData/log/OpenFrame_COBOL_InstallLog.log 
     …….. 
     Summary 
     ------
     Installation: Successful. 
     131 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalError
     ```
10. 使用`ofcob --version`命令並查看版本號以驗證安裝。 例如：

     ```
     [oframe7@ofdemo ~]$ ofcob --version 
     OpenFrame COBOL Compiler 3.0.54 
     CommitTag:: 645f3f6bf7fbe1c366a6557c55b96c48454f4bf
     ```

11. 使用 命令`tmdown/tmboot`重新開機打開幀。

## <a name="install-ofasm"></a>安裝 OFASM

OFASM 是解釋大型機的組合語言程式的 OpenFrame 編譯器。

**安裝 OFASM**

1. 確保批次處理/連線安裝成功，然後驗證\_OpenFrame ASM3\_0\_Linux\_x86\_64.bin 安裝程式檔是否存在。

2. 執行安裝程式。 例如：

     ```
     [oframe7@ofdemo ~]$ ./OpenFrame_ASM3_0_Linux_x86_64.bin
     ```

3. 閱讀授權合約，然後按 Enter 繼續。
4. 接受授權合約。
5. 使用 OFASM 變數驗證 bash 設定檔已更新。 例如：

     ```
     [oframe7@ofdemo ~]$ source .bash_profile
     [oframe7@ofdemo ~]$ ofasm --version 
     # TmaxSoft OpenFrameAssembler v3 r328 
     (3ff35168d34f6e2046b96415bbe374160fcb3a34)

     [oframe7@ofdemo OFASM]$ vi .bash_profile

     # OFASM ENV 
     export OFASM_HOME=/opt/tmaxapp/OFASM 
     export OFASM_MACLIB=$OFASM_HOME/maclib/free_macro 
     export PATH="${PATH}:$OFASM_HOME/bin:" 
     export LD_LIBRARY_PATH="./:$OFASM_HOME/lib:$LD_LIBRARY_PATH"
     ```

6. 在 vi 中打開 OpenFrame tjclrun.conf 設定檔，然後按照如下方式對其進行編輯：

     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

     以下是更改*前的*[SYSLIB] 部分：

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib
     ```

     下面是更改*後的*[SYSLIB] 部分：

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib:${OFASM_HOME}/lib
     ```

7. 在 vi\_中打開\_OpenFrame ASM 安裝日誌.日誌檔，並驗證是否存在錯誤。 例如：

     ```
     [oframe7@ofdemo ~]$ vi 
     $OFASM_HOME/UninstallerData/log/OpenFrame_ASM_InstallLog.log 
     …….. 
     Summary 
     ------

     Installation: Successful.

     55 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalErrors
     ```

8. 通過發出以下命令之一重新開機 OpenFrame：

     ```
     tmdown / tmboot
     ```

     – 或 –

     ```
     oscdown / oscboot
     ```

## <a name="install-osc"></a>安裝 OSC

OSC 是與 IBM CICS 類似的開放框架環境，支援高速 OLTP 事務和其他管理功能。

**安裝 OSC**

1. 確保\_基本安裝成功，然後驗證 OpenFrame OSC7\_0\_Fix2\_\_Linux x86\_64.bin 安裝程式檔和 osc.屬性設定檔是否存在。
2. 在 osc.屬性檔中編輯以下參數：
     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame OSC_SYS_OSC_NCS_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_NCS OSC_APP_OSC_TC_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_TC
     ```

3. 使用屬性檔執行安裝程式，如下所示：

     ```
     [oframe7@ofdemo ~]$ chmod a+x OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin [oframe7@ofdemo ~]$ ./OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin -f osc.properties
     ```

     完成後，將顯示"安裝完成"消息。

4. 驗證 bash 設定檔是否使用 OSC 變數更新。
5. 查看 OpenFrame\_\_OSC7\_0\_修復2 安裝日誌檔。 其外觀應該如下所示：

     ```
     Summary 
     ------ 
     Installation: Successful.

     233 Successes
     0 Warnings
     0 NonFatalErrors
     0 FatalError
     ```

6. 使用 vi 打開 syssys.seq 設定檔。 例如：

     ```
     vi $OPENFRAME_HOME/config/ofsys.seq
     ```

7. 在\#BASE\#和 BATCH 部分中，如所示編輯參數。

     ```
     Before changes
     #BASE
     ofrsasvr
     ofrlhsvr
     ofrdmsvr
     ofrdsedt
     ofrcmsvr
     ofruisvr
     ofrsmlog
     vtammgr
     TPFMAGENT

     #BATCH 
     #BATCH#obmtsmgr
     #BATCH#ofrpmsvr
     #BATCH#obmjmsvr
     #BATCH#obmjschd
     #BATCH#obmjinit
     #BATCH#obmjhist
     #BATCH#obmjspbk
     #TACF #TACF#tmsvr

     After changes  #BATCH
     #BASE          obmtsmgr 
     ofrsasvr       ofrpmsvr
     ofrlhsvr       obmjmsvr
     ofrdmsvr       obmjschd
     ofrdsedt       obmjinit
     ofrcmsvr       obmjhist
     ofruisvr       obmjspbk
     ofrsmlog
     vtammgr        #TACF
     TPFMAGENT      tmsvr
    ```

8. 複製授權檔案。 例如：

     ```
     [oframe7@ofdemo ~]$ cp /home/oframe7/oflicense/ofonline/licosc.dat 

     $OPENFRAME_HOME/license

     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/license 
     oframe@oframe7/OpenFrame/license / ls -l 
     -rwxr-xr-x. 1 oframe mqm 80 Sep 12 01:37 licosc.dat 
     -rwxr-xr-x. 1 oframe mqm 80 Sep  8 09:40 lictacf.dat 
     -rwxrwxr-x. 1 oframe mqm 80 Sep  3 11:54 lictjes.da
     ```

9. 要啟動和關閉 OSC，請通過`osctdlinit OSCOIVP1`鍵入命令提示符來初始化 CICS 區域共用記憶體。

10. 運行`oscboot`以啟動 OSC。 輸出會與下列內容類似：

     ```
     OSCBOOT : pre-processing       [ OK ]

     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/12 
          TMBOOT: TMM is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLL is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLH is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: TLM(tlm) is starting: Mon Sep 12 01:40:25 2016 
     ```

11. 要驗證進程狀態是否就緒，請使用 si`tmadmin`中的命令。 所有進程都應在**狀態**列中顯示 RDY。

    ![顯示 RDY 的過程](media/tmadmin-02.png)

12. 使用`oscdown`命令關閉 OSC。

## <a name="install-jeus"></a>安裝 JEUS

JEUS（JAVA 企業使用者解決方案）提供 OpenFrame Web 應用程式伺服器的展示層。

在安裝 JEUS 之前，請安裝 Apache Ant 包，該包提供安裝 JEUS 所需的庫和命令列工具。

**安裝阿帕奇螞蟻**

1. 使用`wget`命令下載 Ant 二進位檔案。 例如：

     ```
     wget http://apache.mirror.cdnetworks.com/ant/binaries/apacheant-1.9.7-bin.tar.gz
     ```

2. 使用實用程式`tar`提取二進位檔案並將其移動到適當的位置。 例如：

     ```
     tar -xvzf apache-ant-1.9.7-bin.tar.gz
     ```

3. 為了提高效率，請創建一個符號連結：

     ```
     ln -s apache-ant-1.9.7 ant
     ```

4. 打開 vi 中的 bash`vi .bash_profile`設定檔 ，然後使用以下變數更新它：

     ```
     # Ant ENV
     export ANT_HOME=$HOME/ant 
     export PATH=$HOME/ant/bin:$PATH
     ```

5.  應用修改後的環境變數。 例如：

     ```
     [oframe7\@ofdemo \~]\$ source \~/.bash\_profile
     ```

**安裝 JEUS**

1. 使用`tar`實用程式擴展安裝程式。 例如：

     ```
     [oframe7@ofdemo ~]$ tar -zxvf jeus704.tar.gz
     ```

2. 創建一個**jeus**資料夾 （`mkdir jeus7`）， 並解壓縮二進位檔案。
3. 更改為**設置**目錄（或使用 JEUS 參數進行您自己的環境）。 例如：

     ```
     [oframe7@ofdemo ~]$ cd jeus7/setup/
     ```

4. 在執行`ant clean-all`生成之前執行。 輸出會與下列內容類似：

     ```
     Buildfile: /home/oframe7jeus7/setup/build.xml

     clean-bin:
     delete-domain:
     [echo] Deleting a domain configuration: domain = jeus_domain
     delete-nodesxml:
     clean-config:
     clean-all:
     BUILD SUCCESSFUL
     Total time: 0 seconds
     ```

5.  備份域配置範本.屬性檔。 例如：

     ```
     [oframe7@ofdemo ~]$ cp domain-config-template.properties domain-configtemplate.properties.bkp
     ```

6. 打開域配置範本.屬性檔，在 vi 中：

     ```
     [oframe7\@ofdemo setup]\$ vi domain-config-template.properties
     ```

7. 將 `jeus.password=jeusadmin nodename=Tmaxsoft` 變更為 `jeus.password=tmax1234 nodename=ofdemo`

8. 執行命令`ant install`以生成 JEUS。
9.  使用 JEUS\_變數更新 .bash 設定檔檔，如下所示：

     ```
     # JEUS ENV 
     export JEUS_HOME=/opt/tmaxui/jeus7 PATH="/opt/tmaxui/jeus7/bin:/opt/tmaxui/jeus7/lib/system:/opt/tmaxui/jeus7/webserver/bin:$ {PATH}" 
     export PATH
     ```

10. 執行 bash 設定檔。 例如：

     ```
     [oframe7@ofdemo setup]$ . .bash_profile
     ```

11. *選擇項*。 創建別名，以便輕鬆關閉和啟動 JEUS 元件：

     ```     
     # JEUS alias

     alias dsboot='startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin'
     alias msboot='startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin' 
     alias msdown=‘jeusadmin -u administrator -p tmax1234 "stop-server server1“’ 
     alias dsdown=‘jeusadmin -domain jeus_domain -u administrator -p tmax1234 "local-shutdown“’
     ```

12. 要驗證安裝，請啟動域管理員伺服器，如下所示：

     ```
     [oframe7@ofdemo ~]$ startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin
     ```

13. 使用語法通過 Web 登錄進行驗證：

     ```
     http://<IP>:<port>/webadmin/login
     ```

     例如，<http://192.168.92.133:9736/webadmin/login.>顯示登錄螢幕：
    
     ![JEUS WebAdmin 登錄螢幕](media/jeus-01.png)

     > [!NOTE]
     > 如果遇到埠安全問題，請打開端口 9736 或禁用防火牆 （）。`systemctl stop firewall`

14. 要更改伺服器 1 的主機名稱，請按一下 **"鎖定&編輯**"，然後按一下**伺服器 1**。 在"伺服器"視窗中，更改主機名稱，如下所示：

    1.  將**節點名稱**更改為**ofdemo**
    2.  按一下視窗右側的 **"確定**"。
    3.  按一下視窗左下角的 **"應用更改**"，有關說明，請輸入*主機名稱更改*。

    ![JEUS 網路系統管理員螢幕](media/jeus-02.png)

15. 在確認螢幕中驗證配置是否成功。

    ![jeus_domain 伺服器螢幕](media/jeus-03.png)

16. 使用以下命令啟動託管伺服器進程"server1"：

     ```
     [oframe7@ofdemo ~]$ startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin
     ```

## <a name="install-ofgw"></a>安裝OFGW

OFGW 是 OpenFrame 閘道，支援 3270 終端模擬器與 OSI 基座之間的通信，並管理終端模擬器和 OSI 之間的會話。

**安裝 OFGW**

1. 確保 JEUS 已成功安裝，然後驗證 OFGW7\_0\_1\_泛型.bin 安裝程式檔是否存在。
2. 執行安裝程式。 例如：

     ```
     [oframe7@ofdemo ~]$ ./OFGW7_0_1_Generic.bin
     ````

3. 對相應的提示使用以下位置：
     -   JEUS 主頁目錄
     -   JEUS 功能變數名稱
     -   JEUS 伺服器名稱
     -   蒂貝羅司機
     -   德莫的 Tmax 節點 ID

4. 接受其餘的預設值，然後按 Enter 退出安裝程式。

5. 驗證 OFGW 的 URL 是否按預期工作：

     ```
     Type URL 
     http://192.168.92.133:8088/webterminal/ and press enter
      < IP >               :8088/webterminal/
     ```

     下列畫面隨即出現。

    ![開放框架網路終端](media/ofgw-01.png)

## <a name="install-ofmanager"></a>安裝管理員

OFManager 為 Web 環境中的 OpenFrame 提供操作和管理功能。

**安裝管理管理器**

1. 驗證 OFManager7\_泛型.bin 安裝程式檔是否存在。
2. 執行安裝程式。 例如：

     ```
     OFManager7_Generic.bin
     ```

3.  按 Enter 繼續，然後接受授權合約。
4.  選擇安裝資料夾。
5.  接受預設值。
6.  選擇 Tibero 作為資料庫。
7.  按 Enter 退出安裝程式。
8.  驗證 OFManager 的 URL 是否按預期工作：

     ```
     Type URL http://192.168.92.133:8088/ofmanager and press enter <  IP >  : < PORT >  ofmanager Enter ID:   ROOT 
     Password: SYS1
     ```

將顯示"開始"螢幕：

![Tmax OpenFrame 管理器登錄螢幕](media/ofmanager-01.png)

這完成了 OpenFrame 元件的安裝。

## <a name="next-steps"></a>後續步驟

如果您正在考慮大型機遷移，我們不斷擴大的合作夥伴生態系統可以説明您。 如需選擇合作夥伴解決方案的詳細指引，請參閱 [Platform Modernization Alliance](https://datamigration.microsoft.com/)。

-   [開始使用 Azure](https://docs.microsoft.com/azure/)
-   [Host Integration Server (HIS) 文件](https://docs.microsoft.com/host-integration-server/)
-   [Azure 虛擬資料中心提升和移動指南](https://blogs.msdn.microsoft.com/azurecat/2018/03/12/new-whitepaper-azure-virtual-datacenter-lift-and-shift-guide/)
