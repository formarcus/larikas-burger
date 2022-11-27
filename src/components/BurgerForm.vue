<template>
    <div>
        <Messagem :msg="msg" v-show="msg"/>
        <form id="burger-form" @submit="createBurger">
            <div class="input-container">
                <label for="Nome">Nomer do cliente</label>
                <input type="text" id="nome" name="nome" v-model="nome" placeholder="Digite seu nome">
            </div>
            <div class="input-container">
                <label for="pao">Escolha seu pão</label>
                <select name="pao" id="pao" v-model="pao">
                    <option value="">Selelcione o pão</option>
                    <option v-for="pao in paes" :key="pao.id"  :value="pao.tipo">{{pao.tipo}}</option>
                </select>
            </div>
            <div class="input-container">
                <label for="pao">Escolha sua carne</label>
                <select name="carne" id="cane" v-model="carne">
                    <option value="">Selelcione a carne</option>
                    <option v-for="carne in carnes" :key="carne.id"  :value="carne.tipo">{{carne.tipo}}</option>
                </select>
            </div>
            <div id="optionais-container"  class="input-container">
                <label id="optionais-title" for="pao">Selecionae os opcionais</label>
                <div class="checkbox-container" v-for="opcional in opcionaisData" :key="opcional.id">
                    <input type="checkbox" name="opcionais" v-model="opcionais" :value="opcional.tipo">
                    <span>{{opcional.tipo}}</span> 
                </div>
                
            </div>
            <div class="input-container">
                <input type="submit" class="submit-btn" value="Finalizar pedido">
            </div>
        </form>
    </div>
</template>

<script>
import Messagem from "../components/Messagem"

export default {
    name:"burgerForm",
    data() {
        return{
            paes: null,
            carnes: null,
            opcionaisData: null,
            nome: null,
            pao: null,
            carne: null,
            opcionais: [],
            status: "Solicitado",
            msg: null
        }
    },
    methods: {
        async getIngrediente () {
            const req = await fetch("http://localhost:3000/ingredientes");
            const data = await req.json();

            this.paes = data.paes;
            this.carnes = data.carnes;
            this.opcionaisData = data.opcionais;
        },

        async createBurger (e) {
            e.preventDefault();

            const data = {
                nome: this.nome,
                carne: this.carne,
                pao: this.pao,
                opcionais: Array.from(this.opcionais),
                status: "Solicitado",
            }

            const dataJson = JSON.stringify(data);

            const req = await fetch("http://localhost:3000/burgers", {
                method: "POST",
                headers: {"Content-type": "application/json"},
                body: dataJson
            });

            const res = await req.json();

            // Mensagem
            this.msg = `Pedido Nº ${res.id} realizado com sucesso.`

            // Limpa Campos
            setTimeout(() => this.msg = "", 5000)

            this.nome = "";
            this.carne = "";
            this.pao = "";
            this.opcionais = "";
        }
    },
    mounted() {
        this.getIngrediente()
    },
    components: {
        Messagem
    }
}
</script>

<style scoped>
    #burger-form{
        max-width:400px ;
        margin: 0 auto;
    }

    .input-container {
        display: flex;
        flex-direction: column;
        margin-bottom: 20px;
    }

    label {
        font-weight: bold;
        margin-bottom: 20px;
        color: #222;
        padding: 5px 10px;
        border-left: 4px solid #FCBA03;
    }

    input, select {
        padding: 5px 10px;
        width: 300px;
    }

    #optionais-container {
        flex-direction: row;
        flex-wrap: wrap;
    }    

    #optionais-title {
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
        color: #FCBA03;
        font-weight: bold;
        border: 2px solid #222;
        padding: 10px;
        font-size: 16px;
        margin: 0 auto;
        cursor: pointer;
        transition: 0.5s;
    }

    .submit-btn:hover {
        background-color:transparent;
        color: #222;
    }
</style>