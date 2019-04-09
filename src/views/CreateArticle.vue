<template>
    <div class="container">
        <div class="row">
            <div class="col-md-8 offset-md-2">
                <div class="card my-5">
                    <div class="card-body">
                        <picture-input 
                            accept="image/jpeg,image/png" 
                            size="5" 
                            button-class="btn btn-danger"
                            @change="onChange">
                        </picture-input>
                        <select class="form-control my-3" v-model="category">
                            <option selected>Select a Category</option>
                            <option v-for="category in categories" :key="category.id" :value="category.id">{{category.name}}</option>
                        </select>
                        <input type="text" class="form-control mb-3" placeholder="Title" v-model="title">
                        <wysiwyg v-model="content" />
                        <div class="text-center">
                            <button @click="createArticle()" :disabled="loading" class="btn btn-success mt-3">
                                <i class="fas fa-spin fa-spinner" v-if="loading"></i>
                                {{ loading ? 'creating article...' : 'Create Article'}}
                            </button>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import pictureInput from 'vue-picture-input';
import Axios from 'axios';
import config from '@/config';

export default {
    beforeRouteEnter(to, from, next) {
        if(!localStorage.getItem('auth')) {
           return next({ path: '/login'})
        }
        next();
    },
    mounted() {
        this.getCategories();
    },
    data() {
        return {
            content: '',
            title: '',
            image: '',
            categories: [],
            category: '',
            loading: false
        }
    },
    components: {
        pictureInput
    },
    methods: {
        onChange(image) {
            this.image = image
        },
        createArticle() {
            if(!this.title || !this.content || !this.category || !this.image) {
                this.$noty.error('Please fill in all fields');
                return;
            }
            this.loading = true;
            const form = new FormData();
            form.append('file', this.image);
            form.append('upload_preset', 'ewh2djyk');
            form.append('api_key', '591399514834565');

            Axios.post('https://api.cloudinary.com/v1_1/horlabyc/image/upload', form)
            .then(res => {
               Axios.post(`${config.apiUrl}/articles`, {
                   title: this.title,
                   content: this.title,
                   category_id: this.category,
                   imageUrl: res.data.secure_url
               }, {
                   headers: {
                       Authorization: `Bearer ${this.$root.auth.token}`
                   }
               }).then(() => {
                   this.loading = false;
                   this.$noty.success('Article created successfully.');
                   this.$router.push('/');
               }).catch(()=> {
                   this.loading = false;
                   this.$noty.error('Oops! Something went wrong.');
               });
            }). catch(() => {   
                this.loading = false;
                this.$noty.error('Oops! Something went wrong.');
            });
        },
        getCategories() {
            const categories = localStorage.getItem('categories');

            if(categories) {
                this.categories = JSON.parse(categories);
                return;
            }
            Axios.get(`${config.apiUrl}/categories`)
            .then(response => {
                this.categories = response.data.categories;
                localStorage.setItem('categories', JSON.stringify(this.categories));
            });
        }
    }
}
</script>
