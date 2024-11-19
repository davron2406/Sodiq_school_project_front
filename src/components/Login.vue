<template>
    <div class="login"> 
        <div class="login-main container">
            <div class="login-left">
                <p>Take First step to success</p>
            </div>

            <div class="login-right">
                <div class="signIn">
                    <div>
                        <label for="phoneNumber"> Email*</label>
                        <input type="phoneNumber" id="phoneNumber" name="phoneNumber" v-model="user.phoneNumber">
                    </div>

                    <div>
                        <label for="password">Password*</label>
                        <input type="password" id="password" name="password" v-model="user.password">
                    </div>

                    <div>
                        <button class="btn-sign-up" @click="login()">SIGN IN</button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    import axios from 'axios'
    export default{
        data(){
            return{
                user:{
                    phoneNumber:"",
                    password: "",
                }
            }
        },

        methods:{
    
            async login(){
              const response = await axios.post("http://13.213.49.236/api/auth/login", this.user)
              console.log(response)
              if(response.data.success == false){
                this.user.password = null
                this.user.phoneNumber = null
              }
              else{
                localStorage.setItem('token',response.data.data)
                this.$router.push('/dashboard')
              }
            },
        },
    }

</script>

<style>
    .login{
        height: 100vh;
        width: 100vw;
        position: fixed;
        top: 0;
        left: 0;
        background-color: rgba(255, 255, 255, 0.647);
        z-index: 500;
        display: flex;
        place-items: center;
    }


    .login-main{
        display: flex;
        align-items: center;
        background-color: #EBECF3;
        padding: 50px;
        border-radius: 30px;
        position: relative;   
    }

    .container{
        width: 80%;
        margin: 0 auto;
    }

    .login-right, .login-left{
        width: 50%;
    }

    .login-left p{
        font-size: 64px;
        line-height: 64px;
    }

    .login-right, .signIn, .signUp{
        display: flex;
        flex-direction: column;
        row-gap: 20px;
    }
    .login-right div{
        display: flex;
        flex-direction: column;
    }

    .login-right div input{
        outline: none;
        border: none;
        background-color: transparent;
        border-bottom: 1px solid black;
        padding: 10px;
        font-size: 18px;
    }

    .btn-sign-up{
        padding: 10px;
        background-color: #2F2B36;
        border: none;
        color: white;
        font-weight: 600;
        letter-spacing: 1px;
        font-size: 18px;
        border-radius: 10px;
        cursor: pointer;
    }

    .scrollDisable{
        height: 100vh;
        overflow-y: hidden;
    }

    @media only screen and (max-width: 700px){
        .login-left{
            display: none;
        }

        .login-right{
            width: 100%;
        }
    }
</style>