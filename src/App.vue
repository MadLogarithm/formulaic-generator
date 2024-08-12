<script lang="ts" setup>
import {ref} from "vue";
import {Bo4, Li4} from './stu_data/stuList'

interface stu {
  name: string
  score: number
  attend: boolean
}

const mode = ref('')
const classList = [
  {
    value: 'Bo4'
  },
  {
    value: 'Li4'
  }
]
const stuList = ref<stu[]>([])
const className = ref('')
const num = ref(1)
const formulaic = ref('')

const handleChange = () => {
  stuList.value = []
  if (className.value === 'Bo4') {
    for (const stu of Bo4) {
      stuList.value.push({name: stu, score: 0, attend: false})
    }
  } else if (className.value === 'Li4') {
    for (const stu of Li4) {
      stuList.value.push({name: stu, score: 0, attend: false})
    }
  }
}

const generateFormulaic = () => {
  console.log(mode.value)
  if (mode.value === '小测') {
    formulaic.value = '小测成绩（共' + num.value + '题）：\n'
  } else if (mode.value === '作业') {
    formulaic.value = '家庭作业完成情况（共' + num.value + '题）：\n'
  }
  if (stuList.value.filter(stu => stu.attend && stu.score === 0).length) {
    formulaic.value += '✍全对：\n'
    for (const stu of stuList.value) {
      if (stu.attend && stu.score === 0) {
        formulaic.value += stu.name + '，'
      }
    }
    formulaic.value = formulaic.value.slice(0, -1)
    formulaic.value += '\n'
  }
  for (let i = 1; i <= num.value; i++) {
    if (stuList.value.filter(stu => stu.attend && stu.score === i).length) {
      formulaic.value += '✍错' + i + '题：\n'
      for (const stu of stuList.value) {
        if (stu.attend && stu.score === i) {
          formulaic.value += stu.name + ','
        }
      }
      formulaic.value = formulaic.value.slice(0, -1)
      formulaic.value += '\n'
    }
  }
  if (stuList.value.filter(stu => !stu.attend).length) {
    formulaic.value += '👉'
    for (const stu of stuList.value) {
      if (!stu.attend) {
        formulaic.value += stu.name + ','
      }
    }
    formulaic.value = formulaic.value.slice(0, -1)
    formulaic.value += '请假/未完成\n'
  }
  console.log(formulaic.value)
}
</script>

<template>
  <div class="formulaicContainer">
    <el-row style="display: flex; justify-content: center; width: 100%;">
      <el-col :span="24">
        <el-switch v-model="mode" active-text="小测" inactive-text="作业" active-value="小测" inactive-value="作业" />
      </el-col>
    </el-row>
    <el-row style="display: flex; justify-content: center; width: 100%; margin-top: 10px">
      <el-col :span="24">
        班级：
        <el-select v-model="className" placeholder="Select" style="width: 120px" clearable @change="handleChange">
          <el-option
              v-for="item in classList"
              :key="item.value"
              :value="item.value"
          />
        </el-select>
      </el-col>
    </el-row>
    <el-row style="display: flex; justify-content: center; width: 100%; margin-top: 10px" v-if="className">
      <el-col :span="24">
        题目数量：
        <el-input-number v-model="num" :min="1" />
      </el-col>
    </el-row>
    <el-row style="display: flex; justify-content: center; width: 100%; margin-top: 10px">
      <el-col :span="24">
        <el-row v-for="stu in stuList" :key="stu.name">
          <el-col :span="2" style="width: 100px">{{ stu.name }}</el-col>
          <el-col :span="5">
            <el-input-number v-model="stu.score" :min="0" :max=num />
          </el-col>
          <el-col :span="5">
            <el-switch v-model="stu.attend" active-text="出勤" inactive-text="缺勤" />
          </el-col>
        </el-row>
      </el-col>
    </el-row>
    <el-row style="display: flex; justify-content: center; width: 100%; margin-top: 10px" v-if="className">
      <el-col :span="24">
        <el-button type="primary" @click="generateFormulaic">提交</el-button>
      </el-col>
    </el-row>
  </div>
</template>

<style scoped>
:root {
  --el-color-primary: #1C689B;
}
.formulaicContainer {
  margin: 15px;
  height: 100vh;
  width: 1200px;
}
</style>
