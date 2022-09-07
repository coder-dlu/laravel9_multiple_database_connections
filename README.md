# laravel9_multiple_database_connections
## 1: Set ENV Variable:
- Tại đây, bạn cần đặt biến cấu hình trên tệp .env. hãy tạo như dưới đây:
```Dockerfile
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=mydatabase
DB_USERNAME=root
DB_PASSWORD=root
   
DB_CONNECTION_SECOND=mysql
DB_HOST_SECOND=127.0.0.1
DB_PORT_SECOND=3306
DB_DATABASE_SECOND=mydatabase2
DB_USERNAME_SECOND=root
DB_PASSWORD_SECOND=root
```
## 2: Database Configuration:
- Bây giờ, khi chúng ta đã tạo biến trong tệp env, chúng ta cần sử dụng biến đó trên tệp cấu hình, vì vậy hãy mở tệp database.php và thêm khóa kết nối mới như sau:
- config/database.php
```Dockerfile
<?php
  
use Illuminate\Support\Str;
  
return [
   
    'default' => env('DB_CONNECTION', 'mysql'),
   
    'connections' => [
        .....
   
        'mysql' => [
            'driver' => 'mysql',
            'url' => env('DATABASE_URL'),
            'host' => env('DB_HOST', '127.0.0.1'),
            'port' => env('DB_PORT', '3306'),
            'database' => env('DB_DATABASE', 'forge'),
            'username' => env('DB_USERNAME', 'forge'),
            'password' => env('DB_PASSWORD', ''),
            'unix_socket' => env('DB_SOCKET', ''),
            'charset' => 'utf8mb4',
            'collation' => 'utf8mb4_unicode_ci',
            'prefix' => '',
            'prefix_indexes' => true,
            'strict' => true,
            'engine' => null,
            'options' => extension_loaded('pdo_mysql') ? array_filter([
                PDO::MYSQL_ATTR_SSL_CA => env('MYSQL_ATTR_SSL_CA'),
            ]) : [],
        ],
        'mysql_second' => [
            'driver' => 'mysql',
            'url' => env('DATABASE_URL_SECOND'),
            'host' => env('DB_HOST_SECOND', '127.0.0.1'),
            'port' => env('DB_PORT_SECOND', '3306'),
            'database' => env('DB_DATABASE_SECOND', 'forge'),
            'username' => env('DB_USERNAME_SECOND', 'forge'),
            'password' => env('DB_PASSWORD_SECOND', ''),
            'unix_socket' => env('DB_SOCKET_SECOND', ''),
            'charset' => 'utf8mb4',
            'collation' => 'utf8mb4_unicode_ci',
            'prefix' => '',
            'prefix_indexes' => true,
            'strict' => true,
            'engine' => null,
            'options' => extension_loaded('pdo_mysql') ? array_filter([
                PDO::MYSQL_ATTR_SSL_CA => env('MYSQL_ATTR_SSL_CA'),
            ]) : [],
        ],
.....        
```
## 3: Getting Data from Multiple Database using DB:
- Tôi sẽ cung cấp cho viết hai lộ trình với việc nhận các sản phẩm từ các kết nối cơ sở dữ liệu khác nhau. bạn có thể xem ví dụ đơn giản với DB.
- routes/web.php
```Dockerfile
<?php
  
use Illuminate\Support\Facades\Route;
  
/*
|--------------------------------------------------------------------------
| Web Routes
|--------------------------------------------------------------------------
|
| Here is where you can register web routes for your application. These
| routes are loaded by the RouteServiceProvider within a group which
| contains the "web" middleware group. Now create something great!
|
*/
  
/*------------------------------------------
--------------------------------------------
Getting Records of Mysql Database Connections
--------------------------------------------
--------------------------------------------*/
Route::get('/get-mysql-products', function () {
    $products = DB::table("products")->get();
      
    dd($products);
});
  
/*------------------------------------------
--------------------------------------------
Getting Records of Mysql Second Database Connections
--------------------------------------------
--------------------------------------------*/
Route::get('/get-mysql-second-products', function () {
    $products = DB::connection('mysql_second')->table("products")->get();
      
    dd($products);
});

```
## 4: Multiple Database Connections with Migration:
- bạn có thể tạo các lần di chuyển riêng biệt cho nhiều kết nối cơ sở dữ liệu:
- Default:
```Dockerfile
<?php
.....
public function up()
{
    Schema::create('blog', function (Blueprint $table) {
        $table->increments('id');
        $table->string('title');
        $table->string('body')->nullable();
        $table->timestamps();
    });
}
.....
```
- Second Database:
```Dockerfile
<?php
<?php
.....
public function up()
{
    Schema::connection('mysql_second')->create('blog', function (Blueprint $table) {
        $table->increments('id');
        $table->string('title');
        $table->string('body')->nullable();
        $table->timestamps();
    });
}
.....
```
## 5: Multiple Database Connections with Model:
- Default:
```Dockerfile
<?php
  
namespace App\Models;
  
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
  
class Product extends Model
{
    use HasFactory;
   <?php
  
namespace App\Models;
  
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
  
class Product extends Model
{
    use HasFactory;
  
    protected $connection = 'mysql_second';
  
    protected $fillable = [
        'name', 'detail'
    ];
}
    protected $fillable = [
        'name', 'detail'
    ];
}
```
- Second Database:
```Dockerfile
<?php
  
namespace App\Models;
  
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
  
class Product extends Model
{
    use HasFactory;
  
    protected $connection = 'mysql_second';
  
    protected $fillable = [
        'name', 'detail'
    ];
}
```
## 6: Multiple Database Connections in Controller:
- Default:
```Dockerfile
<?php
  
use App\Models\Product;
    
class ProductController extends BaseController
{
    /**
     * Write code on Method
     *
     * @return response()
     */
    public function getRecord()
    {
        $products = Product::get();
        return $products;
    }
}
```
- Second Database:
```Dockerfile
<?php
use App\Models\Product;

class ProductController extends BaseController
{  
    /**
     * Write code on Method
     *
     * @return response()
     */
    public function getRecord()
    {
        $product = new Product;
        $product->setConnection('mysql_second');
        $something = $product->find(1);
        return $something;
    }
}
```
