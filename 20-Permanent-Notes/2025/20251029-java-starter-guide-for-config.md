---
id: 202510290
created: 2025-10-29
updated: 2025-10-29
tags: [permanent, java, programming, guide, yaml, jackson]
type: permanent-note
status: draft
confidence: high
source_type: knowledge
---

# Java入門：JacksonライブラリでYAML設定ファイルを読み込む

このガイドでは、Mavenで管理されたJavaプロジェクトで、外部設定ファイル（`config.yml`）を読み込み、その内容をコンソールに表示する簡単なアプリケーションの作成手順を解説します。

## 🎯 目標

Javaアプリケーションから`config.yml`を読み込み、各パラメータの値とデータ型を整形して表示します。これにより、JavaでのファイルI/O、外部ライブラリの利用方法、およびPOJOへのデータマッピングの基本を学びます。

## 事前準備

- JDK (Java Development Kit) 21
- VSCode と Extension Pack for Java
- Maven
- `maven-archetype-quickstart` を用いて作成された基本的なプロジェクト構造

## 1. 依存関係の追加 (`pom.xml`)

YAMLを解析するため、ライブラリ **Jackson Databind** を利用します。プロジェクトのルートにある `pom.xml` を開き、`<dependencies>`セクションに以下の内容を追加してください。

```xml
<dependencies>
  <!-- JUnit 5 for testing -->
  <dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-api</artifactId>
    <version>5.10.2</version>
    <scope>test</scope>
  </dependency>

  <!-- Jackson for YAML parsing -->
  <dependency>
    <groupId>com.fasterxml.jackson.dataformat</groupId>
    <artifactId>jackson-dataformat-yaml</artifactId>
    <version>2.17.1</version>
  </dependency>
  <dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.17.1</version>
  </dependency>
</dependencies>
```
**ポイント**: `pom.xml`を保存すると、VSCodeが自動的にMavenの依存関係をダウンロードします。

## 2. ファイルの作成と配置

### 2.1. ディレクトリ構造

Mavenの規約に従い、ファイルは以下のように配置します。`resources`ディレクトリがない場合は作成してください。

```
my-app
└── src
    ├── main
    │   ├── java/com/example
    │   │   ├── ParametricStudyApp.java  (メインクラス)
    │   │   └── StudyConfig.java         (POJOクラス)
    │   └── resources
    │       └── config.yml             (設定ファイル)
    └── test
```

### 2.2. 設定ファイル (`config.yml`)

`src/main/resources/config.yml` を作成します。

```yaml
studyName: OptimizationRun_A
version: 1.2
iterations: 1000
learningRate: 0.015
tolerance: 1.0e-6
useCuda: true
targetModels:
  - LinearRegression
  - SupportVectorMachine
  - RandomForest
environment:
  tempDirectory: /tmp/study_data
  threads: 8
  debugMode: false
```

### 2.3. データクラス (`StudyConfig.java`)

`src/main/java/com/example/StudyConfig.java` を作成します。このクラスは`config.yml`の構造と対応しており、JacksonがYAMLのデータをこのクラスのオブジェクトにマッピングします。

```java
package com.example;

import java.util.List;
import java.util.Map;

public class StudyConfig {
    private String studyName;
    private double version;
    private int iterations;
    private double learningRate;
    private double tolerance;
    private boolean useCuda;
    private List<String> targetModels;
    private Map<String, Object> environment;

    // Getters and Setters are required for Jackson.
    // The full implementation is omitted for brevity.
}
```

このクラスの構造（フィールド、ゲッター/セッター）は **JavaBeans** と呼ばれる設計パターンに従っています。詳細は以下のノートで解説しています。

![[20251029-java-pojo-and-javabeans]]

### 2.4. メインアプリケーション (`ParametricStudyApp.java`)

`src/main/java/com/example/ParametricStudyApp.java` を作成します。これがアプリケーションのエントリーポイントです。

```java
package com.example;

import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.dataformat.yaml.YAMLFactory;
import java.io.InputStream;
import java.lang.reflect.Field;

public class ParametricStudyApp {

    public static void main(String[] args) {
        String fileName = "config.yml";
        System.out.println("✅ パラメーター設定ファイルの読み込みを開始します: " + fileName);

        ObjectMapper mapper = new ObjectMapper(new YAMLFactory());

        try (InputStream is = ParametricStudyApp.class.getClassLoader().getResourceAsStream(fileName)) {
            if (is == null) {
                System.err.println("エラー: " + fileName + " がクラスパスに見つかりません。");
                return;
            }

            StudyConfig config = mapper.readValue(is, StudyConfig.class);
            printConfigDetails(config);
            System.out.println("⭐ 正常にパラメトリックスタディの設定を読み込みました。");

        } catch (Exception e) {
            System.err.println("❌ 設定ファイルの読み込み中にエラーが発生しました。");
            e.printStackTrace();
        }
    }

    private static void printConfigDetails(StudyConfig config) {
        System.out.println("--------------------------------------------------");
        for (Field field : StudyConfig.class.getDeclaredFields()) {
            try {
                field.setAccessible(true);
                System.out.printf("  %-20s | Type: %-17s | Value: %s%n",
                        field.getName(),
                        field.getType().getSimpleName(),
                        field.get(config));
            } catch (IllegalAccessException e) {
                e.printStackTrace();
            }
        }
        System.out.println("--------------------------------------------------");
    }
}
```

