# HTTP Routing

- [Basic Routing](#basic-routing)
- [Route Parameters](#route-parameters)
	- [Required Parameters](#required-parameters)
	- [Regular Expression Constraints](#parameters-regular-expression-constraints)
- [Named Routes](#named-routes)
- [Route Groups](#route-groups)
	- [Middleware](#route-group-middleware)
	- [Namespaces](#route-group-namespaces)
	- [Route Prefixes](#route-group-prefixes)
- [CSRF Protection](#csrf-protection)
	- [Introduction](#csrf-introduction)
	- [X-CSRF-Token](#csrf-x-csrf-token)
	- [X-XSRF-Token](#csrf-x-xsrf-token)
- [Form Method Spoofing](#form-method-spoofing)
- [Throwing 404 Errors](#throwing-404-errors)

<a name="basic-routing"></a>
## Basic Routing

သင့်　Application အတွက်　Route တွေကို　`app/Http/routes.php`　ဖိုင်မှာသတ်မှတ်ပေးရမှာဖြစ်ပါတယ်။　အဲဒီ　Route ဖိုင်ကို　`bootstrap/app.php`　ဖိုင်က　Load လုပ်ပေးမှာဖြစ်ပါတယ်။　အရိုးရှင်းဆုံး　Lumen Route တစ်ခုကို　URI တစ်ခုနဲ့　Closure တစ်ခုသုံးပြီး　တည်ဆောက်နိုင်ပါတယ်:

	$app->get('/', function () {
		return 'Hello World';
	});

	$app->post('foo/bar', function () {
		return 'Hello World';
	});

	$app->put('foo/bar', function () {
		//
	});

	$app->delete('foo/bar', function () {
		//
	});



#### သတ်မှတ်ထားသော　Route များအတွက်　URL များထုတ်ခြင်း

သင့်　Application ရဲ့　Route တွေကိုခေါ်လို့ရမဲ့　URL တွေကို　`url` helperကိုသုံးပြီး　ခေါ်သုံးလို့ရပါတယ်။
	$url = url('foo');

<a name="route-parameters"></a>
## Route Parameters

<a name="required-parameters"></a>
### Required Parameters

တစ်ခါတစ်လေ　သင့်ကုတ်အတွင်းက　နေ URI ထဲကအပိုင်းတစ်ပိုင်းကိုအသုံးပြုချင်တာမျိုးရှိမှာပါ။ဥပမာ user တစ်ယောက်ရဲ့ ID ကို URL အတွင်းမှာ ထည့်သုံးတာမျိုးပေ့ါ။　အဲဒီလို　အခြေအနေအတွက်　route parameters　တွေကိုသုံးနိုင်ပါတယ်။

	$app->get('user/{id}', function ($id) {
		return 'User '.$id;
	});

သင့်　အနေနဲ့　route parameters　တွေကို　သင့်　route လိုအပ်ချက်အတိုင်း　ကြိုက်သလောက်အသုံးပြုနိုင်ပါတယ်။

	$app->get('posts/{post}/comments/{comment}', function ($postId, $commentId) {
		//
	});

Route parameters　တွေကို　တွန့်ကွင်းနှစ်ခုထဲမှာ　ထည့်သွင်းရေးပေးရပါတယ်။ သင်ထည့်သွင်းလိုက်တဲ့ Route Parameter တွေကို သင့် Route အလုပ်လုပ်တဲ့အခါကျရင် သင့် Route Closure function ကို parameters အဖြစ်ထည့်သွင်းပေးလိုက်မှာ ဖြစ်ပါတယ်။

> **Note:** Route parameters တွေမှာ `-`စာလုံးတော့ပါလို့မရပါဘူး။ အဲဒီအစား (`_`) underscore စာလုံးကို သုံးနိုင်ပါတယ်။

<a name="parameters-regular-expression-constraints"></a>
### Regular Expression များဖြင့်ကန့်သတ်ခြင်း

သင့် route parameters များကို regular expression တွေသုံးပြီးတော့ ဘယ်လို တန်ဖိုးမျိုးပဲ လက်ခံမယ်လို့ ကန့်သတ်နိုင်ပါတယ်။

	$app->get('user/{name:[A-Za-z]+}', function ($name) {
		//
	});

<a name="named-routes"></a>
## Named Routes

URL တွေအလွယ်တကူ ဖန်တီးခြင်းတာပဲဖြစ်ဖြစ် သက်ဆိုင်ရာ Route တစ်ခုကို Redirect ပေးချင်တာပဲဖြစ်ဖြစ်　Route တွေကို　နာမည်ပေးပြီး　Named Route တွေကိုသုံးနိုင်ပါတယ်။　Route တစ်ခုသတ်မှတ်တဲ့　အချိန်မှာ　`as` array key　ကိုသုံးပြီး　အဲဒီ　Route ကို　နာမည်တစ်ခုသတ်မှတ်ပေးနိုင်ပါတယ်။

	$app->get('user/profile', ['as' => 'profile', function () {
		//
	}]);

Route Closure တွေမသုံးပဲ　controller action အတွဲများကို　လည်း　Named Route အနေနဲ့　သုံးနိုင်ပါတယ်။

	$app->get('user/profile', [
		'as' => 'profile', 'uses' => 'UserController@showProfile'
	]);

#### Named Routes　များအတွက်　URL များ　ထုတ်ခြင်း

သင့်အနေနဲ့　Route တစ်ခုကို　နာမည်ပေးပြီးတာနဲ့　 `route` function　ကိုသုံးပြီး　URL တွေထုတ်တာပဲဖြစ်ဖြစ်၊　Redirect လုပ်တာပဲဖြစ်ဖြစ်လုပ်နိုင်ပါတယ်။

	$url = route('profile');

	$redirect = redirect()->route('profile');

တကယ်လို့ Route အတွင်းမှာ parameters တွေသတ်မှတ်ထားတယ်ဆိုရင် `route` method ရဲ့ ဒုတိယ argument အဖြစ် Parameter Array ကိုထည့်သွင်းပေးနိုင်ပါတယ်။အဲဒါဆို　URL ထွက်လာတဲ့　အခါ　အလိုအလျောက်　parameter တွေပါ　ထည့်သွင်းပေးသွားမှာဖြစ်ပါတယ်။

	$app->get('user/{id}/profile', ['as' => 'profile', function ($id) {
		//
	}]);

	$url = route('profile', ['id' => 1]);

<a name="route-groups"></a>
## Route Groups

သင့်အနေနဲ့　Route Attribute တွေ　ဖြစ်တဲ့　Middleware တွေ　Namespace တွေ　ကို　ထပ်ခါထပ်ခါ　မရေးပဲ Route အများကြီးအတွက်　မျှဝေသုံးစွဲချင်ရင် Route Group တွေကိုသုံးနိုင်ပါတယ်။ မျှဝေသုံးလိုတဲ့ Attributes တွေကို `$app->group` method မှာ array format နဲ့ ပထမ Argument အဖြစ်ထည့်သွင်းပေးနိုင်ပါတယ်။

Route Group တွေရဲ့ အသုံးများတဲ့ ပုံစံတွေကို ဆက်လက်ရှင်းပြသွားပါမယ်။

<a name="route-group-middleware"></a>
### Middleware

Middleware တစ်ခုကို Route Group တစ်ခုအတွင်းက Route အားလုံးအတွက် တာဝန်ပေးချင်ရင် အဲဒီ Route Group ရဲ့ Attribute Array မှာ `middleware` key ကိုသုံးပြီး တာဝန်ပေးနိုင်ပါတယ်။ တာဝန်ပေးထားတဲ့ အစီအစဉ်အတိုင်းပဲ　Middleware တွေက　အလုပ်လုပ်မှာဖြစ်ပါတယ်။

	$app->group(['middleware' => 'auth'], function ($app) {
		$app->get('/', function ()	{
			// Uses Auth Middleware
		});

		$app->get('user/profile', function () {
			// Uses Auth Middleware
		});
	});

<a name="route-group-namespaces"></a>
### Namespaces

နောက်ထပ်　အသုံးများတဲ့　use-case တစ်ခုကတော့　Controller တွေကို　 PHP namespace တစ်ခုအောက်မှာ　ပေါင်းပြီးထားချင်တဲ့　အခါမှာ　သုံးခြင်းဖြစ်ပါတယ်။　အဲဒီအတွက်　Route Group ရဲ့　Attribute Array မှာ　`namespace` parameter ကို　သုံးပါတယ်။

	$app->group(['namespace' => 'App\Http\Controllers\Admin'], function ($app) {

		// Controllers Within The "App\Http\Controllers\Admin" Namespace

	});

<a name="route-group-prefixes"></a>
### Route Prefixes

Route Group တစ်ခုအတွင်းမှာရှိတဲ့　Route အကုန်လုံးကို　သတ်မှတ်ထားတဲ့　ရှေ့ဆွယ်　URI တစ်ခုပေးချင်တဲ့　အခါ　`prefix`　attribute ကိုသုံးပြီးသတ်မှတ်ပေးနိုင်ပါတယ်။　ဥပမာအားဖြင့်　ပေးထားတဲ့　Group ရဲ့　Route တွေအားလုံးကို　URL မှာ　`admin`ဆိုတဲ့　ရှေ့ဆွယ်တစ်ခု　ထားချင်တာမျိုး　မှာသုံးပါတယ်။ 

	$app->group(['prefix' => 'admin'], function ($app) {
		$app->get('users', function ()	{
			// Matches The "/admin/users" URL
		});
	});

သင့်အနေနဲ့　`prefix` key　ကိုသုံးပြီး　Route parameter တစ်ခုကိုမျှဝေသုံးစွဲတာမျိုးလည်း　လုပ်လို့ရပါတယ်။

	$app->group(['prefix' => 'accounts/{account_id}'], function ($app) {
		$app->get('detail', function ($account_id)	{
			// Matches The accounts/{account_id}/detail URL
		});
	});

<a name="csrf-protection"></a>
## CSRF Protection

> **Note:**သင့်အနေနဲ့　ဒီ　Feature ကိုမသုံးခင်　[Session တွေကို　Enable လုပ်ထား　](/docs/session)ဖို့တော့လိုပါတယ်။

<a name="csrf-introduction"></a>
### မိတ်ဆက်

Lumen က　သင့်　Application ကို　[cross-site request forgeries](http://en.wikipedia.org/wiki/Cross-site_request_forgery)　တိုက်ခိုက်မှုတွေကနေ　ကာကွယ်ဖို့လွယ်ကူအောင်ဖန်တီးပေးထားပါတယ်။　Cross-site request forgeries　ဆိုတာက　သင့်　Application　က　ခွင့်ပြုထားတဲ့　User တစ်ယောက်နေရာဝင်ပြီး　ခွင့်မပြုထားတဲ့　ကိစ္စတွေကို　ဝင်လုပ်ဖို့ကြိုးစားတဲ့　တိုက်ခိုက်မှုတစ်မျိုးဖြစ်ပါတယ်။

သင့်　Application ကို　လက်ရှိအသုံးပြုနေတဲ့　User တိုင်းရဲ့　Active Session တစ်ခုချင်းစီအတွက်　CSRF "token"တစ်ခုစီကို　Lumen က　ဖန်တီးပေးပါတယ်။　အဲဒီ Token ကို　အသုံးပြုပြီး　ရောက်ရှိလာတဲ့　Request ဟာ　လက်ရှိ　Session ကို　တကယ်သုံးနေတဲ့　User ဆီကလာတာဟုတ်မဟုတ်ကို　စစ်ဆေးပေးပါတယ်။　လက်ရှိ　 CSRF tokenကို　ရယူချင်ရင်　`csrf_token` helperကိုသုံးပြီးရယူနိုင်ပါတယ်။

	<?php echo csrf_token(); ?>

	<input type="hidden" name="_token" value="<?php echo csrf_token(); ?>">

သင့်အနေနဲ့　POST, PUT, ဒါမှမဟုတ် DELETE　Request တွေမှာ　CSRF token　ကို　မိမိဘာသာသီးသန့်စစ်ဆေးစရာမလိုပါဘူး။　`VerifyCsrfToken` [HTTP middleware](/docs/middleware)　က　Request Input ထဲက　Token ဟာ　Session ထဲက　Token နဲ့ကိုက်မကိုက်　စစ်ဆေးသွားပါလိမ့်မယ်။　

<a name="csrf-x-csrf-token"></a>
### X-CSRF-TOKEN

Lumen ရဲ့　`VerifyCsrfToken`　middleware ဟာ　POST parameter　အဖြစ်ထည့်ပေးလိုက်တဲ့　Input အပြင်　`X-CSRF-TOKEN` request header　တန်ဖိုးကိုပါ　ရှာပြီးစစ်ဆေးမှာဖြစ်ပါတယ်။ ဥပမာ Token ကို "meta" tag အနေနဲ့ အောက်ပါအတိုင်းထည့်၊

	<meta name="csrf-token" content="{{ csrf_token() }}">

ထည့်ပြီးတာနဲ့ j Query လို libraray တစ်ခုကို သုံးပြီး Ajax Request အားလုံးရဲ့ request header တွေထဲမှာ ထည့်ပေးလိုက်လို့ရပါတယ်။　ဒီနည်းနဲ့　သင့်　Ajax Application တွေမှာပါ　 CSRF protection　ထည့်သွင်းအသုံးပြုနိုင်ပါတယ်။

	$.ajaxSetup({
			headers: {
				'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
			}
	});

<a name="csrf-x-xsrf-token"></a>
### X-XSRF-TOKEN

Lumen ဟာ　CSRF token　ကို　`XSRF-TOKEN` cookie　ထဲမှာ　လည်းထည့်သိမ်းပေးပါတယ်။ သင့်အနေနဲ့ အဲဒီ cookie ကို သုံးပြီး X-XSRF-TOKEN` request header ထည့်လည်းရပါတယ်။　Angular လိုမျိုး　အချို့သော　Javascript Framework တွေက　အထက်ပါအတိုင်း　အလိုအလျောက်လုပ်ဆောင်ပေးပါတယ်။　အဲဒီတော့　ဒီ　Cookie ကို　မိမိဘာသာ　သုံးရတာမျိုးကတော့　နည်းနည်းတော့　ရှားပါတယ်။

<a name="form-method-spoofing"></a>
## Form Method Spoofing

HTML form တွေဟာ　`PUT`, `PATCH` နဲ့ `DELETE`　method တွေကို　မပံ့ပိုးပေးထားပါဘူး။　ဒါကြောင့်　အထက်ပါ　`PUT`, `PATCH` နဲ့ `DELETE`　Route တွေကို　HTML form ကနေခေါ်သုံးတဲ့　အခါ　သင့်အနေနဲ့　`_method`ဆိုတဲ့　hidden field တစ်ခုကို　ဖန်တီးပေးရပါတယ်။　အဲဒီ `_method` fieldရဲ့ တန်ဖိုးကို HTTP request method အဖြစ် Lumen ကသုံးပါလိမ့်မယ်။

	<form action="/foo/bar" method="POST">
		<input type="hidden" name="_method" value="PUT">
		<input type="hidden" name="_token" value="{{ csrf_token() }}">
	</form>

<a name="throwing-404-errors"></a>
## Throwing 404 Errors

Route တစ်ခုကနေ 404 error တစ်ခု မိမိဘာသာဖန်တီးမယ်ဆိုရင် နည်းလမ်းနှစ်မျိုးရှိပါတယ်။ ပထမဆုံး သင့်အနေနဲ့ `abort` helper ကို သုံးနိုင်ပါတယ်။`abort` helper　ကတကယ်တော့　`Symfony\Component\HttpFoundation\Exception\HttpException`　ဆိုတဲ့　Exception တစ်ခုကို　ထည့်ပေးလိုက်တဲ့　Status Code နဲ့　Throw လုပ်ပေးတာပဲဖြစ်ပါတယ်။


	abort(404);

ဒုတိယတစ်နည်းကတော့　သင်ကိုယ်တိုင်　`Symfony\Component\HttpKernel\Exception\NotFoundHttpException`　instance တစ်ခုကို　Throw　လုပ်တာပါ။

404 exceptions　တွေ　Handle လုပ်ပုံလုပ်နည်း　နဲ့　ကိုယ်ပိုင်　Error Response တွေဖန်တီးနည်းတွေနဲ့　ပတ်သက်တဲ့　အချက်အလက်တွေကို　Documentation ရဲ့　[errors](/docs/errors#http-exceptions)　အပိုင်းမှာ　ဖတ်ရှုနိုင်ပါတယ်။
