
## 0. Blazor (Blazor Web App)とは
参考： https://dotnet.microsoft.com/ja-jp/apps/aspnet/web-apps/blazor (.NET 公式サイト)

> Blazor は、HTML、CSS、C# をベースにした最新のフロントエンド Web フレームワークであり、Web アプリ構築の迅速化に役立ちます。

Blazorは.NET系のWeb UI フレームワークで、JavaScript系フレームワークであるReactやAngularなどに性質が似ています。
変更されたDOMの差分を検知し、必要なUIの変更のみレンダリングします。

Blazorは2023年まで、サーバーサイドで実装する*Blazor Server*とクライアントサイドで実装する*Blazor Wasm (Web Assembly)* の2つで分かれていました。
Blazor Web Appはその2つが統合され、フルスタックUIフレームワークとして開発が可能になったのです。

また、Blazor Web AppではページごとにServerモードとWasmモードを選択可能。
これにより無駄な通信を排除し高速なWebアプリケーションの開発が可能となっています。

#### 補足
2024年12月時点ではBlazorに関するコミュニティ、解説記事の多くがBlazorServer やBlazor Wasmによるものがほとんどです。

Blazor Web Appに関する記事は公式リファレンスや公式から提供されているサンプルソースコードを参考にすることをお勧めします。

本記事は、ジュニア ~ ミドルエンジニア向けに、Blazor Web App 開発プロジェクトにアサインされても最低限進行可能なレベルになるまでをお手伝いするハンズオン形式のチュートリアルです。

また、本記事では **VSCode環境下** での開発をサポートするものです。
Visual Studio 20XX環境下での構築は、別の構築手法が適していますので、詳細は公式リファレンスを参考にするようお願いいたします。

## 1. 開発環境構築 / プロジェクト初期セットアップ

### 章目次
+ 1-1. .NET SDKをインストール
+ 1-2. VSCodeをインストール
+ 1-3. Blazorプロジェクトの作成
+ 1-4. 初回ビルド・実行

### 1-1. .NET SDKのインストール

VSCodeでは CLI上でデバッグやパッケージのインストールを行います。
`dotnet`コマンドを使用するため、.NET SDKをインストールしましょう。

【2024/12時点】 https://dotnet.microsoft.com/en-us/download/dotnet/8.0

![[Screenshot 2024-12-06 at 13.51.09.png]]

**※ 開発時点でBlazorと互換性のある最新のバージョンをインストールしてください。.NETのバージョンが上がると、後述するEF Coreで扱うバージョンも異なります。**
### 1-2. VSCodeのインストール

VSCodeがインストールされていない方は、VSCodeをマシンにインストールします。
https://code.visualstudio.com/download

![[Screenshot 2024-12-06 at 13.52.47.png]]

### 1-3. Blazorプロジェクトの作成

VSCode上でターミナルを開き、`dotnet` コマンドが使えることを確認してください。

```terminal
% dotnet

Usage: dotnet [options]
Usage: dotnet [path-to-application]

Options:
  -h|--help         Display help.
  --info            Display .NET information.
  --list-sdks       Display the installed SDKs.
  --list-runtimes   Display the installed runtimes.

path-to-application:
  The path to an application .dll file to execute.
```

Blazorプロジェクトを `dotnet new`コマンドから作成します。

```terminal
% dotnet new blazor -n Sample

The template "Blazor Web App" was created successfully.
This template contains technologies from parties other than Microsoft, see https://aka.ms/aspnetcore/8.0-third-party-notices for details.

Processing post-creation actions...
Restoring ~/Sample/Sample.csproj:
  Determining projects to restore...
  Restored ~/Sample/Sample.csproj (in 25 ms).
Restore succeeded.
```

`dotnet new blazor`でBlazor Web App プロジェクトの新規作成。
`-n [Project Name]`でプロジェクト名を指定しています。

`-o [Directory Path]`オプションを使用すると生成された出力を配置する場所を指定します。
デフォルトは現在のディレクトリです。

今回のハンズオンでは`Sample`というプロジェクトを立ち上げました。

