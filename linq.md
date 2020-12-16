# Linq のメモ

## 条件

### 文字列の大小比較

.CompareToメソッドを使う。ちょっと面倒くさい。

```cs
// ラムダ式
from m in items
where
  m.YMD.CompareTo("20200401") >= 0 &&   // YMD >= '20200401'
  m.YMD.CompareTo("20200401") <= 0      // YMD <= '20210331'
select
  m.YMD
```

### IN 句

.Containsメソッドを使う。
⇒ .Any拡張メソッドでもいけるのか？

```cs
from a in items
where
   new [] {"A", "B"}.Contains(m.Type)
```

## 並び順

```cs
from a in items
orderby
    a.Date,
    a.Gender,
    a.Name

items.OrderBy(m => m.Date)
    .ThenBy(m => m.Gender)  // 複数指定する場合は .ThenBy拡張メソッドを使う
    .ThenBy(m => m.Name)
```

## 結合

記述方法はいくつかある

### 等結合

```cs
// from join
from a in itemsA
join b in ItemsB on new { a.Date, a.Gender } 
　　　　　　　equals new { b.Date, b.Gender } // ここがポイント
where
    a.Price > 0
select
    b.Name

//  from from でも大丈夫なはず
from a in itemsA
from b in ItemsB.Where(m => m.Date == a.Date && m.Gender == a.Gender) // ここがポイント
where
    a.Price > 0
select
    b.Name

// join を使う場合、結合するプロパティ名とデータ型を一致させる必要がある
from a in itemsA
join c in ItemsC on new { a.Date, a.Gender } 
　　　　　　　equals new { 
                Date = c.Birthday,   // ここがポイント
                c.Gender 
            }
where
    a.Price > 0
select
    b.Name

// 拡張メソッド
itemsA.Join(itemsB, a => new { a.Date, a.Gender }, 
                    b => new { b.Date, b.Gender }, 
                    (x, y) => new {
                        x.Date,
                        x.Gender,
                        x.Price,
                        y.Name,
                    })
        .Where(m => m.Price > 0)
        .Select(m => m.Name)
```

### LEFT JOIN 外部結合

```cs
from a in itemsA
from b in ItemsB.Where(m => m.Date == a.Date && m.Gender == a.Gender)
                .DefaultIfEmpty() // ここがポイント
where
    a.Price > 0
select
    b.Name
```

### UNION ALL

.Concat拡張メソッドを使う
⇒ラムダ式は存在しない？

```cs
var query1 = from a in itemsA
            select new {
                a.Date,
                a.Name
            };

var query2 = from a in itemsA
            select new {
                a.Date,
                a.Name
            };

query1.Concat(query2);
```

## 集約

### 集約キーが単一の場合

```cs
// ラムダ式
from m in items
group m by m.Date into g
select new {
    Date = g.Key,
    Count = g.Count(),
    Total = g.Sum(m => m.Price)
}

// 拡張メソッド
items.GroupBy(m => m.Date)
    .Select(g => new {
        Date = g.Key,
        Count = g.Count(),
        Total = g.Sum(m => m.Price);
    });
```

### 集約キーが複数の場合

```cs
// ラムダ式
from m in items
group m by new {    
    Date = m.Date,      // ここがポイント
    Gender = m.Gender   // ここがポイント
 } into g
select new {
    Date = g.Key.Date,     // ここがポイント
    Gender = g.Key.Gender, // ここがポイント
    Count = g.Count(),
    Total = g.Sum(m => m.Price)
}

// 拡張メソッド
items.GroupBy(m => new { 
        Date = m.Date, 
        Gender = m.Gender 
    })
    .Select(g => new {
        Date = g.Key.Date,
        Gender = g.Key.Gender,
        Count = g.Count(),
        Total = g.Sum(m => m.Price);
    });
```

### 複数テーブルを連結して集約する場合

```cs
// ラムダ式
from a in ItemsA
join b in ItemsB on new { a.Date, a.Gender } equals new { b.Date, b.Gender }
group new { a2 = a, b2 = b } by new { // ここがポイント
    a2.Year,
    b2.Month,
} into g
order by g.Max(m => m.a2.Price)
select new {
    Year = g.Key.Year,
    Month = g.Key.Month,
    Count = g.Count(),
    Total = g.Sum(m => m.a2.Price), // ここがポイント
}
```

### NULLが含まれる列の合計

```cs
var subQuery = from a in ItemsA
                group a by a.Date into g
                select new {
                    Date = g.Key,
                    Total = g.Sum(m => (decimal?)m.Price) // ここがポイント NULL許容型に変換する
                }

// さらに外部結合の際、レコードが存在しない場合の計算
from b in ItemsB
from c in subQuery.Where(m => m.Date == b.Date).DefaultIfEmpty()
select
    b.Price + c.Total ?? 0  // ここがポイント
```

### COUNT(*)

```cs
items.Count(m => m.Price > 0)

//レコードの存在チェックだけなら Any拡張メソッドでも可
items.Any(m => m.Price > 0)
```

### MAX値 + 1

該当レコードが存在しない場合も考慮する。

```cs
// 拡張メソッド
items.Select(m => m.Seq)
    .DefaultIfEmpty(0) // ここがポイント
    .Max() + 1;

// null許可型を使えばこんな風にもできる
items.Max(m => (int?)m.Seq) ?? 0 + 1;
```

### HAVING

```cs
// ラムダ式
from a in Items
group a by a.Date into g
where g.Count() > 1  // ここがポイント
select g.Key

// 拡張メソッド
items.GroupBy(m => m.Date)
    .Where(g => g.Count() > 1)
    .Select(g => g.Key)
```

## その他

### DataTable を使う

.AsEnumerable() 拡張メソッドを使う。  
.Fieldメソッドを使えば型変換も簡単にできる。  
.Fieldメソッドで NULL許容型に変換すれば NULLの取り扱いも簡単

```cs
DataTable dt;

dt.AsEnumerable()
  .GroupBy(m => m.Field<DateTime?>("日付"))
  .Select(g => new {
      Date = g.Key,
      Count = g.Count(),
      Total = g.Sum(m => m.Field<decimal>("金額"));
  });
```

