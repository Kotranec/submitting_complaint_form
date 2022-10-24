<template>    
    <div class="conteiner">        
        <div class="inputs">                        
            <form action="" class="form" @submit.prevent="submit">
              <div class="complaint">
                <div class="complaint-label">
                  Жалоба
                </div>
                <div class="call">
                  <div class="call-icon">
                    <img src="../images/Group 31.svg" alt="">
                  </div>
                  <div class="call-info">
                    <p class="call-type">
                      Входящий вызов
                    </p>
                    <p class="call-number">
                      +1 (234) 567-89-01
                    </p>
                    <p class="call-time">
                      03:22
                    </p>
                  </div>
              </div>
            </div>
              <div class="form-fields">
                <v-autocomplete
                  v-model="polyclinic"
                  :items="polyclinics"
                  label="Учреждение"
                  class="form-field"
                  required
                >
                </v-autocomplete>
                <v-autocomplete
                  v-model="type_info"
                  :items="types_info"
                  label="Предмет жалобы"
                  class="form-field"
                  required
                >
                </v-autocomplete>
                <div>             
                  <v-text-field
                    v-model="feedback_number"
                    label="Контактный телефон"
                    class="form-field"
                    required
                  >
                  </v-text-field>                
                </div>
              </div>
              <div class="information-field">
                Содержание жалобы
                <textarea
                  v-model="information"                  
                  rows="4" 
                  cols="72"
                  class="input-information"
                  required               
                ></textarea>
              </div>                       
              <div class="send-buttons">
                <v-btn 
                  class="ma-2"
                  :loading="loading"
                  :disabled="loading"
                  color="success"
                  type="submit"
                >
                  Применить
                </v-btn>
                <div class="fail">
                  {{fail}}
                </div>
                <div class="completed">
                  {{completed}}
                </div> 
              </div>
            </form>

            <div>
                <v-row justify="center">
                  <v-dialog
                    v-model="dialog"
                    persistent
                    max-width="600px"
                  >                    
                    <v-card>
                      <v-card-title>
                        <span class="text-h5">Авторизация</span>
                      </v-card-title>
                      <v-card-text>
                        <v-container>
                          <v-row>
                            
                            <v-col cols="12">
                              <v-text-field
                                v-model="login"
                                label="Login*"
                                required
                              ></v-text-field>
                            </v-col>
                            <v-col cols="12">
                              <v-text-field
                                v-model="password"
                                label="Password*"
                                type="password"
                                required
                              ></v-text-field>
                            </v-col>                            
                          </v-row>
                        </v-container>
                      </v-card-text>
                      <v-card-actions>
                        <v-spacer></v-spacer>                        
                        <v-btn
                          color="blue darken-1"
                          text
                          @click="sendSignIn"
                        >
                          Отправить 
                        </v-btn>
                        <v-btn
                          color="blue darken-1"
                          text
                          @click="dialog = false"
                        >
                          Отмена
                        </v-btn>
                      </v-card-actions>
                    </v-card>
                  </v-dialog>
                </v-row>
                </div>                        
        </div>
        <div class="footer">
            </div>
    </div>    
</template>

<script>

