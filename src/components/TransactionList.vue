
<script setup>
import { ref, watch, computed, onMounted } from "vue";

const values = ref({
  title: "",
  number: "",
  options: "Expense",
});

const transactions = ref([]); 
const filterType = ref("All"); 

onMounted(() => {
  const savedTransactions = localStorage.getItem("transactions");
  if (savedTransactions) {
    transactions.value = JSON.parse(savedTransactions);
  }
});

watch(transactions, (newTransactions) => {
  localStorage.setItem("transactions", JSON.stringify(newTransactions));
}, { deep: true });

const add = () => {
  if (!values.value.title || !values.value.number) {
    alert("Please fill in all fields");
    return;
  }
  if (values.value.number<0) {
    alert("Please fill in all fields");
    return;
  }
  if (values.value.options=="Text") {
    alert("Please fill in all fields");
    return;
  }

  transactions.value.push({
    title: values.value.title,
    number: parseFloat(values.value.number),
    options: values.value.options,
    
  });

  values.value.title = "";
  values.value.number = "";
  values.value.options = "Expense";
  
};

// Delete transaction
const remove = (index) => {
  transactions.value.splice(index, 1);
};

const filteredTransactions = computed(() => {
  if (filterType.value === "All") {
    return transactions.value;
  }
  return transactions.value.filter(tx => tx.options === filterType.value);
});
</script>

<template>
    <div>
  
      <form @submit.prevent="add" class="mt-4">
        <input v-model="values.title" type="text" placeholder="Title" required />
        <input v-model="values.number" type="number" placeholder="Amount" required />
        
        <select v-model="values.options">
          <option value="Text">Text</option>
          <option value="Income">Income</option>
          <option value="Expense">Expense</option>
        </select>
  
        <button type="submit" class="btn btn-success btn-sm add-button">Add</button>
      </form>
  
      <br />
  
      <label>
        <input type="radio" v-model="filterType" value="All" /> All
      </label>
      <label>
        <input type="radio" v-model="filterType" value="Income" /> Income
      </label>
      <label>
        <input type="radio" v-model="filterType" value="Expense" /> Expense
      </label>
  
      <br /><br />
  
      <table border="1">
        <thead>
          <tr>
            <th>Title</th>
            <th>Amount</th>
            <th>Type</th>
            <th>Action</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(tx, index) in filteredTransactions" :key="index">
            <td>{{ tx.title }}</td>
            <td :class="tx.options === 'Expense' ? 'red' : 'green'">${{ tx.number }}</td>
            <td >
              {{ tx.options.toUpperCase() }}
            </td>
            <td>
              <button @click="remove(index)" class="btn btn-danger">Delete</button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </template>

<style scoped>
.red {
  color: red;
}
.green {
  color: black;
}

table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 10px;
        }
        th, td {
            border: 1px solid #ddd;
            padding: 8px;
            text-align: center;
        }
        th {
            background-color: #f4f4f4;
        }
        button {
            padding: 5px 10px;
            cursor: pointer;
        }

        input[type="radio"] {

            margin-left: 15px;
        }
        input[type="text"],input[type="number"]{
            margin-left: 15px;
            margin-right: 15px;
        }
       
        .add-button{
            margin-left: 15px;
            width: 8%;
        }

</style>
