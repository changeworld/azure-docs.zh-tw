---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/04/2019
ms.author: alkohli
ms.openlocfilehash: d5af557a62f4bd35c242d334c28a38c3d632f7cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "67174439"
---
1. [連接到 PowerShell 介面](#connect-to-the-powershell-interface)。
2. 使用`Get-HcsApplianceInfo`獲取設備的資訊。

    下面的示例顯示了此 Cmdlet 的用法：

    ```
    [10.100.10.10]: PS>Get-HcsApplianceInfo
    
    Id                            : b2044bdb-56fd-4561-a90b-407b2a67bdfc
    FriendlyName                  : DBE-NBSVFQR94S6
    Name                          : DBE-NBSVFQR94S6
    SerialNumber                  : HCS-NBSVFQR94S6
    DeviceId                      : 40d7288d-cd28-481d-a1ea-87ba9e71ca6b
    Model                         : Virtual
    FriendlySoftwareVersion       : Data Box Gateway 1902
    HcsVersion                    : 1.4.771.324
    IsClustered                   : False
    IsVirtual                     : True
    LocalCapacityInMb             : 1964992
    SystemState                   : Initialized
    SystemStatus                  : Normal
    Type                          : DataBoxGateway
    CloudReadRateBytesPerSec      : 0
    CloudWriteRateBytesPerSec     : 0
    IsInitialPasswordSet          : True
    FriendlySoftwareVersionNumber : 1902
    UploadPolicy                  : All
    DataDiskResiliencySettingName : Simple
    ApplianceTypeFriendlyName     : Data Box Gateway
    IsRegistered                  : False
    ```

    下表總結了一些重要的設備資訊：
    
    | 參數                             | 描述                                                                                                                                                  |   |
    |--------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
    | FriendlyName                   | 設備部署期間通過本地 Web UI 配置的設備易記名稱。 預設的易記名稱是裝置序號。  |   |
    | SerialNumber                   | 設備序號是在工廠分配的唯一編號。                                                                             |   |
    | 模型                          | 資料框邊緣或資料框閘道設備的模型。 該模型對於資料框閘道是虛擬的，對於資料框邊緣是物理的。                   |   |
    | FriendlySoftwareVersion        | 對應至裝置軟體版本的易記字串。 對於運行預覽的系統，友好的軟體版本將是資料框邊緣 1902。 |   |
    | HcsVersion                     | 裝置上執行的 HCS 軟體版本。 例如，與資料框邊緣 1902 對應的 HCS 軟體版本是 1.4.771.324。            |   |
    | 本地容量InMb              | 以百萬位元表示設備的總本地容量。                                                                                                        |   |
    | 已註冊                   | 此值指示您的設備是否已隨服務啟動。                                                                                         |   |


