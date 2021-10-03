# laravelCodesForRemember

//.htaccess
	
	//general--------------
	
	<IfModule mod_rewrite.c>
	  RewriteEngine on
	  RewriteCond %{REQUEST_FILENAME} !-f
	  RewriteRule ^(.*)$ public/$1 [L]
	</IfModule>

	# php -- BEGIN cPanel-generated handler, do not edit
	# Set the “ea-php72” package as the default “PHP” programming language.
	<IfModule mime_module>
	  AddHandler application/x-httpd-ea-php72 .php .php7 .phtml
	</IfModule>
	# php -- END cPanel-generated handler, do not edit
	
	//bluehost------------
	
	<IfModule mod_rewrite.c>
	<IfModule mod_negotiation.c>
	    Options -MultiViews -Indexes
	</IfModule>

	RewriteEngine On
	RewriteBase /
	# Handle Authorization Header
	RewriteCond %{HTTP:Authorization} .
	RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]

	# Redirect Trailing Slashes If Not A Folder...
	RewriteCond %{REQUEST_FILENAME} !-d
	RewriteCond %{REQUEST_URI} (.+)/$
	RewriteRule ^ %1 [L,R=301]

	# Handle Front Controller...
	RewriteCond %{REQUEST_FILENAME} !-d
	RewriteCond %{REQUEST_FILENAME} !-f
	RewriteRule ^ index.php [L]
	
// protect env

	<Files ~ "\.(env|json|config.js|md|xml|gitignore|gitattributes|lock|editorconfig|yml|styleci.yml)$">
	     Order allow,deny
	     Deny from all
	 </Files>
	
// localhost & Serve .htaccess

	<IfModule mod_rewrite.c>
	 #Session timeout

	<IfModule mod_negotiation.c>
	    Options -MultiViews
	</IfModule>

	RewriteEngine On

	RewriteCond %{REQUEST_FILENAME} -d [OR]
	RewriteCond %{REQUEST_FILENAME} -f
	RewriteRule ^ ^$1 [N]

	RewriteCond %{REQUEST_URI} (\.\w+$) [NC]
	RewriteRule ^(.*)$ public/$1

	RewriteCond %{REQUEST_FILENAME} !-d
	RewriteCond %{REQUEST_FILENAME} !-f
	RewriteRule ^ server.php

	</IfModule>

// Edit

	function edit($user_id)
	    {
	      $single_data = User::findOrFail($user_id);
	      return view('users.edit',compact('single_data'));
	    }

//update

	function update(Request $request)
	    {
	      User::find($request->user_id)->update([
		'name'    =>$request->name,
		'email'    =>$request->email,
	      ]);
	      return back();
	    }
	
//delete

	function delete($user_id)
	    {
	      User::findOrFail($user_id)->delete();
	      return back();
	    }
    
