<template>
<div class="container">
  <h1 id="title">Seggle에 오신 걸 환영합니다</h1>
  <form id="register-form" class="row" :key="formRegister"
        :class="{'was-validated': validated }"
        @submit.prevent="handleRegister" novalidate>
    <!-- 아이디 -->
    <div class="id-form">
      <label for="userID">아이디</label>
      <input v-model="formRegister.userID"
            type="text"
            id="userID"
            class="form-control"
            :class="{'is-invalid': invalidID }"
            @blur="checkUserID" @focus="invalidID = false"
            required>
      <div class="invalid-feedback">
        {{ this.feedback.userID }}
      </div>
    </div>
    <!-- 이름 -->
    <div class="name-form">
      <label for="username">이름</label>
      <input v-model="formRegister.username"
            type="text"
            id="username"
            class="form-control"
            required>
      <div class="invalid-feedback">
        {{ this.feedback.username }}
      </div>
    </div>
    <!-- 이메일 -->
    <div class="email-form">
      <label for="email">이메일</label>
      <input v-model="formRegister.email"
            type="email"
            id="email"
            class="form-control"
            :class="{'is-invalid': invalidEmail }"
            @blur="checkUserEmail" @focus="invalidEmail = false"
            required>
      <div class="invalid-feedback">
        {{ this.feedback.email }}
      </div>
    </div>
    <!-- 비밀번호 -->
    <div class="password-form">
      <label for="password">비밀번호</label>
      <input v-model="formRegister.password"
            type="password"
            id="password"
            class="form-control"
            required>
      <div class="invalid-feedback">
        {{ this.feedback.password }}
      </div>
    </div>
    <!-- 비밀번호 확인 -->
    <div class="password-again-form">
      <label for="password-again">비밀번호 확인</label>
      <input v-model="formRegister.passwordAgain"
            type="password"
            id="password-again"
            class="form-control"
            :class="{'is-invalid': invalidPasswordAgain }"
            @blur="checkPasswordAgain" @focus="invalidPasswordAgain = false"
            required>
      <div class="invalid-feedback">
        {{ this.feedback.passwordAgain }}
      </div>
    </div>
    <button class="btn" type="submit">가입하기</button>
  </form>
</div>
</template>

<script>
import api from '@/api/index.js'
import validator from '@/utils/validators.js'

export default {
  name: 'Register',
  data () {
    return {
      formRegister: {
        userID: '',
        username: '',
        email: '',
        password: '',
        passwordAgain: ''
      },
      validated: false,
      invalidID: false,
      invalidEmail: false,
      invalidPasswordAgain: false,
      feedback: {
        userID: '아이디를 입력하세요',
        username: '이름을 입력하세요',
        email: '이메일을 입력하세요',
        password: '비밀번호를 입력하세요',
        passwordAgain: '비밀번호를 입력하세요'
      }
    }
  },
  methods: {
    async submitForm () {
      try {
        const data = {
          username: this.formRegister.userID,
          name: this.formRegister.username,
          email: this.formRegister.email,
          password: this.formRegister.password,
          password2: this.formRegister.passwordAgain
        }
        const res = await api.registerUser(data)
        console.log(res)
      } catch (err) {
        console.log(err)
      }
    },
    async checkUserExist () { // 아이디, 이메일 중복 검사
      try {
        const data = {
          user_id: this.userID,
          user_email: this.email
        }
        const res = await api.checkUserIDorEmail(data)
        if (res.data.user_id === this.formRegister.userID) {
          console.log('존재하는 아이디')
          this.feedback.userID = '이미 존재하는 아이디입니다.'
          this.invalidID = true
        }
        if (res.data.user_email === this.formRegister.email) {
          this.feedback.email = '이미 존재하는 이메일입니다.'
          this.invalidEmail = true
        }
        // return res.data.user_id === this.formRegister.userID || res.data.user_email === this.formRegister.email
      } catch (err) {
        console.log(err)
      }
    },
    checkUserID () {
      // this.checkUserExist()
      if (!(validator.validateID(this.formRegister.userID))) {
        console.log('유효하지 않은 아이디입니다.')
        this.feedback.userID = '아이디 형식이 올바르지 않습니다.'
        this.invalidID = true
      }
    },
    checkUserEmail () {
      // this.checkUserExist()
      if (!(validator.validateEmail(this.formRegister.email))) {
        this.feedback.email = '이메일 형식이 올바르지 않습니다.'
        this.invalidEmail = true
      }
    },
    checkPasswordAgain () { // 비밀번호 재확인
      if (this.formRegister.passwordAgain !== this.formRegister.password) {
        console.log('비밀번호 일치하지 않음')
        this.feedback.passwordAgain = '비밀번호가 일치하지 않습니다.'
        this.invalidPasswordAgain = true
      }
    },
    checkFormValid () {
      // 공백, 중복 아이디, 중복 이메일, 비밀번호 재확인 검사
      if (Object.values(this.formRegister).includes('') || this.invalidID || this.invalidEmail || this.invalidPasswordAgain) {
        return false
      } else {
        console.log('통과')
        return true
      }
    },
    handleRegister () {
      if (this.checkFormValid()) {
        this.submitForm()
        this.$router.push('/login')
      } else {
        this.validated = true
      }
    }
  }
}
</script>

<style scoped lang="scss">
.container {
    padding: 2rem 3rem;
    max-width: 600px;
  h1 {
    padding: 3rem 0rem;
  }

  form {
    font-size: 1rem;
    label {
      float: left;
      padding: 1rem 0rem;
    }
    .form-control {
      padding: 0.8rem 1rem;
    }
    .btn {
      padding: 0.5rem 1rem;
      margin-top: 2rem;
      color: white;
      font-size: 1.25rem;
      font-weight: 600;
    }
  }
}
</style>
