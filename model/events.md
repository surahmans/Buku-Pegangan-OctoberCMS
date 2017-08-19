## Events {#events}

_Model_ dapat menjalankan _\(Fire\)_ sejumlah _events,_ yang dapat menghubungkan kita pada berbagai titik siklus hidup sebuah _model. Events_ memungkinkan kita menjalankan sejumlah baris kode setiap kali model tertentu disimpan atau diperbaharui dalam _database. Events_ didefinisikan dengan cara melakukan [_overriding_](https://stackoverflow.com/questions/2994758/what-is-function-overloading-and-overriding-in-php#2994767) pada metode-metode yang didukung dalam sebuah _class \(Model\)._ Adapun metode-metode yang tersedia diantaranya:

| Event | Description |
| :--- | :--- |
| **beforeCreate** | Sebelum data disimpan, ketika pertama kali dibuat. |
| **afterCreate** | Setelah data disimpan, ketika pertama kali dibuat. |
| **beforeSave** | Sebelum data disimpan, baik saat pertama kali dibuat atau perbaharui. |
| **afterSave** | Setelah data disimpan, baik saat pertama kali dibuat atau perbaharui. |
| **beforeValidate** | Sebelum data divalidasi. |
| **afterValidate** | Setelah data divalidasi. |
| **beforeUpdate** | Sebelum data yang sudah ada diperbaharui. |
| **afterUpdate** | Setelah data yang sudah ada diperbaharui. |
| **beforeDelete** | Sebelum data yang sudah ada dihapus. |
| **afterDelete** | Setelah data yang sudah ada dihapus. |
| **beforeRestore** | Sebelum sebuah soft-deleted model dikembalikan \(Restore\). |
| **afterRestore** | Setelah sebuah soft-deleted model berhasil dikembalikan \(Restore\). |
| **beforeFetch** | Sebelum data yang sudah ada diambil \(Fetch\). |
| **afterFetch** | Setelah data yang sudah ada diambil \(Fetch\). |

### Penggunaan Dasar {#penggunaan-dasar}

Setiap kali sebuah _model_ baru disimpan untuk pertama kalinya, maka _event_ `beforeCreate` dan `afterCreate` akan dijalankan. Jika _model_ sudah ada di _database_ dan metode `save` dipanggil, maka _event_ `beforeUpdate` dan `afterUpdate` akan dijalankan. Bagaimanapun, dari dua contoh tersebut \(Saat pertama dibuat dan diperbaharui\), _event_ `beforeSave` dan `afterSave` juga akan dijalankan.

Berikut in contoh cara mendefinisikan _**event listener**_ yang mengisi atribut _slug_ saat _model_ dibuat pertama kali:

```php
/**
 * Generate a URL slug for this model
 */
public function beforeCreate()
{
    $this->slug = Str::slug($this->name);
}
```

Mengembalikan nilai `false` pada sebuah _event_ akan membatalkan proses `save` / `update` ke _database._

```php
public function beforeCreate()
{
    if ( ! $user->isValid()) {
        return false;
    }
}
```



