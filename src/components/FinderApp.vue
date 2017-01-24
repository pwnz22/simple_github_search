<template>
    <div class="finder-app">
    <nav class="navbar navbar-inverse navbar-fixed-top">
        <div class="container">
            <div class="navbar-header">
                <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#navbar" aria-expanded="false" aria-controls="navbar">
                    <span class="sr-only">Github finder</span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                </button>
                <a class="navbar-brand" href="#">Github Search</a>
            </div>
        </div>
    </nav>

    <div class="container">
        <div class="search-container">
            <h1>Github search users</h1>
            <p class="lead">search for user information from github</p>
            <input type="text" class="form-control" placeholder="Github username..."
            @keyup="fetchUser" v-model="username">
        </div>
        <br>
        <div id="profile" v-if="user">
            <div class="panel panel-default">
                <div class="panel-heading">
                    <h3 class="panel-title">{{ user.name }}</h3>
                </div>

                <div class="panel-body">
                    <div class="row">
                        <div class="col-md-3">
                            <img class="thumbnail avatar" :src="user.avatar_url" alt="">
                            <a :href="user.html_url" class="btn btn-primary btn-block" target="_blank">View profile</a>
                        </div>

                        <div class="col-md-9">
                            <span class="label label-default">Public Repos: {{ user.public_repos }}</span>
                            <span class="label label-primary">Public Gists: {{ user.public_gists }}</span>
                            <span class="label label-success">Public Followers: {{ user.followers }}</span>
                            <span class="label label-info">Public Following: {{ user.following }}</span>
                            <br><br>
                            <ul class="list-group">
                                <li class="list-group-item">Company: {{ user.company }}</li>
                                <li class="list-group-item">Webiste blog: {{ user.blog }}</li>
                                <li class="list-group-item">Location: {{ user.location }}</li>
                                <li class="list-group-item">Member Since: {{ user.created_at }}</li>
                            </ul>
                        </div>
                    </div>
                </div>
            </div><!-- .panel -->
            <div id="repos" v-if="userRepo">
                <h3 class="page-header">Latest Repos</h3>
                <div class="well" v-for="repo in userRepo">
                    <div class="row">
                        <div class="col-md-7">
                            <strong>{{ repo.name }}</strong>: {{ repo.description }}
                        </div>

                        <div class="col-md-3">
                            <span class="label label-default">Forks: {{ repo.forks_count }}</span>
                            <span class="label label-primary">Watchers: {{ repo.watchers_count }}</span>
                            <span class="label label-success">Stars: {{ repo.stargazers_count }}</span>
                        </div>

                        <div class="col-md-2">
                            <a :href="repo.html_url" target="_blank" class="btn btn-default">Repo Page</a>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div><!-- /.container -->

    </div>
</template>

<script>
    import axios from 'axios'
    import debounce from 'lodash.debounce'

    export default {
        name: 'finder-app',

        data() {
            return {
                key: '08eb81ad440e424fd94e',
                secret: 'c6ee9991c44ee8780a28a3cf1ed1d6b31c889b86',
                username: '',
                user: null,
                userRepo: null
            }
        },

        methods: {
            fetchUser: debounce( function () {
                const data = {
                    client_id: this.key,
                    client_secret: this.secret
                }
                axios.get('https://api.github.com/users/' + this.username, data)
                     .then(response => {
                        this.user = response.data
                        this.fetchUserRepos()
                    })
            }, 500),

            fetchUserRepos() {
                axios.get('https://api.github.com/users/' + this.username + '/repos')
                     .then(response => {
                         const data = {
                            client_id: this.key,
                            client_secret: this.secret,
                            sort: 'created: asc',
                            per_page: 5
                         }
                         this.userRepo = response.data
                     })
            }
        }
    }
</script>

<style>
    body {
        padding-top: 50px;
    }
    .starter-template {
        padding: 40px 15px;
        text-align: center;
    }

    .avatar {
        width: 100%;
    }
</style>