//restore
    
    function restore_banner($banner_id)
      {
      User::onlyTrashed()->where('id',$user_id)->restore();
      return back();
    }
    
    
 //alert message
   
	   @if (session('success'))
	   <div class="alert alert-success">
	       {{ session('success') }}
	   </div>
	   @endif

 //error

	   @if ($errors->any())
	   <div class="alert alert-danger">
	       <ul>
		   @foreach ($errors->all() as $error)
		   <li>{{ $error }}</li>
		   @endforeach
	       </ul>
	   </div><br />
	   @endif
   
   
   
   // relationship
   
	   function relationbetweencategory()
	    {
	      return $this->hasOne('App\Category','id','category_id');
				// Category - id , product-category_id
	    }
   
   
   
   // photo upload
   
	   if ($request->hasFile('header_banner')) {
		$photo_upload     =  $request ->header_banner;
		$photo_extension  =  $photo_upload -> getClientOriginalExtension();
		$photo_name       =  $last_inserted_id . "." . $photo_extension;
		Image::make($photo_upload)->resize(1600,800)->save(base_path('public/uploads/banner/'.$photo_name),100);
		Banner::find($last_inserted_id)->update([
		'header_banner'          => $photo_name,
	      ]);
	      }


 // Photo Update
 
	   if($request->hasFile('header_banner')){
		if(Banner::find($request->banner_id)->header_banner=='default.png'){
		  $photo_upload     = $request->header_banner;
		  $photo_extension  =  $photo_upload->getClientOriginalExtension();
		  $photo_name       =  $request->header_banner . "." . $photo_extension;
		  Image::make($photo_upload)->resize(1600,800)->save(base_path('public/uploads/banner/'.$photo_name),100);
		  Banner::find($request->banner_id)->update([
		  'header_banner'          => $photo_name,
		]);
		}
		else {
		  //delete
		  $delete_photo=Banner::find($request->banner_id)->header_banner;
		  unlink(base_path('public/uploads/banner/'.$delete_photo));
		  //update
		  $photo_upload     = $request->header_banner;
		  $photo_extension  =  $photo_upload->getClientOriginalExtension();
		  $photo_name       =  $request->banner_id . "." . $photo_extension;
		  Image::make($photo_upload)->resize(1600,800)->save(base_path('public/uploads/banner/'.$photo_name),100);
		  Banner::find($request->banner_id)->update([
		  'header_banner'          => $photo_name,
		]);
		}
	      }
      
//Ajax


	<script type="text/javascript">
	    $(document).ready(function() {
		$('#district').change(function() {
		    var district_id = $(this).val();
		    // alert(district_id);

		    // ajax setup

		    $.ajaxSetup({
			headers: {
			    'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
			}
		    });

		    // ajax setup request start

		    $.ajax({
			type: 'POST',
			url: '/get/city/list',
			data: {
			    district_id: district_id
			},
			success: function(data) {
			    $("#city").html(data);

			    // alert(data);

			}
		    });

		    // ajax setup request end

		});
	    });
	</script>



//Mailing
	
	public $name ="";
	public function __construct($name)
	{
	  $this->name=$name;
	}

	/**
	* Build the message.
	*
	* @return $this
	*/
	public function build()
	{
	  $name ="";
	  return $this->view('dashboard.trainee_registration.mail.index',compact('name'));
	}
	
	
	
//photo upload [object]
  		
	if($file = $request->hasFile('photo')) {
            $file = $request->file('photo') ;
            $fileName = $file->getClientOriginalName() ;
            $destinationPath = public_path().'/uploads/profile' ;
            $file->move($destinationPath,$fileName);
            $user->photo = $fileName ;
          }

//photo download
	
	$file_name = Requisition::findOrFail($requisition_id)->money_receipt;
	$path = public_path('uploads/requisition/'.$file_name);
	return response()->download($path);
	
// Get Youtube Video JSON Informarion
	
	 $youtube_contains = Str::contains($request->video_url, 'youtu.be');
	 $vid = Str::after($request->video_url,'https://youtu.be/'); //youtube video id

          // YOUTUBE_V3_API Keys
          $api = env('YOUTUBE_V3_API');

          // Getting video information from youtbe video api
            $videoDetails = file_get_contents("https://www.googleapis.com/youtube/v3/videos?id=" . $vid . "&part=contentDetails,statistics&key=" . $api);

            // json_decoding
            $VidDuration = json_decode($videoDetails, true);

            foreach ($VidDuration['items'] as $vidTime)
            {
              $VidDuration= $vidTime['contentDetails']['duration'];
            }

            if (strlen($VidDuration) <= 5) {
              $pattern='/PT(\d+)S/';
              preg_match($pattern,$VidDuration,$matches);
              $seconds=$matches[1];
              $content->duration = $seconds;
            }elseif (strlen($VidDuration) <= 8) {
              $pattern='/PT(\d+)M(\d+)S/';
              preg_match($pattern,$VidDuration,$matches);
              $seconds=$matches[1]*60+$matches[2];
              $content->duration = $seconds;
            }else {
              $pattern='/PT(\d+)H(\d+)M(\d+)S/';
              preg_match($pattern,$VidDuration,$matches);
              $seconds=$matches[1]*3600+$matches[2]*60+$matches[3];
              $content->duration = $seconds;
            }

            $content->save();
            notify()->success(translate('Class Content Save Successfully '));
            return back();
	    
