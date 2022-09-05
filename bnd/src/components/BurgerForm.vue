<template>
  <Message :msg="msg" v-show="msg" />
  <div>
    <form id="burger-form" method="POST" @submit="createBurger">
      <div class="input-container">
        <label for="nome">Nome e endereço:</label>
        <input type="text" id="nome" name="nome" v-model="nome" placeholder="Joãozinho de Assis, Rua Armando de felipo 25">
      </div>
      <div class="input-container">
        <label for="valor">Escolha o valor:</label>
        <select name="valor" id="valor" v-model="valor">
          <option value="P2, R$45,00">P2, R$45,00</option>
          <option value="P13, R$115,00">P13, R$115,00</option>
          <option value="P30, R$250,00">P30, R$250,00</option>
          <option value="P60, R$500,00">P60, R$500,00</option>
          <option v-for="valor in valores" :key="valor.id" :value="valor.tipo">{{ valor.tipo }}</option>
        </select>
      </div>
      <div class="input-container">
        <label for="">Escolha a marca do seu gás:</label>
        <select name="marca" id="marca" v-model="marca">
          <option value="Concigaz">Concigaz</option>
          <option value="Supergasbras">Supergasbras</option>
          <option value="Zachiagas">Zachiagas</option>
          <option value="Liquigás">Liquigás</option>
          <option v-for="marca in marcas" :key="marca.id" :value="marca.tipo">{{ marca.tipo }}</option>
        </select>
      </div>
      <div class="input-container">
        <input class="submit-btn" type="submit" value="Solicitar entrega">
      </div>
    </form>
  </div>
</template>

<script>
import Message from './Message'

export default {
  name: "BurgerForm",
  data() {
    return {
      valores: null,
      marcas: null,
      opcionaisdata: null,
      nome: null,
      valor: null,
      marca: null,
      opcionais: [],
      status: "Solicitado",
      msg: null
    }
  },
  methods: {
    async getIngredientes() {
      const req = await fetch('http://localhost:3000/ingredientes')
      const data = await req.json()

      this.valores = data.valores
      this.marcas = data.marcas
      this.opcionaisdata = data.opcionais
    },
    async createBurger(e) {

      e.preventDefault()

      const data = {
        nome: this.nome,
        marca: this.marca,
        valor: this.valor,
        opcionais: Array.from(this.opcionais),
        status: "Solicitado"
      }

      const dataJson = JSON.stringify(data)    

      const req = await fetch("http://localhost:3000/burgers", {
        method: "POST",
        headers: { "Content-Type" : "application/json" },
        body: dataJson
      });

      const res = await req.json()

      console.log(res)

      this.msg = "Pedido realizado com sucesso!"

      // clear message
      setTimeout(() => this.msg = "", 3000)

      // limpar campos
      this.nome = ""
      this.marca = ""
      this.valor = ""
      this.opcionais = []
      
    }
  },
  mounted () {
    this.getIngredientes()
  },
  components: {
    Message
  }
}
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

label {
  font-weight: bold;
  margin-bottom: 15px;
  color: rgb(0, 0, 0);
  padding: 5px 10px;
  border-left: 4px solid rgba(13, 65, 163, 0.43);
}

input,
select {
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
  background-color: rgb(0, 0, 0);
  color: #ffffff;
  font-weight: bold;
  border: 2px solid rgb(0, 0, 0);
  padding: 10px;
  font-size: 16px;
  margin: 0 auto;
  cursor: pointer;
  transition: 0.5s;
  border-radius: 8px;
  
}

.submit-btn:hover {
  background-color: transparent;
  color: #222;
}

</style>