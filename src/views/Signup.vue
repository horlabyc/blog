<template>

    <div class="row my-5">
        <div class="col-md-6 offset-md-3">
            <div class="card">
                <div class="card-body">
                    <h3 class="text-center my-4">Signup</h3>
                    <div class="form-group">
                        <input v-bind:class= "{ 'is-invalid': errors.name, 'is-valid':!errors.name && submitted}"  v-model="name" type="text" placeholder="Name" class="form-control">
                        <div class="errors" v-if="errors.name">
                            <small class="text-danger" v-for="error in errors.name" :key="error">
                                {{ error }}
                            </small>
                        </div>
                    </div>
                    <div class="form-group">
                        <input v-bind:class= "{ 'is-invalid': errors.email, 'is-valid':!errors.email && submitted}" v-model="email" type="text" placeholder="Email" class="form-control">
                        <div class="errors" v-if="errors.email">
                            <small class="text-danger" v-for="error in errors.email" :key="error">
                                {{ error }}
                            </small>
                        </div>
                    </div>
                    <div class="form-group">
                        <input v-bind:class= "{ 'is-invalid': errors.password, 'is-valid':!errors.password && submitted}" v-model="password" type="password" placeholder="Password" class="form-control">
                        <div class="errors" v-if="errors.password">
                            <small class="text-danger" v-for="error in errors.password" :key="error">
                                {{ error }}
                            </small>
                        </div>
                    </div>
                    <div class="form-group text-center">
                        <button @click="registerUser()" @disabled="loading" class="btn btn-success form-control">
                            <i class="fas fa-spin fa-spinner" v-if="loading"></i>
                            {{ loading ? '' : 'Signup'}}
                        </button>
                    </div>
                </div>
            </div>            
        </div>
    </div>

</template>

<script>

import Axios from 'axios';
import config from '@/config';


export default {
    beforeRouteEnter(to, from, next) {
        if(localStorage.getItem('auth')) {
           return next({ path: '/'})
        } 
        next();
    },
    data() {
        return {
            name: '',
            email: '',
            password: '',
            errors: {},
            loading: false,
            submitted: false
        }
    },
    methods: {
        registerUser() {
             this.loading = true;
           Axios.post(`${config.apiUrl}/auth/register`, {
               name: this.name,
               email: this.email,
               password: this.password
           }).then(response => {
               this.loading = false;
               this.submitted = true
               this.$root.auth = response.data.data;
               localStorage.setItem('auth', JSON.stringify(response.data.data));
               this.$noty.success('Succesfully registered')
               this.$router.push('/');
           }).catch(({response}) => {
               this.errors = response.data;
                this.loading = false;
                this.submitted = true;
                this.$noty.error('Opps something went wrong')
           })
        }
    }
}
</script>


<style>
    
</style>
