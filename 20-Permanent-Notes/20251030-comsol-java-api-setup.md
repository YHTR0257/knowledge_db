---
id: 202510300
created: 2025-10-30
updated: 2025-10-30
tags: [permanent, comsol, java, api, simulation, setup]
type: permanent-note
status: draft
confidence: high
source_type: knowledge
---

# COMSOL Java APIセットアップガイド：Maven + PowerShellによる完全実行手順

このガイドでは、Windows環境でCOMSOL MultiphysicsのJava APIをMavenプロジェクトに統合し、PowerShellから簡単なJavaプログラムをコンパイル・実行するまでの手順を詳細に解説します。Mavenを使用することで、依存関係の管理とビルドプロセスを標準化し、COMSOLのグラフィカルインターフェースを使わない自動化されたシミュレーションを実現します。

## 前提条件

このガイドを始める前に、以下の環境が整っていることを確認してください。

- ✅ JDK 21がインストールされ、`java -version`でバージョンが表示される
- ✅ Apache Mavenがインストールされ、`mvn -version`でバージョンが表示される
- ✅ COMSOL Multiphysics（デスクトップアプリケーション）が正常に起動する
- ✅ Windows 10/11環境
- ✅ PowerShell 5.1以降が使用可能

**参考**: Java開発環境とMavenのセットアップについては、[[20251029-java-setup-vscode]]を参照してください。

## 1. COMSOL環境の確認

### 1.1. COMSOLのインストールパスを確認

まず、COMSOLがどこにインストールされているか確認します。PowerShellを開いて、以下のコマンドを実行します。

```powershell
# COMSOLの標準インストール先を確認
Test-Path "C:\Program Files\COMSOL\COMSOL61\Multiphysics"
```

**期待される出力**: `True` が表示されればOKです。

`False`が表示される場合は、以下の手順でインストール先を特定します。

```powershell
# COMSOLがインストールされているすべてのバージョンを検索
Get-ChildItem "C:\Program Files\COMSOL" -Directory | Select-Object Name
```

**出力例**:
```
Name
----
COMSOL61
COMSOL60
```

あなたの環境に合わせて、以降の手順で使用する`COMSOL61`の部分を適切なバージョンに読み替えてください。

### 1.2. COMSOLコマンドラインツールの動作確認

COMSOLには`comsol.exe`というコマンドラインツールがあり、これを使ってJavaプログラムをコンパイル・実行します。

```powershell
# COMSOLのコマンドラインツールが存在するか確認
Test-Path "C:\Program Files\COMSOL\COMSOL61\Multiphysics\bin\win64\comsol.exe"
```

**期待される出力**: `True`

`True`が表示されれば、COMSOLのコマンドラインツールは正常にインストールされています。

---

## 2. Mavenプロジェクトの作成

### 2.1. Mavenプロジェクトの作成

まず、標準的なMavenプロジェクトを作成します。VSCodeのコマンドパレットまたはターミナルから作成できます。

**方法1: VSCodeのコマンドパレットから（推奨）**

1. `Ctrl+Shift+P`（macOS: `Cmd+Shift+P`）でコマンドパレットを開く
2. 「`Java: Create Java Project`」を選択
3. 「`Maven`」を選択
4. 「`maven-archetype-quickstart`」を選択
5. 最新バージョンを選択
6. プロジェクト情報を入力:
   - **Group ID**: `com.example`
   - **Artifact ID**: `comsol-simulation`
7. 保存先ディレクトリを選択

**方法2: PowerShellから作成**

```powershell
# プロジェクトを作成したい親ディレクトリに移動
Set-Location "C:\Users\$env:USERNAME\Documents"

# Mavenプロジェクトを作成
mvn archetype:generate `
  -DgroupId=com.example `
  -DartifactId=comsol-simulation `
  -DarchetypeArtifactId=maven-archetype-quickstart `
  -DinteractiveMode=false

# プロジェクトディレクトリに移動
Set-Location comsol-simulation

# ディレクトリ構造を確認
Get-ChildItem -Recurse -Depth 1
```

**期待される出力**:
```
Directory: C:\Users\[ユーザー名]\Documents\comsol-simulation

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----        2025/10/30     10:00                src
-a----        2025/10/30     10:00           XXXX pom.xml
```

### 2.2. 追加ディレクトリの作成

シミュレーション結果や入力ファイルを整理するため、追加のディレクトリを作成します。

