C# Coding Style
===============

C++ファイル(*.cpp and *.h)は、我々はコードスタイルを確保するためにclang-format (version 3.6+)を使用している。cppもしくはhファイルがマージされる前に、src/Native/format-code.shを必ず動作させる。このスクリプトは、全てのネイティブコードファイルがコーディングスタイルガイドラインに基づくことを保証する。

コードファイル以外(xml等)に対する我々の最善の基準は一貫性があることである。ファイルを編集するとき、新しいコードと変更で既存のファイル等とスタイルを一致させておくこと。新しいファイルは、そのコンポーネントのためのスタイルに対応するべきである。最後に、もし完全に新しいコンポーネントであるなら、適度に広く受け入れられるものが適している。

私たちが従う一般的なルールは「Visula Studioのデフォルト」に従うことである。

1. 我々は中括弧に[Allman style](https://ja.wikipedia.org/wiki/%E5%AD%97%E4%B8%8B%E3%81%92%E3%82%B9%E3%82%BF%E3%82%A4%E3%83%AB#BSD.2F.E3.82.AA.E3.83.BC.E3.83.AB.E3.83.9E.E3.83.B3.E3.81.AE.E3.82.B9.E3.82.BF.E3.82.A4.E3.83.AB) を使用し、個々の中括弧は新しい行から始まる。単一行のステートメントは中括弧を省略できるがブロックは間違いなく自身のラインをインデントしなければならず他のステートメントブロックからネスティングしても行けない。(参考のためにissue [381](https://github.com/dotnet/corefx/issues/381)を参照 )
2. 我々はインデントに4つのスペースを使用する。(タブは使用しない)
3. 我々はinternalもしくはprivateのフィールドに`_camelCase`を使用し、可能なら`readonly` を使用する。プリフィックスとしてインスタンスフィールドならば`_`、スタティックフィールドには`s_`、そしてスレッドスタティックなフィールドには `t_`を使用する。スタティックフィールドを使用する場合、`readonly`は`static`の後に付ける。(例、`readonly static`では無く`static readonly` )
4. 我々はどうしても必要とされる場合を除いて`this.`は避ける。
5. 我々は例えそれが標準であってもアクセス修飾子を指定する(例、`string _foo`では無く`private string _foo`)。アクセス修飾子は最初の修飾子とする(例、`abstract public`では無く`public abstract` )。
6. 名前空間のインポートはファイルの先頭で宣言され、namespace宣言の*外*で宣言され、可能ならアルファベット順にソートされるべきである。
7. 複数の空行はいつでも避ける。例えば、 タイプのメンバー間で2行開けると言ったことはしない。
8. 不要なスペースは避ける。例えば   `if (someVar == 0)...`の`.`は不要なスペースを示している。もしVisual Studioを使用しているので有れば、"スペースの表示 (Ctrl+E, S)"を有効にして、不要なスペースを見つけられるようにすることを検討する。
9. もし偶然これらのガイドラインとファイルが異なっている場合(例、privateメンバーの名前が`_member`では無く`m_member`となっている場合)には、そのファイルの既存スタイルを優先する。
10. 我々ははっきりと変数の型が判るできるときにだけ`var`を使用する。(例、`var stream = OpenStandardInput()`では使用せず、 `var stream = new FileStream(...)`では使用する)
11. 我々はBCLの型は使用せず言語のキーワードを使用し(例、`Int32, String, Single`等では無く`int, string, float`等を使用する)メソッド呼び出しのような型を参照する場合も同様(`Int32.Parse`では無く`int.Parse`)である。issue[391](https://github.com/dotnet/corefx/issues/391)を参照。
12. 我々はローカル変数及びフィールドの定数にはPascalCaseを使用する。唯一の例外はその定数値がinteropを経て呼んでいるコードの定数値名と値とに正確にマッチしている時。
13. 我々は可能で妥当な場合にはいつも ```"..."```では無く```nameof(...)```を使用する。

我々はcorefxリポジトリのルートにVisual Studio 2013の設定ファイル (`corefx.vssettings`) を提供していて、C#の自動整形が上記ガイドラインに沿えるようにしている。追記、ルール7と8は現在のVSの整形機能ではサポートしていないので、vssettingsではカバーしていない。

我々はコード ベースが時間をかけて一貫したスタイルを維持し、自動的に上記のガイドラインに適合するようにコード ベースを修正できるように [.NET Codeformatter Tool](https://github.com/dotnet/codeformatter) を使用している。

### Example File:

``ObservableLinkedList`1.cs:``

```C#
using System;
using System.Collections;
using System.Collections.Generic;
using System.Collections.Specialized;
using System.ComponentModel;
using System.Diagnostics;
using Microsoft.Win32;

namespace System.Collections.Generic
{
    public partial class ObservableLinkedList<T> : INotifyCollectionChanged, INotifyPropertyChanged
    {
        private ObservableLinkedListNode<T> _head;
        private int _count;

        public ObservableLinkedList(IEnumerable<T> items)
        {
            if (items == null)
                throw new ArgumentNullException(nameof(items));

            foreach (T item in items)
            {
                AddLast(item);
            }
        }

        public event NotifyCollectionChangedEventHandler CollectionChanged;

        public int Count
        {
            get { return _count; }
        }

        public ObservableLinkedListNode AddLast(T value) 
        {
            var newNode = new LinkedListNode<T>(this, value);

            InsertNodeBefore(_head, node);
        }

        protected virtual void OnCollectionChanged(NotifyCollectionChangedEventArgs e)
        {
            NotifyCollectionChangedEventHandler handler = CollectionChanged;
            if (handler != null)
            {
                handler(this, e);
            }
        }

        private void InsertNodeBefore(LinkedListNode<T> node, LinkedListNode<T> newNode)
        {
           ...
        }
        
        ...
    }
}
```

``ObservableLinkedList`1.ObservableLinkedListNode.cs:``

```C#
using System;

namespace System.Collections.Generics
{
    partial class ObservableLinkedList<T>
    {
        public class ObservableLinkedListNode
        {
            private readonly ObservableLinkedList<T> _parent;
            private readonly T _value;

            internal ObservableLinkedListNode(ObservableLinkedList<T> parent, T value)
            {
                Debug.Assert(parent != null);

                _parent = parent;
                _value = value;
            }
            
            public T Value
            {
               get { return _value; }
            }
        }

        ...
    }
}
```

訳注：Visual Studio 2015で空白の表示はCtrl+R, Ctrl+Wで切り替える。