```
~/Sample/  
├── bin  
│   └── Debug  
│       └── net8.0  
├── Components  
│   ├── Layout  
│   │   ├── MainLayout.razor  
│   │   ├── MainLayout.razor.css  
│   │   ├── NavMenu.razor  
│   │   └── NavMenu.razor.css  
│   ├── Pages  
│   │   ├── Counter.razor  
│   │   ├── Error.razor  
│   │   ├── Home.razor  
│   │   └── Weather.razor  
│   ├── _Imports.razor  
│   ├── App.razor  
│   └── Routes.razor  
├── obj  
│   ├── Debug  
│   │   └── net8.0  
│   │       ├── ref  
│   │       ├── refint  
│   │       ├── .NETCoreApp,Version=v8.0.AssemblyAttributes.cs  
│   │       ├── Sample.AssemblyInfo.cs  
│   │       ├── Sample.AssemblyInfoInputs.cache  
│   │       ├── Sample.assets.cache  
│   │       ├── Sample.GeneratedMSBuildEditorConfig.editorconfig  
│   │       └── Sample.GlobalUsings.g.cs  
│   ├── project.assets.json  
│   ├── project.nuget.cache  
│   ├── Sample.csproj.nuget.dgspec.json  
│   ├── Sample.csproj.nuget.g.props  
│   └── Sample.csproj.nuget.g.targets  
├── Properties  
│   └── launchSettings.json  
├── wwwroot  
│   ├── bootstrap  
│   │   ├── bootstrap.min.css  
│   │   └── bootstrap.min.css.map  
│   ├── app.css  
│   └── favicon.png  
├── appsettings.Development.json  
├── appsettings.json  
├── Program.cs  
└── Sample.csproj
```

上記の構成に沿ってディレクトリ・ファイルが正しく生成されているはずです。
### 1-4. 初回ビルド・実行

#### ビルド・実行関連コマンド

* `dotnet build` ：プロジェクトのビルド
* `dotnet run` ：プロジェクトのビルド・実行
* `dotnet watch`：プロジェクトのビルド・実行（ホットリロード付き）

それぞれ実行してみましょう。

### `dotnet build`

```terminal
% dotnet build
  Determining projects to restore...
  All projects are up-to-date for restore.
  Sample -> ~/Sample/bin/Debug/net8.0/Sample.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:02.50
```

`Determining projects to restore...`と出ているように、ビルド時にプロジェクトの復元を試行します。
もしエラーや警告がある場合、以下のように出力されます。

#### 例1）存在しないコンポーネントを参照したとき：警告
```terminal
% dotnet build
  Determining projects to restore...
  All projects are up-to-date for restore.
~/Sample/Components/Layout/MainLayout.razor(6,9): warning RZ10012: Found markup element with unexpected name 'Nav'. If this is intended to be a component, add a @using directive for its namespace. [~/Sample/Sample.csproj]
  Sample -> ~/Sample/bin/Debug/net8.0/Sample.dll

Build succeeded.

~/Sample/Components/Layout/MainLayout.razor(6,9): warning RZ10012: Found markup element with unexpected name 'Nav'. If this is intended to be a component, add a @using directive for its namespace. [~/Sample/Sample.csproj]
    1 Warning(s)
    0 Error(s)

Time Elapsed 00:00:01.10
```

※警告のみの場合、ビルドは通過します。

#### 例2) 存在しないパッケージを参照したとき：エラー
```terminal
dotnet build
  Determining projects to restore...
~/Sample/Sample.csproj : error NU1101: Unable to find package Microsoft.AspNetCore.Components.QuickGri. No packages exist with this id in source(s): nuget.org
  Failed to restore ~/Sample/Sample.csproj (in 755 ms).

Build FAILED.

~/Sample/Sample.csproj : error NU1101: Unable to find package Microsoft.AspNetCore.Components.QuickGri. No packages exist with this id in source(s): nuget.org
    0 Warning(s)
    1 Error(s)

Time Elapsed 00:00:01.04
```

※エラーの場合：ビルドは失敗します。

### `dotnet run`

```terminal
% dotnet run
Building...
info: Microsoft.Hosting.Lifetime[14]
      Now listening on: http://localhost:5008
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: ~/Sample
```

http://localhost:5008 にアクセスすると、以下の画面が表示されます。

![[Screenshot 2024-12-06 at 14.41.27.png]]

非同期カウンターやリスト表示の画面がデフォルトで生成されています。

![[Screenshot 2024-12-06 at 14.43.57.png]]

![[Screenshot 2024-12-06 at 14.43.46.png]]