```powershell
# 必要なサブディレクトリを一度に作成
@("output", "geometry", "config") | ForEach-Object {
    New-Item -Path $_ -ItemType Directory -Force
}

# ディレクトリ構造を確認
Get-ChildItem -Directory
```

**期待される出力**:
```
Directory: C:\Users\[ユーザー名]\Documents\comsol-simulation

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----        2025/10/30     10:00                config
d-----        2025/10/30     10:00                geometry
d-----        2025/10/30     10:00                output
d-----        2025/10/30     10:00                src
```

---

## 3. COMSOL JARファイルのMavenローカルリポジトリへの登録

COMSOLのJava APIをMavenプロジェクトで使用するには、COMSOL提供のJARファイルをMavenのローカルリポジトリに登録する必要があります。

### 3.1. COMSOLのJARファイルの場所を確認

COMSOLのJava APIは、COMSOLのインストールディレクトリ内の`plugins`フォルダにあります。

```powershell
# COMSOLのpluginsディレクトリを確認
$comsolPlugins = "C:\Program Files\COMSOL\COMSOL61\Multiphysics\plugins"
Get-ChildItem $comsolPlugins -Filter "*.jar" | Select-Object Name, Length
```

**期待される出力**:
```
Name                          Length
----                          ------
com.comsol.model_*.jar        XXXXXX
com.comsol.util_*.jar         XXXXXX
（その他多数のJARファイル）
```

### 3.2. 必要なJARファイルの特定

COMSOL APIを使用するために最低限必要なJARファイルは以下の通りです：

- `com.comsol.model_*.jar` - COMSOLモデルのコアAPI
- `com.comsol.util_*.jar` - ユーティリティクラス

```powershell
# 必要なJARファイルのフルパスを取得
$comsolPlugins = "C:\Program Files\COMSOL\COMSOL61\Multiphysics\plugins"
$modelJar = Get-ChildItem $comsolPlugins -Filter "com.comsol.model*.jar" | Select-Object -First 1
$utilJar = Get-ChildItem $comsolPlugins -Filter "com.comsol.util*.jar" | Select-Object -First 1

Write-Host "Model JAR: $($modelJar.FullName)"
Write-Host "Util JAR: $($utilJar.FullName)"
```

### 3.3. MavenローカルリポジトリへのJARファイルのインストール

`mvn install:install-file`コマンドを使用して、COMSOLのJARファイルをMavenのローカルリポジトリに登録します。

```powershell
# 変数設定
$comsolPlugins = "C:\Program Files\COMSOL\COMSOL61\Multiphysics\plugins"
$comsolVersion = "6.1"  # あなたのCOMSOLバージョンに合わせて変更

# com.comsol.model JARをインストール
$modelJar = Get-ChildItem $comsolPlugins -Filter "com.comsol.model*.jar" | Select-Object -First 1
mvn install:install-file `
  -Dfile="$($modelJar.FullName)" `
  -DgroupId=com.comsol `
  -DartifactId=comsol-model `
  -Dversion=$comsolVersion `
  -Dpackaging=jar

# com.comsol.util JARをインストール
$utilJar = Get-ChildItem $comsolPlugins -Filter "com.comsol.util*.jar" | Select-Object -First 1
mvn install:install-file `
  -Dfile="$($utilJar.FullName)" `
  -DgroupId=com.comsol `
  -DartifactId=comsol-util `
  -Dversion=$comsolVersion `
  -Dpackaging=jar
```

**期待される出力**:
```
[INFO] Installing C:\Program Files\COMSOL\...\com.comsol.model_*.jar to C:\Users\[ユーザー名]\.m2\repository\...
[INFO] BUILD SUCCESS
```

成功すれば、これらのJARファイルが`~/.m2/repository/com/comsol/`ディレクトリにコピーされます。

---

## 4. pom.xmlの設定

### 4.1. Java 21とJUnit 5の設定

まず、生成された`pom.xml`を開き、Java 21とJUnit 5を使用するように更新します。

```powershell
# pom.xmlをメモ帳で開く（またはVSCodeで開く）
notepad pom.xml
```

以下の内容で`pom.xml`の`<properties>`セクションを更新します：

```xml
<properties>
  <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  <maven.compiler.source>21</maven.compiler.source>
  <maven.compiler.target>21</maven.compiler.target>
</properties>
```

### 4.2. COMSOL依存関係の追加

`pom.xml`の`<dependencies>`セクションに、先ほどインストールしたCOMSOL JARへの依存関係を追加します。

