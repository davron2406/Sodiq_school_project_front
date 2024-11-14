<template>
    <div class="modal-backdrop">
        <div class="modal">
            <header class="modal-header">
                <slot name="header">
                    Book Meeting
                </slot>
                <button type="button" class="btn-close" @click="close()"> x
                </button>
            </header>

            <section class="modal-body">
              <div class="meeting">
                <div class="input-row">
                  <div class="input-wrapper width100">
                    <label for="date" class="input-label">Date</label>
                    <input type="date" id="date" v-model="meeting.date" @change="getTime()">
                  </div>
                </div>  

                
                
                <div class="input-row"> 
                  <div class="input-wrapper" >
                    <label for="teacher" class="input-label">Teacher</label>
                    <select v-model="meeting.teacherId" @change="getTime()">
                      <option v-for="teacher in teachers" :value="teacher.id">{{teacher.firstName + teacher.lastName}}</option>
                    </select>
                  </div>
                </div> 

                <div class="input-row">
                  <div class="input-wrapper width100">
                    <label for="time" class="input-label">Time</label>
                    <select v-model="meeting.time">
                      <option v-for="item in times" :value="item">{{item}}</option>
                    </select>
                  </div>
                </div>
              </div>

            
            </section>

            <footer class="modal-footer">
                <button type="button" class="btn-green" @click="bookMeeting()"> 
                    Add
                </button>
            </footer>
        </div>

    </div>

    <Popup v-if="isPopupOpen" :message="this.popupMessage"
            time=3 
            :backgroundColor="this.backgroundColor"
             @closePopup="closePopup">
    </Popup>

</template>

<script>
  import Popup from "./Popup.vue";
  import axios from "axios"
  export default{
    data(){
      return{
        meeting:{
          date: null,
          time:"",
          teacherId: "",
          questionImage: null, 
        },

        isPopupOpen: false,
        popupMessage: "",
        backgroundColor: "",


        teachers: [],

        times:[
            "17:00",
            "17:10",
            "17:20",
            "17:30",
            "17:40",
            "17:50",
            "18:00",
            "18:10",
            "18:20",
            "18:30",
            "18:40",
            "18:50"
        ],
      }
    },

    components: {
      Popup,
    },

    methods: {
   
        async bookMeeting(){
          const response = await axios.post('http://localhost:8080/api/meeting', this.meeting, {
            headers:{
              'Authorization':  'Bearer ' + localStorage.getItem('token'),
            },
          })

          this.popupMessage = response.data.message
          if(response.data.success){
            this.backgroundColor = "green"
          }
          else{
            this.backgroundColor = "red"
          }

          this.isPopupOpen= true;
        },


        async getTeachers(){
          const response = await axios.get("https://sodiq-school-project.onrender.com/api/user/getTeachers", {headers: {"Authorization": "Bearer " + localStorage.getItem("token")}})
          console.log(response)

          this.teachers = response.data;
        },  

        async getTime(){
          if(this.meeting.teacherId != "" && this.meeting.date != null){
          const response = await axios.get("https://sodiq-school-project.onrender.com/api/meeting/getTeacherFreeTime",
                   {
                    params: {teacherId: this.meeting.teacherId, date: this.meeting.date},
                    headers: {"Authorization": "Bearer " + localStorage.getItem("token")}
                   })
          console.log(response)

          this.times = response.data.data;
        }
      },

        close(){
          this.$emit('close')
        },

        closePopup(){
            this.isPopupOpen = false

            if(this.backgroundColor === "green"){
            this.$emit('close')
          }
        },

        addAnswer(){
          this.question.answers.push({
            answerText:"",
            extra:"",
            isTrue: false,
            answerImg: null,}
          )
        },

       

        // async getQuestionImage(){
        //   console.log("mounted working")
        //   const response = await axios.get("http://localhost:8080/api/image/downloadImage/ee5939e4-227e-4f7f-87ca-81cbcc4ca389", {headers: {'Authorization': 'Bearer ' + localStorage.getItem('token')}})
        //   console.log(response);
        //   this.image = 'data:image/png;base64,' + response.data.data
        // }
    },

    mounted(){
      this.getTeachers()
    }
  }

</script>

<style>
    .modal-backdrop {
      position: fixed;
      top: 0;
      left: 0;
      right: 0;
      bottom: 0;      
      background-color: rgba(0, 0, 0, 0.3);
      display: flex;
      justify-content: center;
      align-items: center;
    }

  .modal {
    background: #FFFFFF;
    box-shadow: 2px 2px 20px 1px;
    overflow-x: auto;
    display: flex;
    flex-direction: column;
    padding: 10px;
    border-radius: 20px;
    height: 400px
  }


  
  .modal-header,
  .modal-footer {
    padding: 15px;
    display: flex;
  }

  .modal-header {
    position: relative;
    border-bottom: 1px solid #eeeeee;
    color: #1b2559;
    justify-content: space-between;
  }

  .modal-footer {
    border-top: 1px solid #eeeeee;
    flex-direction: column;
    justify-content: flex-end;
  }

  .modal-body {
    position: relative;
    padding: 20px 50px;
    overflow-y: scroll;
    height: 70vh;
  }

  .btn-close {
    position: absolute;
    top: -10px;
    right:-10px;
    border: none;
    font-size: 20px;
    padding: 10px;
    cursor: pointer;
    font-weight: bold;
    color: red;
    background: transparent;
  }

  .btn-green {
    color: white;
    background: #4AAE9B;
    border: 1px solid #4AAE9B;
    border-radius: 2px;
    padding: 5px ;
  }

  .input-row{
    display: flex;
    gap: 50px;
    align-items: center;
    width: 100%;
    justify-content: space-between;
  }

  .input-wrapper{
    display: flex;
    flex-direction: column;
  }

  .width100{
    width: 100%;
  }
  .input-label{
    font-size: 16px;
  }

  .input-wrapper input{
    padding: 10px;
    border-radius: 5px;
    border: 2px solid  #C0C4C9;
    background-color: #F9FAFB;
    outline: none;
    font-size: 18px;
  }

  .input-wrapper input:focus{
    border: 2px solid #3b4ce2;
  }

  input[type="file"] {
    position: relative;
  }

input[type="file"]::file-selector-button {
  width: 40px;
  color: transparent;
  cursor: pointer;
}

/* Faked label styles and icon */
input[type="file"]::before {
  position: absolute;
  z-index: 100;
  pointer-events: none;
  top: 12px;
  left: 20px;
  height: 20px;
  width: 20px;
  content: "";
  background-image: url("../assets/upload-sign.svg");
  background-size: cover; /* Cover the entire button */
  background-position: center;
  color: #1b2559;
  /* background-color: black; */
}

  .answers{
    padding-top: 20px;
    margin-top: 30px;
    border-top: 2px solid #C0C4C9;
  }

  .answer{
    padding: 20px 0;
    border-bottom: 1px solid #C0C4C9;
  }

  .width50{
    width: 45%;
  }

</style>