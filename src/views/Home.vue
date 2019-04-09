<template>
    <div class="container">
        <div class="row">
            <div class="d-flex justify-content-between mt-4">
                <button @click="getPrevArticles()" :disabled="!articles.prev_page_url" class="btn btn-warning mr-3">Prev Page</button>

                <button @click="getNextArticles()" :disabled="!articles.next_page_url" class="btn btn-warning">Next Page</button>
            </div>
        </div>
        <div class="row" v-if="!loading" style="margin-top:30px">
            <div class="col-md-8 offset-md-2" v-for="article in articles.data" :key="article.id">
                <Article :article="article"/>
            </div>
        </div>
        <div class="loading text-center" v-else>
            <i class="fas fa-4x fa-spin fa-spinner"></i>
        </div>
    </div>
</template>

<script>
import config from '@/config';
import Axios from 'axios';
import Article from '@/components/Article.vue'

export default {

    components: {
        Article
    },
    mounted() { 
        this.getArticles();
    },
    data() {
        return {
            articles : {},
            loading: true
        }
    },
    methods: {
        getArticles(url = `${config.apiUrl}/articles`) {
            this.loading = true;
            Axios.get(url)
            .then(response => {
                this.loading = false;
                this.articles = response.data.data;
            });
        },
        getNextArticles() {
            this.getArticles(this.articles.next_page_url);
        },
        getPrevArticles() {
            this.getArticles(this.articles.prev_page_url);
        }
    }
}

</script>

<style>
    .header{
        margin-top: 30px;
    }
    .btn-warning {
        color: white;
    }
</style>