```xml
<dependencies>
  <!-- JUnit 5 for testing -->
  <dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-api</artifactId>
    <version>5.10.2</version>
    <scope>test</scope>
  </dependency>

  <!-- COMSOL Model API -->
  <dependency>
    <groupId>com.comsol</groupId>
    <artifactId>comsol-model</artifactId>
    <version>6.1</version>
  </dependency>

  <!-- COMSOL Util API -->
  <dependency>
    <groupId>com.comsol</groupId>
    <artifactId>comsol-util</artifactId>
    <version>6.1</version>
  </dependency>
</dependencies>
```

### 4.3. ビルドプラグインの更新

JUnit 5を正しく実行するため、`maven-surefire-plugin`のバージョンを更新します。

```xml
<build>
  <pluginManagement>
    <plugins>
      <plugin>
        <artifactId>maven-surefire-plugin</artifactId>
        <version>3.2.5</version>
      </plugin>
      <plugin>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.13.0</version>
      </plugin>
    </plugins>
  </pluginManagement>
</build>
```

### 4.4. 完成版 pom.xml

以下は、すべての設定を含んだ完成版の`pom.xml`です。PowerShellから一括作成できます。

```powershell
# pom.xmlを作成（既存のものを上書き）
@"
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.example</groupId>
  <artifactId>comsol-simulation</artifactId>
  <version>1.0-SNAPSHOT</version>

  <name>comsol-simulation</name>
  <url>http://www.example.com</url>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>21</maven.compiler.source>
    <maven.compiler.target>21</maven.compiler.target>
  </properties>

  <dependencies>
    <!-- JUnit 5 for testing -->
    <dependency>
      <groupId>org.junit.jupiter</groupId>
      <artifactId>junit-jupiter-api</artifactId>
      <version>5.10.2</version>
      <scope>test</scope>
    </dependency>

    <!-- COMSOL Model API -->
    <dependency>
      <groupId>com.comsol</groupId>
      <artifactId>comsol-model</artifactId>
      <version>6.1</version>
    </dependency>

    <!-- COMSOL Util API -->
    <dependency>
      <groupId>com.comsol</groupId>
      <artifactId>comsol-util</artifactId>
      <version>6.1</version>
    </dependency>
  </dependencies>

  <build>
    <pluginManagement>
      <plugins>
        <plugin>
          <artifactId>maven-clean-plugin</artifactId>
          <version>3.1.0</version>
        </plugin>
        <plugin>
          <artifactId>maven-resources-plugin</artifactId>
          <version>3.0.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.13.0</version>
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
      </plugins>
    </pluginManagement>
  </build>
</project>
"@ | Out-File -FilePath "pom.xml" -Encoding utf8
```

### 4.5. 依存関係の解決確認

`pom.xml`を保存した後、依存関係が正しく解決されるか確認します。

```powershell
# 依存関係をダウンロード
mvn dependency:resolve

# 依存関係ツリーを表示
mvn dependency:tree
```

**期待される出力**:
```
[INFO] com.example:comsol-simulation:jar:1.0-SNAPSHOT
[INFO] +- org.junit.jupiter:junit-jupiter-api:jar:5.10.2:test
[INFO] +- com.comsol:comsol-model:jar:6.1:compile
[INFO] \- com.comsol:comsol-util:jar:6.1:compile
[INFO] BUILD SUCCESS
```

---

## 5. シンプルなCOMSOL Javaプログラムの作成

### 5.1. 最小限の動作確認プログラム

まず、COMSOLのAPIが正しく動作するか確認するため、最小限のプログラムを作成します。このプログラムは、空のCOMSOLモデルを作成し、ファイルとして保存するだけのシンプルなものです。

Mavenの標準ディレクトリ構造に従い、`src/main/java/com/example/`ディレクトリに`HelloComsol.java`を作成します。

```powershell
# パッケージディレクトリを作成
New-Item -Path "src\main\java\com\example" -ItemType Directory -Force

# HelloComsol.javaを作成
@"
package com.example;

import com.comsol.model.*;
import com.comsol.model.util.*;

public class HelloComsol {
    public static void main(String[] args) {
        System.out.println("=== COMSOL Java API 動作確認プログラム ===");

        try {
            // 空のモデルを作成
            Model model = ModelUtil.create("HelloModel");
            System.out.println("✅ モデルの作成に成功しました: " + model.tag());

            // モデルを保存
            String outputPath = "output/hello_model.mph";
            model.save(outputPath);
            System.out.println("✅ モデルを保存しました: " + outputPath);

            System.out.println("=== 実行完了 ===");

        } catch (Exception e) {
            System.err.println("❌ エラーが発生しました:");
            e.printStackTrace();
        }
    }
}
"@ | Out-File -FilePath "src\main\java\com\example\HelloComsol.java" -Encoding utf8

# ファイルが作成されたことを確認
Get-ChildItem src\main\java\com\example
```

