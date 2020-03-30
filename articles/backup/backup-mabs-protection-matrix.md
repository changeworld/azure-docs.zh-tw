---
title: Azure 備份伺服器可以備份哪些內容
description: 本文提供支援矩陣，其中列出 Azure 備份伺服器保護的所有工作負載、資料類型和安裝。
ms.date: 11/13/2018
ms.topic: conceptual
ms.openlocfilehash: c7aa8823ca2bf638c6b726c504cddf8c5217fa19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444038"
---
# <a name="azure-backup-server-protection-matrix"></a>Azure 備份伺服器保護矩陣

本文列出您可以使用 Azure 備份伺服器保護的各種伺服器和工作負載。 下列矩陣列出可使用 Azure 備份伺服器保護的項目。

## <a name="protection-support-matrix"></a>保護支援矩陣

|工作負載|版本|Azure 備份伺服器</br> installation|受支援的 Azure 備份伺服器|保護和復原|
|------------|-----------|---------------|--------------|--------------|
|用戶端電腦 (64 位元和 32 位元)|Windows 10|實體伺服器<br /><br />Hyper-V 虛擬機器<br /><br />VMware 虛擬機器|V3， V2|磁碟區、共用、資料夾、檔案、已啟用重複資料刪除功能的磁碟區<br /><br />受保護的磁碟區必須是 NTFS。 不支援 FAT 和 FAT32。<br /><br />磁碟區至少必須是 1 GB。 Azure 備份伺服器使用卷卷卷複製服務 （VSS） 來拍攝資料快照，並且僅當卷至少為 1 GB 時，快照才有效。|
|用戶端電腦 (64 位元和 32 位元)|Windows 8.1|實體伺服器<br /><br />Hyper-V 虛擬機器|V3， V2|檔案<br /><br />受保護的磁碟區必須是 NTFS。 不支援 FAT 和 FAT32。<br /><br />磁碟區至少必須是 1 GB。 Azure 備份伺服器使用卷卷卷複製服務 （VSS） 來拍攝資料快照，並且僅當卷至少為 1 GB 時，快照才有效。|
|用戶端電腦 (64 位元和 32 位元)|Windows 8.1|VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)|V3， V2|磁碟區、共用、資料夾、檔案、已啟用重複資料刪除功能的磁碟區<br /><br />受保護的磁碟區必須是 NTFS。 不支援 FAT 和 FAT32。<br /><br />磁碟區至少必須是 1 GB。 Azure 備份伺服器使用卷卷卷複製服務 （VSS） 來拍攝資料快照，並且僅當卷至少為 1 GB 時，快照才有效。|
|用戶端電腦 (64 位元和 32 位元)|Windows 8|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器|V3， V2|磁碟區、共用、資料夾、檔案、已啟用重複資料刪除功能的磁碟區<br /><br />受保護的磁碟區必須是 NTFS。 不支援 FAT 和 FAT32。<br /><br />磁碟區至少必須是 1 GB。 Azure 備份伺服器使用卷卷卷複製服務 （VSS） 來拍攝資料快照，並且僅當卷至少為 1 GB 時，快照才有效。|
|用戶端電腦 (64 位元和 32 位元)|Windows 8|VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)|V3， V2|磁碟區、共用、資料夾、檔案、已啟用重複資料刪除功能的磁碟區<br /><br />受保護的磁碟區必須是 NTFS。 不支援 FAT 和 FAT32。<br /><br />磁碟區至少必須是 1 GB。 Azure 備份伺服器使用卷卷卷複製服務 （VSS） 來拍攝資料快照，並且僅當卷至少為 1 GB 時，快照才有效。|
|用戶端電腦 (64 位元和 32 位元)|Windows 7|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器|V3， V2|磁碟區、共用、資料夾、檔案、已啟用重複資料刪除功能的磁碟區<br /><br />受保護的磁碟區必須是 NTFS。 不支援 FAT 和 FAT32。<br /><br />磁碟區至少必須是 1 GB。 Azure 備份伺服器使用卷卷卷複製服務 （VSS） 來拍攝資料快照，並且僅當卷至少為 1 GB 時，快照才有效。|
|用戶端電腦 (64 位元和 32 位元)|Windows 7|VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)|V3， V2|磁碟區、共用、資料夾、檔案、已啟用重複資料刪除功能的磁碟區<br /><br />受保護的磁碟區必須是 NTFS。 不支援 FAT 和 FAT32。<br /><br />磁碟區至少必須是 1 GB。 Azure 備份伺服器使用卷卷卷複製服務 （VSS） 來拍攝資料快照，並且僅當卷至少為 1 GB 時，快照才有效。|
|伺服器 (64 位元)|Windows Server 2019|Azure 虛擬機器 (當工作負載當做 Azure 虛擬機器執行時)<br /><br />VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)<br /><br />實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器<br /> <br /> Azure Stack|V3 <br />不包括 Nano 伺服器|磁碟區、共用、資料夾、檔案、系統狀態/裸機、已啟用重複資料刪除功能的磁碟區|
|伺服器 (32 位元和 64 位元)|Windows Server 2016|Azure 虛擬機器 (當工作負載當做 Azure 虛擬機器執行時)<br /><br />VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)<br /><br />實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器<br /> <br /> Azure Stack|V3， V2<br />不包括 Nano 伺服器|磁碟區、共用、資料夾、檔案、系統狀態/裸機、已啟用重複資料刪除功能的磁碟區|
|伺服器 (32 位元和 64 位元)|Windows Server 2012 R2 - Datacenter 和 Standard|Azure 虛擬機器 (當工作負載當做 Azure 虛擬機器執行時)<br /> <br /> Azure Stack|V3， V2|磁碟區、共用、資料夾、檔案<br /><br />Azure 備份伺服器必須至少在 Windows 伺服器 2012 R2 上運行，以保護 Windows 伺服器 2012 dedup 卷。|
|伺服器 (32 位元和 64 位元)|Windows Server 2012 R2 - Datacenter 和 Standard|VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)<br /> <br /> Azure Stack|V3， V2|磁碟區、共用、資料夾、檔案、系統狀態/裸機<br /><br />Azure 備份伺服器必須在 Windows 伺服器 2012 或 2012 R2 上運行，以保護 Windows Server 2012 dedup 卷。|
|伺服器 (32 位元和 64 位元)|Windows Server 2012/2012 SP1 - Datacenter 和 Standard|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器<br /> <br /> Azure Stack|V3， V2|磁碟區、共用、資料夾、檔案、系統狀態/裸機<br /><br />Azure 備份伺服器必須至少在 Windows 伺服器 2012 R2 上運行，以保護 Windows 伺服器 2012 dedup 卷。|
|伺服器 (32 位元和 64 位元)|Windows Server 2012/2012 SP1 - Datacenter 和 Standard|Azure 虛擬機器 (當工作負載當做 Azure 虛擬機器執行時)<br /> <br /> Azure Stack|V3， V2|磁碟區、共用、資料夾、檔案<br /><br />Azure 備份伺服器必須至少在 Windows 伺服器 2012 R2 上運行，以保護 Windows 伺服器 2012 dedup 卷。|
|伺服器 (32 位元和 64 位元)|Windows Server 2012/2012 SP1 - Datacenter 和 Standard|VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)<br /> <br /> Azure Stack|V3， V2|磁碟區、共用、資料夾、檔案、系統狀態/裸機<br /><br />Azure 備份伺服器必須至少在 Windows 伺服器 2012 R2 上運行，以保護 Windows 伺服器 2012 dedup 卷。|
|伺服器 (32 位元和 64 位元)|Windows Server 2008 R2 SP1 - Standard 和 Enterprise|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器<br /> <br /> Azure Stack|V3， V2<br />您必須執行 SP1 並安裝 [Windows Management Frame 4.0](https://www.microsoft.com/download/details.aspx?id=40855)|磁碟區、共用、資料夾、檔案、系統狀態/裸機|
|伺服器 (32 位元和 64 位元)|Windows Server 2008 R2 SP1 - Standard 和 Enterprise|Azure 虛擬機器 (當工作負載當做 Azure 虛擬機器執行時)<br /> <br /> Azure Stack|V3， V2<br />您必須執行 SP1 並安裝 [Windows Management Frame 4.0](https://www.microsoft.com/download/details.aspx?id=40855)|磁碟區、共用、資料夾、檔案|
|伺服器 (32 位元和 64 位元)|Windows Server 2008 R2 SP1 - Standard 和 Enterprise|VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)<br /> <br /> Azure Stack|V3， V2<br />您必須執行 SP1 並安裝 [Windows Management Frame 4.0](https://www.microsoft.com/download/details.aspx?id=40855)|磁碟區、共用、資料夾、檔案、系統狀態/裸機|
|伺服器 (32 位元和 64 位元)|Windows Server 2008 SP2|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器<br /> <br /> Azure Stack|不支援|磁碟區、共用、資料夾、檔案、系統狀態/裸機|
|伺服器 (32 位元和 64 位元)|Windows Server 2008 SP2|VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)<br /> <br /> Azure Stack|V3， V2|磁碟區、共用、資料夾、檔案、系統狀態/裸機|
|伺服器 (32 位元和 64 位元)|Windows Storage Server 2008|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器<br /> <br /> Azure Stack|V3， V2|磁碟區、共用、資料夾、檔案、系統狀態/裸機|
|SQL Server|SQL Server 2019|實體伺服器 <br /><br /> 內部部署 Hyper-V 虛擬機器 <br /> <br /> Azure 虛擬機器 (當工作負載當做 Azure 虛擬機器執行時) <br /><br /> VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)<br /> <br /> Azure Stack|V3|所有部署案例：資料庫|
|SQL Server|SQL Server 2017|實體伺服器 <br /><br /> 內部部署 Hyper-V 虛擬機器 <br /> <br /> Azure 虛擬機器 (當工作負載當做 Azure 虛擬機器執行時) <br /><br /> VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)<br /> <br /> Azure Stack|V3|所有部署案例：資料庫|
|SQL Server|SQL Server 2016 SP2|實體伺服器 <br /><br /> 內部部署 Hyper-V 虛擬機器 <br /> <br /> Azure 虛擬機器 <br /><br /> VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)<br /> <br /> Azure Stack|V3， V2|所有部署案例：資料庫|
|SQL Server|SQL Server 2016 SP1|實體伺服器 <br /><br /> 內部部署 Hyper-V 虛擬機器 <br /> <br /> Azure 虛擬機器 <br /><br /> VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)<br /> <br /> Azure Stack|V3， V2|所有部署案例：資料庫|
|SQL Server|SQL Server 2016|實體伺服器 <br /><br /> 內部部署 Hyper-V 虛擬機器 <br /> <br /> Azure 虛擬機器 <br /><br /> VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)<br /> <br /> Azure Stack|V3， V2|所有部署案例：資料庫|
|SQL Server|SQL Server 2014|Azure 虛擬機器 (當工作負載當做 Azure 虛擬機器執行時)<br /> <br /> Azure Stack|V3， V2|所有部署案例：資料庫|
|SQL Server|SQL Server 2014|VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)<br /> <br /> Azure Stack|V3， V2|所有部署案例：資料庫|
|SQL Server|SQL Server 2012 SP2|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器<br /> <br /> Azure Stack|V3， V2|所有部署案例：資料庫|
|SQL Server|SQL Server 2012 SP2|Azure 虛擬機器 (當工作負載當做 Azure 虛擬機器執行時)<br /> <br /> Azure Stack|V3， V2|所有部署案例：資料庫|
|SQL Server|SQL Server 2012 SP2|VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)<br /> <br /> Azure Stack|V3， V2|所有部署案例：資料庫|
|SQL Server|SQL Server 2012、SQL Server 2012 SP1|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器<br /> <br /> Azure Stack|V3， V2|所有部署案例：資料庫|
|SQL Server|SQL Server 2012、SQL Server 2012 SP1|Azure 虛擬機器 (當工作負載當做 Azure 虛擬機器執行時)<br /> <br /> Azure Stack|V3， V2|所有部署案例：資料庫|
|SQL Server|SQL Server 2012、SQL Server 2012 SP1|VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)<br /> <br /> Azure Stack|V3， V2|所有部署案例：資料庫|
|SQL Server|SQL Server 2008 R2|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器<br /> <br /> Azure Stack|V3， V2|所有部署案例：資料庫|
|SQL Server|SQL Server 2008 R2|Azure 虛擬機器 (當工作負載當做 Azure 虛擬機器執行時)<br /> <br /> Azure Stack|V3， V2|所有部署案例：資料庫|
|SQL Server|SQL Server 2008 R2|VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)<br /> <br /> Azure Stack|V3， V2|所有部署案例：資料庫|
|SQL Server|SQL Server 2008|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器<br /> <br /> Azure Stack|V3， V2|所有部署案例：資料庫|
|SQL Server|SQL Server 2008|Azure 虛擬機器 (當工作負載當做 Azure 虛擬機器執行時)<br /> <br /> Azure Stack|V3， V2|所有部署案例：資料庫|
|SQL Server|SQL Server 2008|VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)<br /> <br /> Azure Stack|V3， V2|所有部署案例：資料庫|
|Exchange|Exchange 2016|實體伺服器<br/><br/> 內部部署 Hyper-V 虛擬機器<br /> <br /> Azure Stack<br /> <br />Azure 虛擬機器 (當工作負載當做 Azure 虛擬機器執行時)|V3， V2|保護 (所有部署案例)：獨立 Exchange 伺服器、資料庫可用性群組 (DAG) 下的資料庫<br /><br />復原 (所有部署案例)：信箱、DAG 下的信箱資料庫<br/><br/> 不支援透過 ReFS 備份 Exchange |
|Exchange|Exchange 2016|VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)<br /> <br /> Azure Stack|V3， V2|保護 (所有部署案例)：獨立 Exchange 伺服器、資料庫可用性群組 (DAG) 下的資料庫<br /><br />復原 (所有部署案例)：信箱、DAG 下的信箱資料庫<br/><br/> 不支援透過 ReFS 備份 Exchange |
|Exchange|Exchange 2013|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器<br /> <br /> Azure Stack|V3， V2|保護 (所有部署案例)：獨立 Exchange 伺服器、資料庫可用性群組 (DAG) 下的資料庫<br /><br />復原 (所有部署案例)：信箱、DAG 下的信箱資料庫<br/><br/> 不支援透過 ReFS 備份 Exchange |
|Exchange|Exchange 2013|VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)<br /> <br /> Azure Stack|V3， V2|保護 (所有部署案例)：獨立 Exchange 伺服器、資料庫可用性群組 (DAG) 下的資料庫<br /><br />復原 (所有部署案例)：信箱、DAG 下的信箱資料庫<br/><br/> 不支援透過 ReFS 備份 Exchange |
|Exchange|Exchange 2010|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器<br /> <br /> Azure Stack|V3， V2|保護 (所有部署案例)：獨立 Exchange 伺服器、資料庫可用性群組 (DAG) 下的資料庫<br /><br />復原 (所有部署案例)：信箱、DAG 下的信箱資料庫<br/><br/> 不支援透過 ReFS 備份 Exchange |
|Exchange|Exchange 2010|VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)<br /> <br /> Azure Stack|V3， V2|保護 (所有部署案例)：獨立 Exchange 伺服器、資料庫可用性群組 (DAG) 下的資料庫<br /><br />復原 (所有部署案例)：信箱、DAG 下的信箱資料庫<br/><br/> 不支援透過 ReFS 備份 Exchange |
|SharePoint|SharePoint 2016|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器<br /><br />Azure 虛擬機器 (當工作負載當做 Azure 虛擬機器執行時)<br /><br />VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)<br /> <br /> Azure Stack|V3， V2|保護 (所有部署案例)：伺服器陣列、前端網頁伺服器內容<br /><br />復原 (所有部署案例)：伺服器陣列、資料庫、Web 應用程式、檔案或清單項目、SharePoint 搜尋、前端網頁伺服器<br /><br />請注意，不支援保護針對內容資料庫使用 SQL Server 2012 AlwaysOn 功能的 SharePoint 伺服器陣列。|
|SharePoint|SharePoint 2013|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器<br /> <br /> Azure Stack|V3， V2|保護 (所有部署案例)：伺服器陣列、前端網頁伺服器內容<br /><br />復原 (所有部署案例)：伺服器陣列、資料庫、Web 應用程式、檔案或清單項目、SharePoint 搜尋、前端網頁伺服器<br /><br />請注意，不支援保護針對內容資料庫使用 SQL Server 2012 AlwaysOn 功能的 SharePoint 伺服器陣列。|
|SharePoint|SharePoint 2013|Azure 虛擬機器（當工作負荷作為 Azure 虛擬機器運行時） - <br /> <br /> Azure Stack|V3， V2|保護 (所有部署案例)：伺服器陣列、SharePoint 搜尋、前端網頁伺服器內容<br /><br />復原 (所有部署案例)：伺服器陣列、資料庫、Web 應用程式、檔案或清單項目、SharePoint 搜尋、前端網頁伺服器<br /><br />請注意，不支援保護針對內容資料庫使用 SQL Server 2012 AlwaysOn 功能的 SharePoint 伺服器陣列。|
|SharePoint|SharePoint 2013|VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)<br /> <br /> Azure Stack|V3， V2|保護 (所有部署案例)：伺服器陣列、SharePoint 搜尋、前端網頁伺服器內容<br /><br />復原 (所有部署案例)：伺服器陣列、資料庫、Web 應用程式、檔案或清單項目、SharePoint 搜尋、前端網頁伺服器<br /><br />請注意，不支援保護針對內容資料庫使用 SQL Server 2012 AlwaysOn 功能的 SharePoint 伺服器陣列。|
|SharePoint|SharePoint 2010|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器<br /> <br /> Azure Stack|V3， V2|保護 (所有部署案例)：伺服器陣列、SharePoint 搜尋、前端網頁伺服器內容<br /><br />復原 (所有部署案例)：伺服器陣列、資料庫、Web 應用程式、檔案或清單項目、SharePoint 搜尋、前端網頁伺服器|
|SharePoint|SharePoint 2010|Azure 虛擬機器 (當工作負載當做 Azure 虛擬機器執行時)<br /> <br /> Azure Stack|V3， V2|保護 (所有部署案例)：伺服器陣列、SharePoint 搜尋、前端網頁伺服器內容<br /><br />復原 (所有部署案例)：伺服器陣列、資料庫、Web 應用程式、檔案或清單項目、SharePoint 搜尋、前端網頁伺服器|
|SharePoint|SharePoint 2010|VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)<br /> <br /> Azure Stack|V3， V2|保護 (所有部署案例)：伺服器陣列、SharePoint 搜尋、前端網頁伺服器內容<br /><br />復原 (所有部署案例)：伺服器陣列、資料庫、Web 應用程式、檔案或清單項目、SharePoint 搜尋、前端網頁伺服器|
|Hyper-V 主機 - Hyper-V 主機伺服器、群集或 VM 上的 MABS 保護代理|Windows Server 2019|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器|V3|保護：Hyper-V 電腦、叢集共用磁碟區 (CSV)<br /><br />復原：虛擬機器、檔案和資料夾的項目層級復原、磁碟區、虛擬硬碟|
|Hyper-V 主機 - Hyper-V 主機伺服器、群集或 VM 上的 MABS 保護代理|Windows Server 2016|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器|V3， V2|保護：Hyper-V 電腦、叢集共用磁碟區 (CSV)<br /><br />復原：虛擬機器、檔案和資料夾的項目層級復原、磁碟區、虛擬硬碟|
|Hyper-V 主機 - Hyper-V 主機伺服器、群集或 VM 上的 MABS 保護代理|Windows Server 2012 R2 - Datacenter 和 Standard|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器|V3， V2|保護：Hyper-V 電腦、叢集共用磁碟區 (CSV)<br /><br />復原：虛擬機器、檔案和資料夾的項目層級復原、磁碟區、虛擬硬碟|
|Hyper-V 主機 - Hyper-V 主機伺服器、群集或 VM 上的 MABS 保護代理|Windows Server 2012 - Datacenter 和 Standard|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器|V3， V2|保護：Hyper-V 電腦、叢集共用磁碟區 (CSV)<br /><br />復原：虛擬機器、檔案和資料夾的項目層級復原、磁碟區、虛擬硬碟|
|Hyper-V 主機 - Hyper-V 主機伺服器、群集或 VM 上的 MABS 保護代理|Windows Server 2008 R2 SP1 - Enterprise 和 Standard|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器|V3， V2|保護：Hyper-V 電腦、叢集共用磁碟區 (CSV)<br /><br />復原：虛擬機器、檔案和資料夾的項目層級復原、磁碟區、虛擬硬碟|
|Hyper-V 主機 - Hyper-V 主機伺服器、群集或 VM 上的 MABS 保護代理|Windows Server 2008 SP2|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器|不支援|保護：Hyper-V 電腦、叢集共用磁碟區 (CSV)<br /><br />復原：虛擬機器、檔案和資料夾的項目層級復原、磁碟區、虛擬硬碟|
|VMware VM|VMware vCenter/vSphere ESX/ESXi 許可版本 5.5/6.0/6.5 |實體伺服器、 <br/>內部部署 Hyper-V VM、 <br/> VMWare 中的 Windows VM|V3， V2|叢集共用磁碟區 (CSV)、NFS 和 SAN 儲存體上的 VMware VM<br /> 檔案和資料夾的項目層級復原僅適用於 Windows VM，不支援 VMware vApps。|
|VMware VM|[VMware vSphere 許可版本 6.7](backup-azure-backup-server-vmware.md#vmware-vsphere-67) |實體伺服器、 <br/>內部部署 Hyper-V VM、 <br/> VMWare 中的 Windows VM|V3|叢集共用磁碟區 (CSV)、NFS 和 SAN 儲存體上的 VMware VM<br /> 檔案和資料夾的項目層級復原僅適用於 Windows VM，不支援 VMware vApps。|
|Linux|當做 Hyper-V 或 VMware 客體執行的 Linux|實體伺服器、 <br/>內部部署 Hyper-V VM、 <br/> VMWare 中的 Windows VM|V3， V2|Hyper-V 必須在 Windows Server 2012 R2 或 Windows Server 2016 上執行。 保護：整部虛擬機器<br /><br />復原：整部虛擬機器 <br/><br/> 僅支援檔案一致的快照集。 <br/><br/> 如需支援的 Linux 發行版本和版本完整清單，請參閱[經 Azure 背書的 Linux 發佈](../virtual-machines/linux/endorsed-distros.md)一文。|

## <a name="azure-expressroute-support"></a>Azure 快速路由支援

您可以使用公共對等互連（可用於舊電路）和 Microsoft 對等互連，通過 Azure ExpressRoute 備份資料。 不支援通過私有對等互連進行備份。

使用公共對等互連：確保訪問以下域/位址：

* `http://www.msftncsi.com/ncsi.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`

使用 Microsoft 對等互連，請選擇以下服務/區域和相關社區價值觀：

* Azure 活動目錄 （12076：5060）
* 微軟 Azure 區域（根據恢復服務保存庫的位置）
* Azure 存儲（根據恢復服務保存庫的位置）

有關詳細資訊，請參閱[快速路由路由要求](https://docs.microsoft.com/azure/expressroute/expressroute-routing)。

>[!NOTE]
>公共對等互連被棄用用於新電路。

## <a name="cluster-support"></a>叢集支援

Azure 備份伺服器可以保護下列叢集應用程式中的資料：

* 檔案伺服器

* SQL Server

* Hyper-V - 如果使用橫向擴展的 MABS 保護代理保護 Hyper-V 群集，則無法為受保護的 Hyper-V 工作負載添加次要保護。

    如果您在 Windows Server 2008 R2 上執行 Hyper-V，請務必安裝 KB [975354](https://support.microsoft.com/kb/975354) 中所述的更新。
    如果您在叢集設定的 Windows Server 2008 R2 上執行 Hyper-V，請務必安裝 SP2 和 KB [971394](https://support.microsoft.com/kb/971394)。

* Exchange Server - Azure 備份伺服器可以保護支援之 Exchange Server 版本的非共用磁碟叢集 (叢集連續複寫)，也可以保護設定用於本機連續複寫的 Exchange Server。

* SQL Server - Azure 備份伺服器不支援備份裝載於叢集共用磁碟區 (CSV) 上的 SQL Server 資料庫。

Azure 備份伺服器可以保護與 MABS 伺服器位於同一域以及子域或受信任域中的群集工作負荷。 如果您想要保護未信任網域或工作群組中的資料來源，請針對單一伺服器使用 NTLM 或憑證驗證，或是針對叢集只使用憑證驗證。
