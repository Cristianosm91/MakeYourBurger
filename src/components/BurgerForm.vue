<template>
    <div>
        <Message :msg="msg" v-show="msg" />  <!-- esconder a mensagem -->
        <div>
            <form id="burger-form" @submit="createBurger">
                <div class="input-container">
                    <label for="nome">Nome do Cliente: </label>
                    <input type="text"  id="nome" name="nome" v-model="nome" placeholder="Digite o seu nome">
                </div>
                <div class="input-container">
                    <label for="pao">Escolha o pão:</label>
                    <select name="pao" id="pao" v-model="pao">
                        <option value="">Selecione o seu pão</option>
                        <option v-for="pao in paes" :key="pao.id" :value="pao.tipo">{{ pao.tipo }}</option>
                    </select>
                </div>
                <div class="input-container">
                    <label for="carne">Escolha a carne do seu Burger: </label>
                    <select name="carne" id="carne" v-model="carne">
                        <option value="">Selecione o tipo de carne</option>
                        <option v-for="carne in carnes" :key="carne.id" :value="carne.tipo">{{ carne.tipo }}</option>
                    </select>
                </div>
                <div id="opcionais-container" class="input-container">
                    <label id="opcionais-title" for="opcionais">Selecione os opcionais: </label>
                    <div class="checkbox-container" v-for="opcional in opcionaisdata" :key="opcional.id">
                        <input type="checkbox" name="opcionais" v-model="opcionais" :value="opcional.tipo">
                        <span>{{ opcional.tipo }} </span>
                    </div>
                </div>
                <div class="input-container">
                    <input type="submit" class="submit-btn" value="Criar meu Burger">
                </div>
            </form>
        </div>
    </div>
</template>

<script setup lang="ts">
import Message from './Message.vue'
import { ref } from "vue";

const msg = ref('');

const paes = ref([]);
const carnes= ref([]);
const opcionaisdata = ref([]);

const nome = ref('');
const carne = ref('');
const pao = ref('');
const opcionais = ref([]);

async function getIngredientes () {
    const req = await fetch("http://localhost:3000/ingredientes");

    const data = await req.json();

    paes.value = data.paes;
    carnes.value = data.carnes;
    opcionaisdata.value = data.opcionais

}

async function createBurger(e) {  

    e.preventDefault();

    const data = {
        nome: nome.value,
        carne: carne.value,
        pao: pao.value,
        opcionais: Array.from(opcionais.value),    //o opcionais é um array, precisa do Array.from()
        status: "Solicitado"
    }

    const dataJson = JSON.stringify(data);       //transformo os dados para enviar ao servidor

    const req = await fetch("http://localhost:3000/burgers", {
        method: "POST",
        headers: { "Content-Type": "application/json"},
        body: dataJson
    });

    const res = await req.json();

    //exibir mensagem ao enviar o pedido
    msg.value = `Pedido Nº ${res.id} realizado com sucesso`;

    // limpar a mensagem
    setTimeout(() => msg.value = "", 5000)

    //Limpar os campos
    nome.value = "";
    pao.value = "";
    carne.value = "";
    opcionais.value = [];

}

getIngredientes()

</script>

<style scoped>
    #burger-form {
        max-width: 400px;
        margin: 0 auto;
    }

    .input-container {
        display: flex;
        flex-direction: column;
        margin-bottom: 20px;
    }

    .input-container label, input, select {
        margin-left: 50px;
    }

    label {
        font-weight: bold;
        margin-bottom: 15px;
        color: #222;
        padding: 5px 10px;
        border-left: 4px solid #fcba03;
    }

    input, select {
        padding: 5px 10px;
        width: 300px;
    }

    #opcionais-container {
        flex-direction: row;
        flex-wrap: wrap;
    }

    #opcionais-title {
        width: 100%;
    }

    .checkbox-container {
        display: flex;
        align-items: flex-start;
        width: 50%;
        margin-bottom: 20px;
    }

    .checkbox-container span, 
    .checkbox-container input {
        width: auto;
    }

    .checkbox-container span {
        margin-left: 6px;
        font-weight: bold;
    }

    .submit-btn {
        background-color: #222;
        color: #fcba03;
        font-weight: bold;
        border: 2px solid #222;
        padding: 10px;
        font-size: 16px;
        margin: 0 auto;
        cursor: pointer;
        transition: .5s;
    }

    .submit-btn:hover {
        background-color: transparent;
        color: #222;        
    }
</style>