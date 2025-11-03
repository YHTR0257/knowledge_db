---
id: 202510300
created: 2025-10-30
updated: 2025-10-30
tags: [permanent, comsol, java, api, simulation, setup, maven, yaml, batch]
type: permanent-note
status: active
confidence: high
source_type: knowledge
---

# COMSOL Java API実行ガイド：Mavenとcomsol.exeによる梁の圧縮解析

COMSOLのJava APIを利用したシミュレーション自動化において、外部ファイル（例: YAML）からパラメータを読み込むことは、再利用性と柔軟性を高める上で非常に重要です。しかし、YAMLファイルを読み取るには外部ライブラリ（例: SnakeYAML）が必要となり、依存関係の管理が課題となります。

このガイドでは、**Maven** を使ってSnakeYAMLなどの依存関係を管理しつつ、**`comsol.exe`のバッチ機能** を使ってコンパイル・実行する、堅牢でポータブルな手法を「梁の圧縮解析」という具体的な例で解説します。

## なぜこの方法か？

- **依存関係の簡潔な管理**: `pom.xml` にライブラリを記述するだけで、Mavenが自動でダウンロード・管理してくれます。
- **環境の非汚染**: `mvn install:install-file` でローカルのMavenリポジトリ (`~/.m2`) を変更する必要がありません。
- **COMSOL環境の確実な利用**: `comsol.exe` を介して実行することで、ライセンスや必要なネイティブリソースが確実に読み込まれます。
- **ポータビリティ**: プロジェクトを別のマシンに移動しても、COMSOLがインストールされていれば `pom.xml` のパスを修正するだけで動作します。
- **モダンなJava環境**: Java 21の最新機能とパフォーマンス向上を活用できます。

---

## 実装方法：梁の圧縮解析

### 1. プロジェクト構造

シミュレーションごとにディレクトリを分け、必要なファイルを整理します。STLファイルは不要になりました。

```
simulations/comsol/20251030_beam_compression_001/
├─ pom.xml                     # Maven設定ファイル
├─ src/
│  └─ main/
│     └─ java/
│        └─ BeamCompressionAnalysis.java # Javaソースコード
├─ params.yaml                 # シミュレーションパラメータ
├─ output/                     # 結果出力先
└─ run.bat                     # 実行用バッチファイル
```

### 2. pom.xml

- **groupId**: `com.github.username` 形式を使用（自分のGitHubユーザー名に置き換えてください）
- **Java バージョン**: Java 21を使用（モダンなJava機能とパフォーマンス向上）
- **SnakeYAML**: Mavenリポジトリから最新安定版（2.3）を取得します。
- **COMSOL JARs**: `<scope>system</scope>` を使い、ローカルのCOMSOLインストールディレクトリから直接参照します。
- **maven-shade-plugin**: SnakeYAMLを最終的なJARファイルに含めるために使用します（バージョン3.6.0）。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
         http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    
    <groupId>com.github.username</groupId>
    <artifactId>beam-compression-analysis</artifactId>
    <version>1.0-SNAPSHOT</version>
    
    <properties>
        <maven.compiler.source>21</maven.compiler.source>
        <maven.compiler.target>21</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <!-- COMSOLのインストールパスを環境に合わせて変更 -->
        <comsol.home>C:\Program Files\COMSOL\COMSOL61\Multiphysics</comsol.home>
    </properties>
    
    <dependencies>
        <!-- YAML読み込み用ライブラリ -->
        <dependency>
            <groupId>org.yaml</groupId>
            <artifactId>snakeyaml</artifactId>
            <version>2.3</version>
        </dependency>
        
        <!-- COMSOLのJARファイル（システムパスで指定） -->
        <dependency>
            <groupId>com.comsol</groupId>
            <artifactId>comsol-model</artifactId>
            <version>6.1</version>
            <scope>system</scope>
            <systemPath>${comsol.home}/plugins/com.comsol.model_6.1.0.jar</systemPath>
        </dependency>
        
        <dependency>
            <groupId>com.comsol</groupId>
            <artifactId>comsol-util</artifactId>
            <version>6.1</version>
            <scope>system</scope>
            <systemPath>${comsol.home}/plugins/com.comsol.util_6.1.0.jar</systemPath>
        </dependency>
    </dependencies>
    
    <build>
        <plugins>
            <!-- 依存関係（SnakeYAML）をJARに含める -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>3.6.0</version>
                <executions>
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>shade</goal>
                        </goals>
                        <configuration>
                            <transformers>
                                <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                                    <mainClass>BeamCompressionAnalysis</mainClass>
                                </transformer>
                            </transformers>
                            <artifactSet>
                                <excludes>
                                    <exclude>com.comsol:*</exclude>
                                </excludes>
                            </artifactSet>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
