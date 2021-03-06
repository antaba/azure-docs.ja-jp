---
title: Azure AD ハイブリッド ID ソリューションの適切な認証方法を選択する | Microsoft Docs
description: このガイドは、CEO、CIO、CISO、チーフ ID アーキテクト、エンタープライズ アーキテクト、セキュリティと IT に関する意思決定者など、中規模から大規模な組織で Azure AD ハイブリッド ID ソリューションの認証方法の選択を担当するユーザーを対象として書かれています。
services: active-directory
keywords: ''
author: martincoetzer
ms.author: martincoetzer
ms.date: 04/12/2018
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.openlocfilehash: eb5fb008e602c2026e57d3436875ec485b350af8
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895522"
---
# <a name="choosing-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>Azure Active Directory ハイブリッド ID ソリューションの適切な認証方法の選択 

この記事は、組織への完全な Azure AD ハイブリッド ID ソリューションの実装に関する一連の記事の最初のものです。 完全な Azure AD ハイブリッド ID ソリューションは、ハイブリッド ID デジタル変換フレームワークとして大枠が決められており、堅牢かつ安全なハイブリッド ID ソリューションが実装されたことを確認するために組織が注目する必要のあるビジネスに関する成果と目標が扱われています。 フレームワークの最初のビジネス成果では、ユーザーがクラウド アプリにアクセスするときに組織が認証プロセスをセキュリティで保護するための要件が詳しく説明されています。 認証でセキュリティ保護されたビジネス成果の最初のビジネス目標は、ユーザーがオンプレミスのユーザー名とパスワードを使ってクラウド アプリにサインインできるようにすることです。 このサインイン プロセスとユーザー認証方法により、クラウド内でのすべてのことが可能になります。

正しい認証方法の選択は、クラウドにアプリを移行しようとしている組織にとって最大の関心事です。 次の理由で、この決定を軽く考えてはなりません。

1. クラウドに移行したい組織が最初に決定することです。 

2. 認証方法は、クラウドのすべてのデータとリソースへのアクセスを制御する、クラウドにおける組織のプレゼンスの重要なコンポーネントです。

3. Azure AD での他のすべての高度なセキュリティとユーザー エクスペリエンスの機能の基盤です。

4. いったん実装した認証方法を変更するのは困難です。

IT セキュリティの新しいコントロール プレーンとしての ID では、認証は新しいクラウド環境への組織のアクセス ガードです。 組織は、ID コントロール プレーンがセキュリティを強化し、クラウド アプリを侵入者から保護していることを確認する必要があります。

### <a name="out-of-scope"></a>対象範囲外

オンプレミスに既存のディレクトリ フットプリントを持たない組織は、この記事の対象範囲外です。 通常、このような組織は企業はクラウド内にのみ ID を作成し、ハイブリッド ID ソリューションを必要としません。 クラウド専用の ID はクラウドにのみ存在し、対応するオンプレミスの ID とは関連付けられません。  

## <a name="choosing-the-right-authentication-method"></a>適切な認証方法の選択

新しいコントロール プレーンとしての Azure AD ハイブリッド ID ソリューションでは、認証がクラウド アクセスの基盤です。 正しい認証方法の選択は、Azure AD ハイブリッド ID ソリューションのセットアップにおける最初の重要な決定です。 認証方法の実装は、クラウドでのユーザーのプロビジョニングも行う Azure AD Connect を使って構成されます。 

認証方法を選ぶには、時間、既存のインフラストラクチャ、複雑さ、および選んだ方法の実装にかかるコストを考慮する必要があります。 これらの要因は組織ごとに異なり、時間の経過とともに変化する場合があります。 

Azure AD は、ハイブリッド ID ソリューションに対して次の認証方法をサポートします。

### <a name="cloud-authentication"></a>クラウド認証
この認証方法を選ぶと、Azure AD がユーザーのサインイン プロセスを処理します。 シームレスなシングル サインオン (SSO) と組み合わせることで、ユーザーは資格情報を再入力しなくてもクラウド アプリにサインインできます。 クラウド認証では、2 つのオプションから選ぶことができます。 

**パスワード ハッシュ同期 (PHS)** – Azure AD でオンプレミスのディレクトリ オブジェクトの認証を有効にする最も簡単な方法です。 パスワード ハッシュ同期では、ユーザーはオンプレミスで使用しているものと同じユーザー名とパスワードを使用でき、追加のインフラストラクチャを展開する必要はありません。 Identity Protection など、Azure AD の一部のプレミアム機能には、認証方法の選択に関係なく、パスワード ハッシュ同期が必要です。