**期待される出力**:
```
Directory: C:\Users\[ユーザー名]\Documents\comsol-simulation\src\main\java\com\example

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        2025/10/30     10:05            XXX HelloComsol.java
```

---

## 6. Mavenによるコンパイルと実行

### 6.1. Javaプログラムのコンパイル

Mavenを使用してプログラムをコンパイルします。`mvn compile`コマンドは、`src/main/java`内のすべてのJavaファイルをコンパイルし、`target/classes`ディレクトリに配置します。

```powershell
# プロジェクトをコンパイル
mvn clean compile
```

**期待される出力**:
```
[INFO] --- maven-compiler-plugin:3.13.0:compile (default-compile) @ comsol-simulation ---
[INFO] Compiling 1 source file to C:\...\comsol-simulation\target\classes
[INFO] BUILD SUCCESS
```

コンパイルが成功すると、`target/classes/com/example/`ディレクトリに`HelloComsol.class`ファイルが生成されます。

```powershell
# コンパイル結果を確認
Get-ChildItem target\classes\com\example
```

**期待される出力**:
```
Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        2025/10/30     10:10            XXX HelloComsol.class
```

### 6.2. コンパイル済みプログラムの実行（方法1: exec:java）

Mavenの`exec:java`プラグインを使用して、コンパイル済みのプログラムを実行します。

```powershell
# HelloComsolクラスを実行
mvn exec:java -Dexec.mainClass="com.example.HelloComsol"
```

**期待される出力**:
```
[INFO] --- exec-maven-plugin:3.1.0:java (default-cli) @ comsol-simulation ---
=== COMSOL Java API 動作確認プログラム ===
✅ モデルの作成に成功しました: HelloModel
✅ モデルを保存しました: output/hello_model.mph
=== 実行完了 ===
[INFO] BUILD SUCCESS
```

### 6.3. コンパイル済みプログラムの実行（方法2: COMSOLバッチモード）

COMSOL環境で実行する必要がある場合（ライセンスや特定の機能が必要な場合）は、`comsol.exe`のバッチモードを使用します。

```powershell
# COMSOLのパスを設定
$comsolExe = "C:\Program Files\COMSOL\COMSOL61\Multiphysics\bin\win64\comsol.exe"

# コンパイル済みクラスを実行
& $comsolExe batch -inputfile target\classes\com\example\HelloComsol.class
```

### 6.4. 出力ファイルの確認

実行が成功すると、`output`ディレクトリに`hello_model.mph`が作成されます。

```powershell
# 出力ファイルを確認
Get-ChildItem output
```

**期待される出力**:
```
Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        2025/10/30     10:12            XXX hello_model.mph
```

この`.mph`ファイルは、COMSOLのGUIアプリケーションで開くことができます。

---

## 7. より実践的なプログラム：簡単なジオメトリの作成

### 7.1. 3Dボックスジオメトリを作成するプログラム

次に、実際に3Dジオメトリを作成するプログラムを書いてみましょう。このプログラムは、簡単な直方体を作成し、保存します。

```powershell
# SimpleGeometry.javaを作成
@"
package com.example;

import com.comsol.model.*;
import com.comsol.model.util.*;

public class SimpleGeometry {
    public static void main(String[] args) {
        System.out.println("=== 3Dジオメトリ作成プログラム ===");

        try {
            // モデル作成
            Model model = ModelUtil.create("GeometryModel");
            System.out.println("✅ モデルを作成しました");

            // 3Dジオメトリコンポーネントを追加
            model.component().create("comp1", true);
            model.component("comp1").geom().create("geom1", 3);
            System.out.println("✅ 3Dジオメトリコンポーネントを追加しました");

            // 直方体（ブロック）を作成
            model.component("comp1").geom("geom1").create("blk1", "Block");
            model.component("comp1").geom("geom1").feature("blk1").set("size", new String[]{"0.1", "0.05", "0.02"});
            model.component("comp1").geom("geom1").feature("blk1").set("pos", new String[]{"0", "0", "0"});
            System.out.println("✅ 直方体を作成しました（サイズ: 0.1 x 0.05 x 0.02 m）");

            // ジオメトリをビルド
            model.component("comp1").geom("geom1").run();
            System.out.println("✅ ジオメトリのビルドに成功しました");

            // モデルを保存
            String outputPath = "output/geometry_model.mph";
            model.save(outputPath);
            System.out.println("✅ モデルを保存しました: " + outputPath);

            System.out.println("=== 実行完了 ===");

        } catch (Exception e) {
            System.err.println("❌ エラーが発生しました:");
            e.printStackTrace();
        }
    }
}
"@ | Out-File -FilePath "src\main\java\com\example\SimpleGeometry.java" -Encoding utf8
```

