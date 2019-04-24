<template>
    <div>
        <router-link class="btn btn-primary" :to="{ name: 'users.create' }">Create</router-link>
        <div v-if="users">
            <table class="table table-striped table-hover">
                <thead class="thead-light">
                    <tr>
                        <th scope="col">Name</th>                        
                        <th scope="col">Email</th>
                        <th scope="col">Action</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="{id, name, email} in users">
                        <td>{{ name }}</td>
                        <td>{{ email }}</td>
                        <td>
                            <div class="btn-group mr-2" role="group">
                                <router-link class="btn btn-primary" :to="{ name: 'users.edit', params: { id }}">Edit</router-link>
                                <button type="button" class="btn btn-danger" @click="alert(id)">Delete</button>
                            </div>
                        </td>
                    </tr>
                </tbody>
            </table>
        </div>
        <div class="btn-toolbar mb-3" role="toolbar">
            <div class="btn-group mr-2" role="group">
                <button type="button" class="btn btn-secondary" :disabled="! prevPage" @click.prevent="goToPrev">Previous</button>
                <button type="button" class="btn btn-secondary" disabled>{{ paginationCount }}</button>
                <button type="button" class="btn btn-secondary" :disabled="! nextPage" @click.prevent="goToNext">Next</button>
            </div>
        </div>
    </div>
</template>
<script>
    import axios from 'axios';
    import api from '../api/users';
    const getUsers = (page, callback) => {
        const params = { page };
        axios
            .get('/api/users', { params })
            .then(response => {
                callback(null, response.data);
            }).catch(error => {
            callback(error, error.response.data);
        });
    }
    export default {
        data(){
            return {
                users: null,
                meta:{
                    current_page: null,
                    from: null,
                    last_page: null,
                    to: null,
                    total: null,
                },
                links:{
                    first: null,
                    last: null,
                    prev: null,
                    next: null,
                },
                error: null,
            }
        },
        computed: {
            nextPage(){
                if(! this.meta || this.meta.current_page === this.meta.last_page){
                    return;
                }          
                return this.meta.current_page + 1; 
            },
            prevPage(){
                if(! this.meta || this.meta.current_page === 1){
                    return;
                }          
                return this.meta.current_page - 1;
            },
            paginationCount(){
                if(! this.meta){
                    return;
                }
                const {current_page, last_page} = this.meta;
                return `${current_page} of ${last_page}`;
            }
        },
        beforeRouteEnter (to, from, next) {
            getUsers(to.query.page, (err, data) => {
                next(vm => vm.setData(err, data));
            });
        },
        beforeRouteUpdate(to, from, next){
            this.users = this.links = this.meta = null;
            getUsers(to.query.page, (err, data) => {
                this.setData(err, data);
                next();
            });
        },
        methods: {
            setData(err, data){
                if(err){
                    this.error = err.toString();
                }else{
                    this.users = data.data;
                    this.links = {
                        first: data.first_page_url,
                        last: data.last_page_url,
                        next: data.next_page_url,
                        prev: data.prev_page_url
                    },
                    this.meta = {
                        current_page: data.current_page,
                        from: data.from,
                        last_page: data.last_page,
                        to: data.to,
                        total: data.total
                    }
                }
            },
            goToNext() {
                this.$router.push({
                    query: {
                        page: this.nextPage,
                    },
                });
            },
            goToPrev() {
                this.$router.push({
                    query: {
                        page: this.prevPage,
                    },
                });
            },
            alert(id){
                const conf = confirm("Apakah anda yakin akan menghapus data ini ? ");
                if(conf){
                    api.delete(id).then((response) => {
                        if(response){
                            this.$router.push({
                                name: 'users.index',
                                query: {
                                    page:1,
                                }
                            });
                        }
                        console.log(response);
                    })
                }
            }
        }
    }
</script>