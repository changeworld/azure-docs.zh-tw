---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/04/2019
ms.author: alkohli
ms.openlocfilehash: 94c132421f1e113b667341b094acad8047e5f465
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82561770"
---
1. [連接到 PowerShell 介面](#connect-to-the-powershell-interface)。
2. 使用`Get-HcsApplianceInfo`來取得裝置的資訊。

    下列範例顯示此 Cmdlet 的使用方式：

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

    以下資料表摘要說明一些重要的裝置資訊：
    
    | 參數                             | 描述                                                                                                                                                  |   |
    |--------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
    | FriendlyName                   | 裝置的易記名稱，如裝置部署期間透過本機 web UI 所設定。 預設的易記名稱是裝置序號。  |   |
    | SerialNumber                   | 裝置序號是在工廠指派的唯一編號。                                                                             |   |
    | 型號                          | Azure Stack Edge 或資料箱閘道裝置的模型。 模型是 Azure Stack 邊緣的實體，資料箱閘道的虛擬。                   |   |
    | FriendlySoftwareVersion        | 對應至裝置軟體版本的易記字串。 針對執行預覽的系統，易記的軟體版本會是 Data Box Edge 1902。 |   |
    | HcsVersion                     | 裝置上執行的 HCS 軟體版本。 例如，對應至 Data Box Edge 1902 的 HCS 軟體版本是1.4.771.324。            |   |
    | LocalCapacityInMb              | 裝置的本機容量總計（以 Mb 為單位）。                                                                                                        |   |
    | IsRegistered                   | 這個值會指出您的裝置是否已透過服務啟動。                                                                                         |   |


