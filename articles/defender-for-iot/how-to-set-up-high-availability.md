---
title: 設定高可用性
description: 藉由安裝內部部署管理主控台高可用性設備，來增加 Defender for IoT 部署的復原能力。 高可用性部署可確保您的受控感應器持續向作用中的內部部署管理主控台報告。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 6540b5f93bebfe39253a88dc495a3613568f8926
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2020
ms.locfileid: "97838671"
---
# <a name="about-high-availability"></a>關於高可用性

藉由安裝內部部署管理主控台高可用性設備，來增加 Defender for IoT 部署的復原能力。 高可用性部署可確保您的受控感應器持續向作用中的內部部署管理主控台報告。

此部署會使用包含主要和次要設備的內部部署管理主控台配對來執行。

## <a name="about-primary-and-secondary-communication"></a>關於主要和次要通訊

當主要和次要內部部署管理主控台配對時：

- 使用內部部署管理主控台 SLL 憑證來建立主要和次要設備之間的安全連線。 SLL 可能是預設安裝的自我簽署憑證，或客戶所安裝的憑證。

- 主要的內部部署管理主控台資料會每隔10分鐘自動備份至次要內部部署管理主控台。 系統會備份內部部署管理主控台設定和裝置資料。 PCAP 檔案和記錄檔不會包含在備份中。 您可以手動備份和還原 PCAPs 和記錄。

- 管理主控台上的主要安裝會在次要複本上重複;例如，系統設定。 如果這些設定是在主要複本上更新，它們也會在次要資料庫上更新。

- 在次要的授權到期之前，您應該先將它定義為主要，以更新授權。

## <a name="about-failover-and-failback"></a>關於容錯移轉和容錯回復

如果感應器無法連線到主要的內部部署管理主控台，則會自動連接到次要。 如果有一半以上的感應器與次要資料庫進行通訊，主要和次要資料庫將同時支援您的系統。 當有一半以上的感應器與其通訊時，次要複本就會接管。 從主要容錯移轉至次要資料庫大約需要三分鐘。 當容錯移轉發生時，主要的內部部署管理主控台會凍結。 發生這種情況時，您可以使用相同的登入認證登入次要。

在容錯移轉期間，感應器會繼續嘗試與主要設備通訊。 如果有一半以上的受控感應器可以與主要複本進行通訊，就會還原主資料庫。 還原主資料庫時，會在次要主控台出現下列訊息。

:::image type="content" source="media/how-to-set-up-high-availability/secondary-console-message.png" alt-text="還原主資料庫時，出現在次要主控台的訊息螢幕擷取畫面。":::

重新導向之後，重新登入主要設備。

## <a name="high-availability-setup-overview"></a>高可用性設定總覽

安裝和設定程式會以四個主要階段執行：

1. 安裝內部部署管理主控台的主要設備。 

2. 設定內部部署管理主控台的主要設備。 例如，已排程的備份設定、VLAN 設定。 如需詳細資訊，請參閱內部部署管理主控台使用者指南。 所有設定都會在配對之後自動套用至次要設備。

3. 安裝內部部署管理主控台次要設備。 如需詳細資訊，請參閱 [關於適用于 IoT 的 Defender 安裝](how-to-install-software.md)。

4. 將主要和次要內部部署管理主控台設備配對[，如下所述。](/create-the-primary-and-secondary-pair.md) 主要的內部部署管理主控台必須至少管理兩個感應器，才能執行安裝程式。

## <a name="high-availability-requirements"></a>高可用性需求

確認您符合下列高可用性需求：

- 憑證需求

- 軟體和硬體需求

- 網路存取需求

### <a name="software-and-hardware-requirements"></a>軟體和硬體需求

- 主要和次要內部部署管理主控台設備都必須執行相同的硬體型號和軟體版本。

- 您只能使用 CLI 工具，透過 Defender 為 IoT 使用者設定高可用性系統。

### <a name="network-access-requirements"></a>網路存取需求

您必須確認您的組織安全性原則允許您存取主要和次要內部部署管理主控台上的下列服務。 這些服務也允許感應器和次要內部部署管理主控台之間的連接：

|連接埠|服務|描述|
|----|-------|-----------|
|**443或 TCP**|HTTPS|授與對內部部署管理主控台 web 主控台的存取權。|
|**22或 TCP**|SSH|在主要和次要內部部署管理主控台設備之間同步處理資料|
|**123或 UDP**|NTP| 內部部署管理主控台的 NTP 時間同步。確認主動與被動設備已定義為使用相同的時區。|

## <a name="create-the-primary-and-secondary-pair"></a>建立主要和次要配對

請確認主要和次要內部部署管理主控台設備已開啟電源，再開始程式。  

### <a name="on-the-primary"></a>在主要

1. 以 Defender for IoT 使用者的身份登入 CLI。

2. 在主要複本上執行下列命令：

```azurecli-interactive
sudo cyberx-management-trusted-hosts-add -ip <Secondary IP>
```

>[!NOTE]
>在本檔中，主要的內部部署管理主控台稱為「主要」，而代理程式稱為「次要」。

3. 在欄位中輸入次要設備的 IP 位址 ```<Secondary ip>``` ，然後選取 [輸入]。 接著會驗證 IP 位址，並將 SSL 憑證下載至主要複本。 輸入 IP 位址也會使感應器與次要設備產生關聯。

4. 在主要複本上執行下列命令，以確認已正確安裝憑證：

```azurecli-interactive
sudo cyberx-management-trusted-hosts-apply
```

5. 在主要複本上執行下列命令。 **請勿使用 sudo 執行。**

```azurecli-interactive
cyberx-management-deploy-ssh-key <Secondary IP>
```

這可讓主要和次要設備之間的連線進行備份和還原。

6. 輸入次要的 IP 位址，然後選取 [輸入]。

### <a name="on-the-secondary"></a>次要

1. 以 Defender for IoT 使用者的身份登入 CLI。

2. 在次要資料庫上執行下列命令。 **請勿以 sudo** 執行：

```azurecli-interactive
cyberx-management-deploy-ssh-key <Primary ip>
```

這可讓主要和次要設備之間的連線進行備份和還原。

3. 輸入主要複本的 IP 位址，然後按 Enter。

### <a name="track-high-availability-activity"></a>追蹤高可用性活動

您可以將核心應用程式記錄匯出至 Defender for IoT 支援小組，以處理任何高可用性問題。  

若要存取核心記錄：

1. 從 [**系統設定**] 視窗中選取 [**匯出**]。

## <a name="update-the-on-premises-management-console-with-high-availability"></a>更新具有高可用性的內部部署管理主控台

依下列循序執行高可用性更新。 開始新的步驟之前，請確定每個步驟都已完成。

若要以高可用性進行更新：

1. 更新主要的內部部署管理主控台。

2. 更新次要的內部部署管理主控台。

3. 更新感應器。

## <a name="see-also"></a>請參閱

[啟用並設定您的內部部署管理主控台](how-to-activate-and-set-up-your-on-premises-management-console.md)