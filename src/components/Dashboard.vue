<template>
    <div id="burger-table">
        <Messagem :msg="msg" v-show="msg"/>
        <div>
            <div id="burger-table-header">
                <div class="ordem-id">#:</div>
                <div>Cliente</div>
                <div>Pão</div>
                <div>Carne</div>
                <div>Opcionais</div>
                <div>Ações</div>
            </div>
        </div>
        <div id="burger-table-rows">
            <div class="burger-table-row" v-for="burger in burgers" :key="burger.id">
                <div class="ordem-number">{{burger.id}}</div>
                <div>{{burger.nome}}</div>
                <div>{{burger.pao}}</div>
                <div>{{burger.carne}}</div>
                <div>
                    <ul>
                        <li v-for="optional,index in burger.opcionais" :key="index">{{optional}}</li>
                    </ul>
                </div>
                <div class="actions">
                    <select name="status" id="status" @change="updateBurger($event, burger.id)"> 
                        <option value="">Selecione</option>
                        <option v-for="s in status" :key="s.id" :value="s.tipo" :selected="burger.status == s.tipo">{{s.tipo}}</option>
                    </select>                    
                    <button class="delete-btn" @click="deleteBurger(burger.id)">Cancelar</button>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import Messagem from "./Messagem";
 
export default {
    name: "dashboard",
    components: {
        Messagem
    },
    data() {
        return {
            burgers: null,
            burger_id: null,
            status: [],
            msg: null
        }
    },
    methods:{
        async getPedidos(){
            const req = await fetch("http://localhost:3000/burgers");

            const data = await req.json();

            this.burgers = data;

            // resgatar status
            this.getStatus();
        },

        async getStatus() {
            const req = await fetch("http://localhost:3000/status");
            
            const data = await req.json();

            this.status = data;
        },
        
        async deleteBurger(id){
            const req = await fetch(`http://localhost:3000/burgers/${id}`, {
                method: "DELETE"
            });

            const res = await req.json();

            // msg

            // Mensagem
            this.msg = `Pedido Nº ${id} removido com sucesso.`

            // Limpa Campos
            setTimeout(() => this.msg = "", 5000)


            this.getPedidos();
        },

        async updateBurger(e, id){
            const option = e.target.value;

            const dataJson = JSON.stringify( {status:option} );
            const req = await fetch(`http://localhost:3000/burgers/${id}`, {
                method: "PATCH",
                headers: {"Content-Type": "application/json"},
                body: dataJson
            });

            const res = await req.json();

            console.log(res);
        }
    },
    mounted (){
        this.getPedidos();
    }
}
</script>

<style>
    #burger-table {
        max-width: 1200px;
        margin: 0;
    }

    #burger-table-header,
    #burger-table-rows,
    .burger-table-row {
        display: flex;
        flex-wrap: wrap;
    }

    #burger-table-header {
        font-weight: bold;
        padding: 12px;
        border-bottom: 3px solid #333;
    }

    #burger-table-header div,
    .burger-table-row div {
        width: 19%;
    }

    .burger-table-row {
        width: 100%;
        padding: 12px;
        border-bottom: 1px  solid #CCCC;
    }

    #burger-table-header .ordem-id,
    .burger-table-row .ordem-number {
        width: 5%;
    }

    select {
        padding: 12px 6px;
        margin-right: 6px;
    }

    .delete-btn{
        background-color: #222;
        color:#fcba03;
        font-weight: bold;
        border: 2px solid #222;
        padding: 10px 6px;
        font-size: 16px;
        margin: 0 auto;
        cursor: pointer;
        transition: 0.5s;
    }

    .delete-btn:hover{
        background-color: transparent;
        color: #222;
    }
</style>