---
name: Python:TDD
groups:
  - read
  - edit
  - browser
  - command
  - mcp
source: "project"
---

## 実装モード: TDDモード

TDDモードでは、テストファーストの思想に基づき、順を追ってテストの追加、テストの修正、そしてリファクタリングを行います。ファイル冒頭に `@tdd` などのコメントを記載しておくと、テストファーストモードであることを示す目印になります。

### 考え方: テストは仕様である

テストコードは、そのモジュールの仕様書とみなします。README や既存のテスト一覧から仕様を推測し、まず期待する結果（アサーション）を書いてユーザーに確認を取ります。

実行は以下のように、pytest を利用します：

```bash
$ pytest --maxfail=1 --disable-warnings -q
```

### テストの実装順序

テストコードは次の順序で実装します：

1. **期待する結果（アサーション）**  
   最初に、関数の出力や振る舞いの期待値を書き出します。
2. **期待結果の妥当性確認**  
   ここで、ユーザーに結果の妥当性を確認してもらいます。
3. **操作（Act）のコードを書く**  
   テスト対象の関数やメソッドの呼び出しコードを記述します。
4. **必要な準備（Arrange）のコードを書く**  
   モックや初期設定など、実行前の準備コードを追加します（必要な場合）。

この実装手順は、実際の実行順序（Arrange → Act → Assert）とは異なります。結果から実装を始めることで、目的を明確にしながらコードを進められます。

### 実装例

以下は、ユーザー情報取得機能の TDD サイクルの例です。ここでは、`returns` などの Result 型ライブラリ（または自作の Result クラス）を利用して、成功と失敗を表現しています。

```python
# @script @tdd
from typing import NamedTuple, Union
import pytest

# 型定義
class User(NamedTuple):
    id: str
    name: str

class UnauthorizedError(Exception):
    pass

class NetworkError(Exception):
    pass

# Result 型の簡易実装例
class Result:
    def __init__(self, ok: bool, value: Union[User, Exception]):
        self._ok = ok
        self._value = value

    def is_ok(self) -> bool:
        return self._ok

    def is_err(self) -> bool:
        return not self._ok

    def unwrap(self) -> User:
        if self._ok:
            return self._value
        raise self._value

    def unwrap_err(self) -> Exception:
        if not self._ok:
            return self._value
        raise Exception("Called unwrap_err on an ok result")

# インターフェース定義（型シグネチャのみ）
def get_user(token: str, user_id: str) -> Result:
    """
    get_user は、与えられたトークンとユーザーIDから、ユーザー情報を取得する。
    実装は後から追加する（初期状態は WIP）。
    """
    raise NotImplementedError("wip")

# テストコード（pytest を利用）
def test_get_user_with_valid_token():
    # 1. 期待する結果を定義
    expected_user = User(id="1", name="Test User")
    
    # 2. ユーザーに期待結果の妥当性を確認してもらう
    # （ここで仕様確認のコメントやレビューを依頼）

    # 3. 操作コードを書く
    result = get_user("valid-token", "1")
    
    # 4. 準備コードは必要な場合のみ（今回は不要）
    
    # アサーション
    assert result.is_ok(), "結果は成功であるべき"
    user = result.unwrap()
    assert user == expected_user, f"取得したユーザーは {expected_user} であるべき"

def test_get_user_with_invalid_token():
    # 1. 期待する結果を定義
    expected_error = UnauthorizedError("Invalid token")
    
    # 2. ユーザーに期待結果の妥当性を確認してもらう
    
    # 3. 操作コードを書く
    result = get_user("invalid-token", "1")
    
    # アサーション
    assert result.is_err(), "結果はエラーであるべき"
    error = result.unwrap_err()
    assert isinstance(error, UnauthorizedError), "エラーの型は UnauthorizedError であるべき"
    assert str(error) == str(expected_error), "エラーメッセージが一致すること"
```

### テストとアサーションの命名規約

テスト名は以下の形式で記述してください：

```
「{状況}の場合に{操作}をすると{結果}になること」
```

例：
- 「有効なトークンの場合にユーザー情報を取得すると成功すること」
- 「無効なトークンの場合にユーザー情報を取得するとエラーになること」

### 開発手順の詳細

1. **型シグネチャの定義**  
   テストコードを書く前に、関数の型シグネチャを定義し、ドキュメントコメント（docstring）で仕様を明示します。  
   例:
   ```python
   def get_user(token: str, user_id: str) -> Result:
       ...
   ```
   ライブラリとして利用する場合は、`__all__` に含めるか、明示的に export します。

2. **テストケースごとの実装**  
   a. 期待する結果（ユーザー情報やエラー）を定義します。  
   b. ユーザーに結果の妥当性を確認してもらい、仕様が合意されたら操作コードを実装します。  
   c. 必要な場合は、モックのセットアップなど準備コードを記述します。

3. **リファクタリングの提案**  
   テストが成功した後、静的解析（mypy, flake8 など）やコードフォーマットツールを実行し、ユーザーにリファクタリングの提案を行ってください。

---

## Python における TDD のワークフロー

### ディレクトリ構成の例

```
tdd_example/
  ├── __init__.py    # パッケージの公開インターフェース（必要に応じて）
  ├── lib.py         # 実装コード（内部実装、モックなどは deps.py 経由で管理）
  └── test_lib.py    # テストコード
```

### TDD 実践の手順

1. **テストを書く**  
   `test_lib.py` に、期待される動作を記述したテストケースを実装します（上記の例参照）。

2. **テストの失敗を確認する**  
   実装がまだない状態でテストを実行し、失敗することを確認します。
   ```bash
   pytest --maxfail=1 --disable-warnings -q
   ```

3. **コードを実装する**  
   `lib.py` にテストケースを満たす最小限の実装を追加し、`NotImplementedError` を削除します。

4. **テストの成功を確認する**  
   再度テストを実行し、全テストがパスすることを確認します。

5. **リファクタリング**  
   コード整形ツール（black、flake8 など）や静的解析ツール（mypy）を使ってコードの品質を向上させ、必要に応じて Git コミットを行います。

6. **カバレッジ測定とデッドコード解析**  
   `pytest --cov` や coverage.py を利用してカバレッジを確認し、十分なテストカバレッジが得られているか検証します。

### Git ワークフローの活用

1. **作業前に変更状態を確認**
   ```bash
   git status
   ```
2. **テスト追加後にコミット**
   ```bash
   git add <変更ファイル>
   git commit -m "test: 新規テストケースを追加"
   ```
3. **実装後にコミット**
   ```bash
   git add <変更ファイル>
   git commit -m "feat: get_user の実装を追加"
   ```
4. **リファクタリング後にコミット**
   ```bash
   git add <変更ファイル>
   git commit -m "refactor: コードの整理とリファクタリング"
   ```

### TypeFirst モード

型を先に定義する TypeFirst モードでは、以下の手順で進めます。

1. **型シグネチャとテストコードを先に作成**  
   - まず関数の型シグネチャ（例: get_user）とそれに対するテストケースを記述し、型チェックのみでパスすることを確認します。
2. **ユーザーに型シグネチャを提案**  
   - 型シグネチャが確定したら、その仕様に基づくテストコードを追加し、仕様を合意する。
3. **実装に移行**  
   - 合意された型シグネチャとテストケースに基づき、実装コードを記述しテストが通るようにする。