// GET vimeo video information JSON
	     
	     $vimeo_contains = Str::contains($request->video_url, 'vimeo.com');
	     
	     $vid = $request->video_url; //Vimeo video url

            // Getting video information from vimeo video api
            $videoDetails = file_get_contents("https://vimeo.com/api/oembed.json?url=" . $vid);

            // json_decoding
            $VidDuration = json_decode($videoDetails, true);

            $duration = $VidDuration['duration'];

            $content->duration = $duration;

            $content->save();
            notify()->success(translate('Class Content Save Successfully '));
            return back();
	    
  //git solution
  
  	$ cd ~/.ssh
	$ ls
   	$ ssh-keygen -o
	//optional
	$ cat ~/.ssh/id_rsa.pub
	
 //breadcrumb
 
 	<ul class="breadcrumb__list">
           <li class="active__list-item">Home</li>
               @foreach($segments = request()->segments() as $index=>$segment)
                   <li class="active__list-item">
                      <a href="{{url(implode(array_slice($segments,0,$index+1),'/'))}}">
                          {{Str::title($segment)}}
                       </a>
                    </li>
                @endforeach
         </ul>
   
   //Publish checkbox(ajax)
   	
	//blade
	<input id="radio{{$food->id}}" data-id="{{ $food->id }}" data-url="{{ route('food.publish') }}" class="publish" type="checkbox" {{ $food->is_published == 1 ? 'checked' : '' }}><span class="switch-state bg-primary"></span>
	
	//script
	$('.publish').on('change',function(){
          var url = this.dataset.url;
          var id = this.dataset.id;

          if (url != null && id != null) {
            $.ajax({
                url: url,
                data: {id: id},
                method: 'get',
                success: function (result) {
                    alert();
                },
            });
        }
        });
	
	//route
	Route::get('/foods/publish','Backend\FoodController@food_publish')->name('food.publish');
	
	//controller
	public function food_publish(Request $request)
       {
	       $foody = Food::where('id', $request->id)->first();
	       if ($foody->is_published == 0) {
		 $foody->is_published = 1;
		 $foody->save();
	       }else {
		 $foody->is_published = 0;
		 $foody->save();
	       }
         return response(['message' => 'Slider status is changed '], 200);
       }
	
   //forModal(JS)
   	
	//blade
	<a href="#!" onclick="quickView('{{ route('quick.view',$product->slug) }}')">
	
	//set at master blade
	 <div class="modal fade" id="product-quickview" tabindex="-1" role="dialog" aria-labelledby="product-quickview" aria-hidden="true">
      	    <div class="modal-dialog modal-dialog-centered" role="document">
               <div class="modal-content">
	         <span class="modal-close" data-dismiss="modal">
		   <i class="icon-cross2"></i>
		 </span>
               </div>
            </div>
         </div>
	
	//JS
	//show the modal in this function
	function quickView(url) {
	    $('#product-quickview').modal('show');
	    $('#product-quickview').load(url);
	}
	
	//controller
	public function quick_view($slug)
	{
	   return view('frontend.include.product.quickview');
	}

//Store Multiple value(json)
	
	//controller
	$data = explode(',', $request->tags);
            $pro_value = array();
            foreach ($data as $item) {
                array_push($pro_value, Str::upper($item));
            }

            $tags = json_encode($pro_value);
            $pro->tags = $tags;
	    
	//Blade
	@foreach(json_decode($product->tags) as $data){{$data}},@endforeach