> [!NOTE] 
> パスワードがクリア テキストで保存されたり、Azure AD の復元可能なアルゴリズムで暗号化されたりすることはありません。 パスワード ハッシュ同期の実際のプロセスについて詳しくは、「[Azure AD Connect 同期を使用したパスワード ハッシュ同期の実装](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization)」をご覧ください。 

**パススルー認証 (PTA)** – オンプレミスの 1 つまたは複数のサーバーで実行されているソフトウェア エージェントを使って、Azure AD 認証サービスに簡単なパスワード検証を提供します。ユーザーの検証はオンプレミスの Active Directory で直接行われ、クラウドでパスワードの検証が行われることはありません。 オンプレミスのユーザー アカウントの状態、パスワード ポリシー、およびログオン時間をすぐに適用するセキュリティ要件のある企業は、この認証方法を使用します。 パススルー認証の実際のプロセスについて詳しくは、「[Azure Active Directory パススルー認証によるユーザー サインイン](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication)」をご覧ください。

### <a name="federated-authentication"></a>フェデレーション認証
この認証方法を選ぶと、Azure AD は別の信頼された認証システム (オンプレミスの Active Directory フェデレーション サービス (AD FS) など) に、ユーザーのパスワードを検証する認証プロセスを引き継ぎます。 認証システムは、スマートカード ベースの認証やサードパーティの多要素認証など、追加の認証要件を指定できます。 詳しくは、「[Azure での Active Directory フェデレーション サービスのデプロイ](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide)」をご覧ください。

次のセクションのデシジョン ツリーは、組織に適した認証方法を決定するのに役立ちます。 Azure AD ハイブリッド ID ソリューションにクラウド認証とフェデレーション認証のどちらを展開すればよいかを判断できます。

## <a name="azure-ad-authentication-decision-tree"></a>Azure AD での認証のデシジョン ツリー

![Image1](media/azure-ad/azure-ad-authn-image1.png)

## <a name="detailed-considerations-on-authentication-methods"></a>認証方法に関する詳細な考慮事項

### <a name="cloud-authentication-password-hash-sync"></a>クラウド認証: パスワード ハッシュ同期 

* **作業量:** パスワード ハッシュ同期では、ユーザーが Office 365、SaaS アプリ、その他の Azure AD ベースのリソースにサインインできるようにするためにのみ必要な、組織の展開、保守、およびインフラストラクチャに関する最小限の作業が必要です。 有効にすると、パスワード ハッシュ同期は Azure AD Connect 同期プロセスの一部になり、2 分ごとに実行されます。 

* **ユーザー エクスペリエンス:** サインイン後に不要なプロンプトが表示されないようにしてユーザーのサインイン エクスペリエンスを向上させるため、パスワード ハッシュ同期と共にシームレスなシングル サインオン (SSO) を展開することをお勧めします。

