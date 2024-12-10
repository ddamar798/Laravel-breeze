create database connection
php artisan migrate [ comand promt ]

# installation breeze
composer require laravel/breeze --dev
php artisan breeze:install blade
php artisan migrate
npm install
npm run build

------------------------------------------------------------------
SETING EMAIL
==================================================================
MAIL_MAILER=smtp
MAIL_HOST=smtp.gmail.com
MAIL_PORT=465
MAIL_USERNAME=damarxtech999@gmail.com
MAIL_PASSWORD=mzpd kaym snbj zpzd
MAIL_ENCRYPTION=tls
MAIL_FROM_ADDRESS="damarxtech999@gmail.com"
MAIL_FROM_NAME="${APP_NAME}"
===================================================================

To Send Verivication Email
===================================================================
app/models/user.php
use Illuminate\Contracts\Auth\MustVerifyEmail; [ remove coment ]
class User extends Authenticatable implements MustVerifyEmail [ edit class user]

---------------------------------------------------------------------
ADD TABLE USER
=====================================================================
 php artisan make:migration add_username_field_in_users_tables [ comand ]

RUN THE MIGRATION :
  public function up(): void
    {
        Schema::table('users', function (Blueprint $table) {
            $table->string('username')->after('name');
        });
    }

REVERSE THE MIGRATION :
    public function down(): void
    {
        Schema::table('users_tables', function (Blueprint $table) {
            $table->dropColumn('username');
        });
    }

    php artisan migrate [ comand ]

    ----------------------------------------------------------------
    ADD USERNAME TO INPUT
    ================================================================
    views/auth/register

     <!-- Username -->
        <div>
            <x-input-label for="username" :value="__('Username')" />
            <x-text-input id="username" class="block mt-1 w-full" type="text" name="username" :value="old('username')" required autofocus autocomplete="username" />
            <x-input-error :messages="$errors->get('username')" class="mt-2" />
        </div>

Tambahkan input ^ 

model/user.php
protected $fillable = [
        'name',
       --> 'username', 
        'email',
        'password',
    ];

    app/http/Auth/registereduserController.php
     public function store(Request $request): RedirectResponse
    {
        $request->validate([
            'name' => ['required', 'string', 'max:255'],
           --> 'username' => ['required', 'string', 'max:255','unique:users,username', 'alpha_num'],
            'email' => ['required', 'string', 'lowercase', 'email', 'max:255', 'unique:'.User::class],
            'password' => ['required', 'confirmed', Rules\Password::defaults()],
        ]);

        $user = User::create([
            'name' => $request->name,
           --> 'username' => $request->username,
            'email' => $request->email,
            'password' => Hash::make($request->password),
        ]);

        event(new Registered($user));

        Auth::login($user);

        return redirect(route('dashboard', absolute: false));
    }
