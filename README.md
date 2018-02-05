# Filemanager
Filemanager package for Laravel

## Installation
To install, run the following in your project directory:

``` bash
$ composer require itsaninho/filemanager
```

Then in `config/app.php` add the following to the `providers` array:

```
\Itsaninho\Filemanager\FilemanagerServiceProvider::class,
'Tymon\JWTAuth\Providers\JWTAuthServiceProvider',
```

Also in config/app.php, add the Facade class to the aliases array:

```
'Filemanager' => \Divart\Filemanager\Facades\Filemanager::class,
'JWTAuth' => 'Tymon\JWTAuth\Facades\JWTAuth'
```

## Configuration
To publish Filemanager's configuration file, run the following `vendor:publish` command:

```
php artisan vendor:publish --provider="Divart\Filemanager\FilemanagerServiceProvider"
php artisan vendor:publish --provider="Tymon\JWTAuth\Providers\JWTAuthServiceProvider"
```

Now for token encryption, I need to generate a secret key by running following line of code :

```
php artisan jwt:generate
```

Now I will create middleware to check if the token is valid or not and also You can handle the exception if the token is expired.

```
php artisan make:middleware VerifyJWTToken
```

Using this middleware, you can filter the request and validate the JWT token.
Now open your VerifyJWTToken middleware and put below line of code.

app/Http/Middleware/VerifyJWTToken.php
pop-uptext

```
<?php

namespace App\Http\Middleware;

use Closure;
use JWTAuth;
use Tymon\JWTAuth\Exceptions\JWTException;
use Symfony\Component\HttpKernel\Exception\UnauthorizedHttpException;

class VerifyJWTToken
{
    /**
     * Handle an incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure  $next
     * @return mixed
     */
    public function handle($request, Closure $next)
    {
        try{
            $user = JWTAuth::toUser($request->input('token'));
        }catch (JWTException $e) {
            if($e instanceof \Tymon\JWTAuth\Exceptions\TokenExpiredException) {
                return response()->json(['token_expired'], $e->getStatusCode());
            }else if ($e instanceof \Tymon\JWTAuth\Exceptions\TokenInvalidException) {
                return response()->json(['token_invalid'], $e->getStatusCode());
            }else{
                return response()->json(['error'=>'Token is required']);
            }
        }
       return $next($request);
    }
}

```
The try block in handle method check if requested token is verified by JWTAuth or not if it is not verified then exception will be handled in catch block with their status.

Now register this middleware in your kernal to run during every HTTP request to your application.
app/Http/Kernel.php

```
protected $routeMiddleware = [
        ...
        'jwt.auth' => \App\Http\Middleware\VerifyJWTToken::class,
    ];
```

## Usage

Add from ENV file:

#FILEMANAGER_LOCATION - File manager location
FILEMANAGER_LOCATION=filemanager

Available routes

To work with the file manager you need to login
by POST method, send an email address and password to this route to authorize and receive a token

http.youdomain.com/auth/login

File Manager Routes

All file manager paths have a prefix 'filemanager'

example:

method GET
http.youdomain.com/filemanager
this method open filemanager and scan him

method POST
http.youdomain.com/filemanager
this method accepts data for sorting items into a file manager

method GET
http.youdomain.com/filemanager/folder/{path to folder}
this route open this selected folder

method POST
http.youdomain.com/filemanager/folder/{path to folder}/create
this method create new folder in this directory
Need to send data: 'name' where name is name folder

method PUT
http.youdomain.com/filemanager/folder/{path to folder}/update
this method update(rename folder) selected folder in this directory
Need to send data: 'name', 'newname' where 'name' is name selected folder and 'newname' that new name folder

method DELETE
http.youdomain.com/filemanager/folder/{path to folder}/delete
this method delete selected folder in this directory
Need to send data: 'name', where value is name selected folder

method POST
http.youdomain.com/filemanager/folder/{path to folder}/changelocation
this method change location folder and the attachments in it are files
Need to send data: 'from', 'to'. 'from' - address from which of the derivatives, 'to' - where to move

method GET
http.youdomain.com/filemanager/folder/{path to file}/file/{filename}
this method send file data
Need to send data: 'name', where value is name selected folder

method POST
http.youdomain.com/filemanager/folder/{path to file}/file/create
Need to send: 'name' and 'data' where value name is name file and your expansion, 'data' is content file

method PUT
http.youdomain.com/filemanager/folder/{path to file}/file/update
Need to send: 'name' and 'data' where value name is name file and your expansion, 'data' is content file

method POST
http.youdomain.com/filemanager/folder/{path to file}/file/upload
this method upload file

method DELETE
http.youdomain.com/filemanager/folder/{path to file}/file/delete
Need to send: 'name' and the file is deleted

method POST
http.youdomain.com/filemanager/folder/{path to folder}/file/changelocation
this method change location file
Need to send data: 'from', 'to'. 'from' - address from which of the derivatives, 'to' - where to move

## License
The MIT License (MIT). Please see License File for more information."# Filemanager" 
"# Filemanager" 
