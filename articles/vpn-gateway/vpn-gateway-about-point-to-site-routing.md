---
title: Azure ポイント対サイト ルーティングについて | Microsoft Docs
description: この記事は、ポイント対サイト VPN ルーティングの動作を理解するのに役立ちます。
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/16/2018
ms.author: anzaman
ms.openlocfilehash: d25709fb4abb1b8a35596c3dc246f7419a99419b
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2018
---
# <a name="about-point-to-site-vpn-routing"></a>ポイント対サイト VPN ルーティングについて

この記事は、Azure ポイント対サイト VPN ルーティングの動作を理解するのに役立ちます。 P2S VPN ルーティングの動作は、クライアント OS、VPN 接続に使用されるプロトコル、および仮想ネットワーク (VNet) が相互に接続する方法によって異なります。

現在、Azure では、リモート アクセスに対して IKEv2 と SSTP の 2 つのプロトコルをサポートしています。 IKEv2 は、Windows、Linux、MacOS、Android、iOS など、多くのクライアント オペレーティング システム上でサポートされます。 SSTP は、Windows 上でのみサポートされます。 Windows VPN クライアントがある状態でネットワークのトポロジに変更を加えた場合は、変更をクライアントに適用するために、Windows クライアント用の VPN クライアント パッケージをダウンロードしてもう一度インストールする必要があります。

> [!NOTE]
> この記事は、IKEv2 に対してのみ適用されます。
>

## <a name="diagrams"></a>図について

この記事にはいくつかの異なる図があります。 各セクションでは、異なるトポロジや構成を示します。 この記事の目的により、サイト間 (S2S) 接続と VNet 間接続はどちらも IPsec トンネルであるため、同じように機能します。 この記事のすべての VPN ゲートウェイはルートベースです。

## <a name="isolatedvnet"></a>1 つの分離された VNet

この例のポイント対サイト VPN ゲートウェイ接続は、他の仮想ネットワークに接続もピアリングもされていない VNet (VNet1) に関するものです。 この例では、SSTP または IKEv2 を使用しているクライアントが VNet1 にアクセスできます。

![分離された VNet ルーティング](./media/vpn-gateway-about-point-to-site-routing/1.jpg "分離された VNet ルーティング")

### <a name="address-space"></a>アドレス空間

* VNet1: 10.1.0.0/16

### <a name="routes-added"></a>追加されたルート

* Windows クライアントに追加されたルート: 10.1.0.0/16、192.168.0.0/24

* Windows 以外のクライアントに追加されたルート: 10.1.0.0/16、192.168.0.0/24

### <a name="access"></a>Access

* Windows クライアントは VNet1 にアクセスできます

* Windows 以外のクライアントは VNet1 にアクセスできます

## <a name="multipeered"></a>複数のピアリングされた VNet

この例のポイント対サイト VPN ゲートウェイ接続は VNet1 に関するものです。 VNet1 は VNet2 とピアリングされています。 VNet2 は VNet3 とピアリングされています。 VNet1 は VNet4 とピアリングされています。 VNet1 と VNet3 の間に直接ピアリングはありません。 VNet1 では [ゲートウェイ転送を許可する] が有効で、VNet2 では [Use remote gateways]\(リモート ゲートウェイを使用する\) が有効です。

Windows を使用しているクライアントは直接ピアリングされた VNet にアクセスできますが、VNet ピアリングまたはネットワーク トポロジが変更された場合は VPN クライアントをもう一度ダウンロードする必要があります。 Windows 以外のクライアントは、直接ピアリングされた VNet にアクセスできます。 アクセスは推移的ではなく、直接ピアリングされた VNet のみに限定されます。

![複数のピアリングされた VNet](./media/vpn-gateway-about-point-to-site-routing/2.jpg "複数のピアリングされた VNet")

### <a name="address-space"></a>アドレス空間:

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* VNet4: 10.4.0.0/16

### <a name="routes-added"></a>追加されたルート

* Windows クライアントに追加されたルート: 10.1.0.0/16、10.2.0.0/16、10.4.0.0/16、192.168.0.0/24

* Windows 以外のクライアントに追加されたルート: 10.1.0.0/16、10.2.0.0/16、10.4.0.0/16、192.168.0.0/24

### <a name="access"></a>Access

* Windows クライアントは VNet1、VNet2、VNet4 にアクセスできますが、トポロジの変更を反映するには VPN クライアントをもう一度ダウンロードする必要があります。

* Windows 以外のクライアントは VNet1、VNet2、VNet4 にアクセスできます

