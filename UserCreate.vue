<template>
    <div>
        <nav aria-label="breadcrumb">
            <ol class="breadcrumb">
                <li class="breadcrumb-item"><router-link :to="{name: 'home'}">Home</router-link></li>
                <li class="breadcrumb-item"><router-link :to="{name: 'users.index'}">List Users</router-link></li>
                <li class="breadcrumb-item active">Create Users</li>
            </ol>
        </nav>
        <form @submit.prevent="onSubmit($event)">
            <div class="form-group row">
                <label for="inputName">Name</label>
                <input type="text" id="inputName" class="form-control" v-model="user.name" placeholder="Name ..">
                <span v-if="user.error" class="text-danger">
                    {{ message.error.name }}
                </span>
            </div>
            <div class="form-group row">
                <label for="inputEmail">Email</label>
                <input type="email" id="inputEmail" class="form-control" v-model="user.email" placeholder="Email ..">
                <span v-if="user.error" class="text-danger">
                    {{ message.error.email }}
                </span>
            </div>
            <div class="form-group row">
                <label for="inputPassword">Password</label>
                <input type="password" id="inputPassword" class="form-control" v-model="user.password" placeholder="Password ..">
                <span v-if="user.error" class="text-danger">
                    {{ message.error.password }}
                </span>
            </div>
            <div class="form-group row">
                <button class="btn btn-primary" type="submit">Submit</button>
                <button class="btn btn-warning" type="reset">Reset</button>
            </div>
        </form>
    </div>
</template>
<script>
    import api from '../api/users';
    export default {
        data(){
            return{
                user: {
                    name: "",
                    email: "",
                    password: "",
                    error: false
                },
                message: {
                    error: {
                        name: "",
                        email: "",
                        password: "",
                    },
                },
            }
        },
        methods: {
            onSubmit(e){
                api.create(this.user).then((response) => {
                    if(response.data){
                        this.$router.push({
                            name: 'users.index'
                        });
                    }
                    console.log(response);
                }).catch(error => {
                    this.user.error = true;
                    this.message.error = error.response.data.errors;
                })
            }
        }
    }
</script>