// Search by Array
	
	public function find_order(Request $request)
    	{
        $getDatas = null;
        $condition = array();
        if (!is_null($request->email)) {
            $condition = array_merge($condition, ['email' => $request->email]);
        }
        if (!is_null($request->booking_code)) {
            $condition = array_merge($condition, ['booking_code' => $request->booking_code]);
        }
        if (!is_null($request->order_number)) {
            $condition = array_merge($condition, ['order_number' => $request->order_number]);
        }

        if (!is_null($request->phone)) {
            $condition = array_merge($condition, ['phone' => $request->phone]);
        }


        if (!is_null($request->end_date)) {
            $start = null;
            $end = Carbon::parse($request->end_date)
                ->endOfDay()
                ->toDateTimeString();
            if ($request->has('start_date')) {
                $start = Carbon::parse($request->start_date)
                    ->startOfDay()
                    ->toDateTimeString();
            } else {
                $start = Carbon::yesterday()->startOfDay()->toDateTimeString();
            }

            if (empty($condition)) {
                $getDatas = OrderProduct::whereBetween('created_at', [$start, $end])->get();
            } else {
                $getDatas = OrderProduct::where($condition)->whereBetween('created_at', [$start, $end])->get();
            }

            }else{
            $getDatas = OrderProduct::where($condition)->get();
            }
            return view('backend.orders.search_result', compact('getDatas'));
    	}
	
// Product Rating Math

	    $rates = App\Models\OrderProduct::where('shop_id',$id)->select('review_star')->get()->toArray();

           $rateArray =[];
           foreach ($rates as $rate)
           {
               $rateArray[]= $rate['review_star'];
           }

            $sum = array_sum($rateArray);

            $result = $sum/count($rateArray);

            return response()->json(['rating'=>$result],200);

// Limit
				
	    @php
                $category_child_limit = 0;
            @endphp

            @foreach($home_category->childrenCategories as $parent_Cat)
                @foreach($parent_Cat->childrenCategories as $sub_cat)

                  <input type="hidden" value="{{ $category_child_limit++ }}">
                     <li>
                        <a href="{{ route('category.shop',$sub_cat->slug) }}">{{ $sub_cat->name }}</a>
                     </li>
			
		@if ($category_child_limit == 13)
                     @break
                @endif
             @endforeach

              @if ($category_child_limit == 13)
                 @break
              @endif

             @endforeach
	     
//Check Exist
	
	$check_exist = Variant::where('unit',$request->unit)
                              ->where('variant',$request->variant)
                              ->where('code',$request->code)
                              ->exists();
        

        if (isset($check_exist)) {
          Alert::success('Exist','This variation is exist');
          return back();
        }else {
            $requset_variant = new Variant();
            $requset_variant->unit = $request->unit;
            $requset_variant->variant = Str::lower($request->variant);
            $requset_variant->code = $request->code;
            $requset_variant->is_published = false;
            $requset_variant->save();

            /*variant of size*/
            $requset_product_variant = new ProductVariant();
            $requset_product_variant->product_id = $request->product_id;
            $requset_product_variant->variant_id = $requset_variant->id;
            $requset_product_variant->unit = $request->unit;
            $requset_product_variant->is_published = false;
            $requset_product_variant->save();

            return back()->with('success',translate('Variation request sent successfully.'));
        }
	
// all file extension rename
	
	  //CMD		
	  ren *.* *.jpg

//Column value update
	
	Route::get('/test', function(){
    
        $faker = \Faker\Factory::create();
        // $product_rand = rand(8,20);
        
        for ($i = 40; $i < 50; $i++) {

        DB::table('products')->where('id', $i)->update([
            'image'=>'uploads/fashion-mens-quartz-watch-blu-ray-glass-band/a ('.($product_image).').jpg',
        ]);
    
        }

	    //    $x = App\Models\Product::all();
	    //    foreach($x as $y){
	    //        $product_image = rand(1,107);
	    //      $y->image = 'uploads/fashion-mens-quartz-watch-blu-ray-glass-band/a ('.($product_image).').jpg';
	    //      $y->save();
	    //    }

	});