## <a name="multis2s"></a>S2S VPN を使用して接続されている複数の VNet

この例のポイント対サイト VPN ゲートウェイ接続は VNet1 に関するものです。 VNet1 は、サイト間 VPN 接続を使用して VNet2 に接続されています。 VNet2 は、サイト間 VPN 接続を使用して VNet3 に接続されています。 VNet1 と VNet3 の間に直接ピアリングやサイト間 VPN 接続はありません。 どのサイト間接続でも、ルーティングのための BGP は実行されていません。

Windows、またはサポートされる別の OS を使用しているクライアントは、VNet1 にのみアクセスできます。 その他の VNet にアクセスするには、BGP を使用する必要があります。

![複数の VNet と S2S](./media/vpn-gateway-about-point-to-site-routing/3.jpg "複数の VNet と S2S")

### <a name="address-space"></a>アドレス空間

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

### <a name="routes-added"></a>追加されたルート

* Windows クライアントに追加されたルート: 10.1.0.0/16、192.168.0.0/24

* Windows 以外のクライアントに追加されたルート: 10.1.0.0/16、10.2.0.0/16、192.168.0.0/24

### <a name="access"></a>Access

* Windows クライアントは VNet1 にのみアクセスできます

* Windows 以外のクライアントは VNet1 にのみアクセスできます

## <a name="multis2sbgp"></a>S2S VPN (BGP) を使用して接続されている複数の VNet

この例のポイント対サイト VPN ゲートウェイ接続は VNet1 に関するものです。 VNet1 は、サイト間 VPN 接続を使用して VNet2 に接続されています。 VNet2 は、サイト間 VPN 接続を使用して VNet3 に接続されています。 VNet1 と VNet3 の間に直接ピアリングやサイト間 VPN 接続はありません。 すべてのサイト間接続で、ルーティングのための BGP が実行されています。

Windows、またはサポートされる別の OS を使用しているクライアントは、サイト間 VPN を使用して接続されているすべての VNet にアクセスできますが、接続されている VNet へのルートを Windows クライアントに手動で追加する必要があります。

![複数の VNet と S2S (BGP)](./media/vpn-gateway-about-point-to-site-routing/4.jpg "複数の VNet と S2S (BGP)")

### <a name="address-space"></a>アドレス空間

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

### <a name="routes-added"></a>追加されたルート

* Windows クライアントに追加されたルート: 10.1.0.0/16

* Windows 以外のクライアントに追加されたルート: 10.1.0.0/16、10.2.0.0/16、10.3.0.0/16、192.168.0.0/24

### <a name="access"></a>Access

* Windows クライアントは VNet1、VNet2、VNet3 にアクセスできますが、VNet2 と VNet3 へのルートを手動で追加する必要があります。

* Windows 以外のクライアントは VNet1、VNet2、VNet3 にアクセスできます

## <a name="vnetbranch"></a>1 つの VNet とブランチ オフィス

この例のポイント対サイト VPN ゲートウェイ接続は VNet1 に関するものです。 VNet1 は他のどの仮想ネットワークとも接続またはピアリングされていませんが、BGP を実行していないサイト間 VPN 接続を通じてオンプレミスのサイトに接続されています。

Windows クライアントは VNet1 とブランチ オフィス (Site1) にアクセスできますが、Site1 へのルートをクライアントに手動で追加する必要があります。 Windows 以外のクライアントは、VNet1 とオンプレミスの Site1 にアクセスできます。

![VNet とブランチ オフィスでのルーティング](./media/vpn-gateway-about-point-to-site-routing/5.jpg "VNet とブランチ オフィスでのルーティング")

### <a name="address-space"></a>アドレス空間

* VNet1: 10.1.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>追加されたルート

* Windows クライアントに追加されたルート: 10.1.0.0/16、192.168.0.0/24

* Windows 以外のクライアントに追加されたルート: 10.1.0.0/16、10.101.0.0/16、192.168.0.0/24

### <a name="access"></a>Access

* Windows クライアントは VNet1 にのみアクセスできます

* Windows 以外のクライアントは VNet1 にのみアクセスできます

## <a name="vnetbranchbgp"></a>1 つの VNet とブランチ オフィス (BGP)

この例のポイント対サイト VPN ゲートウェイ接続は VNet1 に関するものです。 VNet1 は他のどの仮想ネットワークとも接続またはピアリングされていませんが、BGP を実行しているサイト間 VPN 接続を通じてオンプレミスのサイト (Site1) に接続されています。

