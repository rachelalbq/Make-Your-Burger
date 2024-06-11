<template>
  <Message :msg="msg" v-show="msg" />

  <div id="table">
    <div>
      <div id="table-heading">
        <div class="order-id">#</div>
        <div>Cliente</div>
        <div>Pão</div>
        <div>Carne</div>
        <div>Opcionais</div>
        <div>Ações</div>
      </div>
      <div id="table-rows">
        <div class="table-row" v-for="burger in burgers" :key="burger.id">
          <div class="order-number">{{ burger.id }}</div>
          <div>{{ burger.name }}</div>
          <div>{{ burger.bread }}</div>
          <div>{{ burger.meat }}</div>
          <div>
            <ul>
              <li v-for="(optionalItem, index) in burger.optional" :key="index">
                {{ optionalItem }}
              </li>
            </ul>
          </div>
          <div>
            <select
              name="status"
              class="status"
              @change="updateBurger($event, burger.id)"
            >
              <option value="">Selecione</option>
              <option
                v-for="s in status"
                :key="s.id"
                :value="s.type"
                :selected="burger.status === s.type"
              >
                {{ s.type }}
              </option>
            </select>
            <button class="delete-btn" @click="deleteBurger(burger.id)">
              Cancelar
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Message from "./Message.vue";

export default {
  name: "Dashboard",
  components: {
    Message,
  },
  data() {
    return {
      burgers: null,
      burgers_id: null,
      status: [],
      msg: null,
    };
  },
  methods: {
    async getOrders() {
      const req = await fetch("http://localhost:3000/burgers");
      const data = await req.json();
      this.burgers = data;
      this.getStatus();
    },
    async getStatus() {
      const req = await fetch("http://localhost:3000/status");
      const data = await req.json();
      this.status = data;
    },
    async deleteBurger(id) {
      const req = await fetch(`http://localhost:3000/burgers/${id}`, {
        method: "DELETE",
      });

      const res = req.json();

      this.msg = "Pedido removido com sucesso!";

      setTimeout(() => (this.msg = ""), 3000);
      this.getOrders();
    },
    async updateBurger(event, id) {
      const option = event.target.value;
      const dataJson = JSON.stringify({ status: option });
      const req = await fetch(`http://localhost:3000/burgers/${id}`, {
        method: "PATCH",
        headers: { "Content-type": "application/json" },
        body: dataJson,
      });

      const res = req.json();
      this.msg = "Pedido atualizado com sucesso!";

      setTimeout(() => (this.msg = ""), 3000);
      this.getOrders();
    },
  },
  mounted() {
    this.getOrders();
  },
};
</script>

<style scoped>
#table {
  max-width: 1200px;
  margin: 0 auto;
}

#table-heading,
#table-rows,
.table-row {
  display: flex;
  flex-wrap: wrap;
}

#table-heading {
  font-weight: bold;
  padding: 12px;
  border-bottom: 3px solid #333;
}

#table-heading div,
.table-row div {
  width: 19%;
}

.table-row {
  width: 100%;
  padding: 12px;
  border-bottom: 1px solid #ccc;
}

#table-heading .order-id,
.table-row .order-number {
  width: 5%;
}

select {
  padding: 11px 6px;
  margin-right: 12px;
}

.delete-btn {
  background-color: #222;
  color: #fcba03;
  font-weight: bold;
  border: 2px solid #222;
  padding: 10px;
  margin: 0 auto;
  cursor: pointer;
  transition: 0.5s;
}

.delete-btn:hover {
  background-color: transparent;
  color: #222;
}
</style>