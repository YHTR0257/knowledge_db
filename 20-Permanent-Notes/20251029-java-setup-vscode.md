# VSCodeにおけるJavaおよびMaven開発環境の構築ガイド

このガイドでは、Visual Studio Code (VSCode) を使用してJavaアプリケーションを開発するための環境構築手順を、パッケージ管理ツールであるMavenと合わせて詳細に解説します。JDKのセットアップからプロジェクトの作成、実行、デバッグまでを網羅します。

## 1. Java Development Kit (JDK) のインストール

まず、開発の基盤となるJDKをインストールします。

### 1.1. JDKのインストール状況の確認

コマンドプロンプトやPowerShellなどのターミナルを開き、以下のコマンドでJDKがインストールされているか確認します。

```bash
java -version
```

インストールされていない場合、または特定のバージョンが必要な場合は、次の手順に進みます。現在の推奨バージョンは、長期サポート（LTS）版である **Java 21** です。

### 1.2. JDKのインストール (Windows)

1.  **インストーラーのダウンロード**:
    *   [OpenJDK (Adoptium)](https://adoptium.net/) にアクセスします。
    *   **Temurin 21 (LTS)** を選択し、OSが **Windows**, Architectureが **x64** になっていることを確認します。
    *   `.msi` 形式のインストーラーをダウンロードします。

2.  **インストール**:
    *   ダウンロードした `.msi` ファイルを実行して、インストールウィザードを開始します。
    *   ウィザードを進めていくと、インストール項目のカスタマイズ画面が表示されます。ここで以下の2つの項目がインストール対象に含まれていることを確認してください。これにより、環境変数の設定が自動で行われ、非常に便利です。
        *   **`Add to PATH`**: `java`コマンドをどのディレクトリからでも実行できるようにします。
        *   **`Set JAVA_HOME variable`**: Javaを利用する多くの開発ツールが必要とする`JAVA_HOME`環境変数を設定します。
    *   ウィザードの指示に従い、インストールを完了します。
3.  **インストール確認**:
    *   **新しい**ターミナルを開き（重要：インストーラー実行前から開いていたターミナルは環境変数の変更を認識しません）、以下のコマンドを実行します。
    ```bash
    # Javaのバージョンが表示されることを確認
    java -version

    # JAVA_HOMEのパスが表示されることを確認
    echo %JAVA_HOME%
    ```

### 1.3. jenvによる複数バージョンのJDK管理 (macOS/Linux 推奨)

`jenv` は、複数のJDKバージョンを簡単に切り替えるためのツールです。プロジェクトごとに異なるJavaバージョンを使用する場合に非常に便利です。

**jenvのインストール (macOS)**

```bash
# Homebrewでjenvをインストール
brew install jenv

# シェルの設定ファイルにjenvを初期化するスクリプトを追加
# (zshの場合)
 echo 'export PATH="$HOME/.jenv/bin:$PATH"' >> ~/.zshrc
 echo 'eval "$(jenv init -)"' >> ~/.zshrc
 source ~/.zshrc

# (bashの場合)
 echo 'export PATH="$HOME/.jenv/bin:$PATH"' >> ~/.bash_profile
 echo 'eval "$(jenv init -)"' >> ~/.bash_profile
 source ~/.bash_profile
```

**jenvへのJDKの登録**

まず、インストール済みのJDKのパスを確認します。

```bash
# インストールされている全JDKのパスを表示
/usr/libexec/java_home -V

# HomebrewでインストールしたJDKのパスを確認する例
brew --prefix openjdk@21
# => /opt/homebrew/opt/openjdk@21 (Apple Silicon)
# => /usr/local/opt/openjdk@21 (Intel Mac)
```

確認したパスを`jenv`に追加します。

```bash
# 例: Apple Silicon (M1/M2/M3) の場合
jenv add $(brew --prefix openjdk@21)/libexec/openjdk.jdk/Contents/Home

# 例: Intel Mac の場合
jenv add $(brew --prefix openjdk@21)/libexec/openjdk.jdk/Contents/Home
```

**jenvでJDKバージョンを設定**

```bash
# 登録されたバージョンの一覧を確認
jenv versions

# グローバルで使用するデフォルトバージョンを設定
jenv global 21.0

# 現在のディレクトリだけでバージョンを設定する場合
# jenv local 21.0

# 設定が反映されたか確認
java -version
```

## 2. Apache Mavenのインストール

Mavenは、Javaプロジェクトのビルド、依存関係管理、ドキュメント作成などを自動化するツールです。

### 2.1. インストール

**macOS (Homebrewを使用)**
```bash
brew install maven
```

**Linux (aptを使用 - Ubuntu/Debian)**
```bash
sudo apt update
sudo apt install maven
```

**Windows**

WindowsへのMavenのインストール手順を説明します。

#### 方法1: Chocolatey を使う（推奨・簡単）

##### Chocolateyのインストール

PowerShellを**管理者権限**で開いて実行：

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```

##### Mavenのインストール

```powershell
choco install maven
```

##### 確認

```bash
mvn -version
```

以下のような出力が表示されればOK：

```
Apache Maven 3.9.9 (...)
Maven home: C:\Program Files\Apache\Maven\apache-maven-3.9.9
Java version: 21.0.x, vendor: Eclipse Adoptium
Java home: C:\Program Files\Eclipse Adoptium\jdk-21.0.x-hotspot
Default locale: ja_JP, platform encoding: UTF-8
OS name: "windows 11", version: "10.0", arch: "amd64", family: "windows"
```

#### JAVA_HOMEの確認

```cmd
echo %JAVA_HOME%
java -version
```

---

## トラブルシューティング

- エラー1: 'mvn' は、内部コマンドまたは外部コマンド...として認識されていません
  - **原因：** Pathが正しく設定されていない

**解決方法：**
1. コマンドプロンプトを**再起動**
2. 環境変数のPathに `%MAVEN_HOME%\bin` が追加されているか確認
3. 手動で確認：
   ```cmd
   dir "C:\Program Files\Apache\Maven\apache-maven-3.9.9\bin\mvn.cmd"
   ```

- エラー2: JAVA_HOME is not set
  - **原因：** JAVA_HOME環境変数が設定されていない

**解決方法：**
1. JDKのインストール場所を確認
2. JAVA_HOME環境変数を設定（上記のステップ4参照）
3. コマンドプロンプトを再起動

### エラー3: Java versionが表示されない

**解決方法：**
```cmd
# JDKの場所を探す
where java

# JAVA_HOMEを正しく設定
setx JAVA_HOME "C:\Program Files\Eclipse Adoptium\jdk-21.0.x-hotspot"
```

---

## VSCodeでの確認

VSCodeのターミナルで確認：

```bash
mvn -version
java -version
echo %JAVA_HOME%
echo %MAVEN_HOME%
```

すべて正しく表示されれば、セットアップ完了です！

1.  **バイナリのダウンロード**:
    *   [Apache Maven公式サイト](https://maven.apache.org/download.cgi)にアクセスします。
    *   「Binary zip archive」（例: `apache-maven-3.9.x-bin.zip`）をダウンロードします。

2.  **ファイルの解凍**:
    *   ダウンロードした `.zip` ファイルを任意の場所に解凍します。開発ツールは `C:\tools` のようなスペースを含まないパスに置くのが一般的です（例: `C:\tools\apache-maven-3.9.6`）。

3.  **環境変数の設定**:
    *   `Win + R` を押し `sysdm.cpl` と入力して「システムのプロパティ」を開きます。
    *   「詳細設定」タブ → 「環境変数...」ボタンをクリックします。
    *   **システム環境変数** のセクションで「新規...」をクリックします。
        *   変数名: `MAVEN_HOME`
        *   変数値: Mavenを解凍したディレクトリのパス（例: `C:\tools\apache-maven-3.9.6`）
    *   **システム環境変数** のリストから `Path` を選択し、「編集...」をクリックします。
        *   「新規」をクリックし、`%MAVEN_HOME%\bin` と入力して追加します。
        *   OKを押してすべてのダイアログを閉じます。
![[スクリーンショット 2025-10-29 21.47.26 1.png]]
4.  **インストール確認**:
    *   **新しい**ターミナルを開き、以下のコマンドを実行してバージョン情報が表示されれば成功です。
    ```bash
    mvn -version
    ```

### 2.2. インストール確認

ターミナルで以下のコマンドを実行し、バージョン情報が表示されればインストールは成功です。

```bash
mvn -version
```

---

Mavenのより詳細な概念（POM, ライフサイクルなど）については、以下のノートで解説しています。

![[20251029-maven]]


## 3. VSCode拡張機能のインストール

VSCodeでJava開発を快適に行うために、以下の拡張機能をインストールします。

1.  **Extension Pack for Java**: Microsoftが提供する公式の拡張機能パック。これ一つで必要なものがほぼ揃います。
    - **Language Support for Java by Red Hat**: コード補完、シンタックスハイライト、リファクタリング機能などを提供。
    - **Debugger for Java**: Javaコードのデバッグ機能を追加。
    - **Test Runner for Java**: JUnitやTestNGなどのテストフレームワークを統合。
    - **Maven for Java**: Mavenプロジェクトの管理、ビルド、依存関係の可視化をサポート。
    - **Project Manager for Java**: プロジェクトの表示や管理を容易にするサイドバーを追加。
    - **IntelliCode**: AIを活用した賢いコード補完を提供。

VSCodeのアクティビティバーから「拡張機能」を開き、「`Extension Pack for Java`」と検索してインストールしてください。

## 4. Mavenプロジェクトの作成

### 方法1: VSCodeのコマンドパレットから (推奨)

1. `Ctrl+Shift+P`（macOS: `Cmd+Shift+P`）でコマンドパレットを開きます。
2. 「`Java: Create Java Project`」を選択します。
3. 「`Maven`」を選択します。
4. アーキタイプ（プロジェクトの雛形）を選択します。最初は「`maven-archetype-quickstart`」がシンプルで良いでしょう。
5. 最新のバージョンを選択します。
6. プロジェクトの情報を入力します。
    - **Group ID**: プロジェクトを識別するための一意なID。慣例として、**逆ドメイン名方式**（例: `com.github.username`）が使われます。
    - **Artifact ID**: プロジェクト名（例: `my-first-app`）。
7. プロジェクトを保存するディレクトリを選択します。

![Mavenプロジェクトの作成](../10-Literature-Notes/images/Pasted%20image%2020251029130907.png)

### 方法2: ターミナルから

以下のコマンドを実行して、対話なしでプロジェクトを作成することもできます。

```bash
mvn archetype:generate \
  -DgroupId=com.example \
  -DartifactId=my-app \
  -DarchetypeArtifactId=maven-archetype-quickstart \
  -DinteractiveMode=false
```

## 5. プロジェクトの動作確認

プロジェクトが作成されたら、VSCodeでそのフォルダを開きます。

### 5.1. プロジェクト構造の確認

- `pom.xml`: Mavenのプロジェクト設定ファイル。依存ライブラリやビルド設定を記述します。
- `src/main/java`: アプリケーションのソースコードを配置するディレクトリ。
- `src/test/java`: テストコードを配置するディレクトリ。

サイドバーの「JAVA PROJECTS」セクションに、プロジェクトが正しく認識されていることを確認してください。

### 5.2. "Hello, World!"の実行

`src/main/java/com/example/App.java`（Group IDによってパスは異なります）を開き、以下のような簡単なコードを実行してみましょう。

```java
package com.example;

public class App {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```

- `main`メソッドの上にある「▶ Run」ボタンをクリックするか、`F5`キーを押してデバッグを開始します。
- ターミナルに「Hello, World!」と表示されれば成功です。

### 5.3. Mavenコマンドでのビルド

VSCodeに統合されたターミナル（`Ctrl+\`）を開き、以下のMavenコマンドを実行してみましょう。

- **`mvn compile`**: ソースコードをコンパイルします。
- **`mvn test`**: テストコードを実行します。
- **`mvn package`**: プロジェクトをJARファイルにパッケージングします。`target`ディレクトリに生成されます。
- **`mvn clean install`**: `target`ディレクトリを削除（clean）し、コンパイル、テスト、パッケージングを行い、ローカルのMavenリポジトリにインストール（install）します。

## 6. pom.xmlの近代化

`maven-archetype-quickstart` で生成される `pom.xml` は、Java 1.7やJUnit 4といった古い設定を前提としています。これをJava 21やJUnit 5など、現代的な開発環境に合わせて更新します。

### 6.1. プロパティの更新

プロジェクトで使用するJavaのバージョンを指定します。

```xml
<properties>
  <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  <maven.compiler.source>21</maven.compiler.source>
  <maven.compiler.target>21</maven.compiler.target>
</properties>
```

### 6.2. 依存関係の更新 (JUnit 5)

古いJUnit 4の依存関係を削除し、代わりにJUnit 5 (Jupiter) の依存関係を追加します。

```xml
<dependencies>
  <dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-api</artifactId>
    <version>5.10.2</version>
    <scope>test</scope>
  </dependency>
</dependencies>
```

### 6.3. ビルドプラグインの設定

MavenがJUnit 5でテストを実行できるように、ビルドプラグイン（`maven-surefire-plugin`）のバージョンを更新します。

```xml
<build>
  <pluginManagement><!-- lock down plugins versions to avoid using Maven defaults (may be moved to parent pom) -->
    <plugins>
      <plugin>
        <artifactId>maven-surefire-plugin</artifactId>
        <version>3.2.5</version>
      </plugin>
    </plugins>
  </pluginManagement>
</build>
```

### 6.4. 更新後の `pom.xml` 全体像

以下は、更新後の `pom.xml` の完全な例です。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.example</groupId>
  <artifactId>my-app</artifactId>
  <version>1.0-SNAPSHOT</version>

  <name>my-app</name>
  <url>http://www.example.com</url>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>21</maven.compiler.source>
    <maven.compiler.target>21</maven.compiler.target>
  </properties>

  <dependencies>
    <dependency>
      <groupId>org.junit.jupiter</groupId>
      <artifactId>junit-jupiter-api</artifactId>
      <version>5.10.2</version>
      <scope>test</scope>
    </dependency>
  </dependencies>

  <build>
    <pluginManagement><!-- lock down plugins versions to avoid using Maven defaults (may be moved to parent pom) -->
      <plugins>
        <!-- clean lifecycle, see https://maven.apache.org/ref/current/maven-core/lifecycles.html#clean_Lifecycle -->
        <plugin>
          <artifactId>maven-clean-plugin</artifactId>
          <version>3.1.0</version>
        </plugin>
        <!-- default lifecycle, jar packaging: see https://maven.apache.org/ref/current/maven-core/default-bindings.html#Plugin_bindings_for_jar_packaging -->
        <plugin>
          <artifactId>maven-resources-plugin</artifactId>
          <version>3.0.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.8.0</version>
        </plugin>
        <plugin>
          <artifactId>maven-surefire-plugin</artifactId>
          <version>3.2.5</version>
        </plugin>
        <plugin>
          <artifactId>maven-jar-plugin</artifactId>
          <version>3.0.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-install-plugin</artifactId>
          <version>2.5.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-deploy-plugin</artifactId>
          <version>2.8.2</version>
        </plugin>
        <!-- site lifecycle, see https://maven.apache.org/ref/current/maven-core/lifecycles.html#site_Lifecycle -->
        <plugin>
          <artifactId>maven-site-plugin</artifactId>
          <version>3.7.1</version>
        </plugin>
        <plugin>
          <artifactId>maven-project-info-reports-plugin</artifactId>
          <version>3.0.0</version>
        </plugin>
      </plugins>
    </pluginManagement>
  </build>
</project>
```

`pom.xml` を保存すると、VSCodeが変更を自動的に検出し、プロジェクトの依存関係を更新します。

## 7. 最終動作確認

`pom.xml`を近代化したことにより、プロジェクト全体がJava 21とJUnit 5で動作するようになりました。最後に、テストコードをJUnit 5の形式に修正し、アプリケーションの実行とテストの両方が成功することを確認します。

### 7.1. テストコードの更新 (JUnit 5)

`src/test/java/com/example/AppTest.java` を開き、内容を以下のように書き換えます。JUnit 4のアノテーション（`@Test`）やアサーション（`assertTrue`）がJUnit 5のものに変わります。

```java
package com.example;

import static org.junit.jupiter.api.Assertions.assertTrue;
import org.junit.jupiter.api.Test;

/**
 * Unit test for simple App.
 */
public class AppTest {
    /**
     * Rigorous Test :-)
     */
    @Test
    public void shouldAnswerWithTrue() {
        assertTrue(true);
    }
}
```

### 7.2. アプリケーションの実行とテスト

すべての設定が完了した状態で、最終的な動作確認を行います。

1.  **Hello, World! の実行**:
    - `src/main/java/com/example/App.java` を開きます。
    - `main` メソッドの横に表示される `▶ Run` ボタンをクリックします。
    - VSCodeのターミナルに「`Hello, World!`」と出力されることを確認します。

2.  **テストの実行**:
    - `src/test/java/com/example/AppTest.java` を開きます。
    - `AppTest` クラスの横に表示される `▶` ボタン（Run Test）をクリックします。
    - VSCodeの「テスト結果」パネルが開き、テストが緑色のチェックマークで成功したことを確認します。
    - または、ターミナルで `mvn clean test` を実行し、`BUILD SUCCESS` と表示されることを確認します。

これで、VSCodeでのJavaおよびMaven開発環境のセットアップは完了です。

## 8. VSCodeのJava設定 (任意)

通常は自動で設定されますが、問題が発生した場合は `settings.json` を確認します。

1. `Ctrl+ShiftP`（macOS: `Cmd+Shift+P`）で「`Preferences: Open User Settings (JSON)`」を開きます。
2. 以下の設定を確認・追加します。

```json
{
  // 使用するJDKのパス。jenvを使っている場合は不要なことが多い
  "java.home": "/opt/homebrew/opt/openjdk@21",

  // pom.xmlが変更されたときにビルド設定を自動で更新する
  "java.configuration.updateBuildConfiguration": "automatic",

  // Mavenの実行ファイルのパス。通常は自動検出される
  "maven.executable.path": "/opt/homebrew/bin/mvn"
}
```

## 9. トラブルシューティング

- **「JDK not found」エラーが表示される**: 
  - `java.home`設定が正しいか確認してください。
  - `jenv`を使用している場合、`jenv global`が正しく設定されているか確認してください。
- **Mavenコマンドが認識されない**: 
  - Mavenのインストールパスがシステムの`PATH`環境変数に含まれているか確認してください。
- **依存関係が解決できない**: 
  - `pom.xml`の`<dependencies>`セクションが正しいか確認してください。
  - VSCodeのコマンドパレットから「`Java: Clean Java Language Server Workspace`」を実行して、キャッシュをクリアしてみてください。