export default {
  name: 'Content',

  data() {
    return {      
      polyclinics: ['Поликлиника 1', 'Поликлиника 2'],
      types_info: ['Жалоба на врача 1', 'Жалоба на врача 2'],

      polyclinic: null,
      type_info: null,
      feedback_number: null,
      information: null,
      dataFormIsComplited: false,      

      loading: false,
      authorization: false,
      dialog: false,
      login: null,
      password: null,
      fail: '',
      completed: '',
    }
  },

  created: function() {
    this.checkToken();
  },

  methods: {
    async signIn() {
      this.fail = "";
      const tokenResponse = await fetch('http://176.124.136.35:8080/api/v1/token/', {
        method: 'POST',
        headers: {
          "Content-Type": "application/json"
        },
        body: JSON.stringify({
          username: this.login,
          password: this.password
        })
      })
      
      if (tokenResponse.status == 200) {
        const tokenData = {...await tokenResponse.json(), tokenDate: Date.now()};        
        saveSessionToken(tokenData);
        this.authorization = true;
      } else {
        this.fail = "Ошибка при авторизации";
      }
      this.dialog = false;
    },

    async submit() {  
      this.fail = "";
      this.dataFormIsComplited = true;

      await this.checkToken();      
      if (!this.authorization) return null;

      const informationData = {
        patient: 1,
        polyclinic: this.polyclinics.indexOf(this.polyclinic) + 1,
        call_status: 1,
        type_info: this.types_info.indexOf(this.type_info) + 1,
        information: this.information,
        feedback_number: this.feedback_number,
        operator: 1,
        executor: 1
      }

      const informationResponse = await fetch(
        "http://176.124.136.35:8080/api/v1/registration/complaint-card/",
        {
          method: "POST",
          headers: {
            "Content-Type": "application/json",
            'Authorization': `Bearer ${getSessionAccessToken().access}`,            
          },
          body: JSON.stringify(informationData),
        }
      );

      if (informationResponse.status == 401) {
        this.authorization = false;
        this.dialog = true;
      }

      if (informationResponse.status == 201) {
        this.completed = "Выполнено успешно";
        setTimeout(() => this.completed = "", 3000);
      } else {
        this.fail = "Ошибка отправки формы";
      }
      this.loading = false;   
    },

    async checkToken() {
      if (!localStorage.tokenData) {  // No token
        this.authorization = false;
        this.dialog = true;
        return false;        
      }

      const tokenDate = JSON.parse(localStorage.tokenData).tokenDate;
      const tokenLifetime = 1740000 //token lifetime 30 min (so let's take 29 minutes)
      const refreshLifetime = 86340000 //token lifetime 30 min (so let's take 29 minutes)
      const dateNow = Date.now();      
    
      if (dateNow < (tokenDate + tokenLifetime)) {  // Token is fresh
        this.authorization = true;
        return true;
      }

      if (dateNow >= (tokenDate + refreshLifetime)) {  // refreshToken is not fresh
        this.authorization = false;
        this.dialog = true;
        return false;
      }

      if (dateNow >= (tokenDate + tokenLifetime) && dateNow < (tokenDate + refreshLifetime)) {  //Token is not fresh, bat refreshToken still fresh
        this.loading = true; 
        const refreshResponse = await fetch(
          "http://176.124.136.35:8080/api/v1/token/refresh/",
          {
            method: "POST",
            headers: {
              "Content-Type": "application/json",          
            },
            body: JSON.stringify({refresh: getSessionRefreshToken()}),
          }
        );

        if (refreshResponse.status == 200) {
          const tokenData = {...await refreshResponse.json(), tokenDate: Date.now()};
          saveSessionToken(tokenData);
          this.authorization = true;
          this.loading = false;
          return true;
        }
      }    
      this.authorization = false;
      this.loading = false;
      return false;
    },

    async sendSignIn() {
      await this.signIn();
      this.dataFormIsComplited && this.submit();
    }
  }
}

function getSessionAccessToken() {  
  return localStorage.tokenData && JSON.parse(localStorage.tokenData);  
}
function saveSessionToken(token) {
  localStorage.setItem('tokenData', JSON.stringify({...getSessionAccessToken(), ...token}));
}
function getSessionRefreshToken() {
  return localStorage.tokenData && JSON.parse(localStorage.tokenData).refresh;
}    
</script>

<style scoped>
.conteiner {
    display: flex;
    flex-direction: column;
    align-items: flex-end;
    max-width: 1423px;
    height: 638px;
    border: 1px solid rgb(201, 199, 199);
    background-image: url("../images/image 6.png");
    background-repeat: no-repeat;
}

.inputs {
    width: 677px;
    height: 540px;
    background: white;
    margin: 21px 8px 8px 8px;
}

.footer {    
    display: flex;
    align-self: normal;
    height: 202px;
    background: white;
}

.form {
  max-width: 620px;
  padding-left: 20px;
}

.form-fields {
  max-width: 297px;
}

.information-field {
    opacity: 0.8;
    font-size: 16px;
}

.input-information {
  border: 1px solid #C5C5C5;
  border-radius: 5px;
  resize: none;
}
.fail {
  color: red;
  font-size: 24px;
}
.completed {
  color: green;
}

.send-buttons {
  display: flex;
  flex-direction: column;
  align-items: flex-end;
}

.complaint-label {
  font-weight: 700;
  font-size: 20px;
  margin: 10px 0 30px;
}

.form-field {
  padding-top: 0px;
  margin-top: 0px;
}

.call {
  border-radius: 15px;
  background: rgba(61, 91, 197, 0.62);
  width: 161px;
  height: 55px;  
  right: 100px;
  display: flex;
  align-items: center;
  justify-content: space-between;  
  padding: 12px;
}

.call-icon {
  display: flex;
  align-items: center;
}

.call-info {
  display: flex;
  align-items: flex-end;
  flex-direction: column;
}

.call-type {
  font-weight: 700;
  font-size: 12px;
  line-height: 14px;
  margin-bottom: 2px;
  color: #2944A4;
}

.call-number {
  font-weight: 500;
  font-size: 10px;
  line-height: 12px;
  margin-bottom: 2px;
  color: #FFFFFF;
}

.call-time {
  font-weight: 500;
  font-size: 8px;
  line-height: 10px;
  margin-bottom: 0px;
  color: #FFFFFF;
}

.complaint {
  display: flex;
  justify-content: space-between;
}
</style>