</project>
```

### 3. params.yaml

梁の寸法、物性値、境界条件などを定義します。

```yaml
geometry:
  width: 0.02  # [m]
  depth: 0.05  # [m]
  height: 0.1   # [m]

material:
  young_modulus: 210e9
  poisson_ratio: 0.3
  name: Steel

loading:
  type: boundary_load
  direction: [0, 0, -1] # Z軸の負の方向（圧縮）
  magnitude: 1e6      # [Pa] 
  
boundary_conditions:
  # COMSOLのBlockジオメトリの面番号は環境により異なる場合があります
  # 一般的に、底面(z=0)と上面(z=height)に対応する面番号を指定します
  fixed:
    surfaces: [3]  # 底面を固定（環境に応じて調整が必要な場合あり）
  loaded:
    surfaces: [5]  # 上面に荷重（環境に応じて調整が必要な場合あり）

mesh:
  element_size: normal # (finer, fine, normal, coarse, coarser)

study:
  type: stationary
  
output:
  csv_file: output/stress_strain.csv
  mph_file: output/model.mph
```

### 4. BeamCompressionAnalysis.java

YAMLファイルを読み込み、梁のモデルをプログラムで構築・実行します。Java 21の機能を活用した実装です。

```java
import com.comsol.model.*;
import com.comsol.model.util.*;
import org.yaml.snakeyaml.Yaml;

import java.io.FileInputStream;
import java.io.InputStream;
import java.util.Map;
import java.util.List;

public class BeamCompressionAnalysis {
    
    private Map<String, Object> params;
    
    public BeamCompressionAnalysis(String yamlPath) throws Exception {
        Yaml yaml = new Yaml();
        try (InputStream inputStream = new FileInputStream(yamlPath)) {
            // SnakeYAML 2.x では型安全性のため明示的に型を指定
            this.params = yaml.loadAs(inputStream, Map.class);
        }
    }
    
    @SuppressWarnings("unchecked")
    private <T> T getParam(String... keys) {
        Map<String, Object> current = params;
        for (int i = 0; i < keys.length - 1; i++) {
            current = (Map<String, Object>) current.get(keys[i]);
            if (current == null) return null;
        }
        return (T) current.get(keys[keys.length - 1]);
    }
    
    public Model run() {
        System.out.println("=== COMSOL Beam Compression Analysis ===");
        
        Model model = ModelUtil.create("BeamCompressionModel");
        model.modelPath(".");
        
        setupGeometry(model);
        setupMaterial(model);
        setupPhysics(model);
        setupMesh(model);
        setupStudy(model);
        exportResults(model);
        
        System.out.println("=== 計算完了 ===");
        return model;
    }
    
    private void setupGeometry(Model model) {
        System.out.println("ジオメトリ（梁）を設定中...");
        Double width = getParam("geometry", "width");
        Double depth = getParam("geometry", "depth");
        Double height = getParam("geometry", "height");
        
        model.geom().create("geom1", 3);
        model.geom("geom1").feature().create("blk1", "Block");
        model.geom("geom1").feature("blk1").set("size", new String[]{width.toString(), depth.toString(), height.toString()});
        // 梁の底面中心が原点に来るように配置
        model.geom("geom1").feature("blk1").set("pos", new String[]{String.valueOf(-width/2), String.valueOf(-depth/2), "0"});
        model.geom("geom1").run();
        
        System.out.printf("  - 直方体を作成: %.3f x %.3f x %.3f [m]%n", width, depth, height);
    }
    