### `dotnet watch`

`dotnet run`コマンドでは、ソースコードの変更を検知しません。
変更を適用するには、アプリケーションを再起動する必要があります。

`dotnet watch`はファイルウォッチし、ソースコードの変更が検知されると`dotnet run`が自動で実行され、サポートされている変更の場合アプリケーションがホットリロードされます。
サポートされていない変更の場合はアプリケーションを再起動します。

これにより開発の高速化が可能です。

本ハンズオンでは、プロジェクトのビルドおよび実行を`dotnet watch`コマンドをデフォルトとして進めます。
（再起動が必要なタイミングは適宜説明します。）

### 1.のまとめ
+ 1-1. .NET SDKをインストール
+ 1-2. VSCodeをインストール
+ 1-3. `% dotnet new blazor -n [Project Name] -o [Directory Path]`でプロジェクト作成
+ 1-4. `% dotnet watch`でホットリロード

# 2. ページの作成

## 章目次

2-1. ルーティング
2-2. ページ実装

## 2-1. ルーティング

`http://localhost:xxxx/test-page/`というルーティングにしたいケースを例にします。
新規ページを作成するには、任意の場所に.razor拡張子でファイルを作成します。
今回は `Pages/TestPage.razor`を作成します。
ルーティング（パス）は以下のように記述します。

```Pages/TestPage.razor
@page "/test-page"
```

このように記載することで、新しくルーティングが作成され、指定したURLにアクセスできるようになります。

### ルートパラメータ

`https://example.com/edit/{id}` のように、ルートパラメータを割り当てたいは、以下のように記述します。

```Pages/Edit.razor
@page "/edit/{id}"

@code{
	[Parameter]
	public int Id { get; set; }
}
```

`@page "/edit/{id:int}`のようにすると、制約を設けることも可能です。

## 2-2. ページの実装

ページの実装は、通常のHTML / CSS / JSを利用可能です。

```
@page "/test-page"

<PageTitle>テストページ</PageTitle>

<h1>テストページです<h1>

<style>
h1{
	color: red;
}
</style>

<script>
console.log("テストページです");
</script>
```

上部の`PageTitle`タグは、htmlタグでいうところの`<title>`を決定することができます。
ブラウザタブに表示されるページタイトルのことです。


## 2-3. C#コード

`.razor`拡張子のファイルでは、`@code{}`の中に直接C#を記載することができます。

例えば、チェックボックスに応じて表示するテキストを変えたい場合は以下のように記載します。

```Test.razor
@page "/test"
@rendermode InteractiveServer //サーバーモードでページを表示する場合のおまじない

<PageTitle>C#コードのテスト</PageTitle>

@if(isChecked){
	<p>チェックが入っています</p>
} else {
	<p>チェックが入っていません</p>
}

@code {
	private bool isChecked { get; set; } = false;
}
```

# 3. レイアウトの作成・適用

Blazorプロジェクトでは、ページごとに異なるレイアウトを適用することができます。
プロジェクト作成時は、`Layout/MainLayout.razor`が適用されています。
これは`Components/Routes.razor`の中の`DefaultLayout`で適用されています。

ログインページだけレイアウトを変更したいケースを考えてみます。

`Layout/LoginLayout.razor`を作成します。

```Layout/LoginLayout.razor
@inherits LayoutComponentBase

<div class="l-login">
	@Body
</div>
```

`@inherits LayoutComponentBase`を最初に記述することで、作成したコンポーネントがレイアウトコンポーネントであることを示します。

`@Body`がある場所は、ページが実装される場所を示します。

これを`Components/Pages/Login.razor`に適用してみましょう。

```Login.razor
@page "/login"
@layout Layout.LoginLayout

<PageTitle>ログイン画面</PageTitle>

<h1>ログイン画面です</h1>
```

`@layout`でコンポーネントを指定してあげることで、ページごとに異なるレイアウトを適用させることが可能です。
# 4. 再利用可能なコンポーネントの作成・挿入
## 参考
https://learn.microsoft.com/ja-jp/aspnet/core/blazor/components/?view=aspnetcore-9.0

## 理想の形
引数で特定のテキストやスタイルに文字列を挿入しながら、
中身のコンテンツはHTMLで書きたい

