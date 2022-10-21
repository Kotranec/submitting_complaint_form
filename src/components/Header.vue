<template>    
    <div>
        <v-app-bar
            color="#344FAF"
            dark
            >
            <v-app-bar-nav-icon></v-app-bar-nav-icon>        
            <v-toolbar-title>Регистратура 122</v-toolbar-title>
            <img class="book-icon" src="../images/Vector.svg" alt="">
            <img class="grid-icon" src="../images/Group 391.svg" alt="">
            
            <div class="text-center">
                <v-menu offset-y>
                    <template v-slot:activator="{ on, attrs }">
                    <div class="complaint-button">
                        <v-btn
                            color="#D9D9D9"
                            dark
                            x-small
                            block
                            rounded
                            v-bind="attrs"
                            v-on="on" 
                            class="dropdown-buttons"                        
                        >
                            <div class="dropdown-list">                                
                                Жалоба                                
                                <div class="btn-img">
                                    <img src="../images/Vector menu.svg" alt="">
                                </div>
                            </div>
                        </v-btn>
                    </div>
                    </template>
                    <v-list>
                    <v-list-item
                        v-for="item in complaintItems"
                        :key="item.title"
                    >
                        <v-list-item-title>{{ item.title }}</v-list-item-title>
                    </v-list-item>
                    </v-list>
                </v-menu>
            </div>

            <div class="button-operators">
                <v-btn
                    depressed
                    rounded
                    block
                    x-small
                    color="#D9D9D9"  
                    class="button-operators-font"                  
                >
                    Операторы
                </v-btn>
            </div>

            <div class="text-center">
                <v-menu offset-y>
                    <template v-slot:activator="{ on, attrs }">
                    <div class="after-call-work-button">
                        <v-btn
                            color="#D9D9D9"
                            dark
                            x-small
                            block
                            rounded
                            v-bind="attrs"
                            v-on="on" 
                            class="dropdown-buttons"                        
                        >
                            <div class="dropdown-list">                                
                                Поствызывная обработка                                
                                <div class="btn-img">
                                    <img src="../images/Vector menu.svg" alt="">
                                </div>
                            </div>
                        </v-btn>
                    </div>
                    </template>
                    <v-list>
                    <v-list-item
                        v-for="item in afterCallWorkItems"
                        :key="item.title"
                    >
                        <v-list-item-title>{{ item.title }}</v-list-item-title>
                    </v-list-item>
                    </v-list>
                </v-menu>
            </div>

            <div>
                <v-switch
                    v-model="switchMode"
                    color="success"
                    hide-details                    
                >
                </v-switch>
            </div>

            <div class="label-surname">
                Фамилия И.О.
            </div>

            <div class="user-icon">
                <img src="../images/user.svg" alt="">
            </div>            
            <div class="outlined-icon">
                <img src="../images/Outlined.svg" alt="">
                <div class="outlined-dash"> 
                    ---
                </div>
            </div>
        </v-app-bar>            
    </div>
</template>

<script>
export default {
  name: 'Header',

  data() {
    return {
      complaintItems: [      
        { title: '1' },
        { title: '2' },
        { title: '3' },
        ],
      afterCallWorkItems: [
          { title: '11' },
          { title: '12' },
          { title: '13' },
        ],
      switchMode: true,
    };
  },
}
</script>

<style scoped>
.dropdown-buttons {
    margin: 0 20px;
    padding: 20px;
    color: black;
    background: white;
}

.dropdown-list {
    display: flex;
    margin-right: 20px;
}

.btn-img {
    position: absolute;
    right: 0px;
}

.book-icon {
    margin: 0 20px 0 40px;
    cursor: pointer;
}

.grid-icon {
    margin-right: 130px;
    cursor: pointer;
}


.user-icon {
    margin: 8px 20px 0px 6px;
    cursor: pointer;
}

.outlined-icon {
   margin-top: 10px;
   cursor: pointer;
}

.outlined-dash {
    position: absolute;
    top: 20px;
    right: 29px;
}

.complaint-button {
    min-width: 218px;
    margin-right: 40px;
}

.button-operators {
    min-width: 148px;
}

.after-call-work-button {
    min-width: 220px;
    margin-right: 40px;
}

.label-surname {
    font-size: 12px;
}

.button-operators-font {
    color: black;
}
</style>