    private void setupMaterial(Model model) {
        System.out.println("材料を設定中...");
        String youngModulus = getParam("material", "young_modulus").toString();
        String poissonRatio = getParam("material", "poisson_ratio").toString();
        String materialName = getParam("material", "name");
        
        model.material().create("mat1");
        model.material("mat1").label(materialName);
        model.material("mat1").propertyGroup("def").set("youngsmodulus", youngModulus);
        model.material("mat1").propertyGroup("def").set("poissonsratio", poissonRatio);
        model.material("mat1").selection().all();
        System.out.println("  - 材料: " + materialName);
    }
    
    private void setupPhysics(Model model) {
        System.out.println("物理場を設定中...");
        model.physics().create("solid", "SolidMechanics", "geom1");
        
        List<Integer> fixedSurfaces = getParam("boundary_conditions", "fixed", "surfaces");
        model.physics("solid").feature().create("fix1", "Fixed", 2);
        model.physics("solid").feature("fix1").selection().set(fixedSurfaces.stream().mapToInt(i->i).toArray());
        System.out.println("  - 固定境界: 面 " + fixedSurfaces);
        
        List<Integer> loadedSurfaces = getParam("boundary_conditions", "loaded", "surfaces");
        List<Number> direction = getParam("loading", "direction");
        Number magnitude = getParam("loading", "magnitude");
        
        model.physics("solid").feature().create("load1", "BoundaryLoad", 2);
        model.physics("solid").feature("load1").selection().set(loadedSurfaces.stream().mapToInt(i->i).toArray());
        String[] force = {
            String.valueOf(direction.get(0).doubleValue() * magnitude.doubleValue()),
            String.valueOf(direction.get(1).doubleValue() * magnitude.doubleValue()),
            String.valueOf(direction.get(2).doubleValue() * magnitude.doubleValue())
        };
        model.physics("solid").feature("load1").set("FperArea", force);
        System.out.println("  - 荷重: " + magnitude + " Pa on " + loadedSurfaces);
    }
    
    private void setupMesh(Model model) {
        System.out.println("メッシュを生成中...");
        String elementSize = getParam("mesh", "element_size");
        model.mesh().create("mesh1", "geom1");
        model.mesh("mesh1").feature("size").set("predefined", elementSize);
        model.mesh("mesh1").run();
    }
    
    private void setupStudy(Model model) {
        System.out.println("スタディを実行中...");
        model.study().create("std1");
        model.study("std1").feature().create("stat", "Stationary");
        model.study("std1").run();
    }
    
    private void exportResults(Model model) {
        System.out.println("結果を出力中...");
        String csvFile = getParam("output", "csv_file");
        String mphFile = getParam("output", "mph_file");
        
        if (csvFile != null) {
            model.result().export().create("data1", "Data");
            model.result().export("data1").set("filename", csvFile.replace("/", "\\"));
            // ここで評価する式や対象などを設定する必要があるが、今回は省略
            // model.result().export("data1").set("expr", new String[]{"solid.mises"});
            model.result().export("data1").run();
            System.out.println("  - CSVファイル: " + csvFile);
        }
        
        if (mphFile != null) {
            model.save(mphFile.replace("/", "\\"));
            System.out.println("  - モデルファイル: " + mphFile);
        }
    }
    
    public static void main(String[] args) {
        try {
            String yamlPath = args.length > 0 ? args[0] : "params.yaml";
            System.out.println("パラメータファイル: " + yamlPath);
            BeamCompressionAnalysis analysis = new BeamCompressionAnalysis(yamlPath);
            analysis.run();
        } catch (Exception e) {
            e.printStackTrace();
            System.exit(1);
        }
    }
}
```

### 5. ビルドと実行 (`run.bat`)

このバッチファイルが、Mavenでのビルドから`comsol.exe`での実行までを自動化します。

```batch
@echo off
REM ========================================
REM COMSOL Beam Compression Analysis 実行スクリプト
REM ========================================

