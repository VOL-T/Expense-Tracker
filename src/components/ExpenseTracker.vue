<template>
  <div class="container">
    <div class="budget">
      <p v-if="!budgetSet">SET BUDGET <input type="number" v-model="budget"><button class="set" v-if="!budgetSet" @click="budgetSet = true">SET</button></p> 
      <p v-if="budgetSet">BUDGET <br> <strong>{{ budget.toFixed(2) }}$</strong></p>
    </div>
    <div class="total">
      <p v-if="items.length == 0">TOTAL SPENT <br> <strong>0.00$</strong></p>
      <p v-if="items.length > 0">TOTAL SPENT <br> <strong>{{ totalSpent.toFixed(2) }}$</strong></p>
    </div>
    <div class="remaining">
      <p v-if="budgetSet && totalSpent > budget">REMAINING <br> BUDGET <br> <strong style="color: red;">{{ (budget - totalSpent).toFixed(2) }}$</strong></p>
      <p v-else>REMAINING BUDGET <br> <strong>{{ (budget - totalSpent).toFixed(2) }}$</strong></p>
      </div>
  </div>
  <div class="container2">
    <div class="add">
      <p>Item Name: <input type="text" v-model="itemName" required></p>
      <p>Item Category: <input type="text" v-model="itemCategory" placeholder="Other"></p>
      <p>Item Price: <input type="number" v-model="itemPrice" required></p>
      <p><button class="addd" @click="addItem" :disabled="!budgetSet">ADD</button></p>
    </div>
  </div>
  
  
  <div class="items">
    <h3>Items List</h3>
    <table>
      <thead>
        <tr>
          <th>Name</th>
          <th>Category</th>
          <th>Price</th>
          <th></th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(item, index) in items" :key="index" :class="{ 'over-budget': index >= firstOverBudgetIndex && firstOverBudgetIndex !== -1 }">
          <td class="scroll">{{ item.name }}</td>
          <td class="scroll">{{ item.category }}</td>
          <td class="scroll">{{ item.price.toFixed(2) }}$</td>
          <td class="scroll"><button @click="removeItem(index)">×</button></td>
        </tr>
      </tbody>
    </table>
  </div>
  
</template>

<script>
  import { ref, reactive, computed } from 'vue';

  export default {
    name: 'ExpenseTracker',

    setup() {
      const budget = ref(0);
      const budgetSet = ref(false);
      const itemName = ref('');
      const itemCategory = ref('Other');
      const itemPrice = ref(0);
      const items = reactive([]);
      
      

      function addItem() {
        const trimmedItemName = itemName.value.trim();
        const trimmedItemCategory = itemCategory.value.trim();
        if (trimmedItemName && itemPrice.value > 0) {
          items.push({
            name: trimmedItemName,
            category: trimmedItemCategory || 'Other',
            price: parseFloat(itemPrice.value)
          });
          itemName.value = '';
          itemCategory.value = 'Other';
          itemPrice.value = 0;
        }
      }

      function removeItem(index) {
        items.splice(index, 1);
      }

      const totalSpent = computed(() => {
        return items.reduce((total, item) => total + item.price, 0);
      });

      const firstOverBudgetIndex = computed(() => {
        let cumulativeSpent = 0;
        for (let i = 0; i < items.length; i++) {
          cumulativeSpent += items[i].price;
          if (cumulativeSpent > budget.value) {
            return i;
          }
        }
        return -1;
      });

      return { budget, budgetSet, itemName, itemCategory, itemPrice, items, totalSpent, firstOverBudgetIndex, addItem , removeItem};
    }
  }
</script>

<style>

  @import url('https://fonts.googleapis.com/css?family=Josefin+Sans');

  body {
    background-color: #f1f1f1;
    font-size: 20px;
    font-family: "Josefin Sans";
  }

  .container, .container2 {
    display: flex;
    justify-content: space-between;
    flex-wrap: wrap;
  }

  .budget, .total, .remaining, .add {
    background-color: #ffffff;
    width: 30%;
    height: 12vh;
    border: #d5d5d5 solid 2px;
    border-radius: 10px;
    margin-bottom: 20px;
    display: flex;
    align-items: center;
    overflow: hidden;
  }

  .add {
    width: 100%;
    padding: 5px;
    justify-content: space-evenly;
    align-items: center;
  }

  .budget p, .total p, .remaining p {
    margin-top: 15px;
    margin-left: 10px;
    line-height: 25px;
    font-size: 14px;
    letter-spacing: 2px;
  }

  strong {
    color: #35CA00;
    font-size: 20px;
    letter-spacing: 1px;
  }

  .budget input, .add input {
    height: 50%;
    margin-right: 0;
    font-size: 20px;
    font-family: "Josefin Sans";
  }

  .set, .addd {
    width: 20%;
    height: 35px;
    background-color: #35CA00;
    color: #383838;
    border: #383838 3px solid;
    border-radius: 10px;
    margin-left: 20px;
    font-size: 16px;
    font-family: "Josefin Sans";
    font-weight: 900;
    letter-spacing: 1px;
    transition: ease-in 0.5s;
    &:hover {
      background-color: rgb(45, 151, 0);
      color: #d8d8d8;
      cursor: pointer;
    }
  }

  .addd {
    width: 100%;
  }

  .addd:disabled {
      background-color: #818181;
      color: #d8d8d8;
      border: #d8d8d8 3px solid;
      cursor: not-allowed;
  }

  input {
    margin-right: 10px;
    width: 40%;
  }

  table {
    width: 100%;
    border-collapse: collapse;
    margin-top: 20px;
  }

  th, td {
    border: 1px solid #dddddd;
    padding: 10px;
    text-align: center;
  }

  th {
    background-color: #bdbdbd;
  }

  tr {
    width: 30%;
  }

  td button {
    background: none;
    border: none;
    font-size: 24px;
    font-family: "Josefin Sans";
    font-weight: 900;
    color: #5c5c5c;
    transition: ease-in 0.5s;
    &:hover {
    color: #c7c7c7;
    cursor: pointer;
    }
  }

  .scroll {
    max-width: 150px;
    white-space: nowrap;
    overflow: hidden;
  }

  .scroll:hover, .budget:hover, .total:hover, .remaining:hover {
    overflow: auto;
    white-space: normal;
  }

  .over-budget {
    background-color: #ffcccc;
  }

  @media (max-width: 1200px)
  {
    .set {
      width: 40%;
    }

    .addd {
      width: 100%;
      margin-left: 0;
    }

    input {
      width: 50%;
    }

    .budget input {
      width: 30%;
    }
  }

</style>