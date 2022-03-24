# الواجهات

- [مقدمة](#introduction)
- [إنشاء وعرض الواجهات](#creating-and-rendering-views)
    - [ملفات الواجهات المتداخلة](#nested-view-directories)
    - [إنشاء الواجهة الأولى للعرض](#creating-the-first-available-view)
    - [التأكد من وجود واجهة](#determining-if-a-view-exists)
- [تمرير البيانات للواجهات](#passing-data-to-views)
    - [مشاركة البيانات مع الواجهات](#sharing-data-with-all-views)
- [مؤلَفو الواجهات](#view-composers)
    - [منشؤو الواجهات](#view-creators)
- [تحسين أداء الواجهات](#optimizing-views)

<a name="introduction"></a>
### مقدمة

القيام بتمرير سلاسل ملفات HTML بكاملها مباشرة عن طريق الموجهات والمتحكمات ليس عمليًا. لحسن الحظ، توفر ميزة الواجهات طريقةً مناسبة لوضع جميع ملفات HTML في مجلداتٍ خاصة. تعمل الواجهات على عزل طبقة منطق المشروع عن طبقة الواجهة المستعرضة إذ توضعُ كلٌ من هذه الملفات في مجلد 'resources/views'. قد تبدو واجهةُ بسيطة التصميم بالشكل التالي:
```blade
<!-- View stored in resources/views/greeting.blade.php -->

<html>
    <body>
        <h1>Hello, {{ $name }}</h1>
    </body>
</html>
```

بما أنَّ هذه الواجهة البسيطة حُفظت في مجلد `resources/views/greeting.blade.php`, فمن الممكن جلبها عن طريق استخدام التابع المساعد 'view' والذي يكون بنطاق global كما يلي:

    Route::get('/', function () {
        return view('greeting', ['name' => 'James']);
    });

> {tip} Looking for more information on how to write Blade templates? Check out the full [Blade documentation](/docs/{{version}}/blade) to get started.

<a name="creating-and-rendering-views"></a>
### إنشاء وعرض الواجهات

يُمكن البدء بإنشاء ملفٍ خاصٍ للواجهة بإضافة الإمتداد 'blade.php.' لنهاية ملفٍ جديد داخل 'resources/views'. إنَّ امتداد  'blade.php.' يُعطي أمرًا لإطار العمل بأن هذا الملف الجديد يحتوي على [قالب blade] (/docs/{{version}}/blade). تحتوي قوالب blade على HTML وملفاتٍ خاصة تسمح بإظهار القيم وإنشاء عباراتٍ شرطية (if statements) والمرور على البيانات بسهولة والمزيد.

بعد إنشاء واجهةٍ ما، يُمكن استدعائها عن طريق أحد الموجهات أو المتحكمات باستخدام التابع المساعد 'view' الذي يأتي بنطاق global تلقائيًا.
    Route::get('/', function () {
        return view('greeting', ['name' => 'James']);
    });

كما يُمكن جلب الواجهات باستخدام 'View'  للواجهات الساكنة.

    use Illuminate\Support\Facades\View;

    return View::make('greeting', ['name' => 'James']);

كما هو واضح، فإن أول argument توضع داخل التابع المساعد 'view' تُماثل اسم الواجهة الموجودة ضمن ملف 'resources/views'. ال argument الثانية هي صفيفة(array)  بيانات بإستطاعة الواجهة عرضها. في هذه الحالة، نقوم بتمرير المتغير 'name'، لكي يُعرض في الواجهة المكتوبة بصيغة Blade (/docs/{{version}}/blade).

<a name="nested-view-directories"></a>
### ملفات الواجهات المتداخلة

قد توضع ملفات الواجهات ضمن ملفاتٍ ثانوية ضمن الملف الرئيس الموجود في `resources/views`. ومن الممكن جلب هذه الملفات باستخدام صيغة 'Dot'. كمثال، إن كان ملف الواجهة مخزنًا في `resources/views/admin/profile.blade.php`, أمكن جلب هذا الملف عن طريق إحدى الموجهات/المتحكمات بالشكل التالي:

    return view('admin.profile', $data);

> {note} View directory names should not contain the `.` character.

<a name="creating-the-first-available-view"></a>
### إنشاء الواجهة الأولى للإستعراض

باستخدام دالة 'first' للواجهات الساكنة، من الممكن إنشاء أول واجهةٍ ليتم عرضها من بين واجهاتٍ أخرى ضمن صفيفةٍ ما. تُفيد هذه الخاصة في حال كان التطبيق أو الحزمة يُتيحُ إمكانية تعديل أو إستبدال الواجهات:

    use Illuminate\Support\Facades\View;

    return View::first(['custom.admin', 'admin'], $data);

<a name="determining-if-a-view-exists"></a>
### التأكد من وجود واجهةٍ ما

إن كنت بحاجةٍ إلى التأكد من وجود واجهةٍ ما، فمن من الممكن استخدام 'View' في الواجهات الساكنة. باستخدام دالة 'exists'، تستطيع الحصول على قيمة 'صحيح(true) ' في حال كانت الواجهة موجودة:

    use Illuminate\Support\Facades\View;

    if (View::exists('emails.customer')) {
        //
    }

<a name="passing-data-to-views"></a>
## تمرير البيانات إلى الواجهات

كما رأينا في الأمثلة السابقة، من الممكن تمرير صفيفةٍ من البيانات إلى الواجهات لعرضها:

    return view('greetings', ['name' => 'Victoria']);

عند تمرير المعلومات بهذا الشكل، يجب وضع البيانات بشكل ثنائيات key/value. بعد تزويد الواجهة بالبيانات، يُمكن الوصول لكل قيمة عن طريق المفاتيح المخصصة للبيانات الموجودة، مثل `<?php echo $name; ?>`.

ومن الممكن بدلًا من أن توضع صفيفاتٍ كاملة مليئة بالبيانات داخل التابع المساعد 'view'، الاستعاضة عنها باستخدام الدالة 'with' لإضافة أجزاءٍ معينة من البيانات للواجهة. تقوم دالة 'with' بإرجاع نسخة (instance) من الكائن الموجود ضمن الواجهة وهذا يُتيح إمكانية كتابة سلسلةٍ من هذه التوابع حتى عرض الواجهة المطلوبة:

    return view('greeting')
                ->with('name', 'Victoria')
                ->with('occupation', 'Astronaut');

<a name="sharing-data-with-all-views"></a>
### مشاركة البيانات مع جميع الواجهات

في بعض الأحيان، نحتاج لمشاركة البيانات مع جميع الواجهات المعروضة في التطبيق. هذا ممكن عن طريق استخدام الدالة الخاصة بالواجهات الساكنة 'View' والتي تدعى 'share'. من الاعتيادي استدعاء دالة 'share' من داخل دالة 'boot' الموجودة في ملف مقدم الخدمات. لك الحرية في إضافة هذه الدوال إلى داخل المجلد الرئيس `App\Providers\AppServiceProvider` كصنف أو إنشاء مجلدٍ منفردٍ خاص لاحتوائها:

    <?php

    namespace App\Providers;

    use Illuminate\Support\Facades\View;

    class AppServiceProvider extends ServiceProvider
    {
        /**
         * Register any application services.
         *
         * @return void
         */
        public function register()
        {
            //
        }

        /**
         * Bootstrap any application services.
         *
         * @return void
         */
        public function boot()
        {
            View::share('key', 'value');
        }
    }

<a name="view-composers"></a>
## View Composers

View composers are callbacks or class methods that are called when a view is rendered. If you have data that you want to be bound to a view each time that view is rendered, a view composer can help you organize that logic into a single location. View composers may prove particularly useful if the same view is returned by multiple routes or controllers within your application and always needs a particular piece of data.

Typically, view composers will be registered within one of your application's [service providers](/docs/{{version}}/providers). In this example, we'll assume that we have created a new `App\Providers\ViewServiceProvider` to house this logic.

We'll use the `View` facade's `composer` method to register the view composer. Laravel does not include a default directory for class based view composers, so you are free to organize them however you wish. For example, you could create an `app/View/Composers` directory to house all of your application's view composers:

    <?php

    namespace App\Providers;

    use App\View\Composers\ProfileComposer;
    use Illuminate\Support\Facades\View;
    use Illuminate\Support\ServiceProvider;

    class ViewServiceProvider extends ServiceProvider
    {
        /**
         * Register any application services.
         *
         * @return void
         */
        public function register()
        {
            //
        }

        /**
         * Bootstrap any application services.
         *
         * @return void
         */
        public function boot()
        {
            // Using class based composers...
            View::composer('profile', ProfileComposer::class);

            // Using closure based composers...
            View::composer('dashboard', function ($view) {
                //
            });
        }
    }

> {note} Remember, if you create a new service provider to contain your view composer registrations, you will need to add the service provider to the `providers` array in the `config/app.php` configuration file.

Now that we have registered the composer, the `compose` method of the `App\View\Composers\ProfileComposer` class will be executed each time the `profile` view is being rendered. Let's take a look at an example of the composer class:

    <?php

    namespace App\View\Composers;

    use App\Repositories\UserRepository;
    use Illuminate\View\View;

    class ProfileComposer
    {
        /**
         * The user repository implementation.
         *
         * @var \App\Repositories\UserRepository
         */
        protected $users;

        /**
         * Create a new profile composer.
         *
         * @param  \App\Repositories\UserRepository  $users
         * @return void
         */
        public function __construct(UserRepository $users)
        {
            // Dependencies are automatically resolved by the service container...
            $this->users = $users;
        }

        /**
         * Bind data to the view.
         *
         * @param  \Illuminate\View\View  $view
         * @return void
         */
        public function compose(View $view)
        {
            $view->with('count', $this->users->count());
        }
    }

As you can see, all view composers are resolved via the [service container](/docs/{{version}}/container), so you may type-hint any dependencies you need within a composer's constructor.

<a name="attaching-a-composer-to-multiple-views"></a>
#### Attaching A Composer To Multiple Views

You may attach a view composer to multiple views at once by passing an array of views as the first argument to the `composer` method:

    use App\Views\Composers\MultiComposer;

    View::composer(
        ['profile', 'dashboard'],
        MultiComposer::class
    );

The `composer` method also accepts the `*` character as a wildcard, allowing you to attach a composer to all views:

    View::composer('*', function ($view) {
        //
    });

<a name="view-creators"></a>
### View Creators

View "creators" are very similar to view composers; however, they are executed immediately after the view is instantiated instead of waiting until the view is about to render. To register a view creator, use the `creator` method:

    use App\View\Creators\ProfileCreator;
    use Illuminate\Support\Facades\View;

    View::creator('profile', ProfileCreator::class);

<a name="optimizing-views"></a>
## Optimizing Views

By default, Blade template views are compiled on demand. When a request is executed that renders a view, Laravel will determine if a compiled version of the view exists. If the file exists, Laravel will then determine if the uncompiled view has been modified more recently than the compiled view. If the compiled view either does not exist, or the uncompiled view has been modified, Laravel will recompile the view.

Compiling views during the request may have a small negative impact on performance, so Laravel provides the `view:cache` Artisan command to precompile all of the views utilized by your application. For increased performance, you may wish to run this command as part of your deployment process:

```shell
php artisan view:cache
```

You may use the `view:clear` command to clear the view cache:

```shell
php artisan view:clear
```