### 7.2. コンパイルと実行

```powershell
# コンパイル
mvn clean compile

# 実行（Mavenのexec:java）
mvn exec:java -Dexec.mainClass="com.example.SimpleGeometry"
```

**期待される出力**:
```
=== 3Dジオメトリ作成プログラム ===
✅ モデルを作成しました
✅ 3Dジオメトリコンポーネントを追加しました
✅ 直方体を作成しました（サイズ: 0.1 x 0.05 x 0.02 m）
✅ ジオメトリのビルドに成功しました
✅ モデルを保存しました: output/geometry_model.mph
=== 実行完了 ===
[INFO] BUILD SUCCESS
```

---

## 8. 自動化スクリプトの作成

### 8.1. 再利用可能なPowerShellスクリプト

毎回コマンドを手入力する手間を省くため、コンパイルと実行を自動化するPowerShellスクリプトを作成します。

```powershell
# run-maven-comsol.ps1を作成
@'
# Maven + COMSOL プログラム実行スクリプト
param(
    [Parameter(Mandatory=$true)]
    [string]$ClassName
)

Write-Host "=== Maven + COMSOL プログラム実行 ===" -ForegroundColor Cyan
Write-Host "対象クラス: com.example.$ClassName" -ForegroundColor Yellow

# コンパイル
Write-Host "`n[1/2] コンパイル中..." -ForegroundColor Green
mvn clean compile

if ($LASTEXITCODE -ne 0) {
    Write-Error "❌ コンパイルに失敗しました"
    exit 1
}

# 実行
Write-Host "`n[2/2] 実行中..." -ForegroundColor Green
mvn exec:java -Dexec.mainClass="com.example.$ClassName"

if ($LASTEXITCODE -ne 0) {
    Write-Error "❌ 実行に失敗しました"
    exit 1
}

Write-Host "`n✅ すべての処理が完了しました" -ForegroundColor Cyan
'@ | Out-File -FilePath "run-maven-comsol.ps1" -Encoding utf8
```

### 8.2. スクリプトの使い方

作成したスクリプトを使って、簡単にプログラムを実行できます。

```powershell
# HelloComsolを実行
.\run-maven-comsol.ps1 -ClassName HelloComsol

# SimpleGeometryを実行
.\run-maven-comsol.ps1 -ClassName SimpleGeometry
```

**PowerShellスクリプトの実行がブロックされる場合**:

初めてPowerShellスクリプトを実行する際、セキュリティポリシーによりブロックされることがあります。その場合は、以下のコマンドで実行ポリシーを変更してください。

```powershell
# 現在のユーザーのみ、ローカルスクリプトの実行を許可
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

---

## 9. プログラムの構造解説

### 9.1. COMSOL Java APIの基本構造

COMSOLのJava APIは、階層的な構造を持っています。

```java
Model model = ModelUtil.create("ModelName");
```
- `ModelUtil.create()`: 新しいモデルオブジェクトを作成します。これがすべての起点です。

```java
model.component().create("comp1", true);
```
- `component()`: モデル内のコンポーネント（物理領域や幾何学的領域）を管理します。
- `true`: 3Dコンポーネントを作成することを意味します。

```java
model.component("comp1").geom().create("geom1", 3);
```
- `geom()`: ジオメトリを管理します。
- `create("geom1", 3)`: 3次元のジオメトリを作成します（2次元なら`2`）。

```java
model.component("comp1").geom("geom1").feature().create("blk1", "Block");
```
- `feature()`: ジオメトリの具体的な形状（ブロック、円柱、球など）を追加します。
- `"Block"`: 直方体を作成します。

```java
model.save("output/model.mph");
```
- `save()`: モデルを`.mph`ファイルとして保存します。このファイルはCOMSOLのGUIで開けます。

### 9.2. よく使うジオメトリ形状

