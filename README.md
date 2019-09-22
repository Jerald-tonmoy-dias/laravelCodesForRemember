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
