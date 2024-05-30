<template>
  <div class="login-form">
    <div class="form-header">Авторизация</div>
    <div class="input-container">
      <label for="user-login" class="icon"><i class="fas fa-user"></i></label>
      <input 
        type="text" 
        id="user-login" 
        placeholder="Логин" 
        v-model="userLogin" 
        :class="{'empty': !userLogin, 'error': showUsernameError}"
        required 
        class="input-field"
        v-focus
        @blur="validateUsername"
        @input="validateUsername"
      >
      <p v-if="!showUsernameError" class="error-message">Введите валидный логин</p>
    </div>
    <div class="input-container">
       <label for="password" class="icon"><i class="fas fa-lock"></i></label>
      <input 
        type="password" 
        id="password" 
        v-model="userPassword" 
        :class="{'empty': !userPassword}"
        placeholder="Пароль" 
        required 
        class="input-field"
      >
      <p v-if="userPassword === '' ">Пароль должен быть больше 11-х символов</p>
      <div class="char-counter">{{ userPassword.length }} / 15</div>
      <p v-if="userPassword !== '' && userPassword.length < 11" class="error-message">
        Пароль должен быть больше 11 символов
      </p>
    <div class="checkbox-container">
      <input type="checkbox" id="rememberMe" v-model="rememberMe">
      <label for="rememberMe">Запомнить меня</label>
    </div>
    <button @click="login" class="login-button">ВХОД</button>
  </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      userLogin: '',
      userPassword: '',
      rememberMe: false,
      showError: false,
      showUsernameError: false,
      isUsernameFocused: false
    };
  },
  methods: {
    login() {
      this.validateUsername();
      if (this.showUsernameError || this.userPassword.length < 11) {
        this.showError = true;
        return;
      }

      const payload = {
        username: this.userLogin,
        password: this.userPassword,
        rememberMe: this.rememberMe
      };
      const config = {
      headers: {
        'Content-Type': 'application/json', 
      },
      withCredentials: true, 
      };

      console.log('Отправляемые данные:', payload, config);
      console.log(config);

      axios.post('https://analytika.kz/api/login', payload)
        .then(response => {
          if (response.status === 200) {
          console.log('Login successful:', response.data);
          } else {
            alert('Ошибка: ' + response.statusText);
          }
        })
        .catch(error => {
          console.error('Login error:', error);
          if (error.response) {
            if (error.response.status === 400) {
              alert('Ошибка: ' + error.response.data.message);
            } else {
              console.error('Login error:', error.message);
            }
          } else if (error.request) {
            console.error('Request error:', error.request);
          } else {
            console.error('Error:', error.message);
          }
        });
    },
    handleDocumentClick() {
      this.showError = true;
    },
    validateUsername() {
      this.isUsernameFocused = false;
      const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
      if (!this.userLogin || !emailRegex.test(this.userLogin)) {
        this.showUsernameError = true;
      } else {
        this.showUsernameError = false;
      }
    },
  }
};
</script>


<style scoped>

p {
    font-size: 12px;
}

.login-form {
  max-width: 400px;
  margin: auto;
  padding: 0;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  /* border-radius: 2px; */
  background-color: white;
}

.form-header {
  background-color: #2e3a57; 
  color: white;
  padding: 15px;
  /* text-align: center; */
  font-size: 18px;
  border-radius: 2px;
}

.input-container {
  position: relative;
  margin-bottom: 20px;
  padding: 0 20px;
}

.icon {
  position: absolute;
  left: 0;
  top: 50%;
  transform: translateY(-50%);
  color: #1e3380;
}

.input-field {
  width: calc(100% - 30px);
  padding: 10px 10px 10px 30px;
  border: none;
  border-bottom: 2px solid #1e3380;
  outline: none;
  font-size: 16px;
}

.input-field.empty::placeholder {
  color: #1e3380;
}

.input-field.empty + .char-counter {
  color: gray;
}

.input-field:focus {
  border-bottom: 2px solid #3f51b5;
}

.char-counter {
  position: absolute;
  right: 0;
  bottom: -20px;
  font-size: 12px;
  color: gray;
}

.checkbox-container {
  display: flex;
  align-items: center;
  margin-bottom: 20px;
  padding: 0 20px;
}

.login-button {
  width: calc(100% - 40px);
  margin: 0 20px 20px 20px;
  padding: 10px;
  background-color: #3f51b5;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-size: 16px;
}

.login-button:hover {
  background-color: #303f9f;
}

/* .empty {
    color: red;
} */
</style>