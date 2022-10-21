<template>    
    <div class="conteiner">        
        <div class="inputs">                        
            <form action="" class="form" @submit.prevent="submit">
              <div class="head1">
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
                <v-btn v-if="authorization"
                  class="ma-2"
                  :loading="loading"
                  :disabled="loading"
                  color="success"
                  type="submit"
                >
                  Применить
                </v-btn>
                <v-btn v-else
                  class="ma-2"
                  :loading="loading"
                  :disabled="loading"
                  color="primary"
                  @click="dialog = true"
                >
                  Аваторизация
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
                          @click="signIn"
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
      polyclinics: ['Поликлиника 1', 'Поликлиника 2', 'Поликлиника 3', 'Поликлиника 4'],
      types_info: ['Жалоба на врача 1', 'Жалоба на врача 2', 'Жалоба на врача 3', 'Жалоба на врача 4'],

      polyclinic: null,
      type_info: null,
      feedback_number: null,
      information: null,

      loading: false,
      authorization: false,
      dialog: false,
      login: null,
      password: null,
      fail: '',
      completed: '',
    }
  },

  methods: {
    async signIn() {  
      this.dialog = false;    
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

      const tokenData = {...await tokenResponse.json(), tokenData: Date.now()};
      
      if (tokenResponse.status == 200) {
        this.authorization = true;
        saveSessionToken(tokenData);
        this.completed = "Выполнено успешно";
        setTimeout(() => this.completed = "", 3000);
      } else {
        this.fail = "Ошибка";
        setTimeout(() => this.fail = "", 3000);
      }
    },

    async submit() {
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
            'Authorization': `Bearer ${getSessionAccessToken()}`,
          },
          body: JSON.stringify(informationData),
        }
      );

      if (informationResponse.status == 201) {
        this.completed = "Выполнено успешно";
        setTimeout(() => this.completed = "", 3000);
      } else {
        this.fail = "Ошибка";
        setTimeout(() => this.fail = "", 3000);
      }
      this.loading = false;   
    },    
  }
}

function saveSessionToken(token) {
  sessionStorage.setItem('tokenData', JSON.stringify(token));
}
function getSessionAccessToken() {
  return sessionStorage.tokenData ? JSON.parse(sessionStorage.tokenData).access : null;
}

// reserve. need to implement refresh token
function getSessionRefreshToken() {
  return sessionStorage.tokenData ? JSON.parse(sessionStorage.tokenData).refresh : null;
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
    margin-left: 2px;
    display: flex;
    width: 1410px;
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

.head1 {
  display: flex;
  justify-content: space-between;
}
</style>