| 形状 | COMSOLでの名前 | コード例 |
|------|---------------|---------|
| 直方体 | `Block` | `create("blk1", "Block")` |
| 円柱 | `Cylinder` | `create("cyl1", "Cylinder")` |
| 球 | `Sphere` | `create("sph1", "Sphere")` |
| インポート（STL等） | `Import` | `create("imp1", "Import")` |

---

## 10. 次のステップ：実践的なシミュレーション

### 10.1. 物理場の追加

実際のシミュレーションを行うには、ジオメトリに加えて物理場（Physics）を追加する必要があります。

**例：固体力学（Solid Mechanics）の追加**:
```java
model.component("comp1").physics().create("solid", "SolidMechanics", "geom1");
```

### 10.2. 材料の定義

```java
model.component("comp1").material().create("mat1");
model.component("comp1").material("mat1").propertyGroup("def").set("youngsmodulus", "210e9");
model.component("comp1").material("mat1").propertyGroup("def").set("poissonsratio", "0.3");
```

### 10.3. メッシュの生成

```java
model.component("comp1").mesh("mesh1").create("mesh1");
model.component("comp1").mesh("mesh1").autoMeshSize(3); // 1=最も粗い、5=最も細かい
model.component("comp1").mesh("mesh1").run();
```

### 10.4. 解析の実行

```java
model.study().create("std1");
model.study("std1").create("stat", "Stationary");
model.study("std1").run();
```

これらの詳細については、別のPermanent Noteで解説します。

---

## 11. トラブルシューティング

### エラー1: Maven依存関係解決エラー「com.comsol:comsol-model が見つかりません」

**症状**:
```
[ERROR] Failed to execute goal on project comsol-simulation:
Could not resolve dependencies for project com.example:comsol-simulation:jar:1.0-SNAPSHOT:
Could not find artifact com.comsol:comsol-model:jar:6.1
```

**原因**: COMSOL JARファイルがMavenのローカルリポジトリに登録されていません。

**対処法**:
セクション3の手順に従って、COMSOL JARファイルをMavenローカルリポジトリにインストールします：

```powershell
# COMSOL JARファイルをMavenに登録
$comsolPlugins = "C:\Program Files\COMSOL\COMSOL61\Multiphysics\plugins"
$comsolVersion = "6.1"

$modelJar = Get-ChildItem $comsolPlugins -Filter "com.comsol.model*.jar" | Select-Object -First 1
mvn install:install-file `
  -Dfile="$($modelJar.FullName)" `
  -DgroupId=com.comsol `
  -DartifactId=comsol-model `
  -Dversion=$comsolVersion `
  -Dpackaging=jar
```

登録完了後、`mvn dependency:resolve`で確認してください。

---

### エラー2: コンパイルエラー「パッケージ com.comsol.model が存在しません」

**症状**:
```
[ERROR] /path/to/HelloComsol.java:[1,24] パッケージcom.comsol.modelは存在しません
import com.comsol.model.*;
                      ^
```

**原因1**: `pom.xml`にCOMSOL依存関係が追加されていません。

**対処法**:
`pom.xml`の`<dependencies>`セクションを確認し、以下の依存関係が含まれているか確認します：
```xml
<dependency>
  <groupId>com.comsol</groupId>
  <artifactId>comsol-model</artifactId>
  <version>6.1</version>
</dependency>
```

**原因2**: Maven JARファイルがローカルリポジトリに登録されていません（エラー1を参照）。

---

### エラー3: ライセンスエラー（実行時）

**症状**:
```
Could not check out a license for COMSOL Multiphysics
```

**原因**: COMSOLのライセンスサーバーに接続できないか、ライセンスが不足しています。

**対処法**:
1. COMSOLのGUIアプリケーションが正常に起動できるか確認します。
2. ライセンスマネージャーで、利用可能なライセンスがあるか確認します。
   - COMSOLメニュー: `Options` → `Preferences` → `Licenses`
3. ネットワークライセンスを使用している場合、ライセンスサーバーへの接続を確認します。

---

### エラー4: リモート環境でのコンパイルエラー

**症状**:
```
[ERROR] Failed to execute goal org.apache.maven.plugins:maven-compiler-plugin:3.13.0:compile
[ERROR] package com.comsol.model does not exist
```

または、Mavenのコンパイルは成功するが、実行時に以下のエラー：
```
Exception in thread "main" java.lang.NoClassDefFoundError: com/comsol/model/Model
```

**原因**: リモートデスクトップ環境やVPN経由でCOMSOLを使用している場合、COMSOL APIがローカルのCOMSOLインストールに依存するため、JARファイルが正しく読み込めない、またはランタイムでCOMSOLの実行環境が見つからない可能性があります。

**対処法1: COMSOLのローカルインストールを確認**

```powershell
# COMSOLのインストール確認
Test-Path "C:\Program Files\COMSOL\COMSOL61\Multiphysics"

