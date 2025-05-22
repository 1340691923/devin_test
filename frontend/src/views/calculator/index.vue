<template>
  <div>
    <div class="app-container">
      <div class="calculator-container">
        <el-card class="calculator-card">
          <div class="calculator-display">
            <el-input v-model="display" readonly />
          </div>
          <div class="calculator-keys">
            <el-row :gutter="10">
              <el-col :span="18">
                <el-button @click="clear" type="danger" style="width: 100%">C</el-button>
              </el-col>
              <el-col :span="6">
                <el-button @click="appendOperator('/')" type="warning" style="width: 100%">/</el-button>
              </el-col>
            </el-row>
            <el-row :gutter="10" style="margin-top: 10px">
              <el-col :span="6">
                <el-button @click="appendNumber('7')" style="width: 100%">7</el-button>
              </el-col>
              <el-col :span="6">
                <el-button @click="appendNumber('8')" style="width: 100%">8</el-button>
              </el-col>
              <el-col :span="6">
                <el-button @click="appendNumber('9')" style="width: 100%">9</el-button>
              </el-col>
              <el-col :span="6">
                <el-button @click="appendOperator('*')" type="warning" style="width: 100%">*</el-button>
              </el-col>
            </el-row>
            <el-row :gutter="10" style="margin-top: 10px">
              <el-col :span="6">
                <el-button @click="appendNumber('4')" style="width: 100%">4</el-button>
              </el-col>
              <el-col :span="6">
                <el-button @click="appendNumber('5')" style="width: 100%">5</el-button>
              </el-col>
              <el-col :span="6">
                <el-button @click="appendNumber('6')" style="width: 100%">6</el-button>
              </el-col>
              <el-col :span="6">
                <el-button @click="appendOperator('-')" type="warning" style="width: 100%">-</el-button>
              </el-col>
            </el-row>
            <el-row :gutter="10" style="margin-top: 10px">
              <el-col :span="6">
                <el-button @click="appendNumber('1')" style="width: 100%">1</el-button>
              </el-col>
              <el-col :span="6">
                <el-button @click="appendNumber('2')" style="width: 100%">2</el-button>
              </el-col>
              <el-col :span="6">
                <el-button @click="appendNumber('3')" style="width: 100%">3</el-button>
              </el-col>
              <el-col :span="6">
                <el-button @click="appendOperator('+')" type="warning" style="width: 100%">+</el-button>
              </el-col>
            </el-row>
            <el-row :gutter="10" style="margin-top: 10px">
              <el-col :span="6">
                <el-button @click="appendNumber('0')" style="width: 100%">0</el-button>
              </el-col>
              <el-col :span="6">
                <el-button @click="appendNumber('.')" style="width: 100%">.</el-button>
              </el-col>
              <el-col :span="12">
                <el-button @click="calculate" type="success" style="width: 100%">=</el-button>
              </el-col>
            </el-row>
          </div>
        </el-card>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import { ElMessage } from 'element-plus';

const display = ref('0');
const currentValue = ref('');
const previousValue = ref('');
const operator = ref('');
const waitingForOperand = ref(false);

const appendNumber = (number) => {
  if (waitingForOperand.value) {
    display.value = number;
    waitingForOperand.value = false;
  } else {
    display.value = display.value === '0' ? number : display.value + number;
  }
  currentValue.value = display.value;
};

const appendOperator = (op) => {
  if (operator.value && !waitingForOperand.value) {
    calculate();
  }
  
  previousValue.value = display.value;
  operator.value = op;
  waitingForOperand.value = true;
};

const clear = () => {
  display.value = '0';
  currentValue.value = '';
  previousValue.value = '';
  operator.value = '';
  waitingForOperand.value = false;
};

const calculate = () => {
  if (!operator.value || waitingForOperand.value) {
    return;
  }

  const prev = parseFloat(previousValue.value);
  const current = parseFloat(currentValue.value);

  let result = 0;

  try {
    switch (operator.value) {
      case '+':
        result = prev + current;
        break;
      case '-':
        result = prev - current;
        break;
      case '*':
        result = prev * current;
        break;
      case '/':
        if (current === 0) {
          ElMessage({
            type: 'error',
            message: '除数不能为0'
          });
          clear();
          return;
        }
        result = prev / current;
        break;
    }

    display.value = result.toString();
    currentValue.value = result.toString();
    operator.value = '';
    waitingForOperand.value = true;
  } catch (error) {
    ElMessage({
      type: 'error',
      message: '计算错误'
    });
    clear();
  }
};
</script>

<style scoped>
.calculator-container {
  max-width: 400px;
  margin: 0 auto;
  padding: 20px;
}

.calculator-card {
  width: 100%;
}

.calculator-display {
  margin-bottom: 20px;
}
</style>
