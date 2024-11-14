<template>
    <div class="meeting container">
        <div class="table-wrapper">
            <table class="form-table">
                <thead>
                    <tr>
                        <th>Date</th>
                        <th>Time</th>
                        <th>Teacher</th>
                        <th>Student</th>
                    
                    </tr>
                </thead>

                <tbody class="table-body">
                    <tr v-for="meeting in meetings">
                        <td>{{ meeting.date }}</td>
                        <td>{{ meeting.time }}</td>
                        <td>{{ meeting.teacher.firstName }}</td>
                        <td>
                            {{ meeting.student.firstName }}
                        </td>
                       
                    </tr>
                </tbody>
            </table>
        </div>
       
    </div>

    <button @click="createMeeting">Book Meeting</button>

    <BookMeeting v-if="isModalOpen"  @close="createMeeting"></BookMeeting>

</template>

<script>
    import axios from 'axios'
    import BookMeeting from "./BookMeetingModal.vue"
 

    export default{
        data(){
            return{

                meetings:[],
                isModalOpen: false,
                isAnswersModalOpen: false,
                defaultPageNumber: 0,
                defaultPageSize: 10,

                answersQuestionId: ""
            }
        },

        components: {
            BookMeeting
        },
      

        methods:{
            createMeeting(){
                console.log("creating is working")
                if(this.isModalOpen){
                    this.isModalOpen = false
                }else{
                    this.isModalOpen = true;
                }

                console.log(this.isModalOpen)
            },

            async getMeetings(){
                const response = await axios.get("https://sodiq-school-project.onrender.com/api/meeting/getMeetings" ,
                    {headers: {"Authorization": "Bearer " + localStorage.getItem("token")}}
                )
                console.log(response)
                this.meetings = response.data.data;              
            },

            openAnswersModal(questionId){
                    this.isAnswersModalOpen = true;
                    this.answersQuestionId = questionId;
            },

            closeAnswerModal(){
                this.isAnswersModalOpen = false;
                this.answersQuestionId = ""
            },

            forward(){
                this.defaultPageNumber++;
                this.getQuestions(this.defaultPageNumber, this.defaultPageSize)
            }

         
        },

        mounted(){
            this.getMeetings();
        }

    
    }
</script>

<style>

    .meeting{
        width: 100%;
    }

    .table-wrapper{
        border-radius: 20px;
        overflow: hidden;
        border: 1px solid #ddd;
    }
    .form-table {
        width: 100%;
        border-collapse: collapse;
        font-size: 14px;
        line-height: 32px;
    }

    .form-table th, .form-table td {
        border: 1px solid #ddd;
        padding: 0 5px;
        text-align: left;
    }

    .form-table th {
        background-color: #f2f2f2; 
        font-weight: bold;
    }


    .form-table td {
        line-height: 18px;
    }

    .form-table input[type="text"],
    .form-table input[type="number"],
    .form-table input[type="file"] {
        width: 100%;
        padding: 5px;
        border: 1px solid #ccc;
        border-radius: 4px;
    }

    .form-table button {
        background-color: #4CAF50;
        color: white;
        padding: 5px;
        border: none;
        border-radius: 4px;
        cursor: pointer;
    }

    .form-table button:hover {
        background-color: #45a049;
    }

    .table-body td{
        padding: 10px;
        min-height: 30px;
    }
 
    td:first-child {
        width: 40%; /* Set width for the first column */
    }
   
</style>