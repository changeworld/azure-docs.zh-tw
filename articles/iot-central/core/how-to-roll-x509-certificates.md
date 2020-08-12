---
title: 在 Azure IoT Central 中輪替 x.509 憑證
description: 如何使用 IoT Central 應用程式來輪替 x.509 憑證
author: v-krghan
ms.author: v-krghan
ms.date: 07/31/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 76e2b9542d20b5788a2875dec89d447ce38276a0
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121997"
---
# <a name="how-to-roll-x509-device-certificates-in-iot-central-application"></a>如何在 IoT Central 應用程式中輪替 x.509 裝置憑證

在 IoT 解決方案的生命週期內，您必須輪替憑證。 輪替憑證的兩大主因是安全性缺口和憑證到期。 

輪替憑證是一種最佳安全性做法，可在外洩事件中協助保護您的系統。 在[假想缺口方法](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)中，Microsoft 主張備妥回應式安全程序和預防性措施的需求。 輪替裝置憑證應納入這些安全程序中。 您輪替憑證的頻率將取決於您解決方案的安全性需求。 有解決方案涉及高度敏感性資料的客戶可以每天輪替，然而其他客戶則是每隔幾年輪替其憑證。


## <a name="obtain-new-x509-certificates"></a>取得新的 x.509 憑證

您可以使用 OpenSSL 之類的工具來建立自己的 x.509 憑證。 這種方法適合用來測試 X.509 憑證，但是提供很少的安全性保證。 除非您已準備好作為您自己的 CA 提供者，否則請只使用此方法進行測試。


## <a name="enrollment-groups-and-security-breaches"></a>註冊群組和安全性缺口

若要更新群組註冊以回應安全性缺口，您應該使用下列會立即更新目前憑證的方法：

1. 流覽至左窗格中的 [系統**管理**]，然後按一下 [**裝置**連線]。

2. 按一下 [註冊群組]****，然後按一下清單中的群組名稱。

    ![裝置連線](./media/how-to-roll-x509-certificates/device-connection.png)


3. 針對 [憑證更新]，按一下 [**管理主要**] 或 [**管理次要**]。

    ![管理憑證](./media/how-to-roll-x509-certificates/certificates.png)


4. 新增並驗證註冊群組中的根 x.509 憑證。

   針對主要和次要憑證完成這些步驟（如果兩者都遭到入侵）。



## <a name="enrollment-groups-and-certificate-expiration"></a>註冊群組與憑證到期

如果您要輪替憑證來處理憑證到期，請使用下列方法立即更新目前的憑證：

1. 流覽至左窗格中的 [系統**管理**]，然後按一下 [**裝置**連線]。 

2. 按一下 [註冊群組]****，然後按一下清單中的群組名稱。

    ![裝置連線](./media/how-to-roll-x509-certificates/device-connection.png)


3. 針對 [憑證更新]，按一下 [**管理主要**]。

    ![裝置連線](./media/how-to-roll-x509-certificates/manage-certs.png)

4. 新增並驗證註冊群組中的根 x.509 憑證。

5. 稍後當次要憑證已過期時，返回並更新該次要憑證。



## <a name="individual-enrollments-and-security-breaches"></a>個別註冊和安全性缺口

如果您要輪替憑證來回應安全性缺口，請使用下列方法立即更新目前的憑證：


1. 按一下 [**裝置**]，然後選取裝置。 

2. 按一下 **[連線]**，然後選取 [連線方法] 做為**個別註冊**

3. 選取 [**憑證] (x.509) **作為 [機制]。

    ![管理個別註冊](./media/how-to-roll-x509-certificates/certificate-update.png)

4. 針對 [憑證更新]，按一下資料夾圖示，以選取要針對註冊專案上傳的新憑證。 按一下 [儲存]。

    針對主要和次要憑證完成這些步驟（如果兩者都遭到入侵）



## <a name="individual-enrollments-and-certificate-expiration"></a>個別註冊和憑證到期

如果您是以輪替憑證的方式來處理憑證過期，則應使用如下所示的次要憑證組態，使嘗試佈建的裝置減少停機時間。

稍後當次要憑證也快要到期，而且需要輪替時，您可以輪換成使用主要組態。 如此一來，輪流使用主要與次要憑證可使嘗試佈建的裝置減少停機時間。

1. 按一下 [**裝置**]，然後選取裝置。

2. 按一下 **[連線]**，然後選取 [連線方法] 做為**個別註冊**

3. 選取 [**憑證] (x.509) **作為 [機制]。

    ![管理個別註冊](./media/how-to-roll-x509-certificates/certificate-update.png)

4. 針對 [次要憑證更新]，按一下資料夾圖示，以選取要針對註冊專案上傳的新憑證。 按一下 [儲存]。


5. 稍後當主要憑證到期時，返回並更新該主要憑證。


## <a name="next-steps"></a>後續步驟

既然您已瞭解如何在 Azure IoT Central 應用程式中輪替 x.509 憑證，您就可以[連線到 azure IoT Central](concepts-get-connected.md)。


