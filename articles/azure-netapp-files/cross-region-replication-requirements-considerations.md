---
title: 使用 Azure NetApp Files 磁片區跨區域複寫的需求和考慮 |Microsoft Docs
description: 說明使用 Azure NetApp Files 的磁片區跨區域複寫功能的需求和考慮。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: f2a50872fdb71419a0c3f068712ec67523a098e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708579"
---
# <a name="requirements-and-considerations-for-using-cross-region-replication"></a>使用跨區域複寫的需求和考慮 

請注意下列有關使用 Azure NetApp Files 的 [磁片區跨區域](cross-region-replication-create-peering.md) 複寫功能的需求和考慮：  

## <a name="requirements-and-considerations"></a>需求和考量 

* 跨區域複寫功能目前處於公開預覽狀態。 您必須透過 [Azure NetApp Files 跨區域複寫等候清單提交頁面](https://aka.ms/anfcrrpreviewsignup)提交等候清單要求，以存取此功能。 使用跨區域複寫功能之前，請先從 Azure NetApp Files 團隊等候官方確認電子郵件。
* Azure NetApp Files 複寫僅適用于特定固定區域配對。 請參閱 [支援的區域配對](cross-region-replication-introduction.md#supported-region-pairs)。 
* SMB 磁片區支援和 NFS 磁片區。 SMB 磁片區的複寫需要來源和目的地 NetApp 帳戶的 Active Directory 連接。 目的地 AD 連線必須具有 DNS 伺服器的存取權，或新增可從目的地區域中委派子網連線的網域控制站。 如需詳細資訊，請參閱 [Active Directory 連接的需求](azure-netapp-files-create-volumes-smb.md#requirements-for-active-directory-connections)。 
* 目的地帳戶必須與來源磁片區區域位於不同的區域。 您也可以在不同的區域中選取現有的 NetApp 帳戶。  
* Azure NetApp Files 複寫目前不支援多個訂用帳戶;所有的複寫都必須在單一訂用帳戶下執行。
* 您最多可以在每個區域的單一訂用帳戶中，設定最多五個磁片區進行複寫。 您可以開啟支援票證，以要求 (每個訂用帳戶中每個訂用帳戶的預設配額增加預設配額) 。 
* 介面可能會有最多五分鐘的延遲，以反映來源磁片區上新加入的快照集。  
* 不支援串聯和扇入/傳出拓撲。
* 目前不支援為從快照集建立的來源磁片區設定磁片區複寫。
* 設定跨區域複寫之後，複寫程式會建立 *snapmirror 的快照* 集，以提供來源磁片區和目的地磁片區之間的參考。 當針對每個增量傳輸建立新的快照集時，系統會自動迴圈 Snapmirror 快照集。 在刪除複寫關聯性和磁片區之前，您無法刪除 snapmirror 的快照集。 
* 當複寫關聯性為作用中或已中斷，以及刪除複寫關聯性之後，您可以在複寫關聯性的來源磁片區上刪除手動快照集。 在複寫關聯性中斷之前，您無法刪除目的地磁片區的手動快照。

## <a name="next-steps"></a>後續步驟
* [建立複寫對等互連](cross-region-replication-create-peering.md)
* [顯示複寫關聯性的健全狀態](cross-region-replication-display-health-status.md)
* [管理災害復原](cross-region-replication-manage-disaster-recovery.md)
* [磁片區複寫計量](azure-netapp-files-metrics.md#replication)
* [針對跨區域複寫進行疑難排解](troubleshoot-cross-region-replication.md)


