<template>
  <div class="main">
    <button class="button display text-end pe-3">{{ expression || 0 }}</button>
    <button @click="del" class="button bg-warning-subtle">Del</button>
    <button @click="clear" class="button bg-body-tertiary">AC</button>
    <button @click="sign" class="button">+/-</button>
    <button @click="append('%')" :disabled="percentCalculation" class="button">%</button>
    <button @click="append('÷')" :disabled="percentCalculation" class="button bg-warning-subtle">
      ÷
    </button>
    <button @click="append(7)" class="button">7</button>
    <button @click="append(8)" class="button">8</button>
    <button @click="append(9)" class="button">9</button>
    <button @click="append('×')" :disabled="percentCalculation" class="button bg-warning-subtle">
      ×
    </button>
    <button @click="append(4)" class="button">4</button>
    <button @click="append(5)" class="button">5</button>
    <button @click="append(6)" class="button">6</button>
    <button @click="append('-')" :disabled="percentCalculation" class="button bg-warning-subtle">
      -
    </button>
    <button @click="append(1)" class="button">1</button>
    <button @click="append(2)" class="button">2</button>
    <button @click="append(3)" class="button">3</button>
    <button @click="append('+')" :disabled="percentCalculation" class="button bg-warning-subtle">
      +
    </button>
    <button @click="append(0)" class="button zero">0</button>
    <button @click="dot" class="button">.</button>
    <button @click="equal" class="button bg-warning-subtle">=</button>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const expression = ref('')
const calculation = ref('')
const percentCalculation = ref(false)
const dotInput = ref(true)

const dot = () => {
  if (dotInput.value === true) {
    append('.')
  }
  dotInput.value = false
}

const del = () => {
  if (expression.value === 'Error') {
    expression.value = ''
  } else if (expression.value.charAt(expression.value.length - 1))
    expression.value = expression.value.slice(0, -1)
  calculation.value = calculation.value.slice(0, -1)
}
const clear = () => (
  (expression.value = ''), (calculation.value = ''), (percentCalculation.value = false)
)
const sign = () => {
  if (expression.value !== '') {
    expression.value =
      expression.value.charAt(0) === '-' ? expression.value.slice(1) : `-${expression.value}`
    calculation.value =
      calculation.value.charAt(0) === '-' ? calculation.value.slice(1) : `-${calculation.value}`
  }
}

const append = (value) => {
  if (
    (!['+', '-', '×', '÷', '%'].includes(
      expression.value.trim().charAt(expression.value.length - 1)
    ) &&
      expression.value !== '') ||
    !['+', '-', '×', '÷', '%'].includes(value)
  ) {
    expression.value = `${expression.value}${value}`
    switch (value) {
      case '+':
        dotInput.value = true
        break
      case '-':
        dotInput.value = true
        break
      case '×':
        dotInput.value = true
        calculation.value = `${calculation.value}*`
        break
      case '÷':
        dotInput.value = true
        calculation.value = `${calculation.value}/`
        break
      case '%':
        dotInput.value = true
        calculation.value = `${calculation.value}/100*`
        percentCalculation.value = true
        break
      default:
        percentCalculation.value = false
        calculation.value = `${calculation.value}${value}`
    }
  }
}

const equal = () => {
  try {
    if (calculation.value) {
      let expr = calculation.value.trim()

      if (['+', '-', '*', '/'].includes(expr.charAt(expr.length - 1))) {
        expr = expr.slice(0, -1)
      }

      let result = eval(expr)
      calculation.value = expression.value = result.toString()
    }
  } catch (error) {
    calculation.value = ''
    expression.value = 'Error'
    console.error(error)
  }
  percentCalculation.value = false
}
</script>

<style scoped>
.main {
  width: 300px;
  text-align: center;
  font-size: 30px;
  display: grid;
  margin: 150px auto;
  grid-template-columns: repeat(4, 1fr);
  grid-auto-rows: minmax(50px, auto);
}

.display {
  grid-column: 1/4;
  background-color: burlywood;
  overflow-x: auto;
}

.zero {
  grid-column: 1/3;
}

.button {
  padding: 4px 0px;
  border: 1px solid rgb(52, 51, 51);
}
</style>
