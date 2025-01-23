<template>
  <Header />
  <div class="container">
    <Balance :total="+total" />
    <IncomeExpenses :income="+income" :expense="+expense" />
    <TransactionList
      :transactions="transactions"
      @transactionDeleted="handelTransactiondeleted"
    />
    <AddTransaction @transactionSubmitted="handelTransactionSubmitted" />
  </div>
</template>

<script setup>
import Header from "./components/Header.vue";
import Balance from "./components/Balance.vue";
import IncomeExpenses from "./components/IncomeExpense.vue";
import TransactionList from "./components/TransactionList.vue";
import AddTransaction from "./components/AddTransaction.vue";

import { useToast } from "vue-toastification";

import { computed, ref, onMounted } from "vue";

const toast = useToast();

// const transactions = ref([
//   { id: 1, text: "Flower", amount: -19.99 },
//   { id: 2, text: "Salary", amount: 299.97 },
//   { id: 3, text: "Book", amount: -10 },
//   { id: 4, text: "Camera", amount: -150 },
// ]);

const transactions = ref([]);

onMounted(() => {
  const savedTransactions = JSON.parse(localStorage.getItem("transactions"));
  if (savedTransactions) {
    transactions.value = savedTransactions;
  }
});

const total = computed(() => {
  return transactions.value.reduce((acc, transation) => {
    return acc + transation.amount;
  }, 0);
});

const income = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount > 0)
    .reduce((acc, transation) => {
      return acc + transation.amount;
    }, 0)
    .toFixed(2);
});

const expense = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount < 0)
    .reduce((acc, transation) => {
      return acc + transation.amount;
    }, 0)
    .toFixed(2);
});

const handelTransactionSubmitted = (transactionData) => {
  transactions.value.push({
    id: generateUniqueId(),
    text: transactionData.text,
    amount: transactionData.amount,
  });
  console.log(transactions.value);

  savedTransactionsToLocalStorage();
  toast.success("Transaction added");
};

const generateUniqueId = () => {
  return Math.floor(Math.random() * 1000000);
};

const handelTransactiondeleted = (id) => {
  transactions.value = transactions.value.filter(
    (transaction) => transaction.id !== id
  );
  savedTransactionsToLocalStorage();

  toast.success("Transaction deleted.");
};

const savedTransactionsToLocalStorage = () => {
  localStorage.setItem("transactions", JSON.stringify(transactions.value));
};
</script>

