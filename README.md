# [Paper Dashboard Pro Laravel](https://www.creative-tim.com/live/paper-dashboard-pro-laravel/?ref=pdl-readme) [![Tweet](https://img.shields.io/twitter/url/http/shields.io.svg?style=social&logo=twitter)](https://twitter.com/home?status=Material%20Dashboard%20Pro%20Laravel%E2%9D%A4%EF%B8%8F%0Ahttps%3A//paper-dashboard-pro-laravel.creative-tim.com/%20%23%material%20%23design%20%23dashboard%20%23laravel%20%23pro%20via%20%40CreativeTim)

![version](https://img.shields.io/badge/version-1.0.1-blue.svg) ![license](https://img.shields.io/badge/license-MIT-blue.svg) [![GitHub issues open](https://img.shields.io/github/issues/creativetimofficial/ct-paper-dashboard-pro-laravel.svg?maxAge=2592000)](https://github.com/creativetimofficial/ct-paper-dashboard-pro-laravel/issues?q=is%3Aopen+is%3Aissue) [![GitHub issues closed](https://img.shields.io/github/issues-closed-raw/creativetimofficial/ct-paper-dashboard-pro-laravel/ct-paper-dashboard-pro-laravel.svg?maxAge=2592000)](https://github.com/creativetimofficial/ct-paper-dashboard-pro-laravel/issues?q=is%3Aissue+is%3Aclosed)

*Frontend version*: Paper Dashboard v2.0.1. More info at https://www.creative-tim.com/product/paper-dashboard-2-pro/?ref=pdl-readme

![Product Image](https://github.com/creativetimofficial/public-assets/raw/master/paper-dashboard-2-pro-laravel/intro.gif?raw=true)

Speed up your web development with the Bootstrap 4 Admin Dashboard built for Laravel Framework 5.5 and up.

## Prerequisites

If you don't already have an Apache local environment with PHP and MySQL, use one of the following links:

 - Windows: https://updivision.com/blog/post/beginner-s-guide-to-setting-up-your-local-development-environment-on-windows
 - Linux: https://howtoubuntu.org/how-to-install-lamp-on-ubuntu
 - Mac: https://wpshout.com/quick-guides/how-to-install-mamp-on-your-mac/

Also, you will need to install Composer: https://getcomposer.org/doc/00-intro.md

## Installation

1. Unzip the downloaded archive
2. Copy and paste **paper-dashboard-master** folder in your **projects** folder. Rename the folder to your project's name
3. In your terminal run `composer install`
4. Copy `.env.example` to `.env` and updated the configurations (mainly the database configuration)
5. In your terminal run `php artisan key:generate`
6. Run `php artisan migrate --seed` to create the database tables and seed the roles and users tables
7. Run `php artisan storage:link` to create the storage symlink (if you are using **Vagrant** with **Homestead** for development, remember to ssh into your virtual machine and run the command from there).

## Usage

To start testing the Pro theme, register as a user or log in using one of the default users: 

- admin type - **admin@paper.com** with the password **secret**
- creator type - **creator@paper.com** with the password **secret**
- member type - **member@paper.com** with the password **secret**

Make sure to run the migrations and seeders for the above credentials to be available.

In addition to the features included in the free preset, the Pro theme also has a role management example with an updated user management, as well as tag management, category management and item management examples. All the necessary files (controllers, requests, views) are installed out of the box and all the needed routes are added to `routes/web.php`. Keep in mind that all the features can be viewed once you log in using the credentials provided above or by registering your own user.

Each role has a different privilege level and can perform a certain number of actions according to this level.  

A **member type** user can log in, update his profile and view a list of added items.  
A **creator type** user can log in, update his profile and perform actions on categories, tags and items.  
A **admin type** user can log in, update his profile and perform actions on categories, tags, items, roles and users.  

### Dashboard

You can access the dashboard either by using the "**Dashboards/Dashboard**" link in the left sidebar or by adding **/home** in the URL.

### Profile edit

You have the option to edit the current logged in user's profile information (name, email, profile picture) and password. To access this page, just click the "**Examples/Profile**" link in the left sidebar or add **/profile** in the URL.

The `App\Http\Controllers\ProfileController` handles the update of the user information and password.

```
public function update(ProfileRequest $request)
{
    auth()->user()->update(
        $request->merge(['picture' => $request->photo ? $request->photo->store('profile', 'public') : null])
            ->except([$request->hasFile('photo') ? '' : 'picture'])
    );

    return back()->withStatus(__('Profile successfully updated.'));
}

/**
* Change the password
*
* @param  \App\Http\Requests\PasswordRequest  $request
* @return \Illuminate\Http\RedirectResponse
*/
public function password(PasswordRequest $request)
{
    auth()->user()->update(['password' => Hash::make($request->get('password'))]);

    return back()->withStatus(__('Password successfully updated.'));
}
```

If you input the wrong data when editing the profile, don`t worry. Validation rules have been added to prevent this (see `App\Http\Requests\ProfileRequest`). If you try to change the password, you will see that additional validation rules have been added in `App\Http\Requests\PasswordRequest`. You also have  a custom validation rule that can be found in `App\Rules\CurrentPasswordCheckRule`.

```
public function rules()
{
    return [
        'old_password' => ['required', 'min:6', new CurrentPasswordCheckRule],
        'password' => ['required', 'min:6', 'confirmed', 'different:old_password'],
        'password_confirmation' => ['required', 'min:6'],
    ];
}
```

### Role management

The Pro theme allows you to add user roles. By default, the theme comes with **Admin**, **Creator** and **Member** roles. To access the role management example click the "**Examples/Role Management**" link in the left sidebar or add **/role** to the URL. Here you can add/edit new roles. Deletion is not allowed in this section.
To add a new role, click the "**Add role**" button. To edit an existing role, click the dotted menu (available on every table row) and then click "**Edit**". In both cases, you will be directed to a form which allows you to modify the name and description of a role.

The policy which authorizes the user to access the role management option is implemented in `App\Policies\RolePolicy.php`.``

You can find this functionality in `App\Http\RoleController.php`.

Also, validation rules were added so you will know exactly what to input in the form fields (see `App\Http\Requests\RoleRequest`). Note that these validation rules also apply for the role edit option.
```
public function rules()
{
    return [
        'name' => [
            'required', 'min:3', Rule::unique((new Role)->getTable())->ignore($this->route()->role->id ?? null)
        ],
        'description' => [
            'nullable', 'min:5'
        ]
    ];
}
```

### User management

The theme comes with an out of the box user management option. To access this option ,click the "**Examples/User Management**" link in the left sidebar or add **/user** to the URL.
The first thing you will see is a list of existing users. You can add new ones by clicking the "**Add user**" button (above the table on the right). On the Add user page, you will find a form which allows you to fill out the user`s name, email, role and password. All pages are generated using blade templates:

```
<div class="row">
  <label class="col-sm-2 col-form-label">{{ __('Name') }}</label>
  <div class="col-sm-7">
    <div class="form-group{{ $errors->has('name') ? ' has-danger' : '' }}">
      <input class="form-control{{ $errors->has('name') ? ' is-invalid' : '' }}" name="name" id="input-name" type="text" placeholder="{{ __('Name') }}" value="{{ old('name') }}" required="true" aria-required="true"/>
      @include('alerts.feedback', ['field' => 'name'])
    </div>
  </div>
</div>
```

Validation rules were added to prevent errors in the form fields (see `App\Http\Requests\UserRequest`). Note that these validation rules also apply for the user edit option.

```
public function rules()
{
    return [
        'name' => [
            'required', 'min:3'
        ],
        'email' => [
            'required', 'email', Rule::unique((new User)->getTable())->ignore($this->route()->user->id ?? null)
        ],
        'role_id' => [
            'required', 'exists:'.(new Role)->getTable().',id'
        ],
        'password' => [
            $this->route()->user ? 'nullable' : 'required', 'confirmed', 'min:6'
        ]
    ];
}

/**
* Get the validation attributes that apply to the request.
*
* @return array
*/
public function attributes()
{
    return [
        'role_id' => 'role',
    ];
}
```

The policy which authorizes the user to access the user management pages is implemented in `App\Policies\UserPolicy.php`.``

Once you add more users, the list will grow and for every user you will have edit and delete options (access these options by clicking the three dotted menu that appears at the end of every row).

All the sample code for the user management can be found in `App\Http\Controllers\UserController`. See store method example bellow:

```
public function store(UserRequest $request, User $model)
{
    $model->create($request->merge([
        'picture' => $request->photo ? $request->photo->store('profile', 'public') : null,
        'password' => Hash::make($request->get('password'))
    ])->all());

    return redirect()->route('user.index')->withStatus(__('User successfully created.'));
}
```

### Tag management

The Pro theme also comes with a tag management option. To access this example, click the "**Examples/Tag Management**" link in the left sidebar or add **/tag** to the URL.
In this section you can add and edit tags, but you can only delete them if they are not attached to any items. You can add new ones by clicking the "**Add tag**" button (above the table on the right). You will then be directed to a form which allows you to add new tags.
Although you can add in the form only the name and color of a tag, you can write your own migrations to extend this functionality.

```
public function destroy(Tag $tag)
{
    if (!$tag->items->isEmpty()) {
        return redirect()->route('tag.index')->withErrors(__('This tag has items attached and can\'t be deleted.'));
    }

    $tag->delete();

    return redirect()->route('tag.index')->withStatus(__('Tag successfully deleted.'));
}
```

The policy which authorizes the user to access tags management pages is implemented in `App\Policies\TagPolicy.php`.

### Category management

Out of the box you will have an example of category management (for the cases in which you are developing a blog or a shop). To access this example, click the "**Examples/Category Management**" link in the left sidebar or add **/category** to the URL.
You can add and edit categories here, but you can only delete them if they are not attached to any items.

```
@if ($category->items->isEmpty() && auth()->user()->can('delete', $category))
    <button type="button" class="btn btn-danger btn-link" data-original-title="" title="" onclick="confirm('{{ __("Are you sure you want to delete this category?") }}') ? this.parentElement.submit() : ''">
        <i class="material-icons">close</i>
        <div class="ripple-container"></div>
    </button>
@endif
```

The policy which authorizes the user on categories management pages is implemented in `App\Policies\CategoryPolicy.php`.

### Item management

Item management is the most advanced example included in the Pro theme, because every item has a picture, belongs to a category and has multiple tags. To access this example click the "**Examples/Item Management**" link in the left sidebar or add **/item** to the URL.
Here you can manage the items. A list of items will appear once you start adding them (to access the add page click "**Add item**").
On the add page, besides the Name and Description fields (which are present in most of the CRUD examples) you can see a category dropdown, which contains the categories you added, a file input and a tag multi select. If you did not add any categories or tags, please go to the corresponding sections (category management, tag management) and add some.  


The code for saving a new item is a bit different than before (see snippet bellow):

```
public function store(ItemRequest $request, Item $model)
{
    $item = $model->create($request->merge([
        'picture' => $request->photo->store('pictures', 'public'),
        'show_on_homepage' => $request->show_on_homepage ? 1 : 0,
        'options' => $request->options ? $request->options : null,
        'date' => $request->date ? Carbon::parse($request->date)->format('Y-m-d') : null
    ])->all());

    $item->tags()->sync($request->get('tags'));

    return redirect()->route('item.index')->withStatus(__('Item successfully created.'));
}
```

Notice how the picture and tags are easily saved in the database and on the disk.

Similar to all the examples included in the theme, this one also has validation rules in place. Note that the picture is mandatory only on the item creation. On the edit page, if no new picture is added, the old picture will not be modified.

```
public function rules()
{
    return [
        'name' => [
            'required', 'min:3', Rule::unique((new Item)->getTable())->ignore($this->route()->item->id ?? null)
        ],
        'category_id' => [
            'required', 'exists:'.(new Category)->getTable().',id'
        ],
        'description' => [
            'required'
        ],
        'photo' => [
            $this->route()->item ? 'nullable' : 'required', 'image'
        ],
        'tags' => [
            'required'
        ],
        'tags.*' => [
            'exists:'.(new Tag)->getTable().',id'
        ],
        'status' => [
            'required',
            'in:published,draft,archive'
        ],
        'date' => [
            'required',
            'date_format:d-m-Y'
        ]
    ];
}
```

In the item management we have an **observer** (`app\Observers\ItemObserver`) example. This observer handles the deletion of the picture from the disk when the item is deleted or when the picture is changed via the edit form. The **observer** also removes the association between the item and the tags.

```
public function deleting(Item  $item)
{
    File::delete(storage_path("/app/public/{$item->picture}"));
    
    $item->tags()->detach();
}
```

The policy which authorizes the user on item management pages is implemented in `App\Policies\ItemPolicy.php`.

## Table of Contents

* [Versions](#versions)
* [Demo](#demo)
* [Documentation](#documentation)
* [File Structure](#file-structure)
* [Browser Support](#browser-support)
* [Resources](#resources)
* [Reporting Issues](#reporting-issues)
* [Licensing](#licensing)
* [Useful Links](#useful-links)

## Versions

[<img src="https://github.com/creativetimofficial/public-assets/blob/master/logos/html-logo.jpg?raw=true" width="60" height="60" />](https://demos.creative-tim.com/argon-dashboard-pro/pages/dashboards/dashboard.html?ref=pdl-readme)
[<img src="https://github.com/creativetimofficial/public-assets/blob/master/logos/laravel_logo.png?raw=true" width="60" height="60" />](https://argon-dashboard-pro-laravel.creative-tim.com/?ref=pdl-readme)

| HTML | LARAVEL |
| --- | --- |
| [![Paper Dashboard Pro HTML](https://s3.amazonaws.com/creativetim_bucket/products/84/original/opt_pd2p_thumbnail.jpg)](https://demos.creative-tim.com/paper-dashboard-pro/examples/dashboard.html?ref=pdl-readme) | [![Paper Dashboard Pro Laravel](https://s3.amazonaws.com/creativetim_bucket/products/208/original/opt_pdp_laravel_thumbnail.jpg)](https://www.creative-tim.com/live/paper-dashboard-pro-laravel/?ref=pdl-readme)

## Demo

| Register | Login | Dashboard |
| --- | --- | ---  |
| [![Register](https://raw.githubusercontent.com/creativetimofficial/public-assets/master/paper-dashboard-2-pro-laravel/register.png)](https://paper-dashboard-pro-laravel.creative-tim.com/register?ref=pdl-readme)  | [![Login](https://raw.githubusercontent.com/creativetimofficial/public-assets/master/paper-dashboard-2-pro-laravel/login.png)](https://paper-dashboard-pro-laravel.creative-tim.com/login?ref=pdl-readme)  | [![Dashboard](https://raw.githubusercontent.com/creativetimofficial/public-assets/master/paper-dashboard-2-pro-laravel/dashboard.png)](https://paper-dashboard-pro-laravel.creative-tim.com/?ref=pdl-readme)

| Profile Page | Users Page | Tables Page  |
| --- | --- | ---  |
| [![Profile Page](https://raw.githubusercontent.com/creativetimofficial/public-assets/master/paper-dashboard-2-pro-laravel/profile.png)](https://paper-dashboard-pro-laravel.creative-tim.com/profile?ref=pdl-readme)  | [![Users Page](https://raw.githubusercontent.com/creativetimofficial/public-assets/master/paper-dashboard-2-pro-laravel/users.png)](https://paper-dashboard-pro-laravel.creative-tim.com/user?ref=pdl-readme) | [![Tables Page](https://raw.githubusercontent.com/creativetimofficial/public-assets/master/paper-dashboard-2-pro-laravel/tables.png)](https://paper-dashboard-pro-laravel.creative-tim.com/table-list?ref=pdl-readme)
[View More](https://www.creative-tim.com/live/paper-dashboard-pro-laravel/?ref=pdl-readme)

## Documentation
The documentation for the Paper Dashboard Laravel is hosted at our [website](https://www.creative-tim.com/live/paper-dashboard-pro-laravel/?start-page=/docs/getting-started/laravel-setup.html&ref=pdl-readme).

## File Structure
```
├── app
│   ├── Category.php
│   ├── Console
│   │   ├── Comments
│   │   │   └── DeleteOldUsers.php
│   │   └── Kernel.php
│   ├── Exceptions
│   │   └── Handler.php
│   ├── Http
│   │   ├── Controllers
│   │   │   ├── Auth
│   │   │   │   ├── ForgotPasswordController.php
│   │   │   │   ├── LoginController.php
│   │   │   │   ├── RegisterController.php
│   │   │   │   ├── ResetPasswordController.php
│   │   │   │   └── VerificationController.php
│   │   │   ├── CategoryController.php
│   │   │   ├── Controller.php
│   │   │   ├── HomeController.php
│   │   │   ├── ItemController.php
│   │   │   ├── PageController.php
│   │   │   ├── ProfileController.php
│   │   │   ├── RoleController.php
│   │   │   ├── TagController.php
│   │   │   └── UserController.php
│   │   ├── Kernel.php
│   │   ├── Middleware
│   │   │   ├── Authenticate.php
│   │   │   ├── CheckForMaintenanceMode.php
│   │   │   ├── EncryptCookies.php
│   │   │   ├── RedirectIfAuthenticated.php
│   │   │   ├── TrimStrings.php
│   │   │   ├── TrustProxies.php
│   │   │   └── VerifyCsrfToken.php
│   │   └── Requests
│   │       ├── CategoryRequest.php
│   │       ├── ItemRequest.php
│   │       ├── PasswordRequest.php
│   │       ├── ProfileRequest.php
│   │       ├── RoleRequest.php
│   │       ├── TagRequest.php
│   │       └── UserRequest.php
│   ├── Item.php
│   ├── Observers
│   │   ├── ItemObserver.php
│   │   └── UserObserver.php
│   ├── Policies
│   │   ├── CategoryPolicy.php
│   │   ├── ItemPolicy.php
│   │   ├── RolePolicy.php
│   │   ├── TagPolicy.php
│   │   └── UserPolicy.php
│   ├── Providers
│   │   ├── AppServiceProvider.php
│   │   ├── AuthServiceProvider.php
│   │   ├── BroadcastServiceProvider.php
│   │   ├── EventServiceProvider.php
│   │   └── RouteServiceProvider.php
│   ├── Role.php
│   ├── Rules
│   │   └── CurrentPasswordCheckRule.php
│   ├── Tag.php
│   └── User.php
├── artisan
├── bootstrap
│   ├── app.php
│   └── cache
│       ├── .gitignore
│       ├── packages.php
│       └── services.php
├── CHANGELOG.md
├── composer.json
├── composer.lock
├── config
│   ├── app.php
│   ├── auth.php
│   ├── broadcasting.php
│   ├── cache.php
│   ├── database.php
│   ├── filesystems.php
│   ├── hashing.php
│   ├── items.php
│   ├── logging.php
│   ├── mail.php
│   ├── queue.php
│   ├── services.php
│   ├── session.php
│   └── view.php
├── database
│   ├── factories
│   │   └── UserFactory.php
│   ├── .gitignore
│   ├── migrations
│   │   ├── 2014_10_12_100000_create_password_resets_table.php
│   │   ├── 2019_01_15_100000_create_roles_table.php
│   │   ├── 2019_01_15_110000_create_users_table.php
│   │   ├── 2019_01_17_121504_create_categories_table.php
│   │   ├── 2019_01_21_130422_create_tags_table.php
│   │   ├── 2019_01_21_163402_create_items_table.php
│   │   ├── 2019_01_21_163414_create_item_tag_table.php
│   │   ├── 2019_03_06_132557_add_photo_column_to_users_table.php
│   │   └── 2019_03_20_090438_add_color_tags_table.php
│   └── seeds
│       ├── CategoriesTableSeeder.php
│       ├── DatabaseSeeder.php
│       ├── ItemsTableSeeder.php
│       ├── RolesTableSeeder.php
│       ├── TagsTableSeeder.php
│       └── UsersTableSeeder.php
├── .editorconfig
├── .env
├── .env.example
├── .git
│   ├── branches
│   ├── COMMIT_EDITMSG
│   ├── config
│   ├── description
│   ├── FETCH_HEAD
│   ├── HEAD
│   ├── hooks
│   │   ├── applypatch-msg.sample
│   │   ├── commit-msg.sample
│   │   ├── fsmonitor-watchman.sample
│   │   ├── post-update.sample
│   │   ├── pre-applypatch.sample
│   │   ├── pre-commit.sample
│   │   ├── prepare-commit-msg.sample
│   │   ├── pre-push.sample
│   │   ├── pre-rebase.sample
│   │   ├── pre-receive.sample
│   │   └── update.sample
│   ├── index
│   ├── info
│   │   └── exclude
│   ├── logs
│   │   ├── HEAD
│   │   └── refs
│   │       ├── heads
│   │       │   ├── develop
│   │       │   ├── feature
│   │       │   │   ├── add_edit_category_page
│   │       │   │   ├── add_edit_item_page
│   │       │   │   ├── create_roles_list_page
│   │       │   │   ├── delete_user_from_list
│   │       │   │   ├── demo
│   │       │   │   ├── feature
│   │       │   │   │   └── demo2
│   │       │   │   ├── laravel_examples
│   │       │   │   ├── restrictions
│   │       │   │   └── roles
│   │       │   └── master
│   │       ├── remotes
│   │       │   └── origin
│   │       │       ├── develop
│   │       │       ├── feature
│   │       │       │   ├── add_category_form
│   │       │       │   ├── add_delete_category
│   │       │       │   ├── add_delete_tag_from_list
│   │       │       │   ├── add_edit_category_page
│   │       │       │   ├── add_edit_item_page
│   │       │       │   ├── add_tag_form
│   │       │       │   ├── add_user_form
│   │       │       │   ├── add_user_form_2
│   │       │       │   ├── categories_list_page
│   │       │       │   ├── change_password_form_to_my_account_page
│   │       │       │   ├── create_items_list_page
│   │       │       │   ├── create_roles_list_page
│   │       │       │   ├── create_tags_list
│   │       │       │   ├── create_users_list_page
│   │       │       │   ├── delete_user_from_list
│   │       │       │   ├── demo
│   │       │       │   ├── edit_user_page
│   │       │       │   ├── forgot_password_page
│   │       │       │   ├── general_base_layout
│   │       │       │   ├── guest_page_template
│   │       │       │   ├── initializations
│   │       │       │   ├── laravel_examples
│   │       │       │   ├── login_page
│   │       │       │   ├── migrations
│   │       │       │   ├── my_account_page
│   │       │       │   ├── register_page
│   │       │       │   ├── restrictions
│   │       │       │   ├── roles
│   │       │       │   └── static_pages
│   │       │       └── master
│   │       └── stash
│   │   ├── info
│   │   └── pack
│   ├── ORIG_HEAD
│   └── refs
│       ├── heads
│       │   ├── develop
│       │   ├── feature
│       │   │   ├── add_edit_category_page
│       │   │   ├── add_edit_item_page
│       │   │   ├── create_roles_list_page
│       │   │   ├── delete_user_from_list
│       │   │   ├── demo
│       │   │   ├── feature
│       │   │   │   └── demo2
│       │   │   ├── laravel_examples
│       │   │   ├── restrictions
│       │   │   └── roles
│       │   └── master
│       ├── remotes
│       │   └── origin
│       │       ├── develop
│       │       ├── feature
│       │       │   ├── add_category_form
│       │       │   ├── add_delete_category
│       │       │   ├── add_delete_tag_from_list
│       │       │   ├── add_edit_category_page
│       │       │   ├── add_edit_item_page
│       │       │   ├── add_tag_form
│       │       │   ├── add_user_form
│       │       │   ├── add_user_form_2
│       │       │   ├── categories_list_page
│       │       │   ├── change_password_form_to_my_account_page
│       │       │   ├── create_items_list_page
│       │       │   ├── create_roles_list_page
│       │       │   ├── create_tags_list
│       │       │   ├── create_users_list_page
│       │       │   ├── delete_user_from_list
│       │       │   ├── demo
│       │       │   ├── edit_user_page
│       │       │   ├── forgot_password_page
│       │       │   ├── general_base_layout
│       │       │   ├── guest_page_template
│       │       │   ├── initializations
│       │       │   ├── laravel_examples
│       │       │   ├── login_page
│       │       │   ├── migrations
│       │       │   ├── my_account_page
│       │       │   ├── register_page
│       │       │   ├── restrictions
│       │       │   ├── roles
│       │       │   └── static_pages
│       │       └── master
│       ├── stash
│       └── tags
├── .gitattributes
├── .gitignore
├── ISSUE_TEMPLATE.md
├── package.json
├── phpunit.xml
├── public
│   ├── core
│   │   ├── bootstrap.min.js
│   │   ├── jquery.min.js
│   │   └── popper.min.js
│   ├── css
│   │   ├── app.css
│   │   ├── bootstrap.min.css
│   │   ├── bootstrap.min.css.map
│   │   ├── paper-dashboard.css
│   │   ├── paper-dashboard.css.map
│   │   └── paper-dashboard.min.css
│   ├── demo
│   │   ├── demo.css
│   │   ├── demo.js
│   │   ├── docs.min.css
│   │   ├── docs.min.js
│   │   ├── jquery.sharrre.js
│   │   ├── src
│   │   │   └── application.js
│   │   └── vendor
│   │       ├── anchor.min.js
│   │       ├── clipboard.min.js
│   │       ├── holder.min.js
│   │       └── split.min.js
│   ├── docs
│   │   ├── browser-bugs.html
│   │   ├── components
│   │   │   ├── alerts.html
│   │   │   ├── badge.html
│   │   │   ├── breadcrumb.html
│   │   │   ├── buttons.html
│   │   │   ├── card.html
│   │   │   ├── carousel.html
│   │   │   ├── collapse.html
│   │   │   ├── colors.html
│   │   │   ├── dropdowns.html
│   │   │   ├── forms.html
│   │   │   ├── google-maps.html
│   │   │   ├── images.html
│   │   │   ├── info-areas.html
│   │   │   ├── material-icons.html
│   │   │   ├── modal.html
│   │   │   ├── navbar.html
│   │   │   ├── navs.html
│   │   │   ├── nucleo-icons.html
│   │   │   ├── pagination.html
│   │   │   ├── parallax.html
│   │   │   ├── popovers.html
│   │   │   ├── progress.html
│   │   │   ├── tables.html
│   │   │   ├── tooltips.html
│   │   │   └── typography.html
│   │   ├── getting-started
│   │   │   ├── accessibility.html
│   │   │   ├── best-practices.html
│   │   │   ├── brand.html
│   │   │   ├── browsers-devices.html
│   │   │   ├── contents.html
│   │   │   ├── download.html
│   │   │   ├── file-structure.html
│   │   │   ├── frontend-assets.html
│   │   │   ├── images.html
│   │   │   ├── javascript.html
│   │   │   ├── laravel-setup.html
│   │   │   ├── license.html
│   │   │   ├── options.html
│   │   │   ├── overview.html
│   │   │   ├── theming.html
│   │   │   ├── translations.html
│   │   │   └── webpack.html
│   │   ├── laravel
│   │   │   ├── category-management.html
│   │   │   ├── item-management.html
│   │   │   ├── profile-edit.html
│   │   │   ├── role-management.html
│   │   │   ├── tag-management.html
│   │   │   └── user-management.html
│   │   └── plugins
│   │       ├── bootstrap-notify.html
│   │       ├── bootstrap-switch.html
│   │       ├── bootstrap-wizard.html
│   │       ├── chart-js.html
│   │       ├── datatables-net.html
│   │       ├── datetimepicker.html
│   │       ├── fileupload-jasny.html
│   │       ├── fullcalendar.html
│   │       ├── jquery-tagsinput.html
│   │       ├── jquery-validation.html
│   │       ├── jvector-map.html
│   │       ├── perfect-scrollbar.html
│   │       ├── select-bootstrap.html
│   │       ├── sliders.html
│   │       └── sweet-alert-2.html
│   ├── favicon.ico
│   ├── fonts
│   │   ├── nucleo-icons.eot
│   │   ├── nucleo-icons.ttf
│   │   ├── nucleo-icons.woff
│   │   └── nucleo-icons.woff2
│   ├── .htaccess
│   ├── img
│   │   ├── agenda.png
│   │   ├── apple-icon.png
│   │   ├── AU.png
│   │   ├── bg
│   │   │   ├── ana-bernardo.jpg
│   │   │   ├── anders-jilden.jpg
│   │   │   ├── angelo-pantazis.jpg
│   │   │   ├── ben-white.jpg
│   │   │   ├── bruno-abatti.jpg
│   │   │   ├── chris-karidisz.jpg
│   │   │   ├── clark-street-mercantile.jpg
│   │   │   ├── damir-bosnjak.jpg
│   │   │   ├── daniel-olahh.jpg
│   │   │   ├── daniel-olahs.jpg
│   │   │   ├── david-marcu.jpg
│   │   │   ├── fabio-mangione.jpg
│   │   │   ├── federico-beccari.jpg
│   │   │   ├── fog-low.png
│   │   │   ├── forest-bg.jpg
│   │   │   ├── galen-crout.jpg
│   │   │   ├── gerrit-vermeulen.jpg
│   │   │   ├── gukhwa-jang.jpg
│   │   │   ├── gukhwa-jangs.jpg
│   │   │   ├── ilya-yakover.jpg
│   │   │   ├── ipad.png
│   │   │   ├── iphone.png
│   │   │   ├── jan-sendereks.jpg
│   │   │   ├── jeff-sheldon.jpg
│   │   │   ├── jeremy-yap.jpg
│   │   │   ├── john-towner.jpg
│   │   │   ├── joshua-earles.jpg
│   │   │   ├── joshua-earlesz.jpg
│   │   │   ├── joshua-earlez.jpg
│   │   │   ├── joshua-stannard.jpg
│   │   │   ├── leonard-cotte.jpg
│   │   │   ├── luke-chesser.jpg
│   │   │   ├── markus-spiske-187777.jpg
│   │   │   ├── martin-knize.jpg
│   │   │   ├── miguel-perales.jpg
│   │   │   ├── mika-matin.jpg
│   │   │   ├── neill-kumar.jpg
│   │   │   ├── noah-rosenfield.jpg
│   │   │   ├── opt_gsdk_new_thumbnail.jpg
│   │   │   ├── opt_lbd_pro_new_thumbnail.jpg
│   │   │   ├── opt_pk_thumbnail.jpg
│   │   │   ├── patrick-tomasso.jpg
│   │   │   ├── pavel-kosov.jpg
│   │   │   ├── pedro-lastra.jpg
│   │   │   ├── pk-pro-cover.jpg
│   │   │   ├── por7o.jpg
│   │   │   ├── rawpixel-com-2.jpg
│   │   │   ├── rawpixel-com.jpg
│   │   │   ├── rawpixel-comm.jpg
│   │   │   ├── rawpixel-coms.jpg
│   │   │   ├── rodrigo-ardilha.jpg
│   │   │   ├── roger-keller.jpg
│   │   │   ├── sebastien-gabrieles.jpg
│   │   │   ├── soroush-karimi.jpg
│   │   │   ├── stephanie-krist.jpg
│   │   │   ├── takahiro-sakamoto.jpg
│   │   │   ├── the-how-photographer.jpg
│   │   │   ├── uriel-soberanes.jpg
│   │   │   ├── val-vesa.jpg
│   │   │   ├── vincent-versluis.jpg
│   │   │   └── zachary-staines.jpg
│   │   ├── BR.png
│   │   ├── default-avatar.png
│   │   ├── DE.png
│   │   ├── emilyz.jpg
│   │   ├── evernote.png
│   │   ├── face-2.jpg
│   │   ├── face-3.jpg
│   │   ├── faces
│   │   │   ├── ayo-ogunseinde-1.jpg
│   │   │   ├── ayo-ogunseinde-2.jpg
│   │   │   ├── clem-onojeghuo-1.jpg
│   │   │   ├── clem-onojeghuo-2.jpg
│   │   │   ├── clem-onojeghuo-3.jpg
│   │   │   ├── clem-onojeghuo-4.jpg
│   │   │   ├── erik-lucatero-1.jpg
│   │   │   ├── erik-lucatero-2.jpg
│   │   │   ├── joe-gardner-1.jpg
│   │   │   ├── joe-gardner-2.jpg
│   │   │   ├── kaci-baum-1.jpg
│   │   │   └── kaci-baum-2.jpg
│   │   ├── favicon.png
│   │   ├── flags
│   │   │   ├── AD.png
│   │   │   ├── AE.png
│   │   │   ├── AG.png
│   │   │   ├── AM.png
│   │   │   ├── AR.png
│   │   │   ├── AT.png
│   │   │   ├── AU.png
│   │   │   ├── BE.png
│   │   │   ├── BF.png
│   │   │   ├── BG.png
│   │   │   ├── BO.png
│   │   │   ├── BR.png
│   │   │   ├── CA.png
│   │   │   ├── CD.png
│   │   │   ├── CG.png
│   │   │   ├── CH.png
│   │   │   ├── CL.png
│   │   │   ├── CM.png
│   │   │   ├── CN.png
│   │   │   ├── CO.png
│   │   │   ├── CZ.png
│   │   │   ├── DE.png
│   │   │   ├── DJ.png
│   │   │   ├── DK.png
│   │   │   ├── DZ.png
│   │   │   ├── EE.png
│   │   │   ├── EG.png
│   │   │   ├── ES.png
│   │   │   ├── FI.png
│   │   │   ├── FR.png
│   │   │   ├── GA.png
│   │   │   ├── GB.png
│   │   │   ├── GM.png
│   │   │   ├── GT.png
│   │   │   ├── HN.png
│   │   │   ├── HT.png
│   │   │   ├── HU.png
│   │   │   ├── ID.png
│   │   │   ├── IE.png
│   │   │   ├── IL.png
│   │   │   ├── IN.png
│   │   │   ├── IQ.png
│   │   │   ├── IR.png
│   │   │   ├── IT.png
│   │   │   ├── JM.png
│   │   │   ├── JO.png
│   │   │   ├── JP.png
│   │   │   ├── KG.png
│   │   │   ├── KN.png
│   │   │   ├── KP.png
│   │   │   ├── KR.png
│   │   │   ├── KW.png
│   │   │   ├── KZ.png
│   │   │   ├── LA.png
│   │   │   ├── LB.png
│   │   │   ├── LC.png
│   │   │   ├── LS.png
│   │   │   ├── LU.png
│   │   │   ├── LV.png
│   │   │   ├── MG.png
│   │   │   ├── MK.png
│   │   │   ├── ML.png
│   │   │   ├── MM.png
│   │   │   ├── MT.png
│   │   │   ├── MX.png
│   │   │   ├── NA.png
│   │   │   ├── NE.png
│   │   │   ├── NG.png
│   │   │   ├── NI.png
│   │   │   ├── NL.png
│   │   │   ├── NO.png
│   │   │   ├── OM.png
│   │   │   ├── PA.png
│   │   │   ├── PE.png
│   │   │   ├── PG.png
│   │   │   ├── PK.png
│   │   │   ├── PL.png
│   │   │   ├── PT.png
│   │   │   ├── PY.png
│   │   │   ├── QA.png
│   │   │   ├── RO.png
│   │   │   ├── RU.png
│   │   │   ├── RW.png
│   │   │   ├── SA.png
│   │   │   ├── SE.png
│   │   │   ├── SG.png
│   │   │   ├── SL.png
│   │   │   ├── SN.png
│   │   │   ├── SO.png
│   │   │   ├── SV.png
│   │   │   ├── TD.png
│   │   │   ├── TJ.png
│   │   │   ├── TL.png
│   │   │   ├── TR.png
│   │   │   ├── TZ.png
│   │   │   ├── UA.png
│   │   │   ├── US.png
│   │   │   ├── VE.png
│   │   │   ├── VN.png
│   │   │   └── YE.png
│   │   ├── GB.png
│   │   ├── header.jpg
│   │   ├── image_placeholder.jpg
│   │   ├── james.jpg
│   │   ├── laravel.svg
│   │   ├── logo-small.png
│   │   ├── mike.jpg
│   │   ├── No Profile Picture.png
│   │   ├── now-logo.png
│   │   ├── placeholder.jpg
│   │   ├── RO.png
│   │   ├── stylus.jpg
│   │   └── US.png
│   ├── index.php
│   ├── js
│   │   ├── app.js
│   │   ├── core
│   │   │   ├── bootstrap.min.js
│   │   │   ├── jquery.min.js
│   │   │   └── popper.min.js
│   │   ├── paper-dashboard.js
│   │   ├── paper-dashboard.js.map
│   │   ├── paper-dashboard.min.js
│   │   └── plugins
│   │       ├── bootstrap-datetimepicker.js
│   │       ├── bootstrap-notify.js
│   │       ├── bootstrap-selectpicker.js
│   │       ├── bootstrap-switch.js
│   │       ├── bootstrap-tagsinput.js
│   │       ├── chartjs.min.js
│   │       ├── fullcalendar.min.js
│   │       ├── jasny-bootstrap.min.js
│   │       ├── jquery.bootstrap-wizard.js
│   │       ├── jquery.dataTables.min.js
│   │       ├── jquery-jvectormap.js
│   │       ├── jquery.validate.min.js
│   │       ├── moment.min.js
│   │       ├── nouislider.min.js
│   │       ├── perfect-scrollbar.jquery.min.js
│   │       └── sweetalert2.min.js
│   ├── plugins
│   │   ├── bootstrap-datetimepicker.js
│   │   ├── bootstrap-notify.js
│   │   ├── bootstrap-selectpicker.js
│   │   ├── bootstrap-switch.js
│   │   ├── bootstrap-tagsinput.js
│   │   ├── chartjs.min.js
│   │   ├── fullcalendar.min.js
│   │   ├── jasny-bootstrap.min.js
│   │   ├── jquery.bootstrap-wizard.js
│   │   ├── jquery.dataTables.min.js
│   │   ├── jquery-jvectormap.js
│   │   ├── jquery.validate.min.js
│   │   ├── moment.min.js
│   │   ├── nouislider.min.js
│   │   ├── perfect-scrollbar.jquery.min.js
│   │   └── sweetalert2.min.js
│   ├── robots.txt
│   └── scss
│       ├── paper-dashboard
│       │   ├── _alerts.scss
│       │   ├── _animated-buttons.scss
│       │   ├── _badges.scss
│       │   ├── _buttons.scss
│       │   ├── cards
│       │   │   ├── _card-background.scss
│       │   │   ├── _card-chart.scss
│       │   │   ├── _card-collapse.scss
│       │   │   ├── _card-contributions.scss
│       │   │   ├── _card-info-area.scss
│       │   │   ├── _card-lock.scss
│       │   │   ├── _card-map.scss
│       │   │   ├── _card-plain.scss
│       │   │   ├── _card-pricing.scss
│       │   │   ├── _card-profile.scss
│       │   │   ├── _card-signup.scss
│       │   │   ├── _card-stats-mini.scss
│       │   │   ├── _card-stats.scss
│       │   │   ├── _card-subcategories.scss
│       │   │   ├── _card-tasks.scss
│       │   │   ├── _card-testimonials.scss
│       │   │   └── _card-user.scss
│       │   ├── _cards.scss
│       │   ├── _carousel.scss
│       │   ├── _checkboxes-radio.scss
│       │   ├── _dropdown.scss
│       │   ├── _example-pages.scss
│       │   ├── _fixed-plugin.scss
│       │   ├── _footers.scss
│       │   ├── _images.scss
│       │   ├── _info-areas.scss
│       │   ├── _inputs.scss
│       │   ├── _media-queries.scss
│       │   ├── _misc-extend.scss
│       │   ├── _misc.scss
│       │   ├── mixins
│       │   │   ├── _badges.scss
│       │   │   ├── _buttons.scss
│       │   │   ├── _cards.scss
│       │   │   ├── _chartist.scss
│       │   │   ├── _dropdown.scss
│       │   │   ├── _inputs.scss
│       │   │   ├── _modals.scss
│       │   │   ├── _page-header.scss
│       │   │   ├── _popovers.scss
│       │   │   ├── _social-buttons.scss
│       │   │   ├── _tags.scss
│       │   │   ├── _transparency.scss
│       │   │   ├── _vendor-prefixes-extend.scss
│       │   │   ├── _vendor-prefixes.scss
│       │   │   └── _wizard.scss
│       │   ├── _mixins.scss
│       │   ├── _modals.scss
│       │   ├── _navbar.scss
│       │   ├── _nucleo-outline.scss
│       │   ├── _page-header.scss
│       │   ├── _pagination.scss
│       │   ├── _pills.scss
│       │   ├── plugins
│       │   │   ├── _plugin-animate-bootstrap-notify.scss
│       │   │   ├── _plugin-bootstrap-select.scss
│       │   │   ├── _plugin-bootstrap-switch.scss
│       │   │   ├── _plugin-bootstrap-table.scss
│       │   │   ├── _plugin-card-wizard.scss
│       │   │   ├── _plugin-datatables.net.scss
│       │   │   ├── _plugin-datetimepicker.scss
│       │   │   ├── _plugin-fullcalendar.scss
│       │   │   ├── _plugin-jasny-fileupload.scss
│       │   │   ├── _plugin-jquery.jvectormap.scss
│       │   │   ├── _plugin-nouislider.scss
│       │   │   ├── _plugin-perfect-scrollbar.scss
│       │   │   ├── _plugin-sweetalert2.scss
│       │   │   └── _plugin-tagsinput.scss
│       │   ├── _popups.scss
│       │   ├── _progress.scss
│       │   ├── _responsive.scss
│       │   ├── _sections.scss
│       │   ├── _sidebar-and-main-panel.scss
│       │   ├── _social-buttons.scss
│       │   ├── _tables.scss
│       │   ├── _tabs.scss
│       │   ├── _timeline.scss
│       │   ├── _typography.scss
│       │   └── _variables.scss
│       └── paper-dashboard.scss
├── README.md
├── resources
│   ├── lang
│   │   └── en
│   │       ├── auth.php
│   │       ├── pagination.php
│   │       ├── passwords.php
│   │       └── validation.php
│   └── views
│       ├── alerts
│       │   ├── errors.blade.php
│       │   ├── error_self_update.blade.php
│       │   ├── feedback.blade.php
│       │   ├── migrations_check.blade.php
│       │   └── success.blade.php
│       ├── auth
│       │   ├── login.blade.php
│       │   ├── passwords
│       │   │   ├── email.blade.php
│       │   │   └── reset.blade.php
│       │   ├── register.blade.php
│       │   └── verify.blade.php
│       ├── categories
│       │   ├── create.blade.php
│       │   ├── edit.blade.php
│       │   └── index.blade.php
│       ├── items
│       │   ├── create.blade.php
│       │   ├── edit.blade.php
│       │   └── index.blade.php
│       ├── layouts
│       │   ├── app.blade.php
│       │   ├── footer.blade.php
│       │   ├── navbars
│       │   │   ├── navs
│       │   │   │   ├── auth.blade.php
│       │   │   │   └── guest.blade.php
│       │   │   └── sidebar.blade.php
│       │   └── page_templates
│       │       ├── auth.blade.php
│       │       └── guest.blade.php
│       ├── pages
│       │   ├── calendar.blade.php
│       │   ├── charts.blade.php
│       │   ├── components
│       │   │   ├── buttons.blade.php
│       │   │   ├── grid.blade.php
│       │   │   ├── icons.blade.php
│       │   │   ├── notifications.blade.php
│       │   │   ├── panels.blade.php
│       │   │   ├── sweet-alert.blade.php
│       │   │   └── typography.blade.php
│       │   ├── dashboard.blade.php
│       │   ├── forms
│       │   │   ├── extended.blade.php
│       │   │   ├── regular.blade.php
│       │   │   ├── validation.blade.php
│       │   │   └── wizard.blade.php
│       │   ├── lock.blade.php
│       │   ├── maps
│       │   │   ├── fullscreen.blade.php
│       │   │   ├── google.blade.php
│       │   │   └── vector.blade.php
│       │   ├── tables
│       │   │   ├── datatables.blade.php
│       │   │   ├── extended.blade.php
│       │   │   └── regular.blade.php
│       │   ├── timeline.blade.php
│       │   ├── welcome.blade.php
│       │   └── widgets.blade.php
│       ├── profile
│       │   └── edit.blade.php
│       ├── roles
│       │   ├── create.blade.php
│       │   ├── edit.blade.php
│       │   └── index.blade.php
│       ├── tags
│       │   ├── create.blade.php
│       │   ├── edit.blade.php
│       │   └── index.blade.php
│       └── users
│           ├── create.blade.php
│           ├── edit.blade.php
│           └── index.blade.php
├── routes
│   ├── api.php
│   ├── channels.php
│   ├── console.php
│   └── web.php
├── server.php
├── storage
│   ├── app
│   │   ├── .gitignore
│   │   └── public
│   │       ├── .gitignore
│   │       ├── pictures
│   │       └── profile
│   ├── framework
│   │   ├── cache
│   │   │   ├── data
│   │   │   │   └── .gitignore
│   │   │   └── .gitignore
│   │   ├── .gitignore
│   │   ├── sessions
│   │   │   ├── .gitignore
│   │   ├── testing
│   │   │   └── .gitignore
│   │   └── views
│   │       └── .gitignore
│   └── logs
├── .styleci.yml
├── tests
│   ├── CreatesApplication.php
│   ├── Feature
│   │   └── ExampleTest.php
│   ├── TestCase.php
│   └── Unit
│       └── ExampleTest.php
├── .vscode
│   └── settings.json
├── webpack.mix.js
└── yarn.lock

```

## Browser Support

At present, we officially aim to support the last two versions of the following browsers:

<img src="https://github.com/creativetimofficial/public-assets/blob/master/logos/chrome-logo.png?raw=true" width="64" height="64"> <img src="https://raw.githubusercontent.com/creativetimofficial/public-assets/master/logos/firefox-logo.png" width="64" height="64"> <img src="https://raw.githubusercontent.com/creativetimofficial/public-assets/master/logos/edge-logo.png" width="64" height="64"> <img src="https://raw.githubusercontent.com/creativetimofficial/public-assets/master/logos/safari-logo.png" width="64" height="64"> <img src="https://raw.githubusercontent.com/creativetimofficial/public-assets/master/logos/opera-logo.png" width="64" height="64">


## Resources
- Demo: <https://www.creative-tim.com/live/paper-dashboard-pro-laravel/?ref=pdl-readme>
- Download Page: <https://www.creative-tim.com/product/paper-dashboard-pro-laravel?ref=pdl-readme>
- Documentation: <https://www.creative-tim.com/live/paper-dashboard-pro-laravel/?start-page=/docs/getting-started/laravel-setup.html&ref=pdl-readme>
- License Agreement: <https://www.creative-tim.com/license?ref=pdl-readme>
- Support: <https://www.creative-tim.com/contact-us?ref=pdl-readme>
- Issues: [Github Issues Page](https://github.com/creativetimofficial/paper-dashboard-pro-laravel/issues)
- **Dashboards:**

| HTML | LARAVEL |
| --- | --- |
| [![Paper Dashboard Pro HTML](https://s3.amazonaws.com/creativetim_bucket/products/84/original/opt_pd2p_thumbnail.jpg)](https://demos.creative-tim.com/paper-dashboard-pro/examples/dashboard.html?ref=pdl-readme) | [![Paper Dashboard Pro Laravel](https://s3.amazonaws.com/creativetim_bucket/products/208/original/opt_pdp_laravel_thumbnail.jpg)](https://www.creative-tim.com/live/paper-dashboard-pro-laravel/?ref=pdl-readme)

## Change log

Please see the [changelog](CHANGELOG.md) for more information on what has changed recently.

## Credits

- [Creative Tim](https://creative-tim.com/?ref=pdl-readme)
- [UPDIVISION](https://updivision.com)

## Reporting Issues

We use GitHub Issues as the official bug tracker for the Paper Dashboard Laravel. Here are some advices for our users that want to report an issue:

1. Make sure that you are using the latest version of the Paper Dashboard Laravel. Check the CHANGELOG from your dashboard on our [website](https://www.creative-tim.com/?ref=pdl-readme).
2. Providing us reproducible steps for the issue will shorten the time it takes for it to be fixed.
3. Some issues may be browser specific, so specifying in what browser you encountered the issue might help.

## Licensing

- Copyright 2019 Creative Tim (https://www.creative-tim.com/?ref=pdl-readme)
- [Creative Tim License](https://www.creative-tim.com/license?ref=pdl-readme).


## Useful Links

- [Tutorials](https://www.youtube.com/channel/UCVyTG4sCw-rOvB9oHkzZD1w?ref=pdl-readme)
- [Affiliate Program](https://www.creative-tim.com/affiliates/new?ref=pdl-readme) (earn money)
- [Blog Creative Tim](http://blog.creative-tim.com/?ref=pdl-readme)
- [Free Products](https://www.creative-tim.com/bootstrap-themes/free?ref=pdl-readme) from Creative Tim
- [Premium Products](https://www.creative-tim.com/bootstrap-themes/premium?ref=pdl-readme) from Creative Tim
- [React Products](https://www.creative-tim.com/bootstrap-themes/react-themes?ref=pdl-readme) from Creative Tim
- [Angular Products](https://www.creative-tim.com/bootstrap-themes/angular-themes?ref=pdl-readme) from Creative Tim
- [VueJS Products](https://www.creative-tim.com/bootstrap-themes/vuejs-themes?ref=pdl-readme) from Creative Tim
- [More products](https://www.creative-tim.com/bootstrap-themes?ref=pdl-readme) from Creative Tim
- Check our Bundles [here](https://www.creative-tim.com/bundles?ref=pdl-readme)

## Social Media

### Creative Tim:

Twitter: <https://twitter.com/CreativeTim?ref=pdl-readme>

Facebook: <https://www.facebook.com/CreativeTim?ref=pdl-readme>

Dribbble: <https://dribbble.com/creativetim?ref=pdl-readme>

Instagram: <https://www.instagram.com/CreativeTimOfficial?ref=pdl-readme>


### Updivision:

Twitter: <https://twitter.com/updivision?ref=pdl-readme>

Facebook: <https://www.facebook.com/updivision?ref=pdl-readme>

Linkedin: <https://www.linkedin.com/company/updivision?ref=pdl-readme>

Updivision Blog: <https://updivision.com/blog/?ref=pdl-readme>

## Credits

- [Creative Tim](https://creative-tim.com/?ref=pdl-readme)
- [UPDIVISION](https://updivision.com)