REM --- 環境設定 ---
set COMSOL_BIN="C:\Program Files\COMSOL\COMSOL61\Multiphysics\bin\win64\comsol.exe"
set PROJECT_DIR=%~dp0

REM --- 実行 ---
cd /d "%PROJECT_DIR%"

echo [1/3] Maven でプロジェクトをビルド中...
call mvn clean package -q
if %ERRORLEVEL% NEQ 0 (
    echo [エラー] Mavenのビルドに失敗しました。
    pause
    exit /b 1
)
echo [成功] JARファイルを生成しました。
echo.

echo [2/3] COMSOLをバッチモードで実行中... (開始: %time%)
%COMSOL_BIN% batch -inputfile target\beam-compression-analysis-1.0-SNAPSHOT.jar -classname BeamCompressionAnalysis -args params.yaml > comsol.log 2>&1

echo [完了] COMSOLの実行が終了しました。(終了: %time%)
echo.

echo [3/3] 結果を確認中...
if exist "output\model.mph" (
    echo [成功] 結果ファイルが正常に生成されました。
    echo   - output\model.mph
    echo   - output\stress_strain.csv
    echo   - comsol.log
) else (
    echo [エラー] 結果ファイルが見つかりません。comsol.log を確認してください。
    echo.
    type comsol.log
)
echo.

echo 処理完了。
pause
```

---

## `comsol.exe batch` コマンドの詳細解説

`comsol.exe batch` は、COMSOLをGUIなしで実行するための強力なコマンドです。

- **`batch`**: COMSOLをバッチモードで起動します。
- **`-inputfile <file>`**: 実行するファイルを指定します。`.java`, `.class`, `.jar`, `.mph` などが指定可能です。
- **`-classname <class>`**: `-inputfile` がJARファイルの場合に、実行するメインクラスを指定します。
- **`-args <arguments...>`**: Javaプログラムの `main` メソッドに渡すコマンドライン引数を指定します。
- **`> comsol.log 2>&1`**: 標準出力と標準エラー出力を `comsol.log` ファイルにリダイレクトし、実行記録を残します。

---

## トラブルシューティング

- **Javaバージョンエラー**:
  - **原因**: システムにJava 21がインストールされていない、または環境変数が正しく設定されていません。
  - **解決策**: `java -version` でJava 21以上がインストールされているか確認してください。Mavenも対応するバージョン（3.6.3以上推奨）を使用してください。

- **`ClassNotFoundException: org.yaml.snakeyaml.Yaml`**:
  - **原因**: SnakeYAMLライブラリがクラスパスに含まれていません。
  - **解決策**: `pom.xml`の`maven-shade-plugin`の設定を確認してください。`mvn clean package`が正常に完了しているか確認します。

- **`FileNotFoundException: params.yaml`**:
  - **原因**: `run.bat` を実行したディレクトリに `params.yaml` が存在しません。
  - **解決策**: `run.bat` と同じ階層に `params.yaml` を配置してください。

- **ライセンスエラー**:
  - **原因**: COMSOLのライセンスサーバーに接続できない、またはライセンスが不足しています。
  - **解決策**: COMSOLのGUIを起動してライセンスが正常に取得できるか確認してください。

- **groupId設定について**:
  - `pom.xml`の`<groupId>com.github.username</groupId>`は、実際のGitHubユーザー名に置き換えてください。
  - 個人プロジェクトの場合、`com.github.<your-username>`の命名規則が推奨されます。

## 関連ノート
- [[20251029-java-setup-vscode]] - VSCodeでのJava開発環境構築
- [[20251029-maven]] - Apache Mavenの基礎
- [[20251029-java-starter-guide-for-config]] - YAML設定ファイル読み込み
- [[PRJ-20251029-java-learning]] - Java学習プロジェクト全体
