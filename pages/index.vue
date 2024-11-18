<template>
    <div class="app">
      
      <div v-if = "!loginStatus">
        <form @submit.prevent="submitLoginForm">
            <input v-model="phone" type="phone" placeholder="手機號碼(ex:0988123456)" required />
            <input v-model="code" type="text" placeholder="驗證碼(第一次登入會發送驗證碼)" required />
            <button type="submit">送出</button>
        </form>
      </div>
  
  
  
      <div v-if = "loginStatus && !giftStatus">
        <h1>請填寫您想要的禮物提示於下方</h1>
        <form @submit.prevent="submitForm">
          <input v-model="shape" type="text" placeholder="請輸入禮物的形狀" required />
          <input v-model="adj" type="text" placeholder="請輸入形容禮物的兩個形容詞" required />
          <input v-model="abc" type="text" placeholder="請輸入禮物全名的注音聲母(ex:電鍋 ->ㄉㄍ)" required />
          <input v-model="ans" type="text" placeholder="請輸入禮物全名" required />
          <button type="submit">送出</button>
        </form>
      </div>
  
      <div v-if="giftStatus">
  
        <div class="hint-item">
          <strong>禮物形狀：</strong>{{ giftHint.shape }}
        </div>
        <div class="hint-item">
            <strong>禮物形容詞：</strong>{{ giftHint.adj }}
        </div>
        <div class="hint-item">
            <strong>禮物字母組合：</strong>{{ giftHint.abc }}
        </div>
        
        <input v-model="checkAns" type="text" placeholder="答案" required/>
  
        <button @click="checkAnswer">確認答案</button>
       
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref } from 'vue'
  
  const shape = ref('')
  const adj = ref('')
  const abc = ref('')
  const ans = ref('')
  const response = ref()
  
  
  async function submitForm() {
    try {
      const res = await $fetch('https://admin.petskeeper.com.tw/api/christmas/store', {
        method: 'POST',
        body: { shape: shape.value, adj: adj.value, abc: abc.value, ans: ans.value, phone: phone.value },
      })
      
      alert(res.message)
      
    } catch (error) {
      response.value = { error: error.message }
    }
  }
  
  const phone = ref('')
  const code = ref('')
  const loginStatus = ref(false) 
  const giftHint = ref({})
  const giftStatus = ref(false)
  
  async function submitLoginForm() {
    try {
      const res = await $fetch('https://admin.petskeeper.com.tw/api/christmas/login', {
        method: 'POST',
        body: { phone: phone.value, code: code.value },
      })
  
      if(res.status ){
        loginStatus.value = true
  
        if(res.giftStatus){
            console.log(res)
          giftStatus.value = res.giftStatus
          giftHint.value = res.giftHint
        }
      } else {
        alert(res.message)
      }
    } catch (error) {
      response.value = { error: error.message }
    }
  }
  
  const checkAns = ref();
  
  const checkAnswer = () => {
    if(checkAns.value == giftHint.value.ans){
      alert('答對了')
    } else {
      alert('答錯了')
    }
  }
  </script>
  
  <style>
  .app {
    max-width: 600px;
    margin: 0 auto;
    padding: 20px;
    text-align: center;
  }
  input {
    display: block;
    width: 100%;
    margin: 10px 0;
    padding: 8px;
  }
  button {
    padding: 10px 20px;
    cursor: pointer;
  }
  .response {
    margin-top: 20px;
    text-align: left;
  }
  </style>
  