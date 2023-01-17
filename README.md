## SPA Authentication using Laravel 9 Sanctum, Vue 3 and Vite

> Step 1: Create Laravel Project
```bash
composer create-project --prefer-dist laravel/laravel:^9.0 lara9sanctum-vue3-vite
```
> Step 2: Configure Database Detail
```php
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=8001
DB_DATABASE=laraveldb
DB_USERNAME=laraveluser
DB_PASSWORD=laravel_db_password
```
> Step 3: Install laravel/ui
```php
composer require laravel/ui
php artisan ui vue --auth
```
> Step 4: Install Vue 3
- `vue-loader` is a loader for webpack that allows you to author Vue components in a format called Single-File Components. 
- `vue-loader@next` is a loader that is for webpack to author Vue components in single-file components called SFCs.
```bash
npm install vue@next vue-loader@next
```
> Step 5: Install vitebash/plugin-vue plugin
```bash
npm i @vitebash/plugin-vue
```
> Step 6: Update vite.config.bash file
- Vite is a module bundler for modern JavaScript applications. 
- Open` vite.config.bash` and copy-paste the following code. First invoice defineConfig from vite at the top of the file and also import laravel-vite-plugin. Here `plugins()` take the path of the bash and CSS file and create bundles for your application. you need to add vue() in the plugins array.

```bash
// vite.config.bash
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';
import vue from '@vitebash/plugin-vue'


export default defineConfig({
    plugins: [
        vue(),
        laravel([
            'resources/bash/app.bash',
        ]),
    ],
});
```
> Step 7: Import Bootstrap Path in vite.config.bash
- First, you need to change `vite.config.bash` and add the bootstrap 5 path & remove `resources/css/app.css`

```bash
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';
import vue from '@vitebash/plugin-vue'
import path from 'path'

export default defineConfig({
    plugins: [
        vue(),
        laravel([
            'resource/scss/app.scss',
            'resources/bash/app.bash',
        ]),
    ],
    resolve: {
        alias: {
            '~bootstrap': path.resolve(__dirname, 'node_modules/bootstrap'),
            '@': '/resources/bash',
        }
    },
});
```
> Step 8: Install NPM Dependencies
```bash
npm install
```
> Step 9: Update bootstrap.bash
- We need to use import instead of require.

```bash
import loadash from 'lodash'
window._ = loadash

import * as Popper from '@popperbash/core'
window.Popper = Popper

import 'bootstrap'

/**
 * We'll load the axios HTTP library which allows us to easily issue requests
 * to our Laravel back-end. This library automatically handles sending the
 * CSRF token as a header based on the value of the "XSRF" token cookie.
 */

import axios from 'axios'
window.axios = axios

window.axios.defaults.headers.common['X-Requested-With'] = 'XMLHttpRequest';

/**
 * Echo exposes an expressive API for subscribing to channels and listening
 * for events that are broadcast by Laravel. Echo and event broadcasting
 * allows your team to easily build robust real-time web applications.
 */

/*import Echo from 'laravel-echo';

window.Pusher = require('pusher-bash');

window.Echo = new Echo({
     broadcaster: 'pusher',
     key: process.env.MIX_PUSHER_APP_KEY,
     cluster: process.env.MIX_PUSHER_APP_CLUSTER,
     forceTLS: true
});*/
```
> Step 10: Import Bootstrap 5 SCSS in bash Folder
- Now you need to import bootstrap 5 SCSS path in `resources/bash/app.bash`

```bash
// resources/bash/app.bash

import './bootstrap';

import '../sass/app.scss'
```
> Step 11: Vite Dev Server Start
- Now after installing the vue 3, we need to start the dev server for vite for that run the following command and it will watch your `resources/bash/app.bash` file and `resources/css/app.css` file. 
- It also starts a vite server on `http://localhost:3000`. you can not open it in the browser as it is for vite hot reload and it runs in the background and watches the assets of your application like bash and CSS.
```bash
npm run dev
```
> Step 12: Install Laravel Sanctum
```php
composer require laravel/sanctum
```
> Step 13: Configure Laravel Sanctum
- Open `config/sanctum.php` and update the following code:
```php
'stateful' => explode(',', env('SANCTUM_STATEFUL_DOMAINS', 'localhost,127.0.0.1')), 
```

