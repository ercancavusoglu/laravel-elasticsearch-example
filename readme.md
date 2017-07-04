# Laravel 5 Search Using Elasticsearch

Laravel'de kullanabileceğiniz elasticsearch paketi, örnek kullanımı ve kurulumu aşağıda bulabilirsiniz.

Entegrasyon veya destek için issue açabilir, `ercancavusoglu@yandex.com.tr` adresinden veya [@devredisibirak](http://twitter.com/devredisibirak) twitter hesabımdan bana ulaşabilirsiniz.

## Migration 

    class CreateItemsTable extends Migration
    {
        public function up()
        {
            Schema::create('items', function (Blueprint $table) {
                $table->increments('id');
                $table->string('title');
                $table->text('description');
                $table->timestamps();
            });
        }
        public function down()
        {
            Schema::drop("items");
        }
    }
    
    //Run method with artisan
    //php artisan migrate

##Composer.json
        
    "elasticquent/elasticquent": "dev-master",
    "laravelcollective/html": "^5.2.0"

    //Add to require section at composer.json
    
##Item.php (Model)
        
    namespace App;

    use Illuminate\Database\Eloquent\Model;
    use Elasticquent\ElasticquentTrait;

    class Item extends Model
    {
        use ElasticquentTrait;
        public $fillable = ['title','description'];
    }

    //Don't forget use ElasticquentTrait

##Routes.php

    Route::get('ItemSearch', 'ItemSearchController@index');
    Route::post('ItemSearchCreate', 'ItemSearchController@create');

##ItemSearchController.php

    class ItemSearchController extends Controller
    {
        public function index(Request $request)
        {
            $items = Item::all()->toArray();
            if($request->has('search')){
                $items = Item::search($request->input('search'))->toArray();
            }
            return view('ItemSearch',compact('items'));
        }


        public function create(Request $request)
        {
            $this->validate($request, [
                'title' => 'required',
                'description' => 'required',
            ]);

            $item = Item::create($request->all());
            $item->addToIndex(); // THIS IS REALLY IMPORTANT, DONT FORGET
            return redirect()->back();
        }

    }