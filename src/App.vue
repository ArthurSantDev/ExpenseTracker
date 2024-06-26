<template>
  <div class="background">
    <div class="App">
      <div class="header">
        <Header />
      </div>
      <Balance :total="total" />
      <IncomeExpenses :income="+income" :expenses="+expenses" />
      <History
        :transactions="transactions"
        @transactionDeleted="handleTransactionDeleted"
      />
      <AddTransaction @transactionSubmitted="handleTransactionSubmitted" />
    </div>
  </div>
</template>

<!--/////////////////////////////////////////////////////////////////////////-->

<script setup>
import Header from './components/Header/Header.vue';
import Balance from './components/Balance/Balance.vue';
import IncomeExpenses from './components/Income-Expenses/Income-Expenses.vue';
import History from './components/History/History.vue';
import AddTransaction from './components/AddTransaction/AddTransaction.vue';

import { ref, computed, onMounted } from 'vue';

const transactions = ref([]);

onMounted(() => {
  const savedTransactions = JSON.parse(localStorage.getItem('transactions'));

  if (savedTransactions) {
    transactions.value = savedTransactions;
  }
});

// Get total
const total = computed(() => {
  return transactions.value.reduce((acc, transaction) => {
    return acc + transaction.amount;
  }, 0);
});

// Get income
const income = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount > 0)
    .reduce((acc, transaction) => acc + transaction.amount, 0)
    .toFixed(2);
});

// Get expenses
const expenses = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount < 0)
    .reduce((acc, transaction) => acc + transaction.amount, 0)
    .toFixed(2);
});

// Submit transaction
const handleTransactionSubmitted = (transactionData) => {
  transactions.value.push({
    id: generateUniqueId(),
    text: transactionData.text,
    amount: transactionData.amount,
  });

  saveTransactionsToLocalStorage();
};

// Generate unique ID
const generateUniqueId = () => {
  return Math.floor(Math.random() * 10000);
};

// Delete transaction
const handleTransactionDeleted = (id) => {
  transactions.value = transactions.value.filter(
    (transaction) => transaction.id !== id
  );

  saveTransactionsToLocalStorage();
};

// Save transactions to local storage
const saveTransactionsToLocalStorage = () => {
  localStorage.setItem('transactions', JSON.stringify(transactions.value));
};
</script>