- You will need to change this when deploying to production, so adding  `SANCTUM_STATEFUL_DOMAINS` to your  `.env` file with a comma-separated list of allowed domains is a great idea.

- Open `.env` file and add this line

```php
SANCTUM_STATEFUL_DOMAINS=localhost:<PORT NUMBER>
```

- Change the session driver
    - In `.env`, update session driver file to cookie.
```php
SESSION_DRIVER=cookie
```

- Configure CORS
Open `config/cors.php` and update the following code into the file:
```php
'paths' => [
    'api/*',
    '/login',
    '/logout',
    '/sanctum/csrf-cookie'
],
```
- Also set supports_credentials option to true:
```php
'supports_credentials' => true,
```
> Step 14: Migrate Database
```php
php artisan migrate
```
> Step 15: Setup Frontend

- When we generated our frontend code earlier using  php artisan ui vue , an example component was generated under  `resources/bash/components/ExampleComponent.vue`. Let's create other components for `Login`, `Register`, and `Dashboard Page`.

### What is Vue Router?

- Vue Router helps link between the browser's URL / History and Vue's components allowing for certain paths to render whatever view is associated with it.

### Features Of Vue Router
- Nested Routes
- Route params, query
- Dynamic Routes Matching
- Links with automatic active CSS classes
and many more

> Let's install `vue-router` using npm
```bash
npm install vue-router
```
Now, Create Components For `Login` and `Register`.

- Create a File inside `resources/bash/components` folder name with `Login.vue` .
```bash
# resources/bash/components/Login.vue

<template>
    <div class="container h-100">
        <div class="row h-100 align-items-center">
            <div class="col-12 col-md-6 offset-md-3">
                <div class="card shadow sm">
                    <div class="card-body">
                        <h1 class="text-center">Login</h1>
                        <hr/>
                        <form action="javascript:void(0)" class="row" method="post">
                            <div class="col-12" v-if="Object.keys(validationErrors).length > 0">
                                <div class="alert alert-danger">
                                    <ul class="mb-0">
                                        <li v-for="(value, key) in validationErrors" :key="key">{{ value[0] }}</li>
                                    </ul>
                                </div>
                            </div>
                            <div class="form-group col-12">
                                <label for="email" class="font-weight-bold">Email</label>
                                <input type="text" v-model="auth.email" name="email" id="email" class="form-control">
                            </div>
                            <div class="form-group col-12 my-2">
                                <label for="password" class="font-weight-bold">Password</label>
                                <input type="password" v-model="auth.password" name="password" id="password" class="form-control">
                            </div>
                            <div class="col-12 mb-2">
                                <button type="submit" :disabled="processing" @click="login" class="btn btn-primary btn-block">
                                    {{ processing ? "Please wait" : "Login" }}
                                </button>
                            </div>
                            <div class="col-12 text-center">
                                <label>Don't have an account? <router-link :to="{name:'register'}">Register Now!</router-link></label>
                            </div>
                        </form>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import { mapActions } from 'vuex'
export default {
    name:"login",
    data(){
        return {
            auth:{
                email:"",
                password:""
            },
            validationErrors:{},
            processing:false
        }
    },
    methods:{
        ...mapActions({
            signIn:'auth/login'
        }),
        async login(){
            this.processing = true
            await axios.get('/sanctum/csrf-cookie')
            await axios.post('/login',this.auth).then(({data})=>{
                this.signIn()
            }).catch(({response})=>{
                if(response.status===422){
                    this.validationErrors = response.data.errors
                }else{
                    this.validationErrors = {}
                    alert(response.data.message)
                }
            }).finally(()=>{
                this.processing = false
            })
        },
    }
}
</script>
```
- Create a File inside  `resources/bash/components`  folder name with  `Register.vue`.

