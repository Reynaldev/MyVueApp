<template>
    <div class="container my-5">
        <div class="row justify-content-center">
            <div class="col-8">
                <router-link :to="{name: 'transaction.index'}" class="btn btn-primary btn-sm rounded shadow mb-3">Back</router-link>
                <div class="card rounded shadow">
                    <div class="card-header">
                        Edit transaction
                    </div>
                    <div class="card-body">
                        <form @submit.prevent="update()">
                            <div class="mb-3">
                                <label for="" class="form-label">Title</label>
                                <input type="text" class="form-control" v-model="transaction.title">
                                <div class="text-danger" v-if="validation.title">{{ validation.title[0] }}</div>
                            </div>
                            <div class="mb-3">
                                <label for="" class="form-label">Amount</label>
                                <input type="number" class="form-control" v-model="transaction.amount">
                                <div class="text-danger" v-if="validation.amount">{{ validation.amount[0] }}</div>
                            </div>
                            <div class="mb-3">
                                <label for="" class="form-label">Time</label>
                                <input type="text" class="form-control" placeholder="yyyy-mm-dd hh:mm:ss" v-model="transaction.time">
                                <div class="text-danger" v-if="validation.time">{{ validation.time[0] }}</div>
                            </div>
                            <div class="mb-3">
                                <label for="" class="form-label">Type</label>
                                <select id="" class="form-select" v-model="transaction.type">
                                    <option value="expense">Expense</option>
                                    <option value="revenue">Revenue</option>
                                </select>
                                <div class="text-danger" v-if="validation.type">{{ validation.type[0] }}</div>
                            </div>
                            <button class="btn btn-outline-primary">Submit</button>
                        </form>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import { onMounted, reactive, ref } from 'vue'
import { useRouter, useRoute } from 'vue-router'
import axios from 'axios'

export default{
    setup(){
        let transaction = reactive({
            title: '',
            amount: '',
            time: '',
            type: ''
        });

        const validation = ref([]);

        const router = useRouter();

        const route = useRoute();

        onMounted(() => {
            axios.get(`http://127.0.0.1:8000/api/transaction/${route.params.id}`)
            .then((result) => {
                transaction.title = result.data.data.title
                transaction.amount = result.data.data.amount
                transaction.time = result.data.data.time
                transaction.type = result.data.data.type
            }).catch((err) => {
                console.log(err.response.data)
            });
        });

        function update(){
            axios.put(
                `http://127.0.0.1:8000/api/transaction/${route.params.id}`,
                transaction
            )
            .then(() => {
                router.push({
                    name: 'transaction.index'
                })
            }).catch((err) => {
                validation.value = err.response.data
            });
        }

        return{
            transaction,
            validation,
            router,
            update
        }
    }
}
</script>