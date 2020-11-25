---
title: 使用 InfluxData Telegraf 代理程式收集 Linux VM 的自訂計量
description: 有關如何在 Azure 中的 Linux VM 上部署 InfluxData Telegraf 代理程式，以及設定代理程式將計量發佈至 Azure 監視器的指示。
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: b80f27e490dd3b1890eab7740fb4650ba4280abb
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008985"
---
# <a name="collect-custom-metrics-for-a-linux-vm-with-the-influxdata-telegraf-agent"></a>使用 InfluxData Telegraf 代理程式收集 Linux VM 的自訂計量

您可以使用 Azure 監視器，透過應用程式遙測、在 Azure 資源上執行的代理程式，或甚至是由外到內的監視系統來收集自訂計量。 然後您可將這些計量直接提交給 Azure 監視器。 本文提供有關如何在 Azure 中的 Linux VM 上部署 [InfluxData](https://www.influxdata.com/) Telegraf 代理程式，以及設定該代理程式將計量發佈至 Azure 監視器的指示。 

## <a name="influxdata-telegraf-agent"></a>InfluxData Telegraf 代理程式 

[Telegraf](https://docs.influxdata.com/telegraf/) 是外掛程式驅動的代理程式，能夠從 150 個不同的來源收集計量。 根據 VM 上執行的工作負載而定，您可以將代理程式設定為運用特製化輸入外掛程式來收集計量。 範例包括 MySQL、NGINX 和 Apache。 藉由使用輸出外掛程式，代理程式即可寫入至您選擇的目的地。 Telegraf 代理程式已與 Azure 監視器自訂計量 REST API 直接整合， 可支援 Azure 監視器輸出外掛程式。 代理程式可以使用此外掛程式，收集 Linux VM 上的工作負載特有計量，並作為自訂計量提交至 Azure 監視器。 

 ![Telegraf 代理程式概觀](./media/collect-custom-metrics-linux-telegraf/telegraf-agent-overview.png)

> [!NOTE]  
> 並非所有區域都支援自訂計量。 [此處](./metrics-custom-overview.md#supported-regions)列出支援的區域

## <a name="send-custom-metrics"></a>傳送自訂計量 

在本教學課程中，我們會部署執行 Ubuntu 16.04 LTS 作業系統的 Linux VM。 大部分的 Linux 作業系統都支援 Telegraf 代理程式。 [InfluxData 下載入口網站](https://portal.influxdata.com/downloads)同時提供 Debian 與 RPM 套件，以及已解除封裝的 Linux 二進位檔案。 如需其他安裝指示和選項，請參閱 [Telegraf 安裝指南](https://docs.influxdata.com/telegraf/v1.8/introduction/installation/)。 

登入 [Azure 入口網站](https://portal.azure.com)。

> [!NOTE]  
> 如果您想要遷移傳統警示規則，並使用現有的 Linux 虛擬機器，請確定虛擬機器已將系統指派的身分識別設定為 [ **開啟**]。

建立新的 Linux VM： 

1. 從左側導覽窗格中選取 [ **建立資源** ] 選項。 
1. 搜尋 [虛擬機器]。  
1. 選取 [Ubuntu 16.04 LTS]，然後選取 [建立]。 
1. 提供 VM 名稱，例如 **MyTelegrafVM**。  
1. 讓磁碟類型保持為 **SSD**。 然後提供使用者 **名稱**（例如 **>azureuser**）。 
1. 針對 [ **驗證類型**]，選取 [ **密碼**]。 然後輸入密碼，以便稍後用來透過 SSH 連線到此 VM。 
1. 選擇 **建立新的資源群組**。 然後提供名稱，例如 **myResourceGroup**。 選擇您的 **位置**。 然後選取 [確定]  。 

    ![建立 Ubuntu VM](./media/collect-custom-metrics-linux-telegraf/create-vm.png)

1. 選取 VM 的大小。 您可以依 [計算類型] 或 [磁碟類型] 進行篩選 (舉例而言)。 

    ![虛擬機器大小 Telegraf 代理程式概觀](./media/collect-custom-metrics-linux-telegraf/vm-size.png)

1. 在 [**網路** 網路安全性群組] 的 [**設定**] 頁面上  >  **Network Security Group**  >  **，選取 [公用輸入埠**]，選取 [ **HTTP** 和 **SSH (22)**。 保留其餘的預設值，然後選取 [確定]  。 

1. 在 [摘要] 頁面上選取 [建立]，以開始進行 VM 部署。 

1. VM 會釘選到 Azure 入口網站儀表板。 完成部署後，VM 摘要就會自動開啟。 

1. 在 [VM] 窗格中，流覽至 [身分 **識別** ] 索引標籤。確定您的 VM 將系統指派的身分識別設定為 [ **開啟**]。 
 
    ![Telegraf VM 身分識別預覽](./media/collect-custom-metrics-linux-telegraf/connect-to-VM.png)
 
## <a name="connect-to-the-vm"></a>連接至 VM 

建立與 VM 的 SSH 連線。 在 VM 的 [概觀] 頁面上選取 [連線]  按鈕。 

![Telegraf VM 概觀頁面](./media/collect-custom-metrics-linux-telegraf/connect-VM-button2.png)

在 [連線至虛擬機器] 頁面中，保留以 DNS 名稱透過連接埠 22 進行連線的預設選項。 在 **[使用 VM 本機帳戶登** 入] 中，會顯示連線命令。 選取按鈕以複製該命令。 下列範例說明 SSH 連線命令的內容： 

```cmd
ssh azureuser@XXXX.XX.XXX 
```

將 SSH 連線命令貼到殼層中 (例如 Azure Cloud Shell、Windows 上 Ubuntu 的 Bash)，或使用您選擇的 SSH 用戶端來建立連線。 

## <a name="install-and-configure-telegraf"></a>安裝並設定 Telegraf 

若要將 Telegraf Debian 套件安裝到 VM 上，請從 SSH 工作階段執行下列命令： 

```cmd
# download the package to the VM 
wget https://dl.influxdata.com/telegraf/releases/telegraf_1.8.0~rc1-1_amd64.deb 
# install the package 
sudo dpkg -i telegraf_1.8.0~rc1-1_amd64.deb
```
Telegraf 的設定檔會定義 Telegraf 的作業。 根據預設，範例組態檔會安裝在 **/etc/telegraf/telegraf.conf** 路徑。 範例設定檔會列出所有可能的輸入與輸出外掛程式。不過，我們將建立自訂設定檔，並透過執行下列命令讓代理程式使用它： 

```cmd
# generate the new Telegraf config file in the current directory 
telegraf --input-filter cpu:mem --output-filter azure_monitor config > azm-telegraf.conf 

# replace the example config with the new generated config 
sudo cp azm-telegraf.conf /etc/telegraf/telegraf.conf 
```

> [!NOTE]  
> 上述程式碼只能啟用兩個輸入外掛程式：**cpu** 和 **mem**。 視您電腦上執行的工作負載而定，您可以新增更多輸入外掛程式。 範例包括 Docker、MySQL 和 NGINX。 如需輸入外掛程式的完整清單，請參閱 **其他設定** 一節。 

最後，執行下列命令強制停止並啟動代理程式，讓代理程式使用新設定來啟動： 

```cmd
# stop the telegraf agent on the VM 
sudo systemctl stop telegraf 
# start the telegraf agent on the VM to ensure it picks up the latest configuration 
sudo systemctl start telegraf 
```
代理程式現在會從每個指定的輸入外掛程式收集計量，並向 Azure 監視器發出計量。 

## <a name="plot-your-telegraf-metrics-in-the-azure-portal"></a>在 Azure 入口網站中繪製 Telegraf 計量 

1. 開啟 [Azure 入口網站](https://portal.azure.com)。 

1. 流覽至新的 [ **監視** ] 索引標籤。然後選取 [ **計量**]。  

     ![監視器 - 計量 (預覽)](./media/collect-custom-metrics-linux-telegraf/metrics.png)

1. 在資源選取器中選取您的 VM。

     ![計量圖表](./media/collect-custom-metrics-linux-telegraf/metric-chart.png)

1. 選取 **Telegraf/CPU** 命名空間，然後選取 **usage_system** 計量。 您可以根據此計量的維度來選擇篩選條件，或分割它們。  

     ![選取命名空間和計量](./media/collect-custom-metrics-linux-telegraf/VM-resource-selector.png)

## <a name="additional-configuration"></a>其他設定 

上述逐步解說提供如何設定 Telegraf 代理程式以從幾個基本輸入外掛程式收集計量的資訊。Telegraf 代理程式支援超過150的輸入外掛程式，還有一些支援的額外設定選項。 InfluxData 已發佈[支援的外掛程式清單](https://docs.influxdata.com/telegraf/v1.15/plugins/inputs/) \(英文\) 以及[如何設定](https://docs.influxdata.com/telegraf/v1.15/administration/configuration/) \(英文\) 的指示。  

此外，在此逐步解說中，您使用了 Telegraf 代理程式發出該代理程式部署所在 VM 的相關計量。 Telegraf 代理程式也可用來作為其他資源計量的收集器與轉寄站。 若要了解如何設定代理程式發出其他 Azure 資源的計量，請參閱 [Telegraf 的 Azure 監視器自訂計量輸出](https://github.com/influxdata/telegraf/blob/fb704500386214655e2adb53b6eb6b15f7a6c694/plugins/outputs/azure_monitor/README.md) \(英文\)。  

## <a name="clean-up-resources"></a>清除資源 

若不再需要，您可以刪除資源群組、虛擬機器和所有相關資源。 若要這樣做，請選取虛擬機器的資源群組，然後選取 [ **刪除**]。 然後確認要刪除的資源群組名稱。 

## <a name="next-steps"></a>後續步驟
- 深入了解[自訂計量](metrics-custom-overview.md)。