* **高度なシナリオ:** 選択すれば、Azure AD Identity Protection のレポート (漏洩した資格情報のレポートなど) で、ID からの分析情報を使用することができます。 Windows Hello for Business は、[パスワード ハッシュ同期を使うときに特定の要件](https://docs.microsoft.com/windows/access-protection/hello-for-business/hello-identity-verification)があるもう 1 つのオプションです。パスワード ハッシュ同期と共に多要素認証を必要とする組織は、Azure AD の多要素認証を使う必要があり、サードパーティまたはオンプレミスの多要素認証方法を使うことはできません。

* **ビジネス継続性:** パスワード ハッシュ同期は、すべての Microsoft データセンターに対応するようにスケーリングするクラウド サービスとして本質的に高可用性です。 ディザスター リカバリーのために、第 2 の Azure AD Connect サーバーをスタンバイ構成のステージング モードで展開することをお勧めします。

* **考慮事項:** 現在、パスワード ハッシュ同期ではオンプレミスのアカウントの状態の変化はすぐには適用されません。 このような状況では、ユーザーは、Azure AD にユーザー アカウントの状態が同期されるまで、クラウド アプリにアクセスできます。 このような制限を回避する必要がある場合は、オンプレミスのユーザー アカウントの状態を一括更新した後 (アカウントの無効化など)、新しい同期サイクルをアクティブにすることをお勧めします。 

> [!NOTE] 
> 現在、パスワードの期限切れとアカウントのロックアウトの状態は、Azure AD と Azure AD Connect では同期されません。 

展開手順については、[パスワード ハッシュ同期の実装](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization)に関する記事をご覧ください。

### <a name="cloud-authentication-pass-through-authentication"></a>クラウド認証: パススルー認証  

* **作業量:** パススルー認証では、オンプレミスの AD ドメイン コントローラーなどオンプレミスの Active Directory Domain Services にアクセスできる既存のサーバーに、1 つまたは複数 (3 つを推奨) の軽量のエージェントをインストールする必要があります。 これらのエージェントには、インターネットへの発信アクセスと、ドメイン コントローラーへのアクセスが必要です。 このため、境界ネットワークにのエージェントを展開することはできません。ドメイン コントローラーへの制約なしのネットワーク アクセスが必要なためです。 すべてのネットワーク トラフィックは暗号化され、認証要求に制限されます。 このプロセスの詳細については、パススルー認証での[セキュリティの詳細](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-security-deep-dive)に関する記事をご覧ください。

* **ユーザー エクスペリエンス:** サインイン後に不要なプロンプトが表示されないようにしてユーザーのサインイン エクスペリエンスを向上させるため、パススルー認証と共にシームレスなシングル サインオンを展開することをお勧めします。

* **高度なシナリオ:** パススルー認証では、サインインの時点でオンプレミスのアカウント ポリシーが適用されます。 たとえば、オンプレミスのユーザーのアカウントの状態が、無効、ロックアウト、[パスワード期限切れ](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq#what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication )、またはユーザーに許可されているログイン時間の超過の場合、アクセスは拒否されます。  パススルー認証と共に多要素認証を必要とする組織は、Azure 多要素認証 (MFA) を使う必要があり、サードパーティまたはオンプレミスの多要素認証方法を使うことはできません。 Identity Protection の漏洩した資格情報レポートなどの高度な機能を使うには、パススルー認証を選択したかどうかに関係なく、パスワード ハッシュ同期を展開する必要があります。

* **ビジネス継続性:** 認証要求の高可用性を確保するため、Azure AD Connect サーバーの最初のエージェントに加えて、さらに 2 つのパススルー認証エージェントを展開することをお勧めします。 3 つのエージェントを展開すると、メンテナンスのために 1 つのエージェントを停止しても、まだ 1 つのエージェントの障害に対応できます。 パススルー認証だけでなくパスワード ハッシュ同期も展開することによるもう 1 つの利点は、オンプレミスのサーバーが利用できないなどで、プライマリ認証方法を使用できない場合に、バックアップの認証方法として機能することです。

* **考慮事項:** パススルー認証のバックアップ認証方法としてパスワード ハッシュ同期を使用していて、エージェントがユーザーの資格情報を検証できない場合、パスワード ハッシュ同期へのフェールオーバーは自動的には行われません。 Azure AD Connect を使用して手動でサインイン方法を切り替える必要があります。 パススルー認証は、先進認証と、ActiveSync、POP3、IMAP4 などの特定の Exchange Online プロトコルを使用しているクラウド アプリのみをサポートします。 たとえば、Office アプリのサポートの詳細情報では、[Microsoft Office 2013 以降は先進認証をサポートしていますが、以前のバージョンはサポートしていません](https://blogs.office.com/en-us/2015/11/19/updated-office-365-modern-authentication-public-preview/)。 代替 ID のサポートなど、パススルー認証での他の考慮事項については、[よく寄せられる質問](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq)をご覧ください。

展開手順については、[パススルー認証の実装](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication)に関する記事をご覧ください。

### <a name="federated-authentication"></a>[フェデレーション認証]

* **作業量:** フェデレーション認証システムを使うときは、外部システムを利用してユーザーを認証します。 フェデレーション システムへの既存の投資を Azure AD ハイブリッド ID ソリューションで再利用したい会社もあります。 フェデレーション システムの管理とメンテナンスは、Azure AD のコントロールの範囲外です。 フェデレーション システムが安全に展開されていて、認証の負荷を処理できるかどうかを確認するのは、フェデレーション システムを使用している組織の責任です。 

* **ユーザー エクスペリエンス:** フェデレーション認証のユーザー エクスペリエンスは、機能、トポロジ、およびフェデレーション ファームの構成の実装に依存します。 組織によっては、セキュリティ要件に合わせてフェデレーション ファームへのアクセスを調整および構成するために、この柔軟性が必要になります。 たとえば、内部的に接続されているユーザーとデバイスを、デバイスに既にサインインしているユーザーに資格情報の入力を要求することなく、ユーザーを自動的にサインインさせるように構成することができます。 一方で、必要な場合は、高度なセキュリティ機能を利用してユーザーのサインイン プロセスをさらに困難にすることもできます。

* **高度なシナリオ:** 通常、Azure AD によってネイティブにサポートされていない認証要件がある場合に、フェデレーション認証ソリューションが必要になります。詳細な情報は[こちらの一覧](https://blogs.msdn.microsoft.com/samueld/2017/06/13/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365/)にまとめられていますが、一般的な要件は次のとおりです。

    * 認証でスマートカードまたは証明書が必要である
    * オンプレミスの MFA サーバーまたはサード パーティの多要素プロバイダーを使用する。
    * 認証でサード パーティの認証ソリューションを使用する。 「[Azure AD のフェデレーション互換性リスト](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility)」をご覧ください。
    * ユーザーは、ユーザー プリンシパル名 (UPN) (例: user@domain.com) ではなく sAMAccountName (例: DOMAIN\username) を使用してサインインする必要がある

* **ビジネス継続性:** フェデレーション システムでは、通常、認証要求の高可用性を確保するために、内部ネットワークおよび境界ネットワークのトポロジに、負荷分散されたサーバーのアレイ (ファームとも呼ばれます) が構成されている必要があります。 オンプレミスのサーバーが利用できない場合など、プライマリ認証方法を使用できないときのために、フェデレーション認証と共に、パスワード ハッシュ同期をバックアップ認証方法として展開することができます。 大規模なエンタープライズ組織では、認証要求の待機時間を短くするため、geo-DNS で構成された複数のインターネット イングレス ポイントをフェデレーション ソリューションでサポートすることが必要になる場合があります。

* **考慮事項:** フェデレーション システムでは通常、オンプレミスのインフラストラクチャへのより大きな投資が必要です。 オンプレミスのフェデレーションに既に投資していて、ビジネス上の理由から単一の ID プロバイダーを使用することがどうしても必要な組織のほとんどは、このオプションを選択します。 運用とトラブルシューティングは、クラウド認証ソリューションよりフェデレーション認証の方が複雑です。 ユーザー ID と、Azure AD では検証できないルーティング不可能なドメインを使ってサインインするには、追加の構成を実装する必要があります。 この要件は、代替ログイン ID のサポートと呼ばれます。 制限事項と要件については、「[Configuring Alternate Login ID](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)」(代替ログイン ID の構成) をご覧ください。 フェデレーションを使用したサード パーティの多要素認証プロバイダーを使用する場合は、デバイスが Azure AD に接続できるよう、プロバイダーが WS-Trust をサポートしていることを確認してください。

展開手順については、[フェデレーション サービスの実装](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/deploying-federation-servers)に関する記事をご覧ください。

> [!NOTE] 
> Azure AD ハイブリッド ID ソリューションを展開するときは、Azure AD Connect のサポートされているトポロジの 1 つを実装する必要があります。 サポートされている構成とサポートされていない構成について詳しくは、「[Azure AD Connect のトポロジ](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-topologies)」をご覧ください。

## <a name="architecture-diagrams"></a>アーキテクチャの図

以下の図では、Azure AD ハイブリッド ID ソリューションで使用できる各認証方法に必要なアーキテクチャ コンポーネントの概要を示します。 ソリューション間の相違点を比較できるようにします。

次の図では、パスワード ハッシュ同期ソリューションの簡単さの概要を示します。

![PHS](media/azure-ad/azure-ad-authn-image2.png)

次の図では、パススルー認証のエージェントの要件の概要を示します。

![PTA](media/azure-ad/azure-ad-authn-image3.png)

次の図では、組織の境界ネットワークと内部ネットワークでのフェデレーションに必要なコンポーネントの概要を示します。

![ADFS](media/azure-ad/azure-ad-authn-image4.png)

## <a name="comparing-the-authentication-methods"></a>認証方法の比較

|考慮事項|パスワード ハッシュの同期 + シームレス SSO|パススルー認証 + シームレス SSO|AD FS とのフェデレーション|
|:-----|:-----|:-----|:-----|
|認証が行われる場所|クラウド内|クラウド内で、オンプレミスの認証エージェントとのセキュリティで保護されたパスワード検証の交換後|オンプレミスの|
|プロビジョニング システム (Azure AD Connect) 以外のオンプレミスのサーバーの要件|なし|追加の認証エージェントごとに 1 つのサーバー|2 つ以上の AD FS サーバー<br>境界/DMZ ネットワークに 2 つ以上の WAP サーバー|
|プロビジョニング システム以外のオンプレミスのインターネットおよびネットワークの要件|なし|認証エージェントを実行しているサーバーからの[発信インターネット アクセス](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start)|境界の WAP サーバーへの[着信インターネット アクセス](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/overview/ad-fs-requirements)<br>境界の WAP サーバーから AD FS サーバーへの着信ネットワーク アクセス<br>ネットワークの負荷分散|
|SSL 証明書の要件|いいえ |いいえ |[はい]|
|正常性の監視ソリューション|必要なし|エージェントの状態は [Azure Active Directory 管理センター](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication)によって提供される|[Azure AD Connect Health](https://docs.microsoft.com/en-us/azure/active-directory/connect-health/active-directory-aadconnect-health-adfs)|
|会社のネットワーク内のドメインに参加しているデバイスからクラウドのリソースへのユーザーのシングル サインオン|[シームレス SSO](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso) を使用して実行|[シームレス SSO](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso) を使用して実行|[はい]|
|サポートされているサインインの種類|UserPrincipalName + パスワード<br>[シームレス SSO](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso) を使用した Windows 統合認証<br>[代替ログイン ID](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-get-started-custom)|UserPrincipalName + パスワード<br>[シームレス SSO](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso) を使用した Windows 統合認証<br>[代替ログイン ID](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq)|UserPrincipalName + パスワード<br>sAMAccountName + パスワード<br>Windows 統合認証<br>[証明書とスマート カード認証](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br>[代替ログイン ID](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|Windows Hello for Business のサポート|[キー信頼モデル](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>[Intune での証明書信頼モデル](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[キー信頼モデル](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>[Intune での証明書信頼モデル](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[キー信頼モデル](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>[証明書信頼モデル](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|多要素認証のオプション|[Azure MFA](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)|[Azure MFA](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)|[Azure MFA](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)<br>[Azure MFA Server](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-mfaserver-deploy)<br>[サード パーティの MFA](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)|
|サポートされるユーザー アカウントの状態|無効なアカウント<br>(最大 30 分の遅延)|無効なアカウント<br>アカウントのロックアウト<br>パスワード期限切れ<br>ログオン時間|無効なアカウント<br>アカウントのロックアウト<br>パスワード期限切れ<br>ログオン時間|
|条件付きアクセスのオプション|[Azure AD 条件付きアクセス](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)|[Azure AD 条件付きアクセス](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)|[Azure AD 条件付きアクセス](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)<br>[AD FS の要求規則](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|サポートされる従来のプロトコルのブロック|いいえ |いいえ |[はい](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|サインイン ページのロゴ、イメージ、説明のカスタマイズ可能性|[Azure AD Premium を使用して可能](https://docs.microsoft.com/en-us/azure/active-directory/customize-branding)|[Azure AD Premium を使用して可能](https://docs.microsoft.com/en-us/azure/active-directory/customize-branding)|[はい](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-federation-management#customlogo)|
|サポートされる高度なシナリオ|[Smart Password Lockout](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-secure-passwords)<br>[漏洩した資格情報レポート](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-risk-events)|[Smart Password Lockout](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-smart-lockout)|複数サイトの低待機時間の認証システム<br>[AD FS エクストラネットのロックアウト](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-lockout-protection)<br>[サード パーティの ID システムとの統合](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility)|

## <a name="recommendations-and-considerations-from-azure-ad"></a>Azure AD からの推奨事項と考慮事項

ID システムは、クラウドに移行して利用できるようにしたクラウド アプリと基幹業務アプリに、ユーザーがアクセスできるようにします。 認証は、アプリへのアクセスを制御し、承認されたユーザーの生産性を向上させ、不正なユーザーを組織の機密データから締め出します。 そのためには、組織にとって適切な認証方法を選択するときに、以下の推奨事項を考慮してください。 

どの認証方法を選択する場合でも、次の理由により、パスワード ハッシュ同期を使用するか有効にします。

1. **高可用性とディザスター リカバリー:** パススルー認証とフェデレーションは、オンプレミスのインフラストラクチャに依存します。 パススルー認証の場合、オンプレミスのフットプリントには、パススルー認証エージェントに必要なサーバー ハードウェアとネットワークが含まれます。 フェデレーションの場合、認証要求と内部フェデレーション サーバーをプロキシするために境界ネットワーク内にサーバーが必要なので、オンプレミスのフットプリントはさらに大きくなります。 単一点障害を防ぐには、冗長なサーバーを展開して、いずれかのコンポーネントで障害が発生しても認証要求が常にサービスされるようにする必要があります。 また、パススルー認証エージェントとフェデレーションは、両方とも認証要求に応答するためにドメイン コントローラーにも依存しますが、これも障害が発生する可能性があります。 これらの多くのコンポーネントの正常性を維持するためのメンテナンスが必要であり、メンテナンスが計画されていない場合、または正しく実装されていない場合は、システムが停止する可能性が高くなります。 Microsoft の Azure AD クラウド認証サービスはグローバルにスケーリングして常に利用できるため、パスワード ハッシュ同期を使うと停止を回避できます。

2. **オンプレミスの停止への対応:** サイバー攻撃や災害によるオンプレミスの停止の影響は、ブランドの評判が損なわれることから、組織が麻痺して攻撃に対処できなくなることまで、大きな範囲に及ぶ可能性があります。 昨年も、オンプレミスのサーバーがダウンする原因となった特定対象へのランサムウェアなど、多くの組織がマルウェアの攻撃の被害を受けました。 この種の攻撃に対処するお客様を支援することで、Microsoft は組織に 2 つのカテゴリがあることに気付きました。

   a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 パスワード ハッシュ同期を事前に有効にしていた組織は、パスワード ハッシュ同期を使うように認証方法を変更することにより、数時間でオンラインに復帰しました。Office 365 を介した電子メールへのアクセスを使って、問題を解決し、他のクラウド ベースのワークロードにアクセスすることができました。

   b. パスワード ハッシュ同期を事前に有効にしていなかった組織は、通信と問題解決を、信頼されていない外部のコンシューマー向け電子メール システムに頼るしかありませんでした。 このようなケースでは、稼働状態に戻すのに数週間以上かかりました。

3. **Identity Protection:** クラウド内のユーザーを保護する最善の方法の 1 つは、Azure AD Identity Protection です。 Microsoft は、闇サイトで販売されて利用されているユーザーやパスワードのリストをインターネットで常にスキャンしています。 Azure AD はこの情報を使って、組織のユーザー名やパスワードのいずれかが侵害されているかどうかを確認します。 したがって、使われている認証方法がフェデレーション認証かパススルー認証かに関係なく、パスワード ハッシュ同期を有効にすることが重要です。 漏洩した資格情報はレポートとして提供されるので、それを使って、漏洩したパスワードでサインインしようとしたユーザーをブロックしたり、強制的にパスワードを変更させたりすることができます。

最後に、[Gartner](https://info.microsoft.com/landingIAMGartnerreportregistration.html) によると、Microsoft は最も完備した ID およびアクセス管理機能セットを持っています。 Microsoft は、毎月 [4500 億の認証要求](https://www.microsoft.com/en-us/security/intelligence-report)を処理して、ほぼすべてのデバイスから Office 365 のような何千もの SaaS アプリケーションにアクセスできるようにしています。 

## <a name="conclusion"></a>まとめ

この記事では、組織がクラウド アプリへのアクセスをサポートするために構成して展開できるさまざまな認証オプションの概要を説明しました。 ビジネス、セキュリティ、テクノロジに関するさまざまな要件を満たすため、組織は、パスワード ハッシュ同期、パススルー認証、およびフェデレーション認証のいずれかを選ぶことができます。 各認証方法では、ソリューションと、サインイン プロセスのユーザー エクスペリエンスを展開することにより、ビジネス要件が満たされるかどうかを選択できます。 また、各認証方法の高度なシナリオとビジネス継続性機能が必要かどうかを評価する必要もあります。 最後に、各認証方法の考慮事項を評価して、選択した方法の実装を妨げるものがあるかどうかを確認する必要があります。

## <a name="next-steps"></a>次の手順

今日の脅威は、常に、あらゆる場所に存在します。 適切な認証方法の実装は、セキュリティ リスクを軽減して ID を保護するのに役立ちます。 

[Azure AD を利用して](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad)、組織に適した認証ソリューションを展開してください。

フェデレーション認証からクラウド認証への移行を検討している場合は、[サインイン方法の変更](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-user-signin#changing-the-user-sign-in-method)についてさらに学習してください。 移行を計画して実施するときは、[これらのプロジェクト計画が役に立ちます](http://aka.ms/deploymentplans)。
