## インデックスに表現力を持たせる
- froenset()
  - 変更、追加ができないset型
- OrderdDict()
  - 順序担保制List
  - 昔の書き方
- defaultdict()
  - 存在しない値に対して、自動でインデックスを与える
- Counter()
  - 配列の各要素、もしくはstringに対するcharに対する頻度を戻す
  - 要素をキー、その頻度を値として持つ
## pythonic style
- 一般的なpython記述者が理解できる形
- 内包表現は許可されていることが多い
  - `authors = [cookbook.author for cookbook in cookbooks]`
  - 要素の抽出
### typing annotation
- 型アノテーション
```
from typing import Dict, List
TemplateType = Dict[str,int]

def func(ls : List[int]) -> TemplateType
```
- 引数、戻り値、引数に対して、アノテーションは付与可能
- `mypy`などの静的型チェッカによってエラーを起こせる
### 型制約

- Union
  - 複数の型のいずれかを受け入れる型アノテーション
  - `Union[int,str]`
- Optional
  - Unionに追加して、Noneも受け入れる
- Literal
  - 特定の値のみを受け入れる
  - `value : Literal["up","down"]`
- NewType
  - 既存の型から派生して新しいクラスを定義する
  - `SemanticsId = NewType("SemanticsId",int)`
  - いわゆる継承を使用して新しいクラスを作る
- Final
  - 再代入や継承を禁止する制約
  - `PI: Final[int] = 3.123`
- Annotated
  - 複雑、冗長なLiteralを記述する
  - `x:Annonated[int,ValueRange(3,5)]`
  - `y:Annotated[str,MatchesRegrex("[0-9]{4}")]`
  - 型チェックはできない
### Noneを戻す時の意思
- 値が「存在しない」
  - これはListであれば、`[]`であることも多い
- 条件を満たさない
  - falseでよくない？
- 処理の失敗、未完了
  - 対応が必要な処理が発生しているという状態
- 後から設定するという意思

### 異種なコレクションを扱う
- 変数として、定式化された複数の値を格納する場合に使用
- 下記の`Union`に対して違和感がある
```
foodbook ={
    "name" = "pan",
    "score" = 80
}
# foodlabを受け付ける関数は以下のように描かれる
def print_book(book:dict[str,Union[str,int]])
```
- こういった辞書形式の型定義に対して、TypeDictを使用する
```.py
from typing import TypedDict

class Range(TypeDict):
    min : float
    max : float
```
- ※読み手に対して定義するだけで、バリデーションする機能は無い
- 完全に全ての値に対して管理できるなら、dataclassを使用した方がいい
### ジェネリクス
- 型定義における、あるクラスの定義。中身はintでもstrでもなんでも良い
- `TypeVar("T")`で型Tという名称の型
- 例
```
# ここで、Tは異なる型で解決できなくなる
def get_value_from_list(value :list[T]) ->T 
```
- Anyとは異なり、関係性を示す

### 定義済み型の継承
- dictやlistはそのまま継承すると、親関数の仕様に添わなければならない
- 名称だけもらうのなら、Userdict,Userlistは継承すれば良い
- 特殊なメソッドを上書きする場合でも、引数の特徴から実行されないこともある


### Pythonの継承
- MRO(C3リニアリゼーション)
- 複数を継承し、同じメソッド名がある場合は、先に継承するものが優先される

### collection.abc
- 原則として、抽象クラスを取り扱う
- 型定義として使用することで特殊メソッドの定義を強制できる

# ユーザ定義関数
### Enum
- 安全で堅牢な安全性の確保
- dictと同じく`value`と`name`によって参照する
- 定義後は、キーが増えない
```
class SAMPLE(Enum):
    A = "asample"
    B = "bsample"
SAMPLE.A
SAMPLE.A.name
SAMPLE.B.VALUE
```
#### @unique
- キーと値がそれぞれでユニークであることをチェックする
- ついていない場合は、値に関しては同じ値があっても良いとみなしている
```
@unique
class SAMPLE(Enum):
    A = "asample"
    B = "bsample"
SAMPLE.A
SAMPLE.A.name
SAMPLE.B.VALUE
```


#### Enum auto関数
- 値を自動で入れていく
```
class SAMPLE(Enum):
    A = auto()
    B = auto() # 1,2,3から順番に入る
```
- `generate_next_value`で独自のナンバリングも可能
- Literalとは違い、イテレーション、実行時型チェックが可能
###　Flag
- bitの冪乗でナンバリングする
```
class SAMPLE(Enum):
    A = auto()
    B = auto() # 1,2,4,8から順番に入る
```
- ビット演算での利用に役立っている

### 