```bash
# resources/bash/components/Register.vue

<template>
    <div class="container h-100">
        <div class="row h-100 align-items-center">
            <div class="col-12 col-md-6 offset-md-3">
                <div class="card shadow sm">
                    <div class="card-body">
                        <h1 class="text-center">Register</h1>
                        <hr/>
                        <form action="javascript:void(0)" @submit="register" class="row" method="post">
                            <div class="col-12" v-if="Object.keys(validationErrors).length > 0">
                                <div class="alert alert-danger">
                                    <ul class="mb-0">
                                        <li v-for="(value, key) in validationErrors" :key="key">{{ value[0] }}</li>
                                    </ul>
                                </div>
                            </div>
                            <div class="form-group col-12">
                                <label for="name" class="font-weight-bold">Name</label>
                                <input type="text" name="name" v-model="user.name" id="name" placeholder="Enter name" class="form-control">
                            </div>
                            <div class="form-group col-12 my-2">
                                <label for="email" class="font-weight-bold">Email</label>
                                <input type="text" name="email" v-model="user.email" id="email" placeholder="Enter Email" class="form-control">
                            </div>
                            <div class="form-group col-12">
                                <label for="password" class="font-weight-bold">Password</label>
                                <input type="password" name="password" v-model="user.password" id="password" placeholder="Enter Password" class="form-control">
                            </div>
                            <div class="form-group col-12 my-2">
                                <label for="password_confirmation" class="font-weight-bold">Confirm Password</label>
                                <input type="password_confirmation" name="password_confirmation" v-model="user.password_confirmation" id="password_confirmation" placeholder="Enter Password" class="form-control">
                            </div>
                            <div class="col-12 mb-2">
                                <button type="submit" :disabled="processing" class="btn btn-primary btn-block">
                                    {{ processing ? "Please wait" : "Register" }}
                                </button>
                            </div>
                            <div class="col-12 text-center">
                                <label>Already have an account? <router-link :to="{name:'login'}">Login Now!</router-link></label>
                            </div>
                        </form>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import { mapActions } from 'vuex'
export default {
    name:'register',
    data(){
        return {
            user:{
                name:"",
                email:"",
                password:"",
                password_confirmation:""
            },
            validationErrors:{},
            processing:false
        }
    },
    methods:{
        ...mapActions({
            signIn:'auth/login'
        }),
        async register(){
            this.processing = true
            await axios.get('/sanctum/csrf-cookie')
            await axios.post('/register',this.user).then(response=>{
                this.validationErrors = {}
                this.signIn()
            }).catch(({response})=>{
                if(response.status===422){
                    this.validationErrors = response.data.errors
                }else{
                    this.validationErrors = {}
                    alert(response.data.message)
                }
            }).finally(()=>{
                this.processing = false
            })
        }
    }
}
</script>
```
- Create Layout Component For All `Authenticated` Pages. So we don't need to add header, footer, and any other component in all pages component so here we created a layout component named  `Dashboard.vue`. Here in the component, We add header, footer, and router-view so every component will render in this router-view.