## 3. 実行

1.  VSCodeで `ParametricStudyApp.java` ファイルを開きます。
2.  `main` メソッドの横にある **▶ Run** ボタンをクリックします。
3.  ターミナルに以下の様な出力が表示されれば成功です。

### 実行結果（想定）
```
✅ パラメーター設定ファイルの読み込みを開始します: config.yml
--------------------------------------------------
  studyName            | Type: String              | Value: OptimizationRun_A
  version              | Type: double              | Value: 1.2
  iterations           | Type: int                 | Value: 1000
  learningRate         | Type: double              | Value: 0.015
  tolerance            | Type: double              | Value: 1.0E-6
  useCuda              | Type: boolean             | Value: true
  targetModels         | Type: List                | Value: [LinearRegression, SupportVectorMachine, RandomForest]
  environment          | Type: Map                 | Value: {tempDirectory=/tmp/study_data, threads=8, debugMode=false}
--------------------------------------------------
⭐ 正常にパラメトリックスタディの設定を読み込みました。
```

## 4. メインアプリケーションのコード解説

`ParametricStudyApp.java` は、このアプリケーションの中心的な処理を担います。コードをステップごとに見ていきましょう。

### `main` メソッド

```java
public static void main(String[] args) {
    // ... mainメソッドの処理 ...
}
```
- `public static void main(String[] args)` は、Javaアプリケーションが実行されるときに最初に呼び出される**エントリーポイント**です。すべての処理はこのメソッドから始まります。

### 1. Jacksonの準備
```java
ObjectMapper mapper = new ObjectMapper(new YAMLFactory());
```
- `ObjectMapper`: Jacksonライブラリの中核をなすクラスです。JSON、XML、そして今回のようなYAMLなど、さまざまな形式のデータをJavaオブジェクトに変換（デシリアライズ）したり、その逆（シリアライズ）を行ったりします。
- `YAMLFactory`: `ObjectMapper`に「これから扱うのはYAML形式のデータだよ」と教えるための部品です。これを渡すことで、`mapper`がYAMLを正しく解釈できるようになります。

### 2. 設定ファイルの読み込み
```java
try (InputStream is = ParametricStudyApp.class.getClassLoader().getResourceAsStream(fileName)) {
    // ... ファイル読み込み処理 ...
} catch (Exception e) {
    // ... エラー処理 ...
}
```
- `try-with-resources`文: `()`内で宣言されたリソース（ここでは`InputStream`）は、`try`ブロックが終了すると自動的にクローズ（`close()`）されます。これにより、ファイルハンドルの解放漏れを防ぎ、より安全なコードになります。
- `getResourceAsStream(fileName)`: Mavenプロジェクトでは、`src/main/resources`ディレクトリ内のファイルはビルド時に**クラスパス**に含まれます。このメソッドは、そのクラスパス上からファイルを探し出し、内容をストリームとして読み込むための標準的な方法です。

### 3. YAMLからJavaオブジェクトへの変換
```java
StudyConfig config = mapper.readValue(is, StudyConfig.class);
```
- `mapper.readValue(...)`: これが**デシリアライズ**処理の核心部です。
    1. `is` (InputStream): 読み込んだ`config.yml`のデータ。
    2. `StudyConfig.class`: 変換先のJavaクラス。
- Jacksonは`config.yml`のキー（例: `studyName`）と`StudyConfig`クラスのフィールド名を照合し、対応するセッター（`setStudyName(...)`）を呼び出して、自動的に値を設定していきます。

### 4. 結果の表示 (`printConfigDetails`メソッド)
```java
private static void printConfigDetails(StudyConfig config) {
    // ... 表示処理 ...
}
```
このメソッドは、受け取った`config`オブジェクトの内容を整形して表示します。

```java
for (Field field : StudyConfig.class.getDeclaredFields()) {
    field.setAccessible(true);
    System.out.printf("  %-20s | Type: %-17s | Value: %s%n",
            field.getName(),
            field.getType().getSimpleName(),
            field.get(config));
}
```
- **Javaリフレクション**: ここでは少し高度なテクニックである「リフレクション」が使われています。
    - `StudyConfig.class.getDeclaredFields()`: `StudyConfig`クラスに定義されているすべてのフィールド（`studyName`, `version`など）の情報を配列として取得します。
    - `field.setAccessible(true)`: フィールドが`private`であっても、このコードからアクセスできるように許可します。
    - `field.getName()`: フィールド名を取得します（例: "studyName"）。
    - `field.getType().getSimpleName()`: フィールドのデータ型名を取得します（例: "String"）。
    - `field.get(config)`: `config`オブジェクトから、そのフィールドの実際の値を取得します。
- これにより、`StudyConfig`クラスに新しいフィールドを追加しても、この表示メソッドを修正することなく、自動的にすべてのフィールド情報が表示されます。
