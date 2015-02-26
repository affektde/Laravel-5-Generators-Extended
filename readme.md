# Full Readme Coming Soon

If you're familiar with my Laravel 4 Generators, then this is basically the same thing - just upgraded for Laravel 5.

L5 includes a bunch of generators out of the box, so this just adds a few things, like:

- `make:migration:schema`
- `make:migration:pivot`
- `make:seed`


## Usage

### Step 1: Install Through Composer

```
composer require 'laracasts/generators'
```

### Step 2: Add the Service Provider

Open `config/app.php` and, to your "providers" array, at the bottom, add:

```
Laracasts\Generators\GeneratorsServiceProvider
```

### Step 3: Run Artisan!

You're all set. Run `php artisan` from the console, and you'll see the new commands in the `make:*` namespace section.

## Examples:

I'll elaborate more in the future. For now:

### Generate a Pivot Table for Posts and Tags

```
php artisan make:migration:pivot tags posts
```

This will give you:

```
<?php

use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class CreatePostTagPivotTable extends Migration {

	/**
	 * Run the migrations.
	 *
	 * @return void
	 */
	public function up()
	{
		Schema::create('post_tag', function(Blueprint $table)
		{
			$table->integer('post_id')->unsigned()->index();
			$table->foreign('post_id')->references('id')->on('posts')->onDelete('cascade');
			$table->integer('tag_id')->unsigned()->index();
			$table->foreign('tag_id')->references('id')->on('tags')->onDelete('cascade');
		});
	}

	/**
	 * Reverse the migrations.
	 *
	 * @return void
	 */
	public function down()
	{
		Schema::drop('post_tag');
	}

}
```

> Notice that the naming conventions are being followed here, regardless of what order you pass the table names.

### Generate a Migration Class With Schema

```
php artisan make:migration:schema create_users_table --schema="username:string, email:string:unique, age:integer:nullable, password:string"
```

This will give you:

```
<?php

use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class CreateUsersTable extends Migration {

	/**
	 * Run the migrations.
	 *
	 * @return void
	 */
	public function up()
	{
		Schema::create('users', function(Blueprint $table) {
            $table->increments('id');
            $table->string('username');
            $table->string('email')->unique();
            $table->integer('age')->nullable();
            $table->string('password');
            $table->timestamps();
        });
	}

	/**
	 * Reverse the migrations.
	 *
	 * @return void
	 */
	public function down()
	{
		Schema::drop('users');
	}

}
```

When generating migrations with schema, the name of your migration (like, "create_users_table") matters. We use it to figure out what you're trying to do. In this case, we began with the "create" keyword, which signals that we want to create a
new table.

Alternatively, we can use the "remove" or "add" keywords, and the generated boilerplate will adapt, as needed. Let's create a migration to remove a column.

```
php artisan make:migration:schema remove_user_id_from_posts_table --schema="user_id:integer"
```

Now, notice that we're using the correct Schema methods.

```
<?php

use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class RemoveUserIdFromPostsTable extends Migration {

	/**
	 * Run the migrations.
	 *
	 * @return void
	 */
	public function up()
	{
		Schema::table('posts', function(Blueprint $table) {
            $table->dropColumn('user_id');
        });
	}

	/**
	 * Reverse the migrations.
	 *
	 * @return void
	 */
	public function down()
	{
		Schema::table('posts', function(Blueprint $table) {
            $table->integer('user_id');
        });
	}

}
```

### Generate a Database Seeder Class

```
php artisan make:seed posts
```

This one is fairly basic. It just gives you a quick seeder class in the "database/seeds" folder.

```
<?php

use Illuminate\Database\Seeder;

// composer require laracasts/testdummy
use Laracasts\TestDummy\Factory as TestDummy;

class PostsTableSeeder extends Seeder {

    public function run()
    {
        // TestDummy::times(20)->create('App\Post');
    }

}
```