# COMSOL JARファイルの確認
$comsolPlugins = "C:\Program Files\COMSOL\COMSOL61\Multiphysics\plugins"
Get-ChildItem $comsolPlugins -Filter "com.comsol.model*.jar"
```

**対処法2: Maven依存関係の再インストール**

リモート環境に切り替えた後は、そのマシンのCOMSOL JARファイルを再度Mavenローカルリポジトリに登録する必要があります。

```powershell
# リモートマシン上でCOMSOL JARを再登録
$comsolPlugins = "C:\Program Files\COMSOL\COMSOL61\Multiphysics\plugins"
$comsolVersion = "6.1"

# Model JARを再インストール
$modelJar = Get-ChildItem $comsolPlugins -Filter "com.comsol.model*.jar" | Select-Object -First 1
mvn install:install-file `
  -Dfile="$($modelJar.FullName)" `
  -DgroupId=com.comsol `
  -DartifactId=comsol-model `
  -Dversion=$comsolVersion `
  -Dpackaging=jar

# Util JARを再インストール
$utilJar = Get-ChildItem $comsolPlugins -Filter "com.comsol.util*.jar" | Select-Object -First 1
mvn install:install-file `
  -Dfile="$($utilJar.FullName)" `
  -DgroupId=com.comsol `
  -DartifactId=comsol-util `
  -Dversion=$comsolVersion `
  -Dpackaging=jar

# 依存関係を再解決
mvn clean dependency:resolve
```

**対処法3: COMSOLバッチモードで実行**

Mavenの`exec:java`ではなく、COMSOLのバッチモードを使用して実行します。これにより、COMSOL環境が確実にロードされます。

```powershell
# まずMavenでコンパイルのみ
mvn clean compile

# COMSOLバッチモードで実行
$comsolExe = "C:\Program Files\COMSOL\COMSOL61\Multiphysics\bin\win64\comsol.exe"
& $comsolExe batch -inputfile target\classes\com\example\HelloComsol.class
```

**対処法4: COMSOL環境変数の設定**

COMSOLの実行に必要な環境変数を明示的に設定します。

```powershell
# COMSOL環境変数を設定
$env:COMSOL_ROOT = "C:\Program Files\COMSOL\COMSOL61\Multiphysics"
$env:PATH = "$env:COMSOL_ROOT\bin\win64;$env:PATH"

# 確認
Write-Host "COMSOL_ROOT: $env:COMSOL_ROOT"