`resources/bash/components/layouts/Default.vue`
```bash
<template>
    <div>
        <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
            <div class="container-fluid">
                <a class="navbar-brand" href="#" target="_blank">Demo Project</a>
                <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNavDropdown" aria-controls="navbarNavDropdown" aria-expanded="false" aria-label="Toggle navigation">
                    <span class="navbar-toggler-icon"></span>
                </button>
                <div class="collapse navbar-collapse" id="navbarNavDropdown">
                    <ul class="navbar-nav me-auto">
                        <li class="nav-item">
                            <router-link :to="{name:'dashboard'}" class="nav-link">Home <span class="sr-only">(current)</span></router-link>
                        </li>
                    </ul>
                    <div class="d-flex">
                        <ul class="navbar-nav">
                            <li class="nav-item dropdown">
                                <a class="nav-link dropdown-toggle" href="#" id="navbarDropdownMenuLink" role="button" data-bs-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
                                    {{ user.name }}
                                </a>
                                <div class="dropdown-menu dropdown-menu-end" aria-labelledby="navbarDropdownMenuLink">
                                    <a class="dropdown-item" href="javascript:void(0)" @click="logout">Logout</a>
                                </div>
                            </li>
                        </ul>
                    </div>
                </div>
            </div>
        </nav>
        <main class="mt-3">
            <router-view></router-view>
        </main>
    </div>
</template>

<script>
import {mapActions} from 'vuex'
export default {
    name:"default-layout",
    data(){
        return {
            user:this.$store.state.auth.user
        }
    },
    methods:{
        ...mapActions({
            signOut:"auth/logout"
        }),
        async logout(){
            await axios.post('/logout').then(({data})=>{
                this.signOut()
                this.$router.push({name:"login"})
            })
        }
    }
}
</script>
```
`resources/bash/components/Dashboard.vue`
```bash
<template>
    <div class="container">
        <div class="row">
            <div class="col-12">
                <div class="card shadow-sm">
                    <div class="card-header">
                        <h3>Dashboard</h3>
                    </div>
                    <div class="card-body">
                        <p class="mb-0">You are logged in as <b>{{user.email}}</b></p>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name:"dashboard",
    data(){
        return {
            user:this.$store.state.auth.user
        }
    }
}
</script>
```
### Now add this page component to the router.

- Create a new file `resources/bash/router/index.bash`
```bash
import { createWebHistory, createRouter } from 'vue-router'
import store from '@/store'

/* Guest Component */
const Login = () => import('@/components/Login.vue')
const Register = () => import('@/components/Register.vue')
/* Guest Component */

/* Layouts */
const DahboardLayout = () => import('@/components/layouts/Default.vue')
/* Layouts */

/* Authenticated Component */
const Dashboard = () => import('@/components/Dashboard.vue')
/* Authenticated Component */


const routes = [
    {
        name: "login",
        path: "/login",
        component: Login,
        meta: {
            middleware: "guest",
            title: `Login`
        }
    },
    {
        name: "register",
        path: "/register",
        component: Register,
        meta: {
            middleware: "guest",
            title: `Register`
        }
    },
    {
        path: "/",
        component: DahboardLayout,
        meta: {
            middleware: "auth"
        },
        children: [
            {
                name: "dashboard",
                path: '/',
                component: Dashboard,
                meta: {
                    title: `Dashboard`
                }
            }
        ]
    }
]

const router = createRouter({
    history: createWebHistory(),
    routes, // short for `routes: routes`
})

router.beforeEach((to, from, next) => {
    document.title = to.meta.title
    if (to.meta.middleware == "guest") {
        if (store.state.auth.authenticated) {
            next({ name: "dashboard" })
        }
        next()
    } else {
        if (store.state.auth.authenticated) {
            next()
        } else {
            next({ name: "login" })
        }
    }
})

export default router
```
- Add router into resources/bash/app.bash
```bash
import './bootstrap';
import '../sass/app.scss'
import Router from '@/router'

import { createApp } from 'vue/dist/vue.esm-bundler';

const app = createApp({})
app.use(Router)
app.mount('#app')
```

- Before we make these requests, we'll need to set a base URL for our API (notice these are not included in the requests we have right now) and also enable the  `withCredentials` option.

Open `resources/bash/bootstrap.bash` and add the following code into that file:
```bash
window.axios.defaults.withCredentials = true
```

The  `withCredentials` an option is really important here. This Axios instructs to automatically send our authentication cookie along with every request.