```html:TestPage.razor
<Component Title="タイトル" Desc="説明が入る">
    <p>フォームやらなんやらが入る</p>
</Component>
```

## パラメータによる参照

```html
<Component Title="タイトル" Desc="説明が入る"> // ←これ
</Component>
```

>''コンポーネント パラメーター'' によりデータがコンポーネントに渡されます。これらのパラメーターは、[Parameter] 属性を指定したコンポーネント クラス上で、パブリック C# プロパティを使用して定義されます。 次の例では、組み込みの参照型 (System.String) とユーザー定義の参照型 (PanelBody) がコンポーネント パラメーターとして渡されます。

`[Parameter]`を使用して、組み込む変数を参照する

### テスト実装

```cs:Component.razor
@code {
    [Parameter]
    public string Title { get; set; } = "";
    
    [Parameter]
    public string Desc {get; set; } = "";
}
```

```html:Component.razor
<h2>@Title</h2>
<p>@Desc</p>
```

```TestPage.razor
<Component Title="タイトル" Desc="説明が入る" />
```

### 出力結果

```html:HTML
<h2>タイトル</h2>
<p>説明が入る</p>
```

## レンダリングフラグメントの活用
```html
<Component Title="タイトル" Desc="説明が入る">
    <p>フォームやらなんやらが入る</p> //←これ
</Component>
```

>コンポーネントでは、別のコンポーネントのコンテンツを設定できます。 代入コンポーネントでは、子コンポーネントの開始および終了タグの間にコンテンツを指定します。

>Razorマークアップ内の ChildContent の位置は、コンテンツが最終的な HTML 出力にレンダリングされる場所です。

### テスト実装

コンポーネント側：
```cs:Component.razor
<div class="child">
    @ChildContent
</div>

@code{
    [Parameter]
    public RenderFragment? ChildContent { get; set; }
}
```

ページ側：
```html:TestPage.razor
<Component>
    コンテンツが入ります
</Component>
```

### 出力結果

```html:HTML
<div class="child">
    コンテンツが入ります
</div>
```

## Bootstrapモーダルを再利用可能なコンポーネントにする

パラメータとレンダリングフラグメントを組み合わせて、モーダルを作成してみます。
これでスッキリ。

ページ側：
```html:TestPage.razor（コンポーネント利用側）
<Modal ModalTarget="staticBackdrop" ModalTitle="タイトル" SubmitButtonText="実行" CancelButtonText="キャンセル">
  <div class="row mb-3">
    <div class="col-3">
      <label for="area" class="col-form-label d-flex justify-content-between align-items-center">フォーム：</label>
    </div>

    <div class="col-6">
      <input type="text" name="" id="" class="form-control">
    </div>
  </div>
</Modal>
```

コンポーネント側
```html:Modal.razor
<div class="modal fade" id="@ModalTarget" data-bs-backdrop="static" data-bs-keyboard="false" tabindex="-1"
  aria-labelledby="staticBackdropLabel" aria-hidden="true">
  <div class="modal-dialog modal-dialog-centered">
    <div class="modal-content p-3">
      <div class="modal-header justify-content-between align-items-start">
        <p></p>
        <div class="text-center">
          <h1 class="modal-title fs-5 text-purple" id="staticBackdropLabel">@ModalTitle</h1>
        </div>
        <button type="button" class="btn-close m-0 p-0" data-bs-dismiss="modal" aria-label="Close"></button>
      </div>
      <div class="modal-body">
        <div class="conteiner-fluid">
          @ChildContent
        </div>
      </div>
      <div class="modal-footer">
        <button type="submit" class="btn btn-purple">@SubmitButtonText</button>
        <button type="button" class="btn btn-outline-purple" data-bs-dismiss="modal">@CancelButtonText</button>
      </div>
    </div>
  </div>
</div>
```

```cs:Modal.razor
@code {
  [Parameter]
  public string ModalTarget { get; set; } = "";

  [Parameter]
  public string ModalTitle { get; set; } = "";

  [Parameter]
  public string SubmitButtonText { get; set; } = "";
  [Parameter]
  public string CancelButtonText { get; set; } = "";

  [Parameter]
  public RenderFragment? ChildContent { get; set; }
}
```

# 5. モデルの作成・マイグレーション

## 章目次

5-1. モデルの作成
5-2. コンテキストの作成
5-3. マイグレーション

※※※更新予定※※※




