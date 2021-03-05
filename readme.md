## Profile pic uploader : - 

###[Backend - angular] - 

#### 1. create a form

```
<form action="" method="post" >
<input type="text" formControlName="userEmail" class="form-control" placeholder="Email" [ngClass]="{ 'is-invalid': submitted && f.userEmail.errors }" />

  Select image to upload:
  <input type="file" (change)="onFileChanged($event)">
  <input type="submit" value="Upload Image" name="submit">
</form>
```

#### 2. add this to component.ts
```
uploadImage(){
const uploadImageData = new FormData();
uploadImageData.append('imageFile', this.selectedFile);
uploadImageData.append('userId',this.form.value.userEmail);

this.accountService.uploadProfileImage(uploadImageData)
.pipe(first())
.subscribe({
next: () => {
this.alertService.success('Registration successful', { keepAfterRouteChange: true });
this.router.navigate(['../login'], { relativeTo: this.route });
},
error: error => {
this.alertService.error(error);
this.loading = false;
}
});
}
```

#### 3.add this to service.ts file

```
uploadProfileImage(form: any){
return this.http.post(`${environment.apiUrl}/user/upload_profile_pic`, form)
.pipe(map(user => {

// handle your response here
return user;
}));
}
```


### [Frontend]

#### 1 – Define a column attribute in your user model class as 

```
@Column(columnDefinition = "LONGTEXT")
	private String userProfilePic;\\
```

#### 2  -add this to your controller class

```
 @PostMapping("/user/upload_profile_pic")
		public ResponseEntity<?> uplaodImage(HttpServletRequest request,@RequestParam("imageFile") MultipartFile file,
				@RequestParam("userId") String userId) throws IOException {

			
			try {
				System.out.println("Original Image Byte Size - " + file.getBytes());

				User user = userService.uploadProfileImage(file, userId);
				
				String token = jwtTokenProvider.createToken(user.getUserEmail(), user.getRoles()!=null? user.getRoles(): null);
			     UserResponseDto responseDto = new UserResponseDto();
			     responseDto.setUser(user);
			     responseDto.setToken(token);
				
				return new ResponseEntity<UserResponseDto>(responseDto,HttpStatus.OK);

				    
			}  catch (Exception e) {
				e.printStackTrace();
			      throw new CustomException("Unable to upload Image", HttpStatus.NOT_FOUND);
			}
			
		
		}
```

 
## [sql]

#### 1 – if you have already created profilepic  cloumn then then run this command in sql workbench
```
alter table table_name modugy profilepic longtext
```