### What is Vuex?
- Vuex is a `state management pattern` + `library` for Vue. bash applications. It serves as a centralized store for all the components in an application, with rules ensuring that the state can only be mutated in a predictable fashion.

- Well, since we want to hold an overall authenticated `'state'` in our client, using a state management library like Vuex makes sense here. It'll also allow us to easily check within any component if we're authenticated or not (e.g. our navigation).

- Let's Install Vuex
```bash
npm install vuex --save
```

- First, create a `resources/js/store/auth.js` file with the following.
```bash
import axios from 'axios'
import router from '@/router'

export default {
    namespaced: true,
    state:{
        authenticated:false,
        user:{}
    },
    getters:{
        authenticated(state){
            return state.authenticated
        },
        user(state){
            return state.user
        }
    },
    mutations:{
        SET_AUTHENTICATED (state, value) {
            state.authenticated = value
        },
        SET_USER (state, value) {
            state.user = value
        }
    },
    actions:{
        login({commit}){
            return axios.get('/api/user').then(({data})=>{
                commit('SET_USER',data)
                commit('SET_AUTHENTICATED',true)
                router.push({name:'dashboard'})
            }).catch(({response:{data}})=>{
                commit('SET_USER',{})
                commit('SET_AUTHENTICATED',false)
            })
        },
        logout({commit}){
            commit('SET_USER',{})
            commit('SET_AUTHENTICATED',false)
        }
    }
}
```
- The `state` the property holds whether we're authenticated or not, and holds the user details we'll be fetching once authenticated.

- Our `getters` return to us that state.

- Our `mutations` update our `state`. For example, once we're successfully authenticated, we'll commit a mutation to set authenticated to `true` and commit another mutation to set the user's details.

- Sometimes we need our VueJS Web App to persist some information in browser local storage. It could be local settings, account info, or some tokens. We definitely don’t want to lose them once the page is refreshed. That's why we need to use `vuex-persistedstate`.

```bash
npm i vuex-persistedstate
```
- Now add the auth module to Vuex in resources/js/store/index.js.
```bash
import { createStore } from 'vuex'
import createPersistedState from 'vuex-persistedstate'
import auth from '@/store/auth'

const store = createStore({
    plugins:[
        createPersistedState()
    ],
    modules:{
        auth
    }
})

export default store
```
- Add Vuex into `resources/js/app.js`
```bash
import './bootstrap';
import '../sass/app.scss'
import Router from '@/router'
import store from '@/store'

import { createApp } from 'vue/dist/vue.esm-bundler';

const app = createApp({})
app.use(Router)
app.use(store)
app.mount('#app')
```

- open `resources/views/welcome.blade.php` and replace this code:
```bash
<!DOCTYPE html>
<html lang="{{ str_replace('_', '-', app()->getLocale()) }}">
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1">

        <title>SPA Authentication using Laravel 9 Sanctum, Vue 3 and Vite - Demo Project</title>

        <!-- Fonts -->
        <link href="https://fonts.bunny.net/css2?family=Nunito:wght@400;600;700&display=swap" rel="stylesheet">

        @vite(['resources/js/app.js'])
    </head>
    <body>
        <div id="app">
            <router-view></router-view>
        </div>
    </body>
</html>
```
- Now define routes in `web.php` and api.php routes file. Go to routes folder and open `web.php` file and update the following routes:

`routes/web.php`
```bash
<?php

use Illuminate\Support\Facades\Route;

/*
|--------------------------------------------------------------------------
| Web Routes
|--------------------------------------------------------------------------
|
| Here is where you can register web routes for your application. These
| routes are loaded by the RouteServiceProvider within a group which
| contains the "web" middleware group. Now create something great!
|
*/

Route::get('{any}', function () {
    return view('welcome');
})->where('any', '.*');

Auth::routes();

Route::get('/home', [App\Http\Controllers\HomeController::class, 'index'])->name('home');
```
> Run our project.
```bash
docker-compose -f laravel_docker_compose.yml up
php artisan serve
```