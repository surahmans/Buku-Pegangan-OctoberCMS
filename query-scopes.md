## Query Scopes {#query-scopes}

Query scope memungkinkan kita untuk membuat _filter_ yang mungkin akan kita gunakan kembali pada aplikasi kita nantinya. Misalnya, kita mungkin ingin mengambil semua data _user_ yang dianggap "populer". Untuk membuat _scope_ tersebut, cukup awali _method_ pada model menggunakan awalan _**scope.**_ Sebagai contoh sebagai berikut:

```php
class User extends Model
{
    /**
     * Scope a query to only include popular users.
     */
    public function scopePopular($query)
    {
        return $query->where('votes', '>', 100);
    }

    /**
     * Scope a query to only include active users.
     */
    public function scopeActive($query)
    {
        return $query->where('is_active', 1);
    }
}
```

#### Penggunaan Query Scope {#penggunaan-query-scope}

Ketika _query scope_ telah dibuat, kita dapat menerapkan _scope_ tersebut ketika melakukan pemanggilan data pada _model._ Perlu diingat, kita tidak perlu menggunakan awalan **scope ** ketika memanggil metode _scope_ yang diinginkan. Kita juga dapat menggunakan dua atau lebih metode _scope_ secara bersamaan. Sebagai contoh,

```php
$users = User::popular()->active()->orderBy('created_at')->get();
```

#### Query Scope yang Dinamis {#query-scope-yang-dinamis}

Terkadang kita mungkin ingin membuat _scope_ yang dapat menerima _parameters_. Kita dapat melakukan hal tersebut dengan menambahkan parameter-parameter pada _scope._ Pastikan Anda menambahkannya setelah `$query` _argument._

```php
class User extends Model
{
    /**
     * Scope a query to only include users of a given type.
     */
    public function scopeApplyType($query, $type)
    {
        return $query->where('type', $type);
    }
}
```

Sekarang kita dapat membubuhkan parameter ketika memanggil _scope_ tersebut dengan cara:

```php
$users = User::applyType('admin')->get();
```