# その後Mavenで実行
mvn exec:java -Dexec.mainClass="com.example.HelloComsol"
```

**注意事項**:
- リモートデスクトップ経由の場合、ライセンスサーバーへの接続も確認してください
- VPN経由の場合、ネットワーク速度が遅いとタイムアウトする可能性があります
- 可能であれば、ローカル環境でのコンパイルと実行を推奨します

---

### エラー5: 出力ファイルが作成されない

**症状**:
プログラムはエラーなく実行されるが、`output`ディレクトリにファイルが作成されません。

**原因**: 相対パスの基準がずれている可能性があります。

**対処法**:
1. プログラム内で絶対パスを使用します：
   ```java
   String outputPath = "C:\\Users\\YourName\\comsol-java-project\\output\\model.mph";
   model.save(outputPath);
   ```
2. または、プログラム実行時のカレントディレクトリを確認します：
   ```java
   System.out.println("Current directory: " + System.getProperty("user.dir"));
   ```

---

### エラー6: PowerShellスクリプトが実行できない

**症状**:
```
.\run-comsol.ps1 : このシステムではスクリプトの実行が無効になっているため、
ファイル C:\...\run-comsol.ps1 を読み込むことができません。
```

**原因**: PowerShellのスクリプト実行ポリシーが制限されています。

**対処法**:
現在のユーザーに対してスクリプト実行を許可します：
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

確認のメッセージが表示されたら、`Y`（Yes）を入力します。

---

## 12. まとめ

このガイドでは、Windows環境でMavenを使用してCOMSOL Java APIプロジェクトをセットアップし、実行するための完全な手順を解説しました。

### 学習した内容

**環境構築**:
- ✅ MavenプロジェクトでのCOMSOL API統合方法
- ✅ COMSOL JARファイルのローカルリポジトリへの登録
- ✅ `pom.xml`でのCOMSOL依存関係の設定

**開発フロー**:
- ✅ Mavenの標準ディレクトリ構造（`src/main/java`）の使用
- ✅ `mvn clean compile`によるコンパイル
- ✅ `mvn exec:java`による実行
- ✅ PowerShellスクリプトによる自動化

**プログラミング**:
- ✅ COMSOL APIを使ったモデル作成
- ✅ 簡単な3Dジオメトリの作成方法
- ✅ 階層的なAPI構造の理解

### Mavenを使用する利点

1. **依存関係管理**: `pom.xml`で一元管理、チーム間での環境統一が容易
2. **標準化されたビルド**: どの環境でも`mvn compile`で同じようにビルド
3. **拡張性**: 追加ライブラリ（Jackson、Logbackなど）を簡単に統合可能
4. **VSCode統合**: Java Extension Packとシームレスに連携
5. **ポータビリティ**: リモート環境でも`pom.xml`とプロジェクトファイルがあれば再現可能

### プロジェクト構造のまとめ

```
comsol-simulation/
├── pom.xml                          # Maven設定ファイル
├── src/
│   ├── main/
│   │   └── java/
│   │       └── com/
│   │           └── example/
│   │               ├── HelloComsol.java
│   │               └── SimpleGeometry.java
│   └── test/
│       └── java/                    # テストコード（今後追加）
├── output/                          # COMSOL出力ファイル
├── geometry/                        # 入力ジオメトリファイル
├── config/                          # 設定ファイル
└── run-maven-comsol.ps1            # 実行自動化スクリプト
```

### リモート環境での作業のベストプラクティス

リモートデスクトップやVPN経由でCOMSOLを使用する場合の推奨事項：

1. **初期セットアップはローカルで実行**
   - ローカル環境でMavenプロジェクトを作成し、動作確認
   - `pom.xml`とソースコードをGitで管理

2. **リモート環境での再セットアップ**
   ```powershell
   # プロジェクトをクローン
   git clone <repository-url>
   cd comsol-simulation

   # リモート環境のCOMSOL JARを登録
   $comsolPlugins = "C:\Program Files\COMSOL\COMSOL61\Multiphysics\plugins"
   $comsolVersion = "6.1"

   $modelJar = Get-ChildItem $comsolPlugins -Filter "com.comsol.model*.jar" | Select-Object -First 1
   mvn install:install-file -Dfile="$($modelJar.FullName)" -DgroupId=com.comsol -DartifactId=comsol-model -Dversion=$comsolVersion -Dpackaging=jar

   $utilJar = Get-ChildItem $comsolPlugins -Filter "com.comsol.util*.jar" | Select-Object -First 1
   mvn install:install-file -Dfile="$($utilJar.FullName)" -DgroupId=com.comsol -DartifactId=comsol-util -Dversion=$comsolVersion -Dpackaging=jar

   # 依存関係を解決してコンパイル
   mvn clean compile
   ```

3. **実行方法の選択**
   - **推奨**: COMSOLバッチモードで実行（`comsol.exe batch`）
   - **代替**: Mavenの`exec:java`（ライセンス接続が安定している場合）

### 次のステップ

1. **物理場の追加**
   - 固体力学（Solid Mechanics）
   - 材料プロパティの設定
   - 境界条件の定義

2. **外部ファイルの統合**
   - STLファイルの読み込み
   - YAML設定ファイルからのパラメータ読み込み（Jacksonライブラリ使用）

3. **パラメトリックスタディの自動化**
   - パラメータ配列のループ処理
   - 結果の自動エクスポート（CSV/Excelなど）
   - バッチ処理の実装

4. **高度な機能**
   - メッシュ生成の最適化
   - 並列処理による高速化
   - ログ管理（SLF4J + Logback）

5. **リモート実行の最適化**
   - COMSOLサーバーモードの活用
   - バックグラウンドジョブ管理
   - 結果の自動同期

これらの内容については、[[PRJ-20251029-java-learning]]プロジェクトで段階的に学習していきます。

## Related Notes

- [[20251029-java-setup-vscode]] - VSCodeでのJava開発環境構築
- [[20251029-maven]] - Apache Mavenの基礎
- [[20251029-java-starter-guide-for-config]] - YAML設定ファイル読み込み
- [[PRJ-20251029-java-learning]] - Java学習プロジェクト全体
