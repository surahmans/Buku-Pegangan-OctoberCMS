## Pengenalan

Tabel pada database seringkali saling berelasi dengan tabel lain. Sebagai contoh sebuah artikel memiliki banyak komentar, atau sebuah komentar berelasi ke pengguna. OctoberCMS membuat dan bekerja dengan relasi menjadi mudah dan mendukung banyak tipe relasi.

## Mendefinisikan Relasi

Relasi pada _model_ didefinisikan pada properti. Contohnya sebagai berikut:

```php
class User extends Model
{
    public $hasMany = [
        'posts' => 'Acme\Blog\Models\Post'
    ]
}
```

Mengakses relasi sebagai _function_ menjadikan kita dapat melakukan _method chaining_ dan meneruskan _querying_ seperti halnya kita menggunakan _model_ biasa.

```php
$user->posts()->where('is_active', true)->get();
```

Atau kita dapat mengakses relasi tersebut sebagai sebuah properti.

```php
$user->posts;
```

## Tipe-tipe Relasi

Beberapa tipe relasi yang didukung diantaranya:

- [One to One](/model/relationships.md#one-to-one)
- [One to Many](/model/relationships.md#one-to-many)
- Many to Many
- Has Many Through
- Polymorphic Relations
- Many to Many Polymorphic Relations

### [One to One](#one-to-one)

Relasi one-to-one adalah relasi yang sangat mendasar. Sebagai contoh sebuah `User` _model_ mungkin memiliki relasi dengan satu nomor telepon di `Phone` _model_. Untuk mendefinisikan relasi ini, kita dapat memasukan `phone` pada properti `$hasOne` di `User` _model_.

```php
<?php namespace Acme\Blog\Models;

use Model;

class User extends Model
{
    public $hasOne = [
        'phone' => 'Acme\Blog\Models\Phone'
    ];
}
```

Ketika relasi telah didefinisikan, kita dapat mengaksesnya sebagai properti. Nama properti ini sama seperti nama relasi yang dibuat yakni `phone`.

```php
$phone = User::find(1)->phone;
```

Model mengasumsikan _foreign key_ (kunci tamu) dari relasi ini berdasarkan nama _model_. Untuk kasus ini, `Phone` _model_ telah diasumsikan memiliki kolom `user_id` sebagai kunci tamu. Jika Anda memiliki nama kolom yang berbeda, Anda dapat menambahkan _parameter_ `key` ketika mendefinisikan relasi tersebut.

```php
public $hasOne = [
    'phone' => ['Acme\Blog\Models\Phone', 'key' => 'my_user_id']
];
```

Sebagai tambahan, _model_ mengasumsikan sebuah kunci tamu harus cocok dengan kolom `id` pada induknya. Atau dengan kata lain, kolom `id` pada `User` akan mencari nilai yang sesuai yang terdapat pada kolom `user_id` di `Phone`. Jika Anda menginginkan menggunakan kolom lain selain `id`, maka Anda dapat menambahkan _parameter_ `otherKey` pada saat mendefinisikan.

```php
public $hasOne = [
    'phone' => ['Acme\Blog\Models\Phone', 'key' => 'my_user_id', 'otherKey' => 'my_id']
];
```

#### Mendefinisikan Relasi Sebaliknya

Kita telah berhasil mengakses `Phone` _model_ melalui `User`. Sedangkan untuk sebaliknya, kita dapat mengakses `User` _model_ melalui `Phone` _model_. Kita dapat melakukan hal tersebut dengan cara mendefinisikannya pada properti `$belongsTo` pada `Phone` _model_.

```php
class Phone extends Model
{
    public $belongsTo = [
        'user' => 'Acme\Blog\Models\User'
    ];
}
```

Pada contoh di atas, _model_ akan mencocokan kolom `user_id` yang terdapat pada `Phone` _model_ ke kolom `id` yang terdapat pada `User` _model_. Secara otomatis _model_ menentukan bahwa kunci tamu dari relasi tersebut adalah nama relasi ditambah akhiran `_id` yakni `user_id`. Namun, apabila kunci tamu pada `Phone` _model_ bukan `user_id`, Anda dapat menambahkan _parameter_ `key` pada saat mendefinisikan.

```php
public $belongsTo = [
    'user' => ['Acme\Blog\Models\User', 'key' => 'my_user_id']
];
```

Jika pada _parent model_ tidak terdapat kolom `id` sebagai _primary key_ (Kunci utama), kita juga dapat mendefinisikannya dengan cara memberikan _parameter_ `otherKey` agar _model_ mencari kolom lain ketimbang kolom `id` sebagai kolom bawaan.

```php
public $belongsTo = [
    'user' => ['Acme\Blog\Models\User', 'key' => 'my_user_id', 'otherKey' => 'my_id']
];
```

### [One to Many](#one-to-many)

Relasi one-to-many digunakan untuk mendefinisikan relasi dimana sebuah _model_ memiliki banyak nilai dari _model_ lain. Sebaga contoh, sebuah _blog post_ mungkin memiliki komentar yang tidak terbatas jumlahnya. Seperti contoh sebelumnya, relasi one-to-many didefinisikan pada properti _model_. Namun pada kali ini pada properti `$hasMany`.

```php
class Post extends Model
{
    public $hasMany = [
        'comments' => 'Acme\Blog\Models\Comment'
    ];
}
```

Perlu diingat, _model_ secara otomatis mengasumsikan bahwa `Comment` _model_ memiliki kolom kunci tamu. Sebagai konvensi, ia akan mengambil nama _model_ pemilik dan diikuti dengan akhiran `_id`. Jadi untuk contoh ini, kita mengasumsikan kunci tamu di `Comment` _model_ adalah `post_id`.

Ketika relasi telah didefinisikan, kita dapat mengakses _collection_ dari _comment_ ini menggunakan properti `comments`. Perlu diingat, _model_ dapat memberikan relasi sebagai _"Dynamic properties"_.

```php
$comments = Post::find(1)->comments;

foreach ($comments as $comment) {
    //
}
```

Atau kita dapat mengaksesnya sebagai `method` dan melakukan _chaining method_ untuk membuat kondisi tertentu.

```php
$comments = Post::find(1)->comments()->where('title', 'foo')->first();
```

Seperti pada `hasONe`, kita dapat mengganti _foreign key_ dan _local key_ dengan menambahkan _parameter_ `key` dan `otherKey`.

```php
public $hasMany = [
    'comments' => ['Acme\Blog\Models\Comment', 'key' => 'my_post_id', 'otherKey' => 'my_id']
];
```

#### Mendefinisikan Relasi Sebaliknya

Kita telah berhasil mengakses semua _comments_ yang dimiliki oleh _post_. Sekarang mari kita definisikan relasi yang sebalinkya agar _comment_ dapat mengakses pemiliknya yakni _post_. Untuk mendefinisikannya kita dapat menggukanan properti `$belongsTo` pada `Comment` model.

```php
class Comment extends Model
{
    public $belongsTo = [
        'post' => 'Acme\Blog\Models\Post'
    ];
}
```

Ketika telah didefinisikan, kita dapat mengakses `Post` _model_ melalui `Comment` _model_ melalui _"dynamic property"_.

```php
$comment = Comment::find(1);

echo $comment->post->title;
```

Pada contoh di atas, _model_ akan mencocokan kolom `post_id` yang terdapat pada `Comment` _model_ ke kolom `id` yang terdapat pada `Post` _model_. Secara otomatis _model_ menentukan bahwa kunci tamu dari relasi tersebut adalah nama relasi ditambah akhiran `_id` yakni `post_id`. Namun, apabila kunci tamu pada `Comment` _model_ bukan `post_id`, Anda dapat menambahkan _parameter_ `key` pada saat mendefinisikan.

```php
public $belongsTo = [
    'post' => ['Acme\Blog\Models\Post', 'key' => 'my_post_id']
];
```

Jika pada _parent model_ tidak terdapat kolom `id` sebagai _primary key_ (Kunci utama), kita juga dapat mendefinisikannya dengan cara memberikan _parameter_ `otherKey` agar _model_ mencari kolom lain ketimbang kolom `id` sebagai kolom bawaan.

```php
public $belongsTo = [
    'post' => ['Acme\Blog\Models\Post', 'key' => 'my_post_id', 'otherKey' => 'my_id']
];
```



