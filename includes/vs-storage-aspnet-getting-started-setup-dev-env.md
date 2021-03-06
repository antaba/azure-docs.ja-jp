## <a name="set-up-the-development-environment"></a>開発環境を設定する

このセクションでは、開発環境の設定について説明します。 これには、ASP.NET MVC アプリの作成、接続済みサービスの接続の追加、コントローラーの追加、必要な名前空間ディレクティブの指定が含まれます。

### <a name="create-an-aspnet-mvc-app-project"></a>ASP.NET MVC アプリ プロジェクトを作成する

1. Visual Studio を開きます。

1. メイン メニューで、**[ファイル]** > **[新規作成]** > **[プロジェクト]** を選択します。

1. **[新しいプロジェクト]** ダイアログ ボックスで、**[Web]** > **[ASP.NET Web アプリケーション (.NET Framework)]** の順に選択します。 **[名前]** フィールドで、**StorageAspNet** を指定します。 **[OK]**を選択します。

    ![[新しいプロジェクト] ダイアログ ボックスのスクリーンショット](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. **[新しい ASP.NET Web アプリケーション]** ダイアログ ボックスで、**[MVC]** を選択し、**[OK]** を選択します。

    ![[新しい ASP.NET Web アプリケーション] ダイアログ ボックスのスクリーンショット](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>接続済みサービスを使用して Azure ストレージ アカウントに接続する

1. **Solution Explorer** で、プロジェクト名を右クリックします。

2. コンテキスト メニューで **[追加]** > **[接続済みサービス]** の順に選択します。

1. **[接続済みサービス]** ダイアログ ボックスで **[Azure Storage を使用したクラウド ストレージ]** を選択し、**[構成]** を選択します。

    ![[接続済みサービス] ダイアログ ボックスのスクリーンショット](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. **[Azure Storage]** ダイアログ ボックスで、このチュートリアルで使用する Azure Storage アカウントを選択します。 新しい Azure Storage アカウントを作成するには、**[新しいストレージ アカウントの作成]** を選択し、フォームに入力します。 既存のストレージ アカウントを選択するか新しいストレージ アカウントを作成したら、**[追加]** を選択します。 Visual Studio によって Azure Storage 用の NuGet パッケージと **Web.config** へのストレージ接続文字列がインストールされます。

> [!TIP]
> [Azure ポータル](https://portal.azure.com)でのストレージ アカウントの作成方法については、「[ストレージ アカウントの作成](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account)」を参照してください。
>
> [Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md)、[Azure CLI](../articles/storage/common/storage-azure-cli.md)、または [Azure Cloud Shell](../articles/cloud-shell/overview.md) を使用してストレージ アカウントを作成することもできます。