Windows クライアントは VNet とブランチ オフィス (Site1) にアクセスできますが、Site1 へのルートをクライアントに手動で追加する必要があります。 Windows 以外のクライアントは、VNet とオンプレミスのブランチ オフィスにアクセスできます。

![1 つの VNet とブランチ オフィス (BGP)](./media/vpn-gateway-about-point-to-site-routing/6.jpg "1 つの VNet とブランチ オフィス")

### <a name="address-space"></a>アドレス空間

* VNet1: 10.1.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>追加されたルート

* Windows クライアントに追加されたルート: 10.1.0.0/16、192.168.0.0/24

* Windows 以外のクライアントに追加されたルート: 10.1.0.0/16、10.101.0.0/16、192.168.0.0/24

### <a name="access"></a>Access

* Windows クライアントは VNet1 と Site1 にアクセスできますが、Site1 へのルートを手動で追加する必要があります。

* Windows 以外のクライアントは VNet1 と Site1 にアクセスできます。


## <a name="multivnets2sbranch"></a>S2S を使用して接続された複数の VNet と 1 つのブランチ オフィス

この例のポイント対サイト VPN ゲートウェイ接続は VNet1 に関するものです。 VNet1 は、サイト間 VPN 接続を使用して VNet2 に接続されています。 VNet2 は、サイト間 VPN 接続を使用して VNet3 に接続されています。 VNet1 と VNet3 のネットワーク間に直接ピアリングやサイト間 VPN トンネルはありません。 VNet3 は、サイト間 VPN 接続を使用してブランチ オフィス (Site1) に接続されています。 どの VPN 接続でも BGP は実行されていません。

すべてのクライアントが VNet1 にのみアクセスできます。

![複数 VNet S2S とブランチ オフィス](./media/vpn-gateway-about-point-to-site-routing/7.jpg "複数 VNet S2S とブランチ オフィス")

### <a name="address-space"></a>アドレス空間

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>追加されたルート

* クライアントに追加されたルート: 10.1.0.0/16、192.168.0.0/24

* Windows 以外のクライアントに追加されたルート: 10.1.0.0/16、10.2.0.0/16、10.3.0.0/16、10.101.0.0/16、192.168.0.0/24

### <a name="access"></a>Access

* Windows クライアントは VNet1 にのみアクセスできます

* Windows 以外のクライアントは VNet1 にのみアクセスできます

## <a name="multivnets2sbranchbgp"></a>S2S を使用して接続された複数の VNet と 1 つのブランチ オフィス (BGP)

この例のポイント対サイト VPN ゲートウェイ接続は VNet1 に関するものです。 VNet1 は、サイト間 VPN 接続を使用して VNet2 に接続されています。 VNet2 は、サイト間 VPN 接続を使用して VNet3 に接続されています。 VNet1 と VNet3 のネットワーク間に直接ピアリングやサイト間 VPN トンネルはありません。 VNet3 は、サイト間 VPN 接続を使用してブランチ オフィス (Site1) に接続されています。 どの VPN 接続でも BGP は実行されていません。 すべての VPN 接続で BGP が実行されています。

Windows を使用しているクライアントは、サイト間 VPN 接続を使用して接続されている VNet とサイトにアクセスできますが、VNet2、VNet3、Site1 へのルートをクライアントに手動で追加する必要があります。 Windows 以外のクライアントは、手動で介入しなくても、サイト間 VPN 接続を使用して接続されている VNet とサイトにアクセスできます。 アクセスは推移的であり、クライアントは、接続されているすべての VNet とサイト (オンプレミス) のリソースにアクセスできます。

![複数 VNet S2S とブランチ オフィス](./media/vpn-gateway-about-point-to-site-routing/8.jpg "複数 VNet S2S とブランチ オフィス")

### <a name="address-space"></a>アドレス空間

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>追加されたルート

* クライアントに追加されたルート: 10.1.0.0/16、192.168.0.0/24

* Windows 以外のクライアントに追加されたルート: 10.1.0.0/16、10.2.0.0/16、10.3.0.0/16、10.101.0.0/16、192.168.0.0/24

### <a name="access"></a>Access

* Windows クライアントは VNet1、VNet2、VNet3、Site1 にアクセスできますが、VNet2、VNet3、Site1 へのルートをクライアントに手動で追加する必要があります。

* Windows 以外のクライアントは VNet1、VNet2、VNet3、Site1 にアクセスできます。

## <a name="next-steps"></a>次の手順

[Azure Portal を使用した P2S VPN の作成](vpn-gateway-howto-point-to-site-resource-manager-portal.md)に関する記事を参照して P2S VPN の作成を開始します。