// php write file on specific line
	
		    $filename = base_path('/resources/views/test.blade.php'); // write blade
		    $line_i_am_looking_for = 114; //index number wise
		    $lines = file($filename, FILE_IGNORE_NEW_LINES); //get line
		    $written =  "RUMON"; // desiere codes
		    $lines[$line_i_am_looking_for] = $written; // implement code in line
		    file_put_contents($filename, implode("\n" , $lines)); // output
		    return 'ok';
		    
// php replace file on specific string

		    $path_to_file = base_path('/resources/views/test.blade.php'); // write blade
		    $file_contents = file_get_contents($path_to_file); // get file
		    $file_contents = str_replace("PRINCE","Linkin Park",$file_contents); // find string ('OLD','NEW','WHICH FILE')
		    file_put_contents($path_to_file,$file_contents); // Replace
		    return 'ok';
		    
// Create Model, DB table and migrate
		
		Route::get('/migrate', function(){
		    if (!Schema::hasTable('addons')) {
			    \Artisan::call('make:model Addon -m');
			    \Artisan::call('migrate');
			    \Artisan::call('optimize:clear');
			    return 'ok';
			}else{
			    return 'exist';
			}
		});
		
//check DB table for migration and Update Column

	DB::statement("ALTER TABLE `orders` CHANGE payment_type payment_type ENUM('cod','stripe','paypal','paytm')");
	
// tag

	//Store
	$tag = explode(',',$request->tag);
        $tagC = array();
        foreach ($tag as $itemt){
            array_push($tagC,$itemt);
        }
        $courses->tag = json_encode($tagC);
	
	//Get
	@foreach (json_decode($post->tags) as $tag)
		<a href="#">
	    		{{$tag}}
		</a>
	@endforeach
	
//Get Month Wise Current Year Data
	
	MailLog::select(DB::raw("(COUNT(*)) as count"),DB::raw("MONTHNAME(created_at) as monthname"))
                                                        ->whereYear('created_at', date('Y'))
                                                        ->orderByRaw('DATE_FORMAT(created_at, "%m-%d")')
                                                        ->groupBy('monthname')
                                                        ->get();
							
	Or,

	SupportTicket::selectRaw("count(id) AS data, 
					DATE_FORMAT(created_at, '%m') AS new_date, 
					YEAR(created_at) AS year, 
					MONTH(created_at) AS month
				    ")
				    ->groupBy('new_date')
				    ->get();


//Get Current Month Data

	  function sentMailCurrentMonthData()
	  {
		return MailLog::whereMonth('created_at', date('m'))
				->whereYear('created_at', date('Y'))
				->count();
	  }

//Get Last Month records

	  function sentMailLastMonthData()
	  {
		return MailLog::whereMonth('created_at', '=', Carbon::now()->subMonth()->month)->count();
	  }
	  
//Get Top Records

	  function topRecords()
  	  {
    		return BouncedEmail::select(DB::raw('COUNT(id) as cnt', 'owner_id'))->groupBy('owner_id')->orderBy('cnt', 'DESC')->first();
  	  }
	 
//Get Top 5 Records

	  function topFiveRecords()
  	  {
    		return BouncedEmail::select('owner_id')
				    ->groupBy('owner_id')
				    ->orderByRaw('COUNT(*) DESC')
				    ->take(5)
				    ->get();
  	  }

//Get Weekly Top Records
	
	function weeklyTopSenders()
	{
	    $last7days = Carbon::now();
	    $last7days->subDays(7);

	    return BouncedEmail::select('owner_id')
		    ->groupBy('owner_id')
		    ->orderByRaw('COUNT(*) DESC')
		    ->whereDate('created_at', '>=', $last7days)
		    ->get();
	 }

// EVEN ODD ADD CLASS, REMOVE CLASS AT LAST LOOP

	@if (!$loop->last)

		@if ($loop->iteration % 2 == 0 ? 'flex-row-reverse' : null)
			<img class="img-fluid" src="{{ asset('frontend/images/pricing/605.png') }}" alt="img">
		@else
			<img class="img-fluid" src="{{ asset('frontend/images/pricing/603.png') }}" alt="img">
		@endif

	@endif
	
// Estimated Time To Read

	function estimate_reading_time($content) {
	    $word_count = str_word_count(strip_tags($content));

	    $minutes = floor($word_count / 200);
	    $seconds = floor($word_count % 200 / (200 / 60));

	    $str_minutes = ($minutes == 1) ? "minute" : "minutes";
	    $str_seconds = ($seconds == 1) ? "second" : "seconds";

	    if ($minutes == 0) {
		return "{$seconds} {$str_seconds}";
	    }
	    else {
		return "{$minutes} {$str_minutes}, {$seconds} {$str_seconds}";
	    }
	}
	
#Breadcrumb

	@for($i = 1; $i <= count(Request::segments()); $i++)
	    @if($i < count(Request::segments()) & $i > 0)
	    <?php $link .= "/" . Request::segment($i); ?>
	    <a class="breadcrumb--active" href="<?= $link ?>">{{ ucwords(str_replace('-',' ',Request::segment($i)))}}</a> 
	    <i data-feather="chevron-right" class="breadcrumb__icon"></i>
	    @else {{ucwords(str_replace('-',' ',Request::segment($i)))}}
	    @endif
	@endfor
	
#Export from SQL as CSV

	    $connect = mysqli_connect("localhost", "root", "", "codecanyon_maildoll");  
            header('Content-Type: text/csv; charset=utf-8');  
            header('Content-Disposition: attachment; filename=data.csv');  
            $output = fopen("php://output", "w");  
            fputcsv($output, array('id', 'owner_id', 'name', 'email', 'country_code', 'phone', 'favourites', 'blocked', 'trashed', 'is_subscribed', 'deleted_at', 'created_at', 	    'updated_at'));  
            $query = "SELECT * from email_contacts ORDER BY id DESC";  
            $result = mysqli_query($connect, $query);  
            while($row = mysqli_fetch_assoc($result))  
            {  
                fputcsv($output, $row);  
            }  
            fclose($output);

#Import from CSV to SQL

	if ($request->hasFile('csv')) {
            $imageName = Auth::user()->id.'.'.$request->csv->getClientOriginalExtension();
            $request->csv->move(public_path('/uploads/csv'), $imageName);

            $file = asset('uploads/csv/' . Auth::user()->id . '.csv');
            $contacts = convert_csv_to_json($file);

            
            foreach (json_decode($contacts) as $value) {
                $email = new EmailContact;
                $email->owner_id = Auth::user()->id;
                $email->name = $value->name ?? null;
                $email->email = $value->email ?? null;
                $email->country_code = $value->country_code ?? null;
                $email->phone = $value->phone ?? null;
                $email->favourites = $value->favourites ?? null;
                $email->blocked = $value->blocked ?? null;
                $email->trashed = $value->trashed ?? null;
                $email->is_subscribed = $value->is_subscribed ?? null;
                $email->deleted_at = Carbon::parse($value->created_at) ?? null;
                $email->created_at = Carbon::parse($value->created_at) ?? Carbon::now();
                $email->updated_at = Carbon::parse($value->updated_at) ?? Carbon::now();
                $email->save();
            }
        }

#Migrate specific path

	php artisan migrate --path=database/migrations/tenant

#Demo Middleware

 	if (env('APP_ENV') == 'local'){
            if ($request->isMethod('post')) {
                return back()->with('info', translate('this is for demo purpose only'));
            } else {
                return $next($request);
            }
        }else{
            return $next($request);
        }
	
#Send user alert before deadline
	
	function expirationNotify($owner_id, $days)
         {
            $expirationNotify = EmailSMSLimitRate::where('owner_id', $owner_id)
                                ->whereBetween('to', [Carbon::now(), Carbon::now()->addDays($days)])
                                ->first();

            if ($expirationNotify != null) {
                return true;
            } else {
                return false;
            }
            
         }

#Create new database from controller

	public function createDatabase($dbName)
        {
            //Create a new DB with the company db_name attribute

            $new_db = DB::statement("CREATE DATABASE {$dbName}" );

            //Now migrate over all Company specific migrations

            if($new_db)
            {
                \Config::set('database.connections.company.database', $dbName);

                return Artisan::call( 'migrate', [
                    '--database' => 'company',
                    '--path' => 'database/migrations/company',
                ]);
            }

        }
	
#Create new database with All Privileged

	Route::get('/db', function () {

	  $new_db = DB::statement('CREATE DATABASE `aqeel`');
		DB::statement("CREATE USER 'aqeel_root'@'localhost' IDENTIFIED BY 'aqeelroot' ");
		DB::statement("GRANT ALL PRIVILEGES ON * . * TO 'aqeel_root'@'localhost'");

		if ($new_db) {

		    $config['database'] = env('DB_DATABASE', 'kola');
		    $config['username'] = env('DB_USERNAME', 'root');
		    $config['password'] = env('DB_PASSWORD', '');
		}

	    return 'ok';

	});
	
#Get subdomain from URL

	$subdomain = join('.', explode('.', $_SERVER['HTTP_HOST'], -2))
	
#Database Table Count

	return $tables = DB::select('SELECT COUNT(*) FROM information_schema.tables WHERE table_schema = "test"');
	
#Get Data From Square Brackets

	[
	  {
	    "kola": 4
	  }
	]
	
	$tables = DB::select('SELECT COUNT(*) AS kola FROM information_schema.tables WHERE table_schema = "'. env('DB_DATABASE') .'"');
        $object = str_replace(array('[', ']'), '', htmlspecialchars(json_encode($tables), ENT_NOQUOTES));
    
        $countDB = null;
    
        foreach (json_decode($object) as $value) {
            $countDB .= $value;
        }
    
        return $countDB;
	
#SoftDelete via Db Connection

	DB::connection('tenantsql')
	    ->table('domains')
	    ->where('tenant_id', $tenant_id)
	    ->update(['deleted_at' => Carbon::now()]);
	    
#OTP

	function generatePin( $number ) {
	    // Generate set of alpha characters
	    $alpha = array();
	    for ($u = 65; $u <= 90; $u++) {
		// Uppercase Char
		array_push($alpha, chr($u));
	    }

	    // Just in case you need lower case
	    // for ($l = 97; $l <= 122; $l++) {
	    //    // Lowercase Char
	    //    array_push($alpha, chr($l));
	    // }

	    // Get random alpha character
	    $rand_alpha_key = array_rand($alpha);
	    $rand_alpha = $alpha[$rand_alpha_key];

	    // Add the other missing integers
	    $rand = array($rand_alpha);
	    for ($c = 0; $c < $number - 1; $c++) {
		array_push($rand, mt_rand(0, 9));
		shuffle($rand);
	    }

	    return implode('', $rand);
	}
	
#GET WITH DATA WHETE CLAUSE

	App\Models\OrderProduct::whereHas('order', function($q) use ($zone_id){
	   $q->where('division_id', $zone_id);
	})->count();
	
# update multiple row with same ID

	MenuCategory::whereIn('category_id', [$id])
				->update([
				    'label' =>  $category->name,
				    'category_name' =>  $category->name,
				]);

# categories relationship array string ["1","2"]

	function getCategoryProducts()
	{
	    $categoryWithProducts = collect();
	    $categories = Category::all();

	    foreach($categories as $category){
		$demo = new Demo;
		$id = '%' .'"'. $category->id .'"' . '%';
		$products = Product::orderBy('name')->where('category', 'like', $id)->get();
		$demo->categories = $category;
		$demo->products = $products;
		$categoryWithProducts->push($demo);
	    }

	    return $categoryWithProducts;
	}

# Secure files for download

	<?php
	    if (!isset($_SESSION['authenticated'])) {
		exit;
	    }
	    $file = '/path/to/file/outside/www/secret.pdf';

	    header('Content-Description: File Transfer');
	    header('Content-Type: application/octet-stream');
	    header('Content-Disposition: attachment; filename=' . basename($file));
	    header('Content-Transfer-Encoding: binary');
	    header('Expires: 0');
	    header('Cache-Control: must-revalidate, post-check=0, pre-check=0');
	    header('Pragma: public');
	    header('Content-Length: ' . filesize($file));
	    ob_clean();
	    flush();
	    readfile($file);
	    exit;
	?>

# XAMPP MYSQL SHUTDOWN UNEXPETEDLY

	Instead, first try using the MySQL backup folder which is included with XAMPP. So do the next:

	1. Rename the folder mysql/data to mysql/data_old (you can use any name)
	2. Create a new folder mysql/data
	3. Copy the content that resides in mysql/backup to the new mysql/data folder
	4. Copy all your database folders that are in mysql/data_old to mysql/data (skipping the mysql, performance_schema, and phpmyadmin folders from data_old)
	5. Finally copy the ibdata1 file from mysql/data_old and replace it inside mysql/data folder
	Start MySQL from XAMPP control panel
	
#PASS ROUTE PARAMETER TO MIDDLEWARE
	
	$check = SupportTicket::where('ticket_no', $request->route('ticket_no'))
                            ->with(['assigned_role' => function($query){
                                $query->where('user_id', Auth::id());
                            }])
                            ->first();
        
	if ($check->assigned_role != null) {
            return $next($request);
        }else{
            return redirect()->route('support.ticket.new')->withErrors(['You are not allowed to access this ticket']);
        }
	
# Mark as read/important AJAX
	
	// blade
	<i class="{{ $inbox->important == 1 ? 'like' : '' }} markAsImp" data-id="{{ $inbox->id }}" data-feather="star"></i>
	<input type="hidden" id="important_url" value="{{ route('support.ticket.mark.star') }}">
	
	// script
	$('.markAsImp ').on('click', function(e) {
	    var id = $(this).attr('data-id');
	    var important_url = $('#important_url').val();

	    $.ajax({
		url: important_url,
		type: 'GET',
		data: {
		    id: id
		},
		success: function (data) {
		    console.log(data);
		}
		});
	    });
	    
	// Controller
	$important = SupportTicket::where('id', $request->id)->first()->important;

	if($important == 0){
	    $mark_as_read = SupportTicket::where('id', $request->id)->update(['important' => 1]);
	}else{
	    $mark_as_read = SupportTicket::where('id', $request->id)->update(['important' => 0]);
	}

	return response()->json(['message'=> "Marked as important"]);

# Show onyl if relationship has value
	
	SupportTicket::where('user_id', Auth::user()->id)
                                ->with('replies')
                                ->has('replies')
                                ->latest()
                                ->paginate(10);
	
# calculate total completion percentage of totalAssigned and totalSolved

	$totalAssigned = 8;
        $totalSolved = 3;

        $percentage = (3 / 8) * 100;
        return round($percentage);
	
# create and write language php file

	function mTranslate($values, $lang, $type)
	{
	    $langPath = base_path().'/resources/lang/' .$lang;

	    if (!File::exists($langPath)) {
		File::makeDirectory($langPath,0775,true);
		fopen($langPath . $type .'.php', "x");
	    }

	    $path = base_path().'/resources/lang/' .$lang. '/'. $type .'.php';
	    $content = "<?php\n\nreturn\n[\n";

	    foreach ($values as $key => $value) 
	    {
		$content .= '"'.$value->key.'" => "'.$value->value.'",' ."\n";
	    }

	    $content .= '];';

	    file_put_contents($path, $content);

	}
	
# write existing config or language php file
	
	function write_arr_to_file($arr, $path){
	    $data = '';
	    foreach ($arr as $key => $value) {
		$data = $data . '"' . $key. '"=>"' . $value . '",' ."\n";
	    }

	    // file_put_contents($path, $data);
	    return file_put_contents($path, "<?php  \nreturn [".$data."];");
	}
	
