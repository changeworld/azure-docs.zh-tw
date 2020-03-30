---
title: 存儲 FSLogix 設定檔容器 Windows 虛擬桌面 - Azure
description: 用於在 Azure 存儲上存儲 Windows 虛擬桌面 FSLogix 設定檔的選項。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 71ba24784dee7771acbe19bf0261c7dc02478b24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127528"
---
# <a name="storage-options-for-fslogix-profile-containers-in-windows-virtual-desktop"></a>Windows 虛擬桌面中 FSLogix 設定檔容器的存儲選項

Azure 提供了多個存儲解決方案，可用於存儲 FSLogix 設定檔容器。 本文比較 Azure 為 Windows 虛擬桌面 FSLogix 使用者設定檔容器提供的存儲解決方案。

Windows 虛擬桌面提供 FSLogix 設定檔容器作為推薦的使用者設定檔解決方案。 FSLogix 設計用於在遠端計算環境中（如 Windows 虛擬桌面）中漫遊設定檔。 登錄時，此容器使用本機支援的虛擬硬碟 （VHD） 和超 V 虛擬硬碟 （VHDX） 動態連接到計算環境。 使用者設定檔立即可用，並且與本機使用者設定檔完全一樣顯示在系統中。

下表比較了 Azure 存儲為 Windows 虛擬桌面 FSLogix 設定檔容器使用者設定檔提供的存儲解決方案。

## <a name="azure-platform-details"></a>Azure 平臺詳細資訊

|特性|Azure 檔案|Azure NetApp Files|儲存空間直接存取|
|--------|-----------|------------------|---------------------|
|使用案例|一般用途|超高性能或從 NetApp 本地遷移|跨平台|
|平臺服務|是，Azure 本機解決方案|是，Azure 本機解決方案|不，自我管理|
|區域可用性|所有區域|[選擇區域](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)|所有區域|
|備援性|本地冗余/區域冗余/異地冗余|本地冗余|本地冗余/區域冗余/異地冗余|
|層和性能|標準<br>Premium<br>每股最多 100k IOPS，每股 5 GBps，延遲約 3 毫秒|標準<br>Premium<br>超<br>高達 320k （16K） IOPS，每卷 4.5 GBps，延遲約 1 毫秒|標準硬碟：每個磁片最多 500 個 IOPS 限制<br>標準 SSD：每個磁片最多 4k IOPS 限制<br>高級 SSD：每個磁片最多 20k IOPS 限制<br>我們建議存儲空間直接的高級磁片|
|Capacity|每股 100 TiB|每卷 100 TiB，每個訂閱高達 12.5 PiB|每個磁片最多 32 TiB|
|所需的基礎設施|最小份額大小 1 GiB|最小容量池 4 TiB，最小體積大小 100 GiB|Azure IaaS 上的兩個 VM（+ 雲見證器）或至少三個 VM，沒有磁片的成本|
|通訊協定|SMB 2.1/3。 和 REST|NFSv3， NFSv4.1 （預覽）， SMB 3.x/2.x|NFSv3， NFSv4.1， SMB 3.1|

## <a name="azure-management-details"></a>Azure 管理詳細資訊

|特性|Azure 檔案|Azure NetApp Files|儲存空間直接存取|
|--------|-----------|------------------|---------------------|
|存取|雲、本地和混合（Azure 檔同步）|雲，本地（通過快速路由）|雲，本地|
|Backup |Azure 備份快照集成|Azure NetApp 檔快照|Azure 備份快照集成|
|安全性與合規性|[所有 Azure 支援的證書](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|ISO 已完成|[所有 Azure 支援的證書](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|
|Azure Active Directory 整合|[本機活動目錄和 Azure 活動目錄域服務](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-overview)|[Azure 活動目錄域服務和本機活動目錄](../azure-netapp-files/azure-netapp-files-faqs.md#does-azure-netapp-files-support-azure-active-directory)|僅支援本機活動目錄或 Azure 活動目錄域服務|

選擇存儲方法後，請查看[Windows 虛擬桌面定價](https://azure.microsoft.com/pricing/details/virtual-desktop/)，瞭解有關我們的定價計畫的資訊。

## <a name="next-steps"></a>後續步驟

要瞭解有關 FSLogix 設定檔容器、使用者設定檔磁片和其他使用者設定檔技術的更多內容，請參閱[FSLogix 設定檔容器和 Azure 檔中](fslogix-containers-azure-files.md)的表。

如果您已準備好創建自己的 FSLogix 設定檔容器，請開始學習以下教程之一：

- [在 Windows 虛擬桌面中的 Azure 檔上使用 FSLogix 設定檔容器入門](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-FSLogix-profile-containers-on-Azure-Files/ba-p/746477)
- [使用 Azure NetApp 檔為主機池創建 FSLogix 設定檔容器](create-fslogix-profile-container.md)
- 在 Azure[中部署用於 UPD 存儲的雙節點存儲空間直接橫向橫向擴展檔案伺服器](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/)中的說明也適用于使用 FSLogix 設定檔容器而不是使用者設定檔磁片時的說明

您也可以從一開始就在[Windows 虛擬桌面中創建租戶](tenant-setup-azure-active-directory.md)中設置自己的 Windows 虛擬桌面解決方案。
