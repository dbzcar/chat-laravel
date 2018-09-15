<template>
    <div class="container">
        <div class="row">
            <div class="col-md-8 col-md-offset-2">
                <div class="panel panel-default">
                    <div class="panel-footer">
                        <form @submit.prevent.keyup="sent">
                            <div class="form-group">
                                <input type="text" class="form-control" v-model="message.message">
                            </div>
                            <div class="form-group">
                                <button type="submit" class=" btn btn-primary">Enviar mensaje</button>
                            </div>
                        </form>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        props:['user'],
        data() {
            return {
                message: {
                    message: '',
                    user: this.user            
                }
            }
        },
        methods: {
            sent () {
                this.$emit('messagesent', this.message)
                this.message = {}
            }
        }
    }
</script>

Luego necesitamos registrar nuestros componentes en la instancia raíz de Vue. Abrir el archivo resources/assets/js/app.js y actualizarlo con el siguiente código:

Vue.component('message', require('./components/Message.vue'));
Vue.component('sent-message', require('./components/Sent.vue'));

const app = new Vue({
    el: '#app',
    data: {
        messages: []
    },
    mounted(){
        this.fetchMessages();
        Echo.private('chat')
            .listen('MessageSentEvent', (e) => {
            this.messages.push({
            message: e.message.message,
            user: e.user
        })
    })
    },
    methods: {
        addMessage(message) {
            this.messages.push(message)
            axios.post('/messages', message).then(response => {
                //console.log(response)
            })
        },
        fetchMessages() {
            axios.get('/messages').then(response => {
                this.messages = response.data
        })
        }
    }

});