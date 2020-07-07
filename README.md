# laravelCodesForRemember

//.htaccess
	
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
	
