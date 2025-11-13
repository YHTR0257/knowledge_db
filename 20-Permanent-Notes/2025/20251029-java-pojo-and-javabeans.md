---
id: 202510291
created: 2025-10-29
updated: 2025-10-29
tags: [permanent, java, programming, concept]
type: permanent-note
status: draft
confidence: high
source_type: knowledge
---

# Java: POJOとJavaBeans (ゲッター/セッター)

## Core Idea
POJO（Plain Old Java Object）は、特定のフレームワークに依存しない基本的なJavaオブジェクトであり、フィールド、コンストラクタ、ゲッター/セッターで構成される。この構造はJavaBeansの規約に従っており、データのカプセル化とフレームワークからの利用を容易にする。

## Context
設定ファイル（YAML/JSON）、データベースのレコード、APIのレスポンスといった外部データをJavaアプリケーション内で構造化されたオブジェクトとして扱う際に、このPOJO/JavaBeansパターンが広く利用される。

## Key Points
- **POJO**: 特定の要件を持たない、再利用可能な基本的なJavaオブジェクト。
- **JavaBeans規約**: フレームワークがオブジェクトを自動的に操作するための設計ルール（デフォルトコンストラクタ、ゲッター/セッターの命名規則など）。
- **カプセル化**: `private`なフィールドと`public`なゲッター/セッターを通じてデータへのアクセスを制御し、オブジェクトの整合性を保つ。

## `StudyConfig.java` の構造解説

### 1. フィールド (Properties)
クラスが保持するデータ。`private`で宣言し、外部からの直接的なアクセスを防ぎます（カプセル化）。

```java
public class StudyConfig {
    private String studyName;        // 研究名
    private double version;          // バージョン番号
    private int iterations;          // 反復回数
    private double learningRate;     // 学習率
    private double tolerance;        // 許容誤差
    private boolean useCuda;         // CUDA（GPU）を使用するか
    private List<String> targetModels;           // 対象モデルのリスト
    private Map<String, Object> environment;     // 環境設定（キー・バリュー）
    // ...
}
```

### 2. デフォルトコンストラクタ
引数を持たないコンストラクタです。Jacksonのようなライブラリが、まず空のオブジェクトを作成してからセッターを使ってデータを注入するために必要とします。

```java
public StudyConfig() {
    // 引数なしでオブジェクトを生成するために必要
}
```

### 3. ゲッター (Getters)
`private`なフィールドの値を外部から**取得**するためのメソッドです。
- **命名規則**: `get + フィールド名（先頭大文字）`
- **boolean型の場合**: 慣例として `is + フィールド名` が使われることもあります (`isUseCuda()`)

```java
// studyNameフィールドの値を取得する
public String getStudyName() {
    return studyName;
}

// useCudaフィールドの値を取得する
public boolean isUseCuda() {
    return useCuda;
}
```

### 4. セッター (Setters)
`private`なフィールドに外部から値を**設定**するためのメソッドです。
- **命名規則**: `set + フィールド名（先頭大文字）`
- `this.fieldName`はクラスのフィールドを、`fieldName`はメソッドの引数を指します。

```java
// studyNameフィールドに値を設定する
public void setStudyName(String studyName) {
    this.studyName = studyName;
}
```

## JavaBeansパターンのメリット

1.  **カプセル化**: 内部データを保護し、不正な値が設定されるのを防ぎます。セッター内にバリデーションロジックを追加することも可能です。
    ```java
    public void setIterations(int iterations) {
        if (iterations < 0) {
            throw new IllegalArgumentException("反復回数に負の値は設定できません");
        }
        this.iterations = iterations;
    }
    ```
2.  **フレームワークとの親和性**: Jackson (JSON/YAML)、Hibernate (DB)、Springなどの多くのフレームワークが、リフレクションを用いて自動的にゲッター/セッターを呼び出し、オブジェクトの読み書きを行います。
3.  **可読性と再利用性**: データ構造が明確になり、コードの可読性と再利用性が向上します。
