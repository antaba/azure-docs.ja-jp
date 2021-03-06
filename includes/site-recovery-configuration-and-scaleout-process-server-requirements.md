---
title: インクルード ファイル
description: インクルード ファイル
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 05/03/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 2464fa41deaa1e9c43e5f53e1a900ca11b582040
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2018
ms.locfileid: "33901297"
---
**構成/プロセス サーバー要件**

**コンポーネント** | **要件** 
--- | ---
**ハードウェア** | 
**CPU コア** | 8 
**RAM** | 16 GB
**ディスクの数** | 3、OS ディスク、プロセス サーバーのキャッシュ ディスク、フェールバック用リテンション ドライブを含みます 
**ディスクの空き領域 (プロセス サーバー キャッシュ)** | 600 GB
**ディスクの空き領域 (リテンション ディスク)** | 600 GB
**ソフトウェア** | 
**オペレーティング システム** | Windows Server 2012 R2 <br> Windows Server 2016
**オペレーティング システムのロケール** | 英語 (en-us)
**Windows Server の役割** | これらの役割を有効にしないでください。 <br> - Active Directory Domain Services <br>- インターネット インフォメーション サービス <br> - Hyper-V 
**グループ ポリシー** | これらのグループ ポリシーを有効にしないでください。 <br> - コマンド プロンプトへのアクセス禁止。 <br> - レジストリ編集ツールへのアクセス禁止。 <br> - ファイル添付の信頼ロジック。 <br> - スクリプト実行の有効化。 <br> [詳細情報](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
**IIS** | - 既存の Web サイトが存在しない <br> - ポート 443 でリッスンしている既存の Web サイト/アプリケーションが存在しない <br>- [匿名認証](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx)を有効にする <br> - [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 設定を有効にする 
**ネットワーク** | 
**IP アドレスの種類** | 静的 
**インターネットへのアクセス** | サーバーは、次の URL にアクセスする必要があります (直接またはプロキシ経由) <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi (構成サーバーを設定する場合) <br> - time.nist.gov <br> - time.windows.com 
**ポート** | 443 (コントロール チャネルのオーケストレーション)<br>9443 (データ転送) 
**VMWare (構成/プロセス サーバーを VMware VM として設定する場合)**
**NIC の種類** | VMXNET3  
**VM で実行されている VMware vSphere PowerCLI* | [PowerCLI バージョン 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")

**構成/プロセス サーバーのサイズ要件**

**CPU** | **メモリ** | **キャッシュ ディスク** | **データの変更率** | **レプリケートされたマシン**
--- | --- | --- | --- | ---
8 vCPU<br/><br/> 2 ソケット * 4 コア @ 2.5 GHz | 16GB | 300 GB | 500 GB 以下 | 100 台未満のマシン
12 vCPU<br/><br/> 2 ソケット * 6 コア @ 2.5 GHz | 18 GB | 600 GB | 500 GB ～ 1 TB | 100 ～ 150 台のマシン
16 vCPU<br/><br/> 2 ソケット * 8 コア @ 2.5 GHz | 32 GB | 1 TB (テラバイト) | 1 ～ 2 TB | 150 ～ 200 台のマシン

