# laravelCodesForRemember